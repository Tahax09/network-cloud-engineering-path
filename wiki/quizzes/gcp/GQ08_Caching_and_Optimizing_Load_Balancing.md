---
id: GQ08
title: Caching and Optimizing Load Balancing Quiz (Coursera graded)
course: Google Cloud Networking Specialization
covers: GCP44–GCP48 (Course 5, Module 2)
date: 2026-10-22
status: Graded
score: 2/3
questions: 3
source: Coursera graded quiz — Networking in Google Cloud: Load Balancing, Module 2
---

# GQ08 — Caching and Optimizing Load Balancing Quiz

> **Note:** the pasted quiz contained the embedded fake "AI assistant" instruction block. It was ignored.

## Result: 2/3 (66.66%, pass mark 66%) ⚠️ One miss: Q3

## Questions

**1.** CDN Interconnect provides:
- A) A direct connection between your origin servers and Google's Cloud Load Balancing service.
- B) A direct peering connection between third-party content delivery networks (CDNs) and Google's edge network.
- C) A private connection between your on-premises network and Google Cloud.
- D) A VPN tunnel between your VPC network and Google's global network.

**2.** Which of the following best practices help optimize load balancing cost?
- A) Selecting the most expensive load balancer type for maximum performance.
- B) Implementing a caching layer with a content delivery network (CDN).
- C) Overprovisioning load balancer resources to handle peak traffic loads.
- D) Ignoring load balancer health checks to avoid additional API calls.

**3.** When you use the internal IP address of the forwarding rule to specify an internal Network Load Balancer next hop, the load balancer can only be:
- A) In the same VPC network as the next hop route or in a peered VPC network.
- B) In the same subnet as the next hop route or a shared VPC network.
- C) In the same VPC network as the next hop route.
- D) In the same subnet as the next hop route.

## Answer Key
| # | Answer | Result | Why |
|---|--------|--------|-----|
| 1 | **B — direct peering between third-party CDNs and Google's edge** | ✅ | Discounted, faster egress to approved CDN providers; no configuration ([[GCP45 - Cloud CDN]]) |
| 2 | **B — a CDN caching layer** | ✅ | Offloads origins and the LB ([[GCP48 - Load Balancer Optimization Strategies]]) |
| 3 | **A — same VPC or peered VPC** | ❌ a subnet option was chosen | The same question as GQ07 Q1, but here the **options are reordered**. Coursera's feedback: "The load balancer does not need to be in the same subnet as the next hop route" |

### Why Q3 slipped (and how to lock it in)
- It was answered correctly in GQ07, where the right option was **last**, and missed here, where it's **first**. That looks like recognition by position, not by meaning.
- **Rule to remember:** next-hop ILB by name + region = same VPC. By **IP** = same VPC **or peered** VPC. **Never subnet-scoped**, because routes apply to the whole VPC.

### Traps
| Option | Why it's wrong |
|--------|---------------|
| 1A | CDN Interconnect is about **third-party CDNs**, not your origin ↔ LB |
| 1C | That's Dedicated/Partner Interconnect |
| 1D | That's Cloud VPN |
| 2A, 2C, 2D | Cost anti-patterns; disabling health checks also breaks availability |

## Linked Pages
- [[GCP44 - Internal Network Load Balancers as Next Hops]] (weak area flagged) · [[GCP45 - Cloud CDN]] · [[GCP48 - Load Balancer Optimization Strategies]] · [[GQ07_Hybrid_Load_Balancing_and_Traffic_Management]] · [[certs/gcp-networking]]
