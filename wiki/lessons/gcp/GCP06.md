---
id: GCP06
title: "Module Debrief: Routing & Addressing"
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Routing & Addressing
date: 2025-05-28
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, routes, ipv6, byoip, dns, multi-nic, nva, reference]
---

# GCP06 — Module Debrief: Routing & Addressing

## Purpose
Master quick reference for the entire GCP Routing & Addressing module. Use for review and exam prep. All detail is in GCP01–GCP05.

## Routes Quick Reference

| Route Type | Created By | Auto-Updates | Cannot Be Overridden By |
|-----------|-----------|-------------|------------------------|
| System default | VPC creation | No | Less-specific custom routes |
| Subnet route | Subnet creation | Yes | **Anything** — always wins for its range |
| Custom static | Admin | No | — |
| Custom dynamic | Cloud Router (BGP) | Yes | — |
| Policy-based | Admin | No | — |

**Priority:** Specificity beats priority number. Lower priority number wins ties. Default = 1000.

## Multiple NICs on Compute Engine VMs

### Why Multiple NICs
- Network Virtual Appliance (NVA): VM running firewall/routing software between two VPCs
- Direct access to a shared services VPC (logging, AD) while serving traffic in a production VPC

### Rules

| Constraint | Detail |
|------------|--------|
| Different VPCs only | Cannot have two NICs in the same VPC |
| Created at launch only | No add/remove after instance creation |
| Maximum NICs | ~1 per vCPU; min 2, max 8 |
| Default route | **Only nic0** gets the system default route |
| IP forwarding | Must enable `--can-ip-forward` on NVA VMs |
| NIC naming | nic0 = primary (eth0), nic1, nic2... |

**Critical:** Only nic0 gets the default route. Traffic on nic1 going to non-directly-connected networks won't forward via nic0 automatically. Requires OS-level policy routing (`ip rule` + `ip route`).

### NVA Pattern

```
VPC-A (10.0.0.0/16)          NVA VM          VPC-B (10.1.0.0/16)
  nic0: 10.0.0.5      ←─ IP forwarding ─→    nic1: 10.1.0.5
  (has default GW)       (--can-ip-forward)   (needs OS policy route)
```

Configuration required:
- VPC-A: static route to 10.1.0.0/16 via NVA's nic0 (10.0.0.5)
- VPC-B: static route to 10.0.0.0/16 via NVA's nic1 (10.1.0.5)
- NVA: IP forwarding enabled + OS policy routing for nic1→nic0 traffic

## IPv6 Quick Reference

| Feature | Detail |
|---------|--------|
| Stack options | Single (IPv4) or Dual (IPv4+IPv6) |
| IPv6-only subnet | Does not exist |
| Dual-stack on auto mode | Not supported — convert to custom first |
| Internal IPv6 (ULA) | FC00::/7; VPC-internal only |
| External IPv6 (GUA) | 2000::/3; internet-routable |
| IPv6 access type | Set at subnet level; all VMs inherit it |

## BYOIP Quick Reference

| Feature | Detail |
|---------|--------|
| Minimum prefix | /28 (16 addresses) |
| Idle IP cost | Free |
| BGP behavior | More specific prefix (longer /n) always wins |
| Overlap protection | Cannot overlap VPC subnet or alias ranges |

## Cloud DNS Quick Reference

| Topic | Key Fact |
|-------|---------|
| Linux VM DNS | 169.254.169.254 (metadata server) |
| Windows VM DNS | Subnet default gateway |
| Public zone | Authoritative; internet-facing domains |
| Private zone | VPC-only; invisible to public internet |
| Google Public DNS 8.8.8.8 | Recursive resolver — NOT authoritative for your domain |
| SLO | 100% monthly; 60+ consecutive seconds = outage |
| Inbound server policy | On-prem → Cloud DNS |
| Outbound server policy | Cloud DNS → on-prem |
| Failover routing policy | Private zones only |
| Routing policy limit | One type per record set; no nesting |

## Related
- [[GCP01]] — Routes detail
- [[GCP02]] — IPv6 detail
- [[GCP03]] — BYOIP detail
- [[GCP04]] — Cloud DNS detail
- [[gcp-geolocation-lab]] — Geolocation lab
