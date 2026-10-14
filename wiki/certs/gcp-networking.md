---
cert: Google Cloud Networking Specialization
platform: Coursera / Google Cloud
status: In Progress
target_date: TBD
---

# Google Cloud Networking Specialization

**Program:** *Networking in Google Cloud* (Coursera, offered via Re:Coded) — 6 courses.

| # | Course | Status | Wiki lessons |
|---|--------|--------|--------------|
| 1 | Networking in Google Cloud: Fundamentals | ✅ Complete (Coursera) | Transcripts pending — to be ingested |
| 2 | Networking in Google Cloud: Routing and Addressing | 🔄 In progress | GCP01–GCP06 (Routing & Addressing module), GCP07–GCP13 (Private Connection Options module), GQ01 |
| 3 | Networking in Google Cloud: Network Architecture | Not started | — |
| 4 | Networking in Google Cloud: Network Security | Not started | — |
| 5 | Networking in Google Cloud: Load Balancing | Not started | — |
| 6 | Networking in Google Cloud: Hybrid and Multicloud | Not started | — |

## Modules Completed

### Routing & Addressing
| ID | Title | Status |
|----|-------|--------|
| [GCP01](../lessons/gcp/GCP01%20-%20Routes%20and%20Route%20Preferences.md) | Routes and Route Preferences | ✅ Complete |
| [GCP02](../lessons/gcp/GCP02%20-%20IPv6%20in%20Google%20Cloud.md) | IPv6 in Google Cloud | ✅ Complete |
| [GCP03](../lessons/gcp/GCP03%20-%20BYOIP%20-%20Bring%20Your%20Own%20IP.md) | BYOIP — Bring Your Own IP | ✅ Complete |
| [GCP04](../lessons/gcp/GCP04%20-%20Cloud%20DNS.md) | Cloud DNS | ✅ Complete |
| [gcp-geolocation-lab](../labs/gcp-geolocation-lab.md) | Lab: Traffic Steering (Geolocation) | ✅ Complete |
| [GCP06](../lessons/gcp/GCP06%20-%20Module%20Debrief%20-%20Routing%20%26%20Addressing.md) | Module Debrief: Routing & Addressing | ✅ Complete |

### Private Connection Options
| ID | Title | Status |
|----|-------|--------|
| [GCP07](../lessons/gcp/GCP07%20-%20Private%20Connection%20Options%20-%20Overview.md) | Private Connection Options — Overview | ✅ Complete |
| [GCP08](../lessons/gcp/GCP08%20-%20Private%20Google%20Access.md) | Private Google Access | ✅ Complete |
| [GCP09](../lessons/gcp/GCP09%20-%20Private%20Service%20Connect.md) | Private Service Connect | ✅ Complete |
| [GCP10](../lessons/gcp/GCP10%20-%20Private%20Services%20Access.md) | Private Services Access | ✅ Complete |
| [GCP11](../lessons/gcp/GCP11%20-%20Cloud%20NAT.md) | Cloud NAT | ✅ Complete |
| [GCP12](../labs/GCP12%20-%20Lab%20-%20Implement%20Private%20Google%20Access%20and%20Cloud%20NAT.md) | Lab: Implement Private Google Access and Cloud NAT | ✅ Complete |
| [GCP13](../lessons/gcp/GCP13%20-%20Module%20Debrief%20-%20Private%20Connection%20Options.md) | Module Debrief: Private Connection Options | ✅ Complete |
| [GQ01](../quizzes/gcp/GQ01_Private_Connection_Options.md) | Quiz 2 (graded) | ✅ Reviewed |

## Key Exam Facts (collected from sessions)

| Topic | Key Fact |
|-------|---------|
| Subnet route | Always wins — cannot be overridden by any custom route |
| Route priority | Specificity beats number; lower number wins ties; default = 1000 |
| Route + firewall | Both required for traffic to flow |
| Network tag | Scopes route to VMs with matching tag |
| Cloud Router | BGP-based; used with Interconnect and HA VPN; auto-updates |
| Subnet expansion | Irreversible; cannot shrink prefix |
| IPv6-only subnet | Does not exist in GCP |
| Dual-stack on auto mode | Not supported; must use custom mode |
| BYOIP minimum | /28 = 16 addresses |
| BYOIP idle IPs | Free (unlike Google-provided external IPs) |
| BGP specificity | Longer prefix (more specific) wins |
| Linux VM DNS | 169.254.169.254 — NOT an APIPA error |
| 8.8.8.8 | Recursive resolver, NOT authoritative for your domain |
| Failover routing policy | Private zones only |
| Routing policy limit | One type per record set |
| GEO nearest-match | Unmapped regions always get a response |
| Multiple NIC default route | nic0 only; other NICs need OS policy routing |
| NVA setup | --can-ip-forward + custom routes in both VPCs + OS policy routing |
| Private Google Access scope | Per subnet only; no effect on VMs with external IPs; Google APIs only |
| private vs restricted googleapis | private 199.36.153.8/30 (most APIs); restricted 199.36.153.4/30 (VPC-SC APIs only) |
| PGA for on-prem | VPN/Interconnect + DNS override + Cloud Router custom advertisement + VPC route → default-internet-gateway |
| Cloud DNS partial override | Not supported — use BIND |
| PSC | Endpoint → service attachment; no peering/route exchange; overlapping IPs OK; consumer-initiated only |
| PSC interface | Producer-initiated, transitive, uses network attachment |
| PSC endpoint caveats | Not reachable from peered VPCs; can't be in same VPC as the service |
| PSA | VPC Network Peering + allocated range; one range per producer; one connection per producer |
| PSA on-prem | Advertise allocated range + export custom routes on servicenetworking peering |
| PSA disable | Doesn't delete peering or release range |
| Cloud NAT | Outbound only; configured on Cloud Router; distributed (no proxy); manual IPs for allowlists |
| Cloud NAT + PGA | Google API traffic never NAT'd; PGA forced on for NAT'd ranges |
| Private NAT | Inter-VPC NAT between NCC spokes with overlapping ranges |
| IAP SSH | `gcloud compute ssh --tunnel-through-iap`; firewall allows TCP 22 only from 35.235.240.0/20; no bastion/external IP needed |
| NAT propagation | Up to ~3 min after gateway is Running |
| NAT logging | Translations and/or errors (port-exhaustion drops) → Cloud Logging, `resource.type="nat_gateway"` |

## Weak Areas to Revisit
*(none flagged yet)*

## Notes
- All GCP concepts connect to AWS equivalents — use the comparison tables in each lesson
- The geolocation lab (GCP05) is the most practical — can replicate with Cloud DNS and a test VPC
