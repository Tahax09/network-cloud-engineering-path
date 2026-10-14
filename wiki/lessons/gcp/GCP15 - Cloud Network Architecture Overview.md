---
id: GCP15
aliases: [GCP15]
title: Cloud Network Architecture Overview
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: "Course 3: Network Architecture — Module 1: Introduction to Network Architecture"
date: 2026-10-14
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, network-architecture, vpc, subnets, firewall-policies, cloud-router, cloud-vpn, cloud-interconnect, load-balancing, cloud-cdn, network-service-tiers, three-tier, multi-region, design-metrics]
---

# GCP15 — Cloud Network Architecture Overview

## Summary
**Network architecture** is the design of your **virtual network**: how VMs, containers and other resources connect. **Google runs the physical infrastructure; you configure the virtual pieces**: VPCs, subnets, firewalls, Cloud Router, Cloud VPN, Interconnect, load balancers. Design choices drive **scalability, security, performance and cost**, and a poor design leads to outages, breaches and slowness. The method: **assess current and future requirements → define measurable targets → choose the services that meet them.** The worked example takes Cymbal Bank's **3-VM single-subnet app** to a **multi-region, tier-per-subnet, load-balanced** design for a global audience.

---

## Core Concepts

### Shared responsibility for the network
| Google manages | You configure |
|----------------|--------------|
| Physical network, backbone, data centers, Andromeda SDN | VPC networks, subnets, IP plans, routes, firewall rules/policies, Cloud Router/BGP, VPN/Interconnect, load balancers, NAT, DNS, service tier |

(This is the cloud shared-responsibility model from [[L03 - Network Virtualization & Cloud Technologies]], applied to networking.)

### Design process
1. **Assess** current and future needs: number of users, applications, **traffic volume**, security/compliance requirements, geography.
2. **Define quantifiable metrics**, the benchmarks you'll judge designs against:
   | Goal | Example metric |
   |------|----------------|
   | Scalability | Handle 10× users without redesign; IP space for 3 more regions |
   | Security | DB reachable only from the app tier; no external IPs on back-end VMs |
   | Performance | p95 latency < 150 ms for users in Asia/NA/EU |
   | Cost efficiency | Egress cost per user; Standard vs Premium tier choice |
3. **Select services** that meet those metrics (below).

There is **no universal design**: components and protocols are chosen per organization.

### The Google Cloud networking toolkit
| Service | Role in an architecture | Covered in |
|---------|------------------------|-----------|
| **VPC network + subnets** | Global VPC, **regional subnets**; segment by function, security, or project | [[GCP01 - Routes and Route Preferences]], [[GCP02 - IPv6 in Google Cloud]] |
| **Firewall rules → firewall policies** | Granular allow/deny; group rules into **(hierarchical/network) firewall policies** for easier management at scale | Course 4 (Network Security) |
| **Cloud VPN** | Encrypted IPsec tunnels to on-prem over the internet | [[L17 - Network Access and Management]]; Course 6 |
| **Cloud Interconnect** | Dedicated/partner **high-bandwidth, low-latency** private links to on-prem. **HA VPN over Interconnect** adds encryption when required | Course 6 |
| **Cloud Router** | **BGP** route exchange for Interconnect, Cloud VPN, and Router appliances; **control plane for Cloud NAT** | [[GCP01 - Routes and Route Preferences]], [[GCP11 - Cloud NAT]] |
| **Load balancers** | Spread traffic across backends; performance, scale, **high availability** | Course 5 |
| **Cloud CDN** | Cache content at Google's global edge to speed up web apps | Course 5 |
| **Network Service Tiers** | **Premium** vs **Standard** routing of internet traffic (below) | — |
| Private access, Cloud NAT, Cloud DNS | Private API access, outbound internet, name resolution | [[GCP07 - Private Connection Options - Overview]], [[GCP04 - Cloud DNS]] |

> **Transcript clarification:** The lecture lists "policy-based routing" among *Cloud Router* options. **Policy-based routes** are a **VPC routing feature** (route by source/protocol, e.g. to an NVA/ILB) and are separate from Cloud Router, whose job is **BGP**. Cloud Router supports BGP peering, custom route advertisement, and BFD.

