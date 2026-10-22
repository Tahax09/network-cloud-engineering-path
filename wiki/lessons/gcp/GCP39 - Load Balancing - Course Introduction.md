---
id: GCP39
aliases: [GCP39]
title: Load Balancing - Course Introduction
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: "Course 5: Load Balancing — Introduction"
date: 2026-10-22
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, load-balancing, course-intro, hybrid-load-balancing, traffic-management, ilb-next-hop, cloud-cdn, cloud-armor, caching]
---

# GCP39 — Load Balancing: Course Introduction

> 🎛️ **Interactive visualization:** [Hybrid Load Balancer Builder](../../visualizations/gcp/GCP39-41%20-%20Hybrid%20Load%20Balancer%20Builder.html): load balancer picker, NEG types, hybrid NEG configuration checks. Opens in your browser.

## Summary
**Course 5 of 6** in *Networking in Google Cloud*: **Load Balancing**. Topics in order:
1. Overview of **Cloud Load Balancing**
2. **Hybrid load balancing**: across Google Cloud, other clouds and on-prem
3. **Traffic management**: routing on criteria you define, plus a **lab**
4. **Internal passthrough NLBs as next hops**: benefits, caveats, use cases
5. **Cloud CDN** on Google's edge, plus **Cloud Armor with Cloud CDN**, plus a **lab**
6. **Content caching** and load-balancing optimization strategies
7. A quiz

## Core Concepts
### Where this course sits
```
Course 1  Fundamentals            (not yet in wiki)
Course 2  Routing and Addressing  ✅ GCP01–GCP13
Course 3  Network Architecture    ✅ GCP14–GCP22
Course 4  Network Security        ✅ GCP23–GCP38 (GQ04–GQ06)
Course 5  Load Balancing          ◀ you are here (GCP39 →)
Course 6  Hybrid and Multicloud
```

### Already seen that this course builds on
| Topic | Seen in |
|-------|---------|
| Global external Application LB build order (health check → backend service → URL map → proxy → forwarding rule) | [[GCP28 - Lab - Configuring Traffic Blocklisting with Google Cloud Armor]] · [[gcp-command-anatomy]] §9 |
| LB built-in DDoS defense, Cloud CDN, Cloud Armor | [[GCP26 - Google Cloud DDoS Mitigations]] |
| Internal passthrough NLB as next hop for NVAs | [[GCP27 - Types of Complementary Partner Products]] · [[GCP20 - Other Topologies - Mesh, Mirrored, Gated]] |
| Network Service Tiers (Premium = global anycast) | [[GCP15 - Cloud Network Architecture Overview]] |
| ILB as Packet Mirroring collector | [[GCP37 - Packet Mirroring for Network Traffic Inspection]] |

## Key Terms
| Term | Definition |
|------|-----------|
| Hybrid load balancing | One Google LB with backends inside and outside Google Cloud |
| Traffic management | URL map features: weighted splits, header routing, retries, mirroring, rewrites |
| Next-hop ILB | A custom route whose next hop is an internal passthrough NLB (for HA appliances) |
| Cloud CDN | Edge caching for content served through external Application LBs |

## Real-World Connections
- **k3s:** a Service of `type: LoadBalancer` ≈ passthrough NLB; Ingress or Gateway API ≈ Application LB with URL maps. Traffic management ≈ Gateway API HTTPRoute weights and header matches.
- **AWS:** ALB / NLB / GWLB + CloudFront. GCP's single global anycast IP ≈ ALB + Global Accelerator.

## Troubleshooting Scenario
**Scenario (preview):** an app is moving to GKE, but half of it is still on-prem, and leadership wants one public endpoint. **Course answer:** a global external Application LB with a **zonal NEG** (GKE pods) and a **hybrid NEG** (on-prem) behind one backend service ([[GCP41 - Hybrid Load Balancing]]).

## Hands-On Ideas
1. List every LB in a test project: `gcloud compute forwarding-rules list --format="table(name,region,IPAddress,loadBalancingScheme,target.basename())"`.

## Quiz
1. Which is NOT a Course 5 topic?
   - A) Hybrid load balancing  B) Internal NLBs as next hops  C) VPC Service Controls  D) Cloud CDN with Cloud Armor

**Answers:** 1-C

## Weak Areas to Revisit
*(none flagged yet)*

## Linked Pages
- [[GCP40 - Module Introduction - Hybrid Load Balancing and Traffic Management]] · [[GCP41 - Hybrid Load Balancing]]
- [[GCP38 - Network Security Best Practices]] (end of Course 4) · [[certs/gcp-networking]]
