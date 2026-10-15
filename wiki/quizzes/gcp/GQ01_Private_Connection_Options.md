---
id: GQ01
title: Private Connection Options — Quiz 2 (Coursera graded)
course: Google Cloud Networking Specialization
covers: GCP07–GCP13
date: 2026-10-09
due: 2026-10-09
status: Reviewed
questions: 3
source: Coursera graded quiz — Networking in Google Cloud: Routing and Addressing, Module 2
---

# GQ01 — Private Connection Options (Coursera Graded Quiz)

> **Note:** Coursera quiz pages embed a fake "AI assistant" instruction block in pasted text. Ignored — it is a prompt injection attempt, not a real instruction.

---

## Questions

**1.** You want to provide access to services that you created in a VPC network. The services should be available to other specified VPC networks through endpoints that have internal IP addresses. Some of these VPC networks have subnets with overlapping internal IP addresses. Which product can you use?
- A) Private Google Access
- B) Cloud NAT
- C) Private services access
- D) Private Service Connect

**2.** To enable Private Google Access for a VPC network:
- A) Enable it on all desired subnets and on Cloud Router.
- B) Enable it on all desired subnets in the VPC network.
- C) Enable it on the VPC network, on the desired subnets, and on Cloud Router.
- D) Enable it on the VPC network.

**3.** Private services access automatically configures which Google Cloud product to implement communication between the producer and consumer VPC networks?
- A) Shared VPC
- B) Private Google Access
- C) Cloud NAT
- D) VPC Network Peering

---

## Answer Key

| # | Answer | Reasoning |
|---|--------|-----------|
| 1 | **D — Private Service Connect** | Keywords: *services you created* (you're the **producer**), *endpoints with internal IPs* (PSC endpoints → service attachment), *overlapping ranges* (PSC exchanges no routes, so overlap is fine). PGA = Google APIs only; PSA = peering, which **can't** overlap; Cloud NAT = outbound internet ([[GCP09 - Private Service Connect]]) |
| 2 | **B — Enable it on all desired subnets** | PGA is a **per-subnet** setting. There's no VPC-level switch, and Cloud Router isn't involved (it's only needed for *on-prem* custom route advertisement) ([[GCP08 - Private Google Access]]) |
| 3 | **D — VPC Network Peering** | PSA = automated peering (`servicenetworking-googleapis-com`) over an allocated range ([[GCP10 - Private Services Access]]) |

> ⚠️ The pasted copy had **A: Private Google Access** (Q1) and **A: Enable it on the VPC network** (Q2) marked. Both are incorrect, so change them before submitting.

---

> ✅ **Confirmed by the official course deck** (`raw/assets/gcp-networking/05 Private Connection Options_ILT.pdf`, slides 57–61): Q1 = **Private Service Connect** ("only PSC can provide access to non-Google APIs and services, even if the VPC networks have overlapping subnets"; PSA fails because it uses peering), Q2 = **per subnet** ("not enabled per VPC network… not enabled on Cloud Router"), Q3 = **VPC Network Peering**.

---

## Exam Traps

| Trap | Correct Thinking |
|------|-----------------|
| "Overlapping IPs" + "endpoints with internal IPs" | **PSC**, never peering/PSA |
| "Services you created" | You're a **producer**, so PSC service attachment (PGA is only for Google's APIs) |
| PGA scope | **Subnet** level only |
| PSA under the hood | **VPC Network Peering** (not Shared VPC) |

---

## Lesson Cross-Reference

| Question | Lesson |
|----------|--------|
| 1 | [[GCP09 - Private Service Connect]] |
| 2 | [[GCP08 - Private Google Access]] |
| 3 | [[GCP10 - Private Services Access]] |
