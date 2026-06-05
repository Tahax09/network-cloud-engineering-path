---
id: GCP04
title: Cloud DNS
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Routing & Addressing
date: 2025-05-28
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, dns, cloud-dns, routing-policy, geolocation, failover, wrr, hybrid-dns, private-zone, response-policy]
---

# GCP04 — Cloud DNS

## Summary
Cloud DNS is an authoritative nameserver (not a recursive resolver). Know the difference between public/private zones, the three server policy types, and all three routing policy types — especially that failover is private zones only.

## DNS Fundamentals
1. Client queries configured **recursive resolver** (ISP DNS, or 8.8.8.8)
2. Recursive resolver queries **authoritative nameservers** for the domain
3. Authoritative server returns the IP
4. Recursive resolver returns IP to client

**Cloud DNS = authoritative nameserver.**
**Google Public DNS (8.8.8.8) = recursive resolver.**
These are completely different roles.

## GCP Internal DNS Resolution
- **Linux VMs:** Queries go to **169.254.169.254** (GCP metadata server, proxies to Cloud DNS private zones)
- **Windows VMs:** Resolution through subnet's default gateway

**Note:** 169.254.x.x is normally APIPA (DHCP failure). In GCP, it's a deliberately assigned metadata endpoint — NOT an error.

## SLO
Google commits to **100% monthly uptime SLO** for DNS queries from at least one authoritative nameserver.
- Exclusions: maintenance windows, force majeure
- Outage = **60+ consecutive seconds** of unavailability. Sub-minute blips don't count.

## Zone Types

| Zone | Visibility | Use Case |
|------|-----------|---------|
| Public | Internet | External-facing domains, external website records |
| Private | VPC only | Internal namespaces (dev.gcp.example.com), only resolvable from associated VPC |

## Cloud DNS Policies

### Server Policies — Hybrid DNS

**Outbound server policy:** GCP workloads forward queries to on-premises DNS.
- Used when GCP VMs need to resolve on-prem private hostnames
- Traffic: GCP → on-prem
- Configured as DNS forwarding zones

**Inbound server policy:** On-premises workloads forward queries to Cloud DNS.
- Used when on-prem systems need to resolve Cloud DNS private zone names
- Traffic: on-prem → GCP
- Configured as an inbound forwarding entry point (IP within VPC that on-prem forwards to)

One policy can implement both directions simultaneously. Requires VPC networks — not available on legacy networks.

### Response Policies — DNS Firewall
Intercept DNS queries and return modified results, overriding normal resolution.

**Mechanism:** Rules checked before normal resolution. Rule matches → modified response returned immediately. No match → normal resolution continues.

Use cases:
- Block domains (return NXDOMAIN or sinkhole IP)
- Redirect hostnames to different IPs
- Split-horizon DNS

**Response policies are NOT zones** — separate construct, managed via a separate API.

### Routing Policies — Traffic Steering via DNS

| Policy | Behavior | Private Zones Only? | Use Case |
|--------|----------|-------------------|---------|
| WRR | Distributes by weight per target | No | A/B testing, active-active balancing |
| Geolocation | Routes by source geography/GCP region | No | Serve from nearest region |
| Failover | Active-backup based on health checks | **Yes** | Primary-backup for internal services |

**WRR (Weighted Round Robin):** Assign weights (e.g., 90 and 10). DNS returns each target proportionally.

**Geolocation:**
- Internet traffic: geography = client physical location
- GCP-internal: geography = GCP region where query enters Google's network
- **Nearest-match fallback** for unmapped regions — no NXDOMAIN for unmatched sources

**Failover:** Active-backup; health check failure → automatic shift to backup. Private zones only.

**Constraint:** Only ONE routing policy type per record set. No nesting or combining.

## Carl's Hybrid Geolocation Pattern
Single hostname (www.cymbal.com) routes globally to nearest regional endpoint across hybrid environment.

Architecture:
1. Regional NLBs: Asia (GCP), US (GCP), Europe (on-prem)
2. Public Cloud DNS zone for cymbal.com
3. GEO routing policy on www.cymbal.com → maps geography to regional NLB

Result: Tokyo → Asia NLB → GKE Asia | New York → US NLB → GKE US | Frankfurt → European LB → GKE on VMware

## AWS Comparison

| GCP | AWS | Equivalent |
|-----|-----|-----------|
| Public zone | Route 53 public hosted zone | Authoritative for public domain |
| Private zone | Route 53 private hosted zone | VPC-only DNS |
| Geolocation routing policy | Route 53 geolocation routing | Geography-based routing |
| WRR routing policy | Route 53 weighted routing | Weight-based load distribution |
| Failover routing policy | Route 53 failover routing | Active-passive with health checks |
| Response policy (DNS firewall) | Route 53 Resolver DNS Firewall | Block/redirect DNS responses |
| Outbound server policy | Route 53 Resolver outbound endpoint | On-prem forward |
| Inbound server policy | Route 53 Resolver inbound endpoint | On-prem → cloud DNS |

## Key Terms

| Term | Definition |
|------|-----------|
| Recursive resolver | Queries authoritative servers on behalf of clients (8.8.8.8, ISP DNS) |
| Authoritative NS | Holds definitive records for a domain — what Cloud DNS is |
| 169.254.169.254 | GCP metadata server; Linux VM internal DNS resolver |
| Outbound server policy | Cloud DNS → on-premises |
| Inbound server policy | On-premises → Cloud DNS |
| Response policy | DNS firewall — intercepts and modifies responses |
| WRR | Weighted Round Robin — proportional DNS distribution |
| GEO routing | Routes by source region; nearest-match fallback for unmapped |
| Failover policy | Private zones only; active-backup |

## Related
- [[GCP05]] — geolocation lab with CLI commands
- [[GCP06]] — Cloud DNS quick reference
- [[concepts/gcp-dns]] — full concept reference
- [[L04]] — DNS port 53 (TCP and UDP)
