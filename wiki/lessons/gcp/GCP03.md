---
id: GCP03
title: BYOIP — Bring Your Own IP
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Routing & Addressing
date: 2025-05-28
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, byoip, bgp, ip-addressing, migration]
---

# GCP03 — BYOIP (Bring Your Own IP)

## Summary
BYOIP lets enterprises use their existing public IP blocks on GCP without re-addressing. BGP longest-prefix-match (more specific route wins) governs how traffic is steered between on-prem and GCP when both advertise overlapping ranges.

## Purpose
Use existing public IP address blocks on GCP resources during cloud migration. From the internet's perspective, the IP never changes.

## Key Facts
- Available across all GCP regions
- Minimum block size: **/28** (16 addresses)
- Blocks can be distributed to any region
- **Idle BYOIP addresses incur no charges** (unlike Google-provided external IPs)
- Exclusively available to the customer who brought them
- Cannot overlap with any subnet or alias range in the customer's VPC

## Supported vs Not Supported

| BYOIP Works With | BYOIP Does NOT Work With |
|-----------------|--------------------------|
| VM static external IPs | HA VPN gateway tunnels |
| Regional NLB forwarding rules | GKE nodes and pods |
| Global external ALB forwarding rules | Shared VPC service projects |
| Classic VPN (as PRIP) | Managed instance groups with auto IP allocation |
| GKE external forwarding rules | |

## BGP Prefix Specificity with BYOIP
BGP selects the **more specific route (longer prefix)** — identical to longest-prefix-match everywhere in IP routing.

**Example:**
- On-prem advertises 203.0.112.0/20 via BGP
- BYOIP imports 203.0.112.0/23; Google advertises via BGP
- /23 is more specific than /20 → **traffic goes to GCP** for addresses in the /23
- Rest of /20 (not in /23) still routes to on-prem

**Failure mode:** If Google stops advertising the /23, BGP falls back to the /20 (on-prem). No traffic drop — automatic failback.

**Danger of equal-length prefixes:** If both customer and Google advertise the same /20, BGP behavior is unpredictable (depends on other attributes). Results in inconsistent routing and packet loss. Always use a more specific prefix for the BYOIP block.

## Real-World Use Case
Enterprise has 203.0.112.0/23 registered public IPs. Moving web services to GCP but keeping other services on-prem. Import the /23 as BYOIP → GCP advertises /23, on-prem continues advertising /20. All traffic for the /23 goes to GCP; rest stays on-prem. Customers see no IP change.

## Related
- [[GCP01]] — BGP dynamic routing and Cloud Router
- [[L07]] — IP addressing fundamentals and CIDR
- [[GCP06]] — BYOIP quick reference
