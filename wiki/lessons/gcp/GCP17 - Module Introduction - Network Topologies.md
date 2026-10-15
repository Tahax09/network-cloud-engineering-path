---
id: GCP17
aliases: [GCP17]
title: Module Introduction - Network Topologies
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: "Course 3: Network Architecture — Module 2: Network Topologies"
date: 2026-10-15
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, network-topologies, hub-and-spoke, mesh, mirrored, gated, network-connectivity-center]
---

# GCP17 — Module Introduction: Network Topologies

## Summary
**Module 2** of *Network Architecture* introduces **network topologies**, the shape in which VPCs, on-prem sites and services interconnect. It starts with **hub-and-spoke** (structure and applications), then covers alternatives: **mesh**, **mirrored** and **gated**. A hands-on lab builds **hub-and-spoke with Network Connectivity Center (NCC)**.

> **Caption note:** "Network *Typologies*" = **topologies**.

---

## Core Concepts

### Module map
| Lesson | Topology | Page |
|--------|----------|------|
| 1 | **Hub-and-spoke**: one central hub, all others connect through it | [[GCP18 - Hub-and-Spoke Topology]] |
| 2 | **Mesh** (full / partial): every network connects to every other, or strategic links only | [[GCP20 - Other Topologies - Mesh, Mirrored, Gated]] |
| | **Mirrored**: replica of the network in another environment/region (DR, dev/test, performance) | [[GCP20 - Other Topologies - Mesh, Mirrored, Gated]] |
| | **Gated**: controlled egress, ingress, or both (hybrid/multicloud) | [[GCP20 - Other Topologies - Mesh, Mirrored, Gated]] |
| Lab | Hub-and-spoke with NCC | [[GCP19 - Lab - Implement a Hub-and-Spoke Network Using Network Connectivity Center]] |



### Topologies you already know (Network+)
| Network+ ([[L06 - Network Topologies, Architectures & Traffic Flows]]) | Google Cloud equivalent |
|-------------|-------------------------|
| Star / hub-and-spoke WAN | Hub VPC or NCC hub with VPC/hybrid spokes |
| Full mesh / partial mesh | VPC peering between every VPC, or NCC mesh preset |
| Screened subnet / DMZ | Gated topology with NVAs/firewalls at the entry point ([[L20 - Network Defense Techniques]]) |
| DR site (hot/warm) | Mirrored environment in another region ([[L15 - Disaster Recovery]]) |

---

## Key Terms
| Term | Definition |
|------|-----------|
| Network topology | The interconnection shape of networks (hub-and-spoke, mesh, mirrored, gated) |
| Hub-and-spoke | Star design with a central transit/management point |
| Mesh | Every node connects directly to every other |
| Network Connectivity Center (NCC) | Google Cloud's managed hub for connecting VPC and hybrid spokes |

---

## Real-World Connections
- **AWS:** hub-and-spoke = **Transit Gateway**; mesh = full VPC peering; gated = **inspection VPC** with Gateway Load Balancer + firewall appliances.
- **k3s:** a cluster with an **ingress controller** as the only entry point is a small "gated" design; multiple clusters connected through one shared-services cluster is hub-and-spoke.

---

## Troubleshooting Scenario
**Scenario:** Before the module, a team asks: "We have 12 VPCs. Should we peer them all?"
**Answer preview:** a full mesh means 12×11/2 = **66 peerings**, and peering is non-transitive. Module 2's answer is **hub-and-spoke via NCC** (one hub, 12 spokes), with mesh only for small, stable sets.

---

## Hands-On Ideas
1. Sketch the four topologies for a 4-VPC company (prod, dev, shared-services, security) and count the connections in each.
2. Map your current lab setup (Mac, k3s nodes, home router) to one of these topologies.

---

## Quiz
1. Which topology does Module 2 start with?
   - A) Mesh  B) Hub-and-spoke  C) Gated  D) Mirrored
2. Which alternative topologies does the module cover?
   - A) Ring, bus, star  B) Mesh, mirrored, gated  C) Tree, line, point-to-point  D) Spine-leaf only
3. Which service does the lab use to implement hub-and-spoke?
   - A) Cloud NAT  B) Network Connectivity Center  C) Cloud CDN  D) Cloud DNS

**Answers:** 1-B, 2-B, 3-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[GCP16 - Getting Started with Network Architecture]] — End of Module 1
- [[GCP18 - Hub-and-Spoke Topology]] · [[GCP19 - Lab - Implement a Hub-and-Spoke Network Using Network Connectivity Center]]
- [[L06 - Network Topologies, Architectures & Traffic Flows]]
- [[certs/gcp-networking]]
