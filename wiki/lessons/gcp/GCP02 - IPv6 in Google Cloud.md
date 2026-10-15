---
id: GCP02
aliases: [GCP02]
title: IPv6 in Google Cloud
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Routing & Addressing
date: 2025-05-28
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, ipv6, dual-stack, ula, gua, subnet]
---

# GCP02 — IPv6 in Google Cloud

> 🎛️ **Interactive visualization:** [VPC Route Selection](../../visualizations/gcp/GCP01-03%20-%20VPC%20Route%20Selection.html) (IPv6 subnets tab: stack types, /48 → /64 → /96). Opens in your browser.

## Summary
GCP only supports dual-stack (IPv4+IPv6) subnets — there is no IPv6-only subnet. IPv6 access type (internal ULA vs external GUA) is set at the subnet level and inherited by all VMs in that subnet.

## The Subnet Stack Concept
- **Single-stack (default):** IPv4 only
- **Dual-stack:** IPv4 + IPv6
- **IPv6-only: does not exist in GCP** (per lecture)

> **Docs update (2026-10-15):** current Google Cloud docs list **three** stack types: IPv4-only, dual-stack, and **IPv6-only**. IPv6-only VMs reach IPv4 destinations through **DNS64 + NAT64**. You can't convert a subnet between IPv6-only and the other types. Per-VM interface range = **/96** (first address via DHCPv6), subnet = **/64**, internal VPC ULA = **/48** from `fd20::/20`. For the exam, know the lecture answer but expect the docs to win on newer questions.

IPv6 is always additive to IPv4. Dual-stack subnets not supported on auto mode or legacy networks — must use custom mode.

## IPv6 Access Types

| Type | Address Range | Routable to Internet | Use Case |
|------|-------------|---------------------|---------|
| Internal IPv6 (ULA) | FC00::/7 | No — VPC-internal only | VM-to-VM within VPC |
| External IPv6 (GUA) | 2000::/3 | Yes | Internet-facing VMs |

VMs inherit the IPv6 access type from their subnet — all VMs in a subnet get the same type. Cannot mix internal and external IPv6 in the same subnet.

When a dual-stack subnet with external IPv6 is created, GCP automatically adds a system-generated IPv6 default route (`::/0`).

## GCP vs Network+ IPv6 Terminology

| Network+ Term | GCP Term | Range |
|--------------|---------|-------|
| Unique Local | Internal IPv6 (ULA) | FC00::/7 |
| Global Unicast | External IPv6 (GUA) | 2000::/3 |
| Link Local | Auto-assigned per interface | FE80::/10 |

## Key Constraints
- IPv6 only on custom mode VPC subnets
- Access type set at subnet level; all VMs in subnet inherit it
- No mixing of ULA and GUA in same subnet
- Auto mode networks: must convert to custom mode first

## Related
- [[L08 - Modern Networks]] — IPv6 addressing fundamentals (types, compression)
- [[GCP01 - Routes and Route Preferences]] — VPC routes (IPv6 default route added automatically)
- [[GCP06 - Module Debrief - Routing & Addressing]] — IPv6 quick reference