### Network Service Tiers
| | **Premium Tier** (default) | **Standard Tier** |
|-|----------------------------|-------------------|
| Path | Traffic enters/leaves **Google's backbone at the PoP nearest the user** ("cold potato") | Traffic rides the **public internet / ISP transit**, handed off near the **GCP region** ("hot potato") |
| Performance | Higher throughput, lower and more consistent latency | Varies with the internet path |
| Load balancing | **Global** external load balancers, a single anycast IP | **Regional** only |
| Cost | Higher egress price | Lower egress price |
| Use when | Global, latency-sensitive, demanding apps | General workloads where speed isn't the top priority, regional audiences |

### Worked example: Cymbal Bank goes global
**Before:** one subnet, three VMs: **web**, **business logic**, **database**. Fine for a small subsidiary, but a **bottleneck and single point of failure** for a global audience.

**After:**
```
                    Global external Application LB (Premium Tier, one anycast IP)
                     ├──────────────── least-busy backend ────────────────┐
Region A (e.g., us-east1)                              Region B (e.g., europe-west1)
  web-subnet-a      10.10.1.0/24  [web VMs]              web-subnet-b      10.20.1.0/24
  logic-subnet-a    10.10.2.0/24  [logic VMs]            logic-subnet-b    10.20.2.0/24
  db-subnet-a       10.10.3.0/24  [db VM]                db-subnet-b       10.20.3.0/24
            mirrored layout, UNIQUE IP ranges per region (no overlap)
```
**Why it's better:**
| Change | Benefit |
|--------|---------|
| **More server sets** (mirrored per region; more than 2 in reality) | Spreads processing; capacity near users |
| **Load balancer** routes to the **least-busy** set | Avoids bottlenecks, provides failover |
| **Each tier in its own subnet** | Apply different firewall rules and IAM per tier, e.g. **DB accepts traffic only from the business-logic tier** |
| **Mirrored regions, unique ranges** | Regions can be routed between and replicate data without IP conflicts |

> **Design note (beyond the lecture):** VPC firewall rules aren't attached to subnets; they're VPC-wide, with **targets** (all instances, network tags, or **service accounts**). The "lock the DB down to the logic tier" rule is best written as **target SA = `db-sa`, source SA = `logic-sa`, allow tcp:5432**, which keeps working if VMs move between subnets. Subnets still help for **CIDR-based** policy (on-prem ACLs, Cloud NAT mapping, Private Google Access per subnet).

---

## Key Terms

| Term | Definition |
|------|-----------|
| Network architecture | Design of how cloud resources connect and communicate |
| Design metrics | Quantified targets for scalability, security, performance, cost |
| Firewall policy | Grouped firewall rules applied at org/folder (hierarchical) or VPC (network) level |
| Cloud Interconnect | Dedicated or partner private connection to Google (high bandwidth, low latency) |
| HA VPN over Interconnect | IPsec encryption over an Interconnect attachment |
| Cloud Router | Managed BGP speaker; control plane for dynamic routes and Cloud NAT |
| Router appliance | Third-party NVA that peers BGP with Cloud Router (NCC spoke) |
| Cloud CDN | Edge caching on Google's global network |
| Network Service Tiers | Premium (Google backbone, global) vs Standard (public internet, regional) |
| Three-tier application | Web / application logic / database layers |
| Policy-based route | VPC route chosen by source/protocol rather than destination only |

---

## Real-World Connections
- **Network+:** This is **segmentation and three-tier design** ([[L06 - Network Topologies, Architectures & Traffic Flows]], [[L18 - Basic Network Security]]): each tier in its own zone, least-privilege flows between them. The DB-only-from-logic rule is the **screened-subnet** idea ([[L20 - Network Defense Techniques]]). Load balancing is from [[L02 - Networking Appliances & Applications]].
- **AWS mapping:** VPC + subnets per AZ (AWS subnets are **zonal**; GCP subnets are **regional**). Security Groups referencing other SGs ≈ GCP service-account firewall rules. Global Accelerator ≈ Premium Tier. CloudFront ≈ Cloud CDN. Direct Connect ≈ Interconnect.
- **k3s / your Next.js + PostgreSQL apps:** the same three tiers map to **Ingress (web) → app Deployment (logic) → PostgreSQL StatefulSet (DB)**. In Kubernetes the per-tier subnet becomes a **NetworkPolicy**: allow `5432` to pods labeled `tier=db` only from `tier=logic`. Going multi-region means a cluster per region behind a global LB, with **non-overlapping pod/service CIDRs** per cluster.

