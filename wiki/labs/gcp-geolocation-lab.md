---
id: GCP05
title: "Lab: Traffic Steering Using Geolocation"
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Routing & Addressing
date: 2025-05-28
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, dns, geolocation, lab, cloud-dns, routing-policy, firewall, iap, curl, dig]
---

# GCP05 — Lab: Traffic Steering Using Geolocation

## Overview
Cloud DNS routing policies enable DNS-based traffic steering. This lab configures and tests a **Geolocation (GEO) routing policy** — DNS returns a different A record depending on where the query originates. When the source region has no explicit mapping, the **nearest configured region** is returned (nearest-match fallback).

**WRR vs GEO:**
- **WRR:** Different weights per record → distributes traffic proportionally
- **GEO:** Source geography → returns DNS answer for that region; nearest-match for unmapped sources

## Architecture

```
Default VPC
├── us-east1-subnet
│   ├── us-client-vm      ← client only
│   └── us-web-vm         ← server: "Page served from: us-west1"
├── europe-west2-subnet
│   ├── europe-client-vm  ← client only
│   └── europe-web-vm     ← server: "Page served from: europe-central2-c"
└── asia-south1-subnet
    └── asia-client-vm    ← client only (NO server in Asia → nearest-match test)

Cloud DNS Private Zone: example.com
  geo.example.com  A  GEO policy
    us-west1      → us-web-vm internal IP
    europe-central2 → europe-web-vm internal IP
    asia (any)    → nearest match (us-west1 or europe-central2)
```

**Key design decision:** No server VM is created in Asia. This is intentional — the Asia client tests the nearest-match fallback behavior of the GEO routing policy.

---

## Task 1 — Enable APIs

```bash
# Enable Compute Engine API
gcloud services enable compute.googleapis.com

# Enable Cloud DNS API
gcloud services enable dns.googleapis.com

# Verify both are enabled
gcloud services list | grep -E 'compute|dns'
# Expected:
# NAME: compute.googleapis.com
# NAME: dns.googleapis.com
```

**Why:** APIs must be explicitly enabled per project. Cloud DNS is not on by default.

---

## Task 2 — Configure Firewall Rules

### Rule 1 — SSH via IAP
```bash
gcloud compute firewall-rules create fw-default-iapproxy \
  --direction=INGRESS \
  --priority=1000 \
  --network=default \
  --action=ALLOW \
  --rules=tcp:22,icmp \
  --source-ranges=35.235.240.0/20
```

**`35.235.240.0/20` = Google's IAP source range.** IAP tunnels SSH through Google's infrastructure — no public IP needed on the VM. This is the secure, production-correct way to SSH into GCP instances.

### Rule 2 — HTTP to Web Servers
```bash
gcloud compute firewall-rules create allow-http-traffic \
  --direction=INGRESS \
  --priority=1000 \
  --network=default \
  --action=ALLOW \
  --rules=tcp:80 \
  --source-ranges=0.0.0.0/0 \
  --target-tags=http-server
```

**`--target-tags=http-server`** scopes this rule to VMs that carry the `http-server` network tag. Client VMs don't get this tag → rule doesn't apply to them. Server VMs will be created with the tag. Same concept as the route network tags from [[GCP01 - Routes and Route Preferences]].

---

## Task 3 — Launch Client VMs

One client per region. No `http-server` tag — these machines only make DNS queries and curl requests.

```bash
# US client
gcloud compute instances create us-client-vm \
  --machine-type=e2-micro \
  --zone us-west1-c

# Europe client
gcloud compute instances create europe-client-vm \
  --machine-type=e2-micro \
  --zone "europe-central2-c"

# Asia client (nearest-match test — no server in this region)
gcloud compute instances create asia-client-vm \
  --machine-type=e2-micro \
  --zone "asia-southeast1-c"
```

---

## Task 4 — Launch Server VMs

Server VMs in **US and Europe only** — intentionally none in Asia. Each runs Apache with a startup script that writes the region name to its index page, making it easy to verify which server responded.

