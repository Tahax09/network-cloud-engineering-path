# GCP Routing & Addressing

## Route Types
| Type | Created By | Auto-Updates | Wins Over |
|------|-----------|-------------|-----------|
| System default (0.0.0.0/0) | VPC creation | No | Nothing more specific |
| Subnet route | Subnet creation | Yes | **Everything** (always wins for its CIDR) |
| Custom static | Admin | No | — |
| Custom dynamic | Cloud Router (BGP) | Yes | — |
| Policy-based | Admin | No | — |

**Priority rule:** Specificity (longer prefix) beats priority number. Lower number = higher priority. Default = 1000.

**Two requirements for traffic:** Route + firewall rule both required.

**Network tags:** Route with tag → only VMs with matching tag use it.

## Cloud Router
Managed BGP router. Used with: Dedicated Interconnect, Partner Interconnect, HA VPN, Classic VPN (dynamic), NCC.
- Advertises VPC subnet ranges to on-prem via BGP
- Installs on-prem routes as dynamic custom routes
- Auto-reroutes on link failure if another path exists

**Dynamic routing mode:** Regional (default) or Global.

## Multiple NICs
| Constraint | Detail |
|------------|--------|
| VPC restriction | Different VPCs only |
| Timing | At instance creation only |
| Max NICs | ~1 per vCPU, max 8 |
| Default route | nic0 only — others need OS policy routing |
| NVA requirement | `--can-ip-forward` + custom routes in both VPCs + OS policy routing |

## IPv6 in GCP
| Feature | Detail |
|---------|--------|
| Stack options | Single (IPv4) or Dual (IPv4+IPv6) |
| IPv6-only | Does not exist |
| Auto mode | Not supported for dual-stack |
| ULA | FC00::/7 — VPC internal |
| GUA | 2000::/3 — internet routable |

## BYOIP
| Feature | Detail |
|---------|--------|
| Minimum prefix | /28 |
| Idle IP cost | Free |
| BGP | Longer prefix (more specific) always wins |
| Cannot overlap | VPC subnet or alias ranges |

## Linked Lessons
- [[GCP01 - Routes and Route Preferences]] — routes detail
- [[GCP02 - IPv6 in Google Cloud]] — IPv6 detail
- [[GCP03 - BYOIP - Bring Your Own IP]] — BYOIP detail
- [[GCP06 - Module Debrief - Routing & Addressing]] — full module reference
