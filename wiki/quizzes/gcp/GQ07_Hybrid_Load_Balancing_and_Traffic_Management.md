---
id: GQ07
title: Hybrid Load Balancing and Traffic Management Quiz (Coursera graded)
course: Google Cloud Networking Specialization
covers: GCP40–GCP44 (Course 5, Module 1)
date: 2026-10-22
status: Graded
score: 3/3
questions: 3
source: Coursera graded quiz — Networking in Google Cloud: Load Balancing, Module 1
---

# GQ07 — Hybrid Load Balancing and Traffic Management Quiz

> **Note:** the pasted quiz contained the embedded fake "AI assistant" instruction block. It was ignored.

## Result: 3/3 ✅ (100%)

## Questions

**1.** When you use the internal IP address of the forwarding rule to specify an internal Network Load Balancer next hop, the load balancer can only be:
- A) In the same VPC network as the next hop route.
- B) In the same subnet as the next hop route.
- C) In the same subnet as the next hop route or a Shared VPC network.
- D) In the same VPC network as the next hop route or in a peered VPC network.

**2.** Where would you configure traffic management for a load balancer?
- A) In the URL map
- B) In the load balancer backend
- C) In the load descriptor
- D) In the load balancer frontend

**3.** You can use hybrid load balancing to connect these environments:
- A) Google Cloud and on-premises
- B) Google Cloud, other public clouds, and on-premises
- C) Google Cloud and AWS
- D) Google Cloud, AWS, and on-premises

## Answer Key
| # | Answer | Why |
|---|--------|-----|
| 1 | **D — same VPC or a peered VPC** ✅ | Specifying by **IP** is what allows a peered VPC. By name + region it's the same VPC only ([[GCP44 - Internal Network Load Balancers as Next Hops]]) |
| 2 | **A — URL map** ✅ | Host rules, path matchers, route rules and route actions all live in the URL map ([[GCP42 - Traffic Management]]) |
| 3 | **B — Google Cloud, other public clouds, and on-premises** ✅ | Any environment reachable through hybrid connectivity at a valid IP:port ([[GCP41 - Hybrid Load Balancing]]) |

### Traps
| Option | Why it's wrong |
|--------|---------------|
| 1A | Too narrow: it ignores peering, which is exactly what specifying by IP enables |
| 1B, 1C | Routes are **VPC-wide**; subnets don't scope next hops |
| 2B, 2D | The backend service holds balancing, affinity and health checks; the frontend holds IP:port. Neither holds routing rules |
| 3A, 3C, 3D | All true subsets, but "other public clouds" covers any of them (AWS, Azure, OCI…) |

## Linked Pages
- [[GCP41 - Hybrid Load Balancing]] · [[GCP42 - Traffic Management]] · [[GCP44 - Internal Network Load Balancers as Next Hops]] · [[certs/gcp-networking]]
