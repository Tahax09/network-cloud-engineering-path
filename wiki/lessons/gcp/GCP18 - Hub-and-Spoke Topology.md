---
id: GCP18
aliases: [GCP18]
title: Hub-and-Spoke Topology
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: "Course 3: Network Architecture — Module 2: Network Topologies"
date: 2026-10-15
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, hub-and-spoke, star-topology, network-connectivity-center, ncc, vpc-spoke, hybrid-spoke, router-appliance, vpc-peering, cloud-vpn, transitivity, ip-overlap]
---

# GCP18 — Hub-and-Spoke Topology

## Summary
A **hub-and-spoke** (star) topology puts **one central hub** in the middle and connects everything else as **spokes**: other VPCs, remote offices, on-prem data centers. In Google Cloud, a **hub VPC** (or an NCC hub) acts as the **transit point** between spoke networks. The benefits are **central control, simpler administration, easy scaling (add a spoke), and consistent security enforced at the hub**. There are three ways to build it: **VPC Network Peering** (low latency, simple), **Cloud VPN** (encrypted, flexible, more latency and config), and **Network Connectivity Center** (centralized and automated, extra cost). This course uses NCC. NCC spoke types: **VPC spokes** and **hybrid spokes** (HA VPN, Interconnect VLAN attachments, **router appliance** VMs). Rules: **no overlapping IP ranges**, **no IPv6 on hybrid spokes**, privately used public IPs are supported.

---

## Core Concepts

### Scenario (Cymbal: Nur)
Nur manages a growing set of **remote offices and cloud apps** and wants **centralized control** with **simple administration**, so he chooses hub-and-spoke instead of managing every connection individually.

### Shape and features
```
          spoke: VPC (prod)        spoke: VPC (dev)
                     \                /
spoke: on-prem DC ──── [ HUB ] ──── spoke: remote office
                     /                \
          spoke: VPC (shared svc)  spoke: VPC (analytics)
```
| Feature | Meaning |
|---------|---------|
| **Centralized control** | Configure, monitor, troubleshoot from the hub; no logging in to each device |
| **Simplified administration** | No pairwise routing between spokes; the hub routes spoke-to-spoke by defined rules |
| **Scalability** | Add spokes without touching existing ones |
| **Improved security** | Central policies at the hub give consistent protection |

**Trade-offs (beyond the lecture):** the hub is a **single point of failure/bottleneck** if it's built from appliances (NCC itself is a managed global service), spoke-to-spoke traffic may take a **longer path**, and hub capacity and quotas cap growth.

### Three ways to implement it
| Method | How spoke-to-spoke works | Pros | Cons |
|--------|-------------------------|------|------|
| **VPC Network Peering** (hub VPC peered with each spoke) | ⚠️ **Not transitive**: spoke A ↔ hub ↔ spoke B does **not** work by itself. You need a router/firewall **NVA in the hub** plus custom routes, or spoke-to-spoke peerings | Direct, **low latency**, no extra cost | Non-transitive; peering quotas |
| **Cloud VPN** (HA VPN hub ↔ spokes, Cloud Routers) | **Transitive** via BGP: the hub's Cloud Router re-advertises spoke routes (custom advertisement) | Encrypted; works **across regions, projects, and to on-prem** | **Higher latency**, per-tunnel throughput limits, more configuration, tunnel cost |
| **Network Connectivity Center** | Hub exchanges **subnet routes** among VPC spokes; hybrid spokes connect on-prem | **Centralized config, automated provisioning**, integrated security, scales to many spokes, cross-project/org spokes | **Additional cost** vs basic peering/VPN |

> **Lecture clarification:** "The hub automatically routes traffic between spokes" is true for **NCC** and for **VPN with BGP re-advertisement**, but **not** for a plain VPC-peering hub, because peering is **non-transitive** ([[GCP10 - Private Services Access]]). This is a very common exam and design trap.

### NCC spoke types
| Spoke type | Connects | Notes |
|------------|----------|-------|
| **VPC spoke** | A VPC network (same or **different project, even a different organization**) | Exchanges **subnet routes** with other VPC spokes on the hub |
| **Hybrid spoke: HA VPN tunnels** | On-prem / other cloud over IPsec | Dynamic routes via Cloud Router BGP |
| **Hybrid spoke: Interconnect VLAN attachments** | On-prem over Dedicated/Partner Interconnect | High bandwidth |
| **Hybrid spoke: Router appliance** | A **Compute Engine VM** running a router image: an **NCC partner** image or a **custom** image | Peers BGP with Cloud Router; your own logic (SD-WAN, filtering) controls connectivity between spoke and hub |

### Considerations before building
- **IP ranges must not overlap** between the hub and spoke VPCs (subnet routes are exchanged).
- **IPv6 isn't supported for hybrid spokes.**
- **Privately used public IP (PUPI) ranges are supported** by NCC.
- NCC hub **topology presets** (beyond the lecture): **Mesh** (all VPC spokes reach each other; what the lab uses) and **Star** (spokes in an *edge* group reach only the *center* group, e.g. shared services, not each other).

---