### US Server
```bash
gcloud compute instances create us-web-vm \
  --machine-type=e2-micro \
  --zone=us-west1-c \
  --network=default \
  --subnet=default \
  --tags=http-server \
  --metadata=startup-script='#! /bin/bash
    apt-get update
    apt-get install apache2 -y
    echo "Page served from: us-west1" | tee /var/www/html/index.html
    systemctl restart apache2'
```

### Europe Server
```bash
gcloud compute instances create europe-web-vm \
  --machine-type=e2-micro \
  --zone=europe-central2-c \
  --network=default \
  --subnet=default \
  --tags=http-server \
  --metadata=startup-script='#! /bin/bash
    apt-get update
    apt-get install apache2 -y
    echo "Page served from: europe-central2-c" | tee /var/www/html/index.html
    systemctl restart apache2'
```

**`--tags=http-server`:** Applies the network tag → the HTTP firewall rule from Task 2 now applies to these VMs.

**Startup script pattern:** `--metadata=startup-script` runs on first boot. Installs Apache and writes a regional identifier to the default webpage. This is how you verify DNS routing — each curl response tells you which server (region) answered.

---

## Task 5 — Set Environment Variables

Capture internal IPs before creating DNS records.

```bash
export US_WEB_IP=$(gcloud compute instances describe us-web-vm \
  --zone=us-west1-c \
  --format="value(networkInterfaces.networkIP)")

export EUROPE_WEB_IP=$(gcloud compute instances describe europe-web-vm \
  --zone=europe-central2-c \
  --format="value(networkInterfaces.networkIP)")
```

**Why internal IPs:** The DNS private zone resolves to internal IPs — traffic stays within the VPC. External IPs would work but are unnecessary for private zone resolution and would incur egress costs.

**`--format="value(...)"` pattern:** Extracts a single field from gcloud output into a shell variable. Standard GCP scripting pattern — works in Terraform, CI pipelines, and automation scripts.

---

## Task 6 — Create the Private DNS Zone

```bash
gcloud dns managed-zones create example \
  --description=test \
  --dns-name=example.com \
  --networks=default \
  --visibility=private
```

**Key flags:**
- `--dns-name=example.com` — the domain this zone is authoritative for (trailing dot is added automatically here)
- `--networks=default` — scopes the zone to the default VPC; VMs in this VPC can resolve it; external queries get NXDOMAIN
- `--visibility=private` — zone is not visible on the public internet

**Note:** `example.com` is a real public domain but it's safe to use in a private zone because private zone resolution takes precedence for VMs in the associated VPC. External DNS queries for example.com still resolve to the real public domain.

---

## Task 7 — Create the GEO Routing Policy Record

```bash
gcloud dns record-sets create geo.example.com \
  --ttl=5 \
  --type=A \
  --zone=example \
  --routing-policy-type=GEO \
  --routing-policy-data="us-west1=$US_WEB_IP;europe-central2=$EUROPE_WEB_IP"
```

**Breakdown:**
- `--ttl=5` — 5-second TTL for testing (prevents cached responses between test iterations). Use 300+ seconds in production.
- `--routing-policy-type=GEO` — enables geolocation routing
- `--routing-policy-data` — semicolon-delimited list: `region=IP;region=IP`

**Verify the record:**
```bash
gcloud dns record-sets list --zone=example
```

Expected output includes:
```
NAME: geo.example.com.
TYPE: A
TTL: 5
DATA: us-west1: 10.142.0.3; europe-central2: 10.154.0.3
```

Also shows NS and SOA records auto-created by GCP for the zone — `ns-gcp-private.googledomains.com` handles private zone resolution.

---

## Task 8 — Testing

**Test pattern:** `curl geo.example.com` from each client VM. The startup script wrote the region name to each server's index page → the curl response tells you which server (region) answered.

**The loop:** `for i in {1..10}; do echo $i; curl geo.example.com; sleep 6; done`
- 10 iterations with 6-second sleep
- TTL is 5 seconds → sleep ensures each curl gets a fresh (uncached) DNS response
- Confirms consistent routing, not just a single lucky hit

