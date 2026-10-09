---
cert: Google Cloud Networking Specialization
platform: Coursera / Google Cloud
status: In Progress
target_date: TBD
---

# Google Cloud Networking Specialization

## Modules Completed

### Routing & Addressing
| ID | Title | Status |
|----|-------|--------|
| [GCP01](../lessons/gcp/GCP01.md) | Routes and Route Preferences | ✅ Complete |
| [GCP02](../lessons/gcp/GCP02.md) | IPv6 in Google Cloud | ✅ Complete |
| [GCP03](../lessons/gcp/GCP03.md) | BYOIP — Bring Your Own IP | ✅ Complete |
| [GCP04](../lessons/gcp/GCP04.md) | Cloud DNS | ✅ Complete |
| [gcp-geolocation-lab](../labs/gcp-geolocation-lab.md) | Lab: Traffic Steering (Geolocation) | ✅ Complete |
| [GCP06](../lessons/gcp/GCP06.md) | Module Debrief: Routing & Addressing | ✅ Complete |

### Private Connection Options
| ID | Title | Status |
|----|-------|--------|
| [GCP07](../lessons/gcp/GCP07.md) | Private Connection Options — Overview | ✅ Complete |
| [GCP08](../lessons/gcp/GCP08.md) | Private Google Access | ✅ Complete |
| [GCP09](../lessons/gcp/GCP09.md) | Private Service Connect | ✅ Complete |
| [GCP10](../lessons/gcp/GCP10.md) | Private Services Access | ✅ Complete |
| [GCP11](../lessons/gcp/GCP11.md) | Cloud NAT | ✅ Complete |
| [GCP12](../lessons/gcp/GCP12.md) | Module Debrief: Private Connection Options | ✅ Complete |
| [GQ01](../quizzes/GQ01_Private_Connection_Options.md) | Quiz 2 (graded) | ✅ Reviewed |

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

## Weak Areas to Revisit
*(none flagged yet)*

## Notes
- All GCP concepts connect to AWS equivalents — use the comparison tables in each lesson
- The geolocation lab (GCP05) is the most practical — can replicate with Cloud DNS and a test VPC
