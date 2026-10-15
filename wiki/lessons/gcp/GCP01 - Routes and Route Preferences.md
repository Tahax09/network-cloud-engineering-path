---
id: GCP01
aliases: [GCP01]
title: Routes and Route Preferences
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Routing & Addressing
date: 2025-05-28
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, routes, cloud-router, bgp, static-routing, dynamic-routing, network-tags, vpc]
---

# GCP01 — Routes and Route Preferences

> 🎛️ **Interactive visualization:** [VPC Route Selection](../../visualizations/gcp/GCP01-03%20-%20VPC%20Route%20Selection.html) (Route selection tab: step a packet through the routing order). Opens in your browser.

## Summary
GCP routes work like physical routing tables but with important cloud-specific rules: subnet routes always win, network tags scope routes to specific VMs, and a route alone is not enough — firewall rules must also permit the traffic.

## IP Address Space in GCP VPCs
- Subnet expansion is **irreversible** — prefix length decreases (more addresses); cannot shrink
- No overlapping CIDRs within the same VPC (uniqueness is global across all regions)
- Auto mode subnets: start at /20, max /16. Beyond /16 → convert to custom mode
- Cannot mix RFC ranges or cross RFC boundary lines

## The Two Requirements for Traffic to Flow
1. A matching **route** must exist
2. A matching **firewall rule** must permit the traffic

Both are required. A route without a permitting firewall rule = traffic dropped.

## Network Tags
Routes can be scoped to specific VMs via network tags:
- Tag on route → only VMs with that same tag use the route
- Untagged routes → apply to all VMs

## Route Types

| Type | Created By | Auto-Updates | Use Case |
|------|-----------|-------------|---------|
| System default (0.0.0.0/0) | VPC creation | No | Internet egress, Google APIs |
| Subnet route | Subnet creation | Yes | Local VPC forwarding |
| Custom static | You (manual/IaC) | No | Specific next hops, NVA, VPN |
| Custom dynamic | Cloud Router (BGP) | Yes | Interconnect, HA VPN, topology changes |
| Policy-based | You | No | Match src IP + dst IP + protocol |

**Subnet routes always win** — most specific destination, cannot be overridden by any higher-priority custom route.

## Route Priority
**Destination specificity wins first.** A /28 always beats a /24 regardless of priority numbers.

**Priority number breaks ties when specificity is equal.** Lower number = higher priority. Default system route = priority 1000.

To override the default internet route: create a 0.0.0.0/0 custom route with priority < 1000 pointing to desired next hop (e.g., VPN tunnel for forcing all traffic through on-prem security stack).

## Static vs Dynamic Routing

| Dimension | Static | Dynamic (Cloud Router + BGP) |
|-----------|--------|------------------------------|
| Updates | Manual | Automatic |
| Failover | No | Yes (if another path exists) |
| Route advertisement | None | BGP advertises VPC ranges |
| Maintenance | High | Low |
| Best for | Small, stable topologies | Enterprise, frequent changes |

## Cloud Router
Managed BGP router in GCP. Peers with on-premises VPN gateways or routers.
- Automatically advertises VPC subnet ranges to on-prem via BGP
- Receives on-prem routes and installs as dynamic custom routes
- Reroutes traffic when a link fails (if another path exists)

Used with: Dedicated Interconnect, Partner Interconnect, Cross-Cloud Interconnect, HA VPN, Classic VPN with dynamic routing, NCC Router appliances.

**Dynamic routing mode:**
- Regional (default): routes apply only to router's region
- Global: routes propagate to all VPC regions

## Custom Static Route Limitations
- Cannot point to a VLAN attachment
- Manual maintenance required; no auto-failover
- Best managed via Terraform for large deployments

## Default Route Behavior
- Can be deleted — unmatched packets are dropped
- Can be replaced with a custom route pointing to VPN tunnel (forces all internet traffic through on-prem)
- GCP only uses default route if no more-specific route matches

## AWS Comparison

| GCP | AWS | Equivalent |
|-----|-----|-----------|
| Subnet route | VPC local route | Auto-created for local forwarding |
| System default | Default 0.0.0.0/0 → IGW | Internet egress |
| Custom static | Static route entry | Manual next hop |
| Cloud Router + BGP | Virtual Private Gateway + BGP | Dynamic routing |
| Network tag | — | No direct equivalent; use SG/NACL |

## Related
- [[GCP02 - IPv6 in Google Cloud]] — IPv6 in GCP VPCs
- [[GCP06 - Module Debrief - Routing & Addressing]] — full routing module reference
- [[concepts/gcp-routing]] — route types reference
- [[L03 - Network Virtualization & Cloud Technologies]] — VPC and cloud networking fundamentals