### SSH into VMs via IAP
```bash
gcloud compute ssh europe-client-vm --zone europe-central2-c --tunnel-through-iap
gcloud compute ssh us-client-vm --zone us-west1-c --tunnel-through-iap
gcloud compute ssh asia-client-vm --zone <SELECTED-ZONE> --tunnel-through-iap
```

`--tunnel-through-iap` — uses the IAP firewall rule from Task 2. No public IP on the VM required.

### Test Results

| Client VM | Zone | Expected Response | Why |
|-----------|------|-------------------|-----|
| europe-client-vm | europe-central2-c | `Page served from: europe-central2-c` | Exact GEO match |
| us-client-vm | us-west1-c | `Page served from: us-west1` | Exact GEO match |
| asia-client-vm | asia-southeast1-c | `Page served from: us-west1` OR `europe-central2-c` | No Asia mapping → nearest match |

**Europe test output (all 10 iterations consistent):**
```
1
Page served from: europe-central2-c
2
Page served from: europe-central2-c
...
10
Page served from: europe-central2-c
```

**Asia test:** Cloud DNS applies nearest-match — asia-southeast1 is geographically closest to either us-west1 or europe-central2 depending on GCP's internal geography calculation. Response is consistent (same server every time) because Cloud DNS nearest-match is deterministic.

---

## Key Lessons from This Lab

### 1. GEO routing is DNS-level, not network-level
Traffic steering happens at DNS resolution — the client receives a different IP based on where the query originates. No packet inspection, no load balancer rules. The client connects directly to the regional server IP returned by DNS.

### 2. Nearest-match is guaranteed
You don't need to enumerate every possible source region. Cloud DNS always returns a response — it never returns NXDOMAIN for an unmapped source region. Unmapped → nearest configured target.

### 3. TTL controls testing vs production behavior
TTL 5s = every curl loop gets a fresh DNS lookup. This is critical for testing but wasteful in production (every client hits DNS more frequently → higher query volume, slightly more latency). Production TTL of 300s means clients cache the response for 5 minutes.

### 4. Private zone + internal IPs = traffic never leaves the VPC
The DNS record points to internal IPs. curl traffic goes from client VM → internal network → server VM. No public internet involved despite the `http-server` tag allowing port 80 from `0.0.0.0/0`.

### 5. Network tags are the GCP security boundary primitive
The same tag mechanism used in Task 2 (firewall) also applies in GCP01 (routes). Tags let you apply policies to VM subsets without subnet-level rules.

---

## Complete Lab Command Reference

```bash
# Task 1 — Enable APIs
gcloud services enable compute.googleapis.com
gcloud services enable dns.googleapis.com

# Task 2 — Firewall
gcloud compute firewall-rules create fw-default-iapproxy \
  --direction=INGRESS --priority=1000 --network=default \
  --action=ALLOW --rules=tcp:22,icmp --source-ranges=35.235.240.0/20

gcloud compute firewall-rules create allow-http-traffic \
  --direction=INGRESS --priority=1000 --network=default \
  --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server

# Task 3 — Client VMs
gcloud compute instances create us-client-vm --machine-type=e2-micro --zone us-west1-c
gcloud compute instances create europe-client-vm --machine-type=e2-micro --zone europe-central2-c
gcloud compute instances create asia-client-vm --machine-type=e2-micro --zone asia-southeast1-c

# Task 4 — Server VMs (with startup script)
gcloud compute instances create us-web-vm --machine-type=e2-micro --zone=us-west1-c \
  --network=default --subnet=default --tags=http-server \
  --metadata=startup-script='#! /bin/bash
    apt-get update && apt-get install apache2 -y
    echo "Page served from: us-west1" | tee /var/www/html/index.html
    systemctl restart apache2'

gcloud compute instances create europe-web-vm --machine-type=e2-micro --zone=europe-central2-c \
  --network=default --subnet=default --tags=http-server \
  --metadata=startup-script='#! /bin/bash
    apt-get update && apt-get install apache2 -y
    echo "Page served from: europe-central2-c" | tee /var/www/html/index.html
    systemctl restart apache2'

# Task 5 — Capture internal IPs
export US_WEB_IP=$(gcloud compute instances describe us-web-vm \
  --zone=us-west1-c --format="value(networkInterfaces.networkIP)")
export EUROPE_WEB_IP=$(gcloud compute instances describe europe-web-vm \
  --zone=europe-central2-c --format="value(networkInterfaces.networkIP)")

# Task 6 — Private DNS Zone
gcloud dns managed-zones create example \
  --description=test --dns-name=example.com --networks=default --visibility=private

# Task 7 — GEO Routing Policy Record
gcloud dns record-sets create geo.example.com \
  --ttl=5 --type=A --zone=example \
  --routing-policy-type=GEO \
  --routing-policy-data="us-west1=$US_WEB_IP;europe-central2=$EUROPE_WEB_IP"

# Verify
gcloud dns record-sets list --zone=example

# Task 8 — Test from each client
gcloud compute ssh europe-client-vm --zone europe-central2-c --tunnel-through-iap
# (inside VM): for i in {1..10}; do echo $i; curl geo.example.com; sleep 6; done

gcloud compute ssh us-client-vm --zone us-west1-c --tunnel-through-iap
# (inside VM): for i in {1..10}; do echo $i; curl geo.example.com; sleep 6; done

gcloud compute ssh asia-client-vm --zone asia-southeast1-c --tunnel-through-iap
# (inside VM): for i in {1..10}; do echo $i; curl geo.example.com; sleep 6; done
```