---

## Troubleshooting Scenario
**Scenario:** After Cymbal Bank goes multi-region, the European replica DB can't sync from the US primary, and the EU web tier intermittently reaches the US DB directly.
**Analysis:**
1. The team cloned the US subnet layout **including the IP ranges** into Europe. With overlapping `10.10.x.x` ranges in one VPC, subnet creation fails; across peered VPCs the routes conflict. → **Mirrored layout, unique ranges.**
2. The DB firewall rule allowed `source: 10.0.0.0/8`, so any tier in any region can reach either DB. → Restrict by **source service account (logic tier)** and per-region intent.

**Fix:** re-IP the EU subnets (`10.20.x.x`), rewrite the DB rule as SA-to-SA, and verify with **Connectivity Tests** (Network Intelligence Center): EU-logic → EU-DB allowed, EU-web → US-DB denied.

---

## Hands-On Ideas
1. Build the "after" design in a test project, scaled down: one custom VPC, six subnets (3 tiers × 2 regions, unique /24s), e2-micro VMs per tier, and SA-based firewall rules (web→logic 8080, logic→db 5432). Test with `nc -zv`.
2. Compare service tiers: create two VMs with external IPs, one Premium and one Standard (`gcloud compute addresses create … --network-tier=STANDARD`). `traceroute` to each from your Mac and count where the path enters Google's network.
3. Write the design metrics for your own Next.js + PostgreSQL project (users, regions, latency target, DB isolation rule) before choosing services.
4. Translate the three-tier firewall model into k3s NetworkPolicies (default-deny + two allow rules) and test with `kubectl exec … -- nc -zv`.

---

## Quiz
1. In Google Cloud, who is responsible for configuring VPCs, subnets, firewalls and VPN?
   - A) Google  B) The ISP  C) Shared equally, configured by Google  D) The customer
2. What should come first when designing a network architecture?
   - A) Assessing current and future requirements, then defining quantifiable metrics  B) Picking Premium Tier  C) Creating subnets  D) Deploying a load balancer
3. Which service provides high-bandwidth, low-latency private connectivity to on-prem?
   - A) Cloud VPN  B) Cloud CDN  C) Cloud Interconnect  D) Cloud NAT
4. Cloud Router provides BGP for which products?
   - A) Cloud CDN, Cloud DNS  B) Cloud Interconnect, Cloud VPN, Router appliance  C) Cloud Storage, BigQuery  D) Load balancers only
5. Which Network Service Tier keeps traffic on Google's backbone to the PoP nearest the user and supports global load balancing?
   - A) Standard  B) Premium  C) Basic  D) Regional
6. In the Cymbal Bank redesign, why put each tier in its own subnet?
   - A) GCP requires it  B) To apply tier-specific security (firewall/IAM), e.g. the DB accepts only business-logic traffic  C) To save IP addresses  D) To enable Cloud CDN
7. The regions are mirrored. What must differ between them?
   - A) VM types  B) Firewall rule names  C) Subnet IP ranges  D) Nothing
8. Which is the most robust way to allow only the logic tier to reach the DB?
   - A) Firewall rule with source service account = logic tier, target service account = DB, allow tcp:5432  B) Source range `0.0.0.0/0`  C) Put both in one subnet  D) Disable the firewall

**Answers:** 1-D, 2-A, 3-C, 4-B, 5-B, 6-B, 7-C, 8-A

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[GCP14 - Network Architecture - Course Introduction]] — Course 3 overview
- [[GCP01 - Routes and Route Preferences]] · [[GCP11 - Cloud NAT]] · [[GCP07 - Private Connection Options - Overview]]
- [[L06 - Network Topologies, Architectures & Traffic Flows]] · [[L20 - Network Defense Techniques]] · [[L03 - Network Virtualization & Cloud Technologies]]
- [[certs/gcp-networking]]
