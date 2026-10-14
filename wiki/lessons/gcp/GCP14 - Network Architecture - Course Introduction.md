---
id: GCP14
aliases: [GCP14]
title: Network Architecture - Course Introduction
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: "Course 3: Network Architecture — Introduction"
date: 2026-10-14
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, network-architecture, course-intro, subnets, routes, firewalls, load-balancing, topologies]
---

# GCP14 — Network Architecture: Course Introduction

## Summary
This opens **Course 3 of 6** in *Networking in Google Cloud*: **Network Architecture**. The focus moves from individual features (routes, DNS, private access in Course 2) to **designing efficient, scalable networks**, i.e. how the pieces fit together. There are two modules:

| Module | Covers |
|--------|--------|
| **1. Introduction to Network Architecture** | Core building blocks: **subnets, routes, firewalls, load balancing** |
| **2. Network Topologies** | Common Google Cloud topologies and their **strengths and weaknesses** |

---

## Core Concepts

### Where this course sits
```
Course 1  Fundamentals            ✅ (not yet in wiki)
Course 2  Routing and Addressing  ✅ GCP01–GCP13 (+ GQ01)
Course 3  Network Architecture    ◀ you are here (GCP14 →)
Course 4  Network Security
Course 5  Load Balancing
Course 6  Hybrid and Multicloud
```

### Building blocks to review before Module 1
| Component | Already covered in | Architecture question this course adds |
|-----------|-------------------|----------------------------------------|
| **Subnets** | Regional subnets, IPv6 dual-stack ([[GCP02 - IPv6 in Google Cloud]]) | How to plan CIDR ranges across regions, projects and environments without overlap |
| **Routes** | System/custom/dynamic routes, priority ([[GCP01 - Routes and Route Preferences]]) | How traffic flows between VPCs, on-prem and the internet in a given topology |
| **Firewalls** | VPC firewall basics; IAP range ([[GCP12 - Lab - Implement Private Google Access and Cloud NAT]]) | Where to enforce policy (per VPC, hierarchical policies, NVAs) |
| **Load balancing** | Network+ concepts ([[L02 - Networking Appliances & Applications]]) | Which load balancer fits which traffic pattern; deep dive in Course 5 |
| Private access | [[GCP07 - Private Connection Options - Overview]] | How private access options shape the topology |

### Topology preview (likely Module 2 content)
Common Google Cloud designs, to be confirmed against the lessons as they're ingested:
| Topology | Idea | Network+ analogue |
|----------|------|-------------------|
| Single VPC / **Shared VPC** | One host project owns the network; service projects attach | Centralized core |
| **Hub-and-spoke** (VPC peering or **Network Connectivity Center**) | Central hub VPC with shared services; spokes per team/app | Hub-and-spoke WAN ([[L06 - Network Topologies, Architectures & Traffic Flows]]) |
| **Mesh** (full peering) | Every VPC peers with every other | Full mesh: doesn't scale (n(n−1)/2 links) |
| **Hybrid** (VPN / Interconnect to on-prem) | Cloud as an extension of the DC | Site-to-site VPN ([[L17 - Network Access and Management]]) |

---

## Key Terms
| Term | Definition |
|------|-----------|
| Network architecture | Overall design of subnets, routing, security boundaries, and connectivity |
| Topology | The shape of interconnection between networks (hub-and-spoke, mesh, …) |
| Scalability | Ability to add regions, projects, and workloads without redesigning |
| Shared VPC | One host project's VPC used by multiple service projects |
| Network Connectivity Center (NCC) | Google's hub-and-spoke connectivity service (seen in [[GCP11 - Cloud NAT]] for inter-VPC NAT) |

---

## Real-World Connections
- **Network+:** The three-tier and spine-leaf designs from [[L06 - Network Topologies, Architectures & Traffic Flows]] become VPC-level designs here: hub VPC ≈ core, spoke VPCs ≈ access/distribution.
- **AWS:** Shared VPC ≈ **AWS RAM–shared subnets**; hub-and-spoke ≈ **Transit Gateway**; NCC ≈ TGW + Cloud WAN.
- **k3s lab:** Even a home lab has an architecture: one flat LAN, pod CIDR `10.42.0.0/16`, service CIDR `10.43.0.0/16`. Moving it to GCP raises the course's questions: which VPC, which subnets, how to avoid CIDR overlap with on-prem.

---

## Troubleshooting Scenario
**Scenario:** A company grew from 3 to 25 VPCs by peering each new VPC with every existing one. Now new peerings fail and routes are inconsistent.
**Analysis:** A full mesh grows as n(n−1)/2: 25 VPCs need 300 peerings. That hits per-VPC peering limits, and VPC peering is **non-transitive** ([[GCP10 - Private Services Access]]), so the routes become hard to reason about.
**Direction (this course):** redesign as **hub-and-spoke** (NCC or a hub VPC) or **Shared VPC**, which is the kind of topology trade-off Module 2 covers.

---

## Hands-On Ideas
1. Draw your current understanding of a 3-environment design (dev/test/prod) on GCP: one VPC per env, or one Shared VPC with subnets per env? Revisit after Module 2.
2. Plan a non-overlapping CIDR scheme: e.g., `10.10.0.0/16` prod, `10.20.0.0/16` test, `10.30.0.0/16` dev, keeping clear of k3s's `10.42/16` and `10.43/16`.

---

## Quiz
1. Which two modules make up Course 3?
   - A) Routing and DNS  B) Introduction to Network Architecture, and Network Topologies  C) Security and Load Balancing  D) Hybrid and Multicloud
2. Which core components does Module 1 introduce?
   - A) Subnets, routes, firewalls, load balancing  B) IAM, billing, quotas  C) GKE, Cloud Run, App Engine  D) BigQuery, Pub/Sub, Dataflow
3. Why does a full mesh of VPC peerings scale poorly?
   - A) It's encrypted  B) Links grow as n(n−1)/2, peering limits are hit, and peering is non-transitive  C) It needs IPv6  D) It requires Interconnect

**Answers:** 1-B, 2-A, 3-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[GCP13 - Module Debrief - Private Connection Options]] — End of Course 2
- [[GCP01 - Routes and Route Preferences]] · [[GCP02 - IPv6 in Google Cloud]] · [[GCP07 - Private Connection Options - Overview]]
- [[L06 - Network Topologies, Architectures & Traffic Flows]]
- [[certs/gcp-networking]]
