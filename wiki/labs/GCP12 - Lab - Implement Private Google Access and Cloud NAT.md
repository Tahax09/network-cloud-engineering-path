---
id: GCP12
aliases: [GCP12]
title: "Lab: Implement Private Google Access and Cloud NAT"
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Private Connection Options
date: 2026-10-14
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, lab, private-google-access, cloud-nat, iap, iap-tcp-forwarding, firewall, cloud-router, nat-logging, cloud-logging, qwiklabs]
---

# GCP12 — Lab: Implement Private Google Access and Cloud NAT

## Lab Goal
Build a VM with **no external IP**, reach it over **IAP**, then add access in stages and test each one:
1. **Isolated:** no internet, no Google APIs.
2. **+ Private Google Access:** Google APIs work (Cloud Storage); the general internet still doesn't.
3. **+ Cloud NAT:** outbound internet works (`apt-get update`); still **no inbound**.
4. **+ NAT logging:** connection and drop events land in **Cloud Logging**.

This is the hands-on lab for [[GCP08 - Private Google Access]] and [[GCP11 - Cloud NAT]], completed before the module debrief ([[GCP13 - Module Debrief - Private Connection Options]]) and quiz ([[GQ01_Private_Connection_Options]]).

> Qwiklabs temporary credentials from the lab page are intentionally **not** stored here.

---

## Topology
```
                       Google APIs (storage.googleapis.com)
                          ▲  via PGA (default route → default-internet-gateway, stays on Google network)
 Cloud Shell (has ext IP) │
   │ gcloud compute ssh --tunnel-through-iap
   ▼                      │
 IAP (35.235.240.0/20) ──SSH:22──▶ vm-internal 10.130.0.x  (NO external IP)
                                   │  subnet privatenet-us 10.130.0.0/20, us-west1
                                   │  VPC privatenet (custom mode)
                                   ▼
                          Cloud NAT "nat-config" on Cloud Router "nat-router" (us-west1)
                                   ▼
                          Internet (deb.debian.org …)   ✘ no inbound via NAT
```

