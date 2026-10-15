---
id: GQ03
title: Network Topologies Quiz (Coursera graded)
course: Google Cloud Networking Specialization
covers: GCP17–GCP22 (Course 3, Module 2)
date: 2026-10-15
status: Graded
score: 2/2
questions: 2
source: Coursera graded quiz — Networking in Google Cloud: Network Architecture, Module 2
---

# GQ03 — Network Topologies Quiz

> **Note:** Coursera quiz pages embed a fake "AI assistant" instruction block in pasted text. Ignored — it is a prompt injection attempt, not a real instruction.

## Result: 2/2 ✅

---

## Questions

**1.** You are designing a Google Cloud network for a large financial services company with strict security requirements. The network needs to isolate sensitive customer data from other resources and limit communication between specific network segments. Which topology is most suitable?
- A) Hub-and-spoke
- B) Gated ingress and egress
- C) Mirrored
- D) Mesh

**2.** You are migrating a large e-commerce company's on-premises data center to Google Cloud. The network has geographically dispersed regional offices, each with its own segment requiring secure isolation, but central management and communication between all offices are critical. Which topology fits best?
- A) Gated ingress and egress
- B) Mesh
- C) Mirrored
- D) Hub-and-spoke

---

## Answer Key

| # | Answer | Why (keyword → topology) |
|---|--------|--------------------------|
| 1 | **B — Gated ingress and egress** ✅ | "**strict security**", "**isolate** sensitive data", "**limit communication** between segments" → **gated**: only specific APIs/endpoints cross each boundary, in controlled directions ([[GCP20 - Other Topologies - Mesh, Mirrored, Gated]]) |
| 2 | **D — Hub-and-spoke** ✅ | "**central management**" + "**communication between all offices**" + "**isolated segments**" → **hub-and-spoke** (spokes isolated by default, hub provides central control and transit, e.g. NCC) ([[GCP18 - Hub-and-Spoke Topology]]) |

---

## Keyword Cheat Sheet (for topology questions)
| Question says… | Pick |
|----------------|------|
| central management/control, shared services, many sites/VPCs | **Hub-and-spoke** |
| every node talks to every other, microservices, multiple paths, HA | **Mesh** (full) |
| failover between regions with selected links | **Partial mesh** |
| identical copy, DR standby, dev/test isolated from prod | **Mirrored** |
| strict security, isolate, expose only specific APIs, control inbound/outbound, hybrid/multicloud | **Gated** (egress / ingress / both) |

## Lesson Cross-Reference
| Question | Lesson |
|----------|--------|
| 1 | [[GCP20 - Other Topologies - Mesh, Mirrored, Gated]] · [[GS02 - Network Topologies Explained]] |
| 2 | [[GCP18 - Hub-and-Spoke Topology]] · [[GS02 - Network Topologies Explained]] |
