---
id: GCP05
title: "Lab: Traffic Steering Using Geolocation"
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Routing & Addressing
date: 2025-05-28
status: In Progress
transcript_coverage: Tasks 1–3 ingested; Tasks 4+ pending
quiz_attempted: No
weak_areas: []
tags: [gcp, dns, geolocation, lab, cloud-dns, routing-policy, firewall, iap, dig]
---

# GCP05 — Lab: Traffic Steering Using Geolocation

## Overview
Cloud DNS routing policies enable DNS-based traffic steering. This lab configures and tests a **Geolocation (GEO) routing policy** — DNS returns different A records depending on where the query originates. When the source region has no explicit mapping, the nearest configured region is used (nearest-match fallback).

**WRR vs GEO (both are routing policy types):**
- **WRR:** Different weights per record → distributes traffic proportionally
- **GEO:** Source geography → returns DNS answer for that region; nearest-match for unmapped sources

## Architecture

**Default VPC — Client VMs (3 regions):**
- `us-client-vm` — us-west1 (or us-east1)
- `europe-client-vm` — europe-central2 (or europe-west2)
- `asia-client-vm` — asia-southeast1 (nearest-match test — no server in this region)

**Server VMs (2 regions only — no Asia server):**
- `us-web-vm` — us-east1-subnet
- `europe-web-vm` — europe-west2-subnet

**Cloud DNS Private Zone:** `geo.example.com`
- GEO routing policy on the record set
- us-east1 → us-web-vm IP
- europe-west2 → europe-web-vm IP
- asia-client-vm → nearest match (returns closest server IP)

**Key insight:** asia-client-vm has no matching server region. Cloud DNS nearest-match returns the geographically closest configured target — demonstrating the fallback behavior.

---

## Task 1 — Enable APIs

```bash
# Enable Compute Engine API
gcloud services enable compute.googleapis.com

# Enable Cloud DNS API
gcloud services enable dns.googleapis.com

# Verify both are enabled
gcloud services list | grep -E 'compute|dns'
# Expected output:
# NAME: compute.googleapis.com
# NAME: dns.googleapis.com
```

**Why:** Both APIs must be explicitly enabled in a GCP project before using their resources. Cloud DNS is not enabled by default even though Compute is in most environments.

---

## Task 2 — Configure Firewall Rules

Two rules required before creating any VMs:

### Rule 1 — Allow SSH via IAP
```bash
gcloud compute firewall-rules create fw-default-iapproxy \
  --direction=INGRESS \
  --priority=1000 \
  --network=default \
  --action=ALLOW \
  --rules=tcp:22,icmp \
  --source-ranges=35.235.240.0/20
```

**Why `35.235.240.0/20`:** This is Google's Identity-Aware Proxy (IAP) source range. IAP tunnels SSH through Google's infrastructure — you SSH to a GCP endpoint, which proxies the connection to your VM. Allows SSH without assigning a public IP to the VM.

**IAP vs direct SSH:**
- Direct SSH: needs public IP + firewall rule allowing 0.0.0.0/0:22 (bad practice)
- IAP SSH: VM can be private; only IAP range (35.235.240.0/20) needs access to port 22

### Rule 2 — Allow HTTP to Web Servers
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

**Why `--target-tags=http-server`:** The rule only applies to VMs that have the `http-server` network tag. Client VMs don't get this tag → the rule doesn't apply to them. Server VMs get the tag at creation time.

**Network tag pattern:** Tag-based firewall rules are how GCP applies rules to specific VM groups without subnet-level rules. Equivalent to AWS security groups attached to specific instances.

---

## Task 3 — Launch Client VMs

One client VM per region. These are the machines that will query DNS and test geolocation routing.

### US Client
```bash
gcloud compute instances create us-client-vm \
  --machine-type=e2-micro \
  --zone us-west1-c
```

### Europe Client
```bash
gcloud compute instances create europe-client-vm \
  --machine-type=e2-micro \
  --zone "europe-central2-c"
```

### Asia Client (the nearest-match test VM)
```bash
gcloud compute instances create asia-client-vm \
  --machine-type=e2-micro \
  --zone "asia-southeast1-c"
```

**Why no Asia server:** asia-client-vm is in asia-southeast1 but there will be no web server in Asia. This VM tests the nearest-match fallback — Cloud DNS will route its queries to the geographically nearest configured server (US or Europe, whichever is closer).

**e2-micro:** The smallest general-purpose machine type. Sufficient for a client that only runs `curl` and `dig` commands.

---

## Tasks 4+ — Pending
*(Paste the remaining transcript to complete this page)*

Expected remaining tasks:
- Task 4: Launch server VMs (US + Europe only, with `http-server` tag)
- Task 5: Create Cloud DNS private zone for `example.com`
- Task 6: Create GEO routing policy record set
- Task 7: Test DNS resolution from each client VM

---

## Concepts Demonstrated So Far

| Concept | Where |
|---------|-------|
| API enablement | Task 1 — services must be explicitly enabled |
| IAP SSH (35.235.240.0/20) | Task 2 — secure SSH without public IPs |
| Network tags | Task 2 — scopes firewall rule to specific VMs |
| Regional VM deployment | Task 3 — one client per region |
| Nearest-match design | Task 3 — intentional gap in Asia server coverage |

## Key Terms from This Lab

| Term | Meaning |
|------|---------|
| IAP | Identity-Aware Proxy — SSH tunnel through Google's infrastructure |
| `35.235.240.0/20` | IAP source IP range; allow this for SSH without public IPs |
| `http-server` tag | Network tag applied to web servers; scopes HTTP firewall rule |
| `e2-micro` | Smallest general-purpose VM type (2 vCPU shared, 1 GB RAM) |
| GEO routing policy | DNS returns different IPs based on query source region |
| Nearest-match | When no exact region match exists, returns closest configured target |

## What to Observe (when complete)
- US client → resolves to US server IP
- Europe client → resolves to Europe server IP
- Asia client → no mapping → resolves to nearest server IP (nearest-match)
- Query from outside VPC → NXDOMAIN (private zone isolation confirmed)
- TTL 5s → prevents caching during testing; use 300s+ in production

## Related
- [[GCP04]] — Cloud DNS routing policies (theory behind this lab)
- [[GCP06]] — module quick reference
- [[GCP01]] — network tags scoping routes (same concept applies here for firewall rules)