| Resource | Value |
|----------|-------|
| VPC | `privatenet`, **custom** subnet mode |
| Subnet | `privatenet-us`, `us-west1`, `10.130.0.0/20` (PGA **off** at first) |
| Firewall | `privatenet-allow-ssh`: ingress TCP 22 from **`35.235.240.0/20`** (IAP's range only), all instances |
| VM | `vm-internal`, `us-west1-b`, e2-standard-2, Debian 12, **External IPv4: None** |
| Bucket | Multi-region, public access prevention on; test object `access.svg` |
| NAT | `nat-config` on new Cloud Router `nat-router`, default mapping (all subnet ranges, auto IPs) |

---

## Steps & Commands (console steps → gcloud equivalents)

### Task 1: VPC, firewall, VM, IAP
```bash
gcloud compute networks create privatenet --subnet-mode=custom
gcloud compute networks subnets create privatenet-us --network=privatenet \
  --region=us-west1 --range=10.130.0.0/20            # PGA off (default)
gcloud compute firewall-rules create privatenet-allow-ssh --network=privatenet \
  --direction=INGRESS --allow=tcp:22 --source-ranges=35.235.240.0/20
gcloud compute instances create vm-internal --zone=us-west1-b --machine-type=e2-standard-2 \
  --image-family=debian-12 --image-project=debian-cloud \
  --subnet=privatenet-us --no-address                 # --no-address = no external IP
gcloud compute ssh vm-internal --zone us-west1-b --tunnel-through-iap
ping -c 2 www.google.com                              # ✘ fails: no route out
```

### Task 2: bucket + Private Google Access
```bash
export MY_BUCKET=<globally-unique-name>
gcloud storage buckets create gs://$MY_BUCKET --location=US
gcloud storage cp gs://cloud-training/gcpnet/private/access.svg gs://$MY_BUCKET
gcloud storage cp gs://$MY_BUCKET/*.svg .             # Cloud Shell ✔ (has external IP)
# on vm-internal:  gcloud storage cp gs://$MY_BUCKET/*.svg .   ✘ hangs: PGA off
gcloud compute networks subnets update privatenet-us --region=us-west1 \
  --enable-private-ip-google-access
# on vm-internal again: copy ✔
```

### Task 3: Cloud NAT
```bash
# on vm-internal: sudo apt-get update  → only Google-hosted repos respond (via PGA); Debian mirrors hang
gcloud compute routers create nat-router --network=privatenet --region=us-west1
gcloud compute routers nats create nat-config --router=nat-router --region=us-west1 \
  --auto-allocate-nat-external-ips --nat-all-subnet-ip-ranges
# wait 1–3 min for propagation; on vm-internal: sudo apt-get update ✔
```

### Task 4: NAT logging
```bash
gcloud compute routers nats update nat-config --router=nat-router --region=us-west1 \
  --enable-logging --log-filter=ALL                   # console: "Translation and errors"
# generate traffic (apt-get update), then view:
gcloud logging read 'resource.type="nat_gateway"' --limit=5 --format=json
```

---

## Observations (expected results at each stage)

| Test (from `vm-internal`) | Isolated | + PGA | + Cloud NAT |
|---------------------------|:--------:|:-----:|:-----------:|
| SSH via IAP tunnel | ✔ | ✔ | ✔ |
| `ping www.google.com` | ✘ | ✘ | (outbound only) |
| `gcloud storage cp` from bucket | ✘ | **✔** | ✔ |
| `apt-get update`, Google repos (packages.cloud.google.com) | ✘ | **✔** | ✔ |
| `apt-get update`, Debian mirrors | ✘ | ✘ | **✔** |
| Inbound connection from internet to VM | ✘ | ✘ | **✘ (never)** |

**Lab check-in answers:**
- *Did the prompt change to `@vm-internal`?* → **True**. IAP gives SSH without an external IP.
- *Which instance can access the bucket image before PGA?* → **Cloud Shell only**, because it has an external IP. `vm-internal` can only send traffic **within the VPC**.

**NAT logs** (Logs Explorer, `resource.type="nat_gateway"`) show one entry per **new NAT connection**, and an entry per **packet dropped for lack of a NAT port** (if errors are enabled). Each entry includes the VPC, VM, NAT IP:port, and destination. Logs appear after a delay of minutes.

---

## Concepts Demonstrated

| Concept | What the lab showed | Lesson |
|---------|--------------------|--------|
| **No external IP = isolated** | Default VMs get an ephemeral external IP; `None` removes all internet reachability (an org policy can enforce this) | [[GCP07 - Private Connection Options - Overview]] |
| **IAP TCP forwarding** | SSH with no bastion and no external IP; firewall opened **only to 35.235.240.0/20**; access governed by IAM (*IAP-secured Tunnel User*) | Replaces the jump box from [[L17 - Network Access and Management]] |
| **PGA is per subnet** | One toggle on `privatenet-us` unlocked Cloud Storage; it uses the **default route 0.0.0.0/0 → default-internet-gateway**, but traffic stays on Google's network | [[GCP08 - Private Google Access]] |
| **PGA ≠ internet** | Debian mirrors stayed unreachable until NAT | [[GCP08 - Private Google Access]] |
| **Cloud NAT** | Regional, on a Cloud Router (control plane), **outbound only**, managed and highly available | [[GCP11 - Cloud NAT]] |
| **NAT propagation delay** | Up to ~3 min before VMs use a new gateway | [[GCP11 - Cloud NAT]] |
| **NAT logging** | Translation + error events → Cloud Logging, the way to detect **port exhaustion** | [[GCP11 - Cloud NAT]] |

### Notes beyond the lab
- **Don't delete the default route.** PGA in this lab relies on `0.0.0.0/0 → default-internet-gateway`. In hardened VPCs that remove it, add `199.36.153.8/30 → default-internet-gateway` and point DNS at `private.googleapis.com` ([[GCP08 - Private Google Access]]).
- **Cloud NAT forces PGA on** for any subnet range it serves ([[GCP11 - Cloud NAT]]). Here PGA was enabled first anyway; in the reverse order, NAT alone would also have made the bucket copy work.
- **IAP firewall scope:** limiting SSH to `35.235.240.0/20` means even a misconfigured external IP wouldn't expose SSH to the internet: defense in depth ([[L20 - Network Defense Techniques]]).
- **Who can tunnel:** by default only project owners. Grant `roles/iap.tunnelResourceAccessor` to other users per instance or project.

---

## Real-World Connections
- **k3s on GCE (production pattern):** run nodes with `--no-address`, administer them via IAP (`gcloud compute ssh --tunnel-through-iap` or `gcloud compute start-iap-tunnel node1 6443 --local-host-port=localhost:6443` for `kubectl`), pull from **Artifact Registry via PGA**, and pull Docker Hub/GHCR images via **Cloud NAT**. Enable NAT logging to catch port exhaustion during mass image pulls.
- **AWS equivalent:** private subnet + **SSM Session Manager** (≈ IAP) + **S3 gateway endpoint** (≈ PGA) + **NAT Gateway** (≈ Cloud NAT) + VPC Flow Logs / NAT Gateway CloudWatch metrics (≈ NAT logging).
- **Network+:** NAT = PAT ([[L09 - Routing Technologies]]); IAP = zero-trust access instead of a VPN or jump box ([[L08 - Modern Networks]], [[L17 - Network Access and Management]]).

---

## Troubleshooting Scenario
**Scenario:** After the lab, a teammate rebuilds it with Terraform. `apt-get update` on the VM still hangs 10 minutes after the NAT gateway shows *Running*, but `gcloud storage cp` works.
**Analysis:**
1. Storage works, so PGA and the route are fine; the problem is NAT-specific.
2. `gcloud compute routers nats describe nat-config --router=nat-router --region=us-west1` shows `sourceSubnetworkIpRangesToNat: LIST_OF_SUBNETWORKS` with **no subnets listed**. The Terraform module didn't map `privatenet-us`.
3. Alternatively: the NAT is in **us-east1** while the VM is in **us-west1**. Cloud NAT is **regional**.
**Fix:** map the subnet (or use `ALL_SUBNETWORKS_ALL_IP_RANGES`) in the VM's region, then check that NAT logs show translations for the VM's internal IP.

---

## Hands-On Ideas
1. Redo the lab entirely with the gcloud commands above in a personal project (cost: minutes of e2 + NAT; delete everything afterwards).
2. Before and after NAT, run `curl -s ifconfig.me` on `vm-internal`. After NAT it shows the **NAT IP**, not a VM IP.
3. Switch NAT to **manual** IP allocation with a reserved static IP and confirm `ifconfig.me` shows exactly that IP.
4. Use IAP to forward a port instead of SSH: `gcloud compute start-iap-tunnel vm-internal 80 --local-host-port=localhost:8080 --zone us-west1-b` (needs a firewall rule from 35.235.240.0/20 to port 80).
5. Capture on the VM during `apt-get update` (`sudo tcpdump -i ens4 -w nat.pcap port 80 or port 443`). In Wireshark, the source is the **internal IP**: NAT happens beyond the VM, in Google's network fabric ([[WS03 - Where to Capture on the Network]]).

---

## Linked Pages
- [[GCP08 - Private Google Access]] · [[GCP11 - Cloud NAT]] · [[GCP13 - Module Debrief - Private Connection Options]]
- [[GQ01_Private_Connection_Options]] — Module quiz
- [[gcp-private-access]] — Concept reference
- [[gcp-geolocation-lab]] — Previous module's lab
- [[certs/gcp-networking]]