## Key Terms
| Term | Definition |
|------|-----------|
| Hub-and-spoke (star) | Central hub with all networks connected through it |
| Transit point | Network that carries traffic between other networks |
| Transitivity | Whether A can reach C through B. Peering: no; VPN+BGP / NCC: yes |
| Network Connectivity Center (NCC) | Managed global hub for VPC and hybrid spokes |
| VPC spoke | NCC spoke representing a VPC network; exchanges subnet routes |
| Hybrid spoke | NCC spoke for HA VPN tunnels, Interconnect VLAN attachments, or router appliances |
| Router appliance | Compute Engine VM with a (partner or custom) router image peering BGP with Cloud Router |
| Privately used public IP (PUPI) | Public IP ranges used privately inside a VPC |
| Mesh / Star preset | NCC connectivity policies: all-to-all vs center/edge groups |

---

## Real-World Connections
- **Network+:** a star topology and hub-and-spoke WAN ([[L06 - Network Topologies, Architectures & Traffic Flows]]); route advertisement and BGP ([[L09 - Routing Technologies]]); IPsec site-to-site ([[L17 - Network Access and Management]]); an SD-WAN router appliance as a spoke ([[L08 - Modern Networks]]).
- **AWS:** NCC ≈ **Transit Gateway** (VPC attachments ≈ VPC spokes, VPN/Direct Connect attachments ≈ hybrid spokes, TGW route tables ≈ Star/Mesh policies). Plain VPC peering is non-transitive on AWS too.
- **k3s:** a "hub" shared-services VPC (logging/Elasticsearch, CI runners) with app clusters as spokes is a classic design. Keep each cluster's VPC **and pod/service CIDRs** non-overlapping, because NCC exchanges subnet routes (including secondary ranges used by GKE).

---

## Troubleshooting Scenario
**Scenario:** A team builds hub-and-spoke by peering `spoke1-vpc` and `spoke2-vpc` to `hub-vpc`. VMs in each spoke reach the hub, but **spoke1 can't reach spoke2**.
**Analysis:** VPC peering is **non-transitive**. Spoke1 learns hub routes, but not spoke2's routes through the hub.
**Fix options:**
1. **NCC:** add both VPCs as **VPC spokes** on one hub (mesh preset). Subnet routes are exchanged automatically ([[GCP19 - Lab - Implement a Hub-and-Spoke Network Using Network Connectivity Center]]).
2. HA VPN between hub and spokes with Cloud Router **custom advertisements** of the spoke ranges.
3. An NVA in the hub with custom routes (when you need inspection anyway).
**Verify:** Connectivity Tests (Network Intelligence Center) for spoke1-vm → spoke2-vm.

---

## Hands-On Ideas
1. Build the failing scenario above with peering, confirm spoke1 ↛ spoke2, then fix it with NCC.
2. Explore NCC with gcloud: `gcloud network-connectivity hubs create my-hub`, `gcloud network-connectivity spokes linked-vpc-network create spoke1 --hub=my-hub --vpc-network=spoke1-vpc --global`, then `gcloud network-connectivity hubs list-spokes my-hub`.
3. Compare route tables before and after: `gcloud compute routes list --filter="network:spoke1-vpc"`.
4. Read the NCC "Star" preset docs and design a shared-services center with edge spokes that can't reach each other.

---

## Quiz
1. In a GCP hub-and-spoke topology, what role does the hub VPC/NCC hub play?
   - A) Transit point between spoke networks  B) DNS server  C) Load balancer  D) NAT gateway
2. Which is a benefit of hub-and-spoke from the lesson?
   - A) Every device must be configured individually  B) Requires IPv6  C) Eliminates firewalls  D) Centralized control and simpler administration
3. Which implementation offers low latency but is **not transitive** for spoke-to-spoke traffic without extra components?
   - A) NCC  B) Cloud VPN with BGP  C) VPC Network Peering  D) Router appliance
4. Which implementation may add latency and configuration but provides encrypted tunnels across regions and to on-prem?
   - A) VPC Network Peering  B) Cloud VPN  C) Cloud CDN  D) Shared VPC
5. What are NCC's two spoke categories?
   - A) Public and private  B) Zonal and regional  C) VPC spokes and hybrid spokes  D) IPv4 and IPv6
6. Which hybrid spoke runs a router image on a Compute Engine VM?
   - A) VLAN attachment  B) HA VPN tunnel  C) Router appliance  D) VPC spoke
7. Which is a requirement before creating an NCC hub-and-spoke?
   - A) Overlapping IP ranges are required  B) Hub and spoke IP ranges must not overlap  C) IPv6 on hybrid spokes  D) All spokes in one project
8. Are privately used public IP addresses supported by NCC?
   - A) Yes  B) No  C) Only on hybrid spokes  D) Only with IPv6

**Answers:** 1-A, 2-D, 3-C, 4-B, 5-C, 6-C, 7-B, 8-A

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[GCP17 - Module Introduction - Network Topologies]] — Module 2 map
- [[GCP20 - Other Topologies - Mesh, Mirrored, Gated]] — Mesh, mirrored, gated alternatives
- [[GCP19 - Lab - Implement a Hub-and-Spoke Network Using Network Connectivity Center]] — Hands-on lab
- [[GCP10 - Private Services Access]] — Peering non-transitivity
- [[GCP11 - Cloud NAT]] — NCC also hosts inter-VPC (private) NAT
- [[L06 - Network Topologies, Architectures & Traffic Flows]] · [[L09 - Routing Technologies]]
- [[certs/gcp-networking]]