---

## Task 9 — Delete Lab Resources

Good practice: always clean up resources after a lab to avoid unexpected charges. Delete in reverse dependency order — records before zones, VMs before firewall rules.

```bash
# Delete VMs
gcloud compute instances delete -q us-client-vm --zone us-west1-c
gcloud compute instances delete -q us-web-vm --zone us-west1-c
gcloud compute instances delete -q europe-client-vm --zone europe-central2-c
gcloud compute instances delete -q europe-web-vm --zone europe-central2-c
gcloud compute instances delete -q asia-client-vm --zone SELECTED-ZONE

# Delete firewall rules
gcloud compute firewall-rules delete -q allow-http-traffic
gcloud compute firewall-rules delete -q fw-default-iapproxy

# Delete DNS record set (must delete records before deleting the zone)
gcloud dns record-sets delete geo.example.com --type=A --zone=example

# Delete private zone
gcloud dns managed-zones delete example
```

**`-q` flag:** Skips the confirmation prompt (`--quiet`). Safe to use in cleanup scripts where you know exactly what you're deleting.

**Deletion order matters:**
1. DNS record sets must be deleted before the managed zone (zone cannot be deleted while it contains records other than NS and SOA)
2. VMs can be deleted in any order
3. Firewall rules can be deleted after VMs (VMs don't depend on firewall rules to be deleted)

---

## Lab Summary

| Task | What Was Done |
|------|--------------|
| 1 | Enabled Compute and Cloud DNS APIs |
| 2 | Created IAP SSH firewall rule + HTTP rule with `http-server` tag |
| 3 | Launched client VMs in US, Europe, Asia (3 regions) |
| 4 | Launched server VMs in US and Europe only (no Asia server) |
| 5 | Captured server internal IPs as environment variables |
| 6 | Created private DNS zone for `example.com` |
| 7 | Created `geo.example.com` A record with GEO routing policy |
| 8 | Tested from all 3 clients — verified exact match + nearest-match |
| 9 | Deleted all resources |

**What was proven:**
- US client → always resolves to US server ✅
- Europe client → always resolves to Europe server ✅
- Asia client → no policy item → nearest-match server responds ✅
- Private zone → NXDOMAIN from outside VPC ✅

## Related
- [[GCP04 - Cloud DNS]] — Cloud DNS routing policies (theory behind this lab)
- [[GCP01 - Routes and Route Preferences]] — network tags for routes (same mechanism as Task 2 firewall tags)
- [[GCP06 - Module Debrief - Routing & Addressing]] — module quick reference
