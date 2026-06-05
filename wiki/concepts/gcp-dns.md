# GCP Cloud DNS

## Roles
- **Cloud DNS** = authoritative nameserver (holds your zone records)
- **Google Public DNS (8.8.8.8)** = recursive resolver (queries on behalf of clients)
- These are completely different. Do not confuse them.

## Linux VM DNS Resolution
Queries → **169.254.169.254** (GCP metadata server, proxies to Cloud DNS private zones).
NOT an APIPA error — deliberate metadata endpoint.

## Zone Types
| Zone | Visibility | Use Case |
|------|-----------|---------|
| Public | Internet | External domains, website records |
| Private | VPC only | Internal namespaces (dev.example.com) |

## Server Policies (Hybrid DNS)
| Policy | Traffic Direction | Use Case |
|--------|-----------------|---------|
| Outbound | Cloud DNS → on-prem | GCP VMs resolve on-prem private names |
| Inbound | On-prem → Cloud DNS | On-prem systems resolve Cloud DNS private zones |

## Response Policies (DNS Firewall)
Intercept queries before resolution. Rule matches → modified response (NXDOMAIN, sinkhole IP, redirect). Not a zone — separate API.

Use cases: block domains, split-horizon DNS.

## Routing Policies (Traffic Steering)
| Policy | Private Zone Only? | Use Case |
|--------|------------------|---------|
| WRR | No | A/B testing, weighted active-active |
| Geolocation | No | Route by source region; nearest-match fallback |
| Failover | **Yes** | Active-backup with health checks |

**One routing policy type per record set. No nesting.**

**Geolocation nearest-match:** Unmapped regions → geographically closest configured target. Never returns NXDOMAIN for unmatched sources.

## SLO
100% monthly uptime. Outage = 60+ consecutive seconds.

## AWS Comparison
| GCP | AWS |
|-----|-----|
| Public zone | Route 53 public hosted zone |
| Private zone | Route 53 private hosted zone |
| GEO routing policy | Route 53 geolocation routing |
| WRR routing policy | Route 53 weighted routing |
| Failover routing policy | Route 53 failover routing |
| Response policy | Route 53 Resolver DNS Firewall |
| Outbound server policy | Route 53 Resolver outbound endpoint |
| Inbound server policy | Route 53 Resolver inbound endpoint |

## Linked Lessons
- [[GCP04]] — full lesson
- [[GCP05]] — geolocation lab
- [[L04]] — DNS port 53
