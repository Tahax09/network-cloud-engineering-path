---
id: GCP16
aliases: [GCP16]
title: Getting Started with Network Architecture
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: "Course 3: Network Architecture — Module 1: Introduction to Network Architecture"
date: 2026-10-15
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, network-architecture, design-process, stakeholders, requirements, hld, lld, bill-of-materials, capex, opex, compliance, cost-optimization]
---

# GCP16 — Getting Started with Network Architecture

## Summary
This lesson is the **practical design workflow** behind [[GCP15 - Cloud Network Architecture Overview]], and the last lesson of Module 1. It covers the **four key considerations**: **scalability, security (+ compliance), performance, cost efficiency**. Then a five-step process: **identify stakeholders → gather requirements → high-level design → low-level design → bill of materials (BoM) & cost**. Then best practices for **understanding the project/environment** and the **technical constraints** (regulations, cost-saving levers, operations).

---

## Core Concepts

### The four key considerations
| Consideration | Meaning | GCP levers |
|---------------|---------|-----------|
| **Scalability** | Absorb more resources and growing traffic | Regional subnets with room to grow (expand, can't shrink, [[GCP01 - Routes and Route Preferences]]), managed instance groups, global LB, NCC |
| **Security** | Protect against unauthorized access and attacks: firewalls, access controls, encryption | Firewall policies, IAM, private access ([[GCP07 - Private Connection Options - Overview]]), no external IPs + IAP ([[GCP12 - Lab - Implement Private Google Access and Cloud NAT]]) |
| ↳ **Compliance** | Follow organizational, **industry**, and **government** rules | Data residency (region choice), VPC Service Controls, audit logging ([[L18 - Basic Network Security]]: GDPR, PCI DSS, HIPAA) |
| **Performance** | Speed and responsiveness, smooth user experience | Premium Tier, Cloud CDN, region placement near users ([[GCP15 - Cloud Network Architecture Overview]]) |
| **Cost efficiency** | Design + implementation + **operation** cost vs goals | Standard Tier, right-sizing, committed use discounts, managed services, egress-aware placement |

### The design process
```
1. Stakeholders ─▶ 2. Requirements ─▶ 3. High-level design ─▶ 4. Low-level design ─▶ 5. BoM & cost
```
| Step | What you do | Output |
|------|-------------|--------|
| **1. Identify decision makers & stakeholders** | App owners, security architects, solution architects, operations managers. They **change with scope** (one project vs a line of business vs the whole org) | Stakeholder list + who can **decide** (e.g., engineering managers, accounting) |
| **2. Gather requirements** | Interviews, workshops, review existing docs: security, performance, scalability, compliance, budget **including OpEx** | Requirements document with measurable targets |
| **3. High-level design (HLD)** | Overall structure: major components and how they relate; cloud infrastructure, **geographic locations**, connectivity options | Architecture diagram |
| **4. Low-level design (LLD)** | Granular: device/resource configuration, **IP addressing scheme**, security protocols | Config specs, IP plan, firewall matrix |
| **5. Bill of materials & cost** | Everything needed: hardware, software, licenses, services → **CapEx** (upfront) + **OpEx** (ongoing) | BoM + total cost so stakeholders can decide |

### Best practices: understand the project and environment
- **Define objectives and timeline.** They drive resource allocation, architecture complexity, and implementation **phases**.
- **Assess existing infrastructure** (on-prem and cloud) that the VPC must integrate with: existing **VPCs, subnets, security configurations, network policies**. That is where IP overlap and policy conflicts surface early.

### Technical considerations and constraints
| Area | Examples |
|------|----------|
| **Regulations / data privacy** | Affects data segregation, access control, **logging** |
| **Cost saving** | **Right-sizing**, **committed use discounts (CUDs)**, managed services instead of self-run appliances |
| **Operations** | Ongoing management, **monitoring**, **incident response**, defined roles and responsibilities, **automation**, logging configuration |

---

## Key Terms
| Term | Definition |
|------|-----------|
| Scalability / security / performance / cost efficiency | The four design considerations |
| Compliance | Meeting organizational, industry, and government rules |
| Stakeholder | Anyone whose needs the network must satisfy (app owners, security, ops…) |
| High-level design (HLD) | Overall structure and relationships of major components |
| Low-level design (LLD) | Detailed configurations, IP addressing, security protocols |
| Bill of materials (BoM) | Complete list of hardware, software, licenses, services to implement the design |
| CapEx / OpEx | Upfront capital vs ongoing operational expenditure |
| Committed use discount (CUD) | Discount for committing to resource usage for 1 or 3 years |
| Right-sizing | Matching resource size to actual need |

---

## Real-World Connections
- **Network+:** This is the **documentation and change management** discipline from [[L13 - Organizational Processes and Procedures]]: HLD/LLD = logical/physical diagrams, IP plan = IPAM, monitoring and incident response = [[L14 - Network Monitoring]] and [[L15 - Disaster Recovery]].
- **AWS:** The same process underlies the **Well-Architected Framework** pillars (Security, Reliability, Performance Efficiency, Cost Optimization, Operational Excellence, Sustainability). Google's equivalent is the **Google Cloud Architecture Framework**.
- **k3s → cloud migration exercise:** Stakeholder = you. Requirements = Elasticsearch storage growth and Kibana latency. HLD = GKE/GCE in one region. LLD = pod/service CIDRs that don't overlap your LAN. BoM = node types + persistent disk + egress.

---

## Troubleshooting Scenario
**Scenario:** A team deploys a new VPC quickly. Two weeks later, connecting it to the corporate network over Interconnect fails: its `10.0.0.0/16` overlaps on-prem ranges. Security then flags that logs don't meet the bank's retention rules.
**Analysis:** Steps were skipped: **assess existing infrastructure** (IP overlap) and **gather compliance requirements** (logging/retention) before the LLD.
**Fix:** Re-IP from an approved IPAM block, add Cloud Logging sinks with the required retention, and run the process (stakeholders → requirements → HLD → LLD → BoM) for the next VPC.

---

## Hands-On Ideas
1. Write a one-page HLD + LLD for "k3s observability stack on GCP": regions, VPC/subnets, firewall matrix, private access, NAT, monitoring.
2. Build a BoM with the **Google Cloud Pricing Calculator** (VMs, disks, Cloud NAT, egress) and split it into CapEx (none, it's cloud) vs monthly OpEx. Compare a 1-year CUD vs on-demand.
3. List the stakeholders and decision makers for a real project of yours (e.g., a Next.js + PostgreSQL app) and note the one requirement each cares about most.

---

## Quiz
1. Which four considerations does the course name for network design?
   - A) Scalability, security, performance, cost efficiency  B) Latency, jitter, loss, bandwidth  C) IAM, billing, quotas, logging  D) Regions, zones, VPCs, subnets
2. Compliance is described as observing rules from whom?
   - A) Only the cloud provider  B) Only end users  C) The organization, industry, and government bodies  D) The ISP
3. What is the first step in VPC network design?
   - A) Build the BoM  B) Identify decision makers and stakeholders  C) Create subnets  D) Choose regions
4. Which design level specifies IP addressing schemes and device configurations?
   - A) High-level design  B) Bill of materials  C) Requirements gathering  D) Low-level design
5. What does a bill of materials support?
   - A) Routing  B) Calculating total cost, CapEx and OpEx, so stakeholders can make informed decisions  C) Firewall rules  D) DNS
6. Which is a cost-saving strategy named in the lesson?
   - A) Committed use discounts and right-sizing  B) Overprovisioning  C) Using only Premium Tier  D) Disabling logging
7. Why assess existing VPCs, subnets and policies early?
   - A) It's optional  B) To integrate cleanly and catch overlaps and conflicts before low-level design  C) To delete them  D) To pick a VM type

**Answers:** 1-A, 2-C, 3-B, 4-D, 5-B, 6-A, 7-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[GCP15 - Cloud Network Architecture Overview]] — Design goals and toolkit
- [[GCP17 - Module Introduction - Network Topologies]] — Next module
- [[L13 - Organizational Processes and Procedures]] · [[L18 - Basic Network Security]]
- [[certs/gcp-networking]]
