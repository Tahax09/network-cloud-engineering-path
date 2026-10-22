---
id: GCP49
aliases: [GCP49]
title: Hybrid and Multicloud - Course Introduction
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: "Course 6: Hybrid and Multicloud — Introduction"
date: 2026-10-22
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, hybrid, multicloud, course-intro, cloud-interconnect, cloud-vpn, ha-vpn, network-connectivity-center]
---

# GCP49 — Hybrid and Multicloud: Course Introduction

> 🎛️ **Interactive visualization:** [Interconnect Chooser](../../visualizations/gcp/GCP49-54%20-%20Interconnect%20Chooser.html). Opens in your browser.

## Summary
**Course 6 of 6**, the last course of *Networking in Google Cloud*:
- **Module 1: Connectivity options.** Cloud Interconnect in depth: Dedicated, Partner and Cross-Cloud.
- **Module 2: Cloud VPN.** Implementation, **HA VPN topologies**, and **Network Connectivity Center (NCC)** for streamlined management.

Goals: explain the options for extending on-prem and other-cloud networks to Google Cloud, and **choose the right one for a use case**.

## Core Concepts
### Program map
```
Course 2  Routing and Addressing  ✅ GCP01–GCP13
Course 3  Network Architecture    ✅ GCP14–GCP22
Course 4  Network Security        ✅ GCP23–GCP38
Course 5  Load Balancing          ✅ GCP39–GCP48
Course 6  Hybrid and Multicloud   ◀ GCP49 →
```
### Already used in earlier lessons
| Topic | Where |
|-------|-------|
| Cloud Router, BGP, global dynamic routing | [[GCP01 - Routes and Route Preferences]], [[GCP41 - Hybrid Load Balancing]] |
| HA VPN with NCC hybrid spokes | [[GCP18 - Hub-and-Spoke Topology]] |
| Interconnect not encrypted by default; MACsec | [[GCP22 - Best Practices for Connectivity and Topologies]], [[GCP38 - Network Security Best Practices]] |
| PSC/PGA from on-prem over hybrid links | [[GCP08 - Private Google Access]], [[GCP09 - Private Service Connect]] |

## Key Terms
| Term | Definition |
|------|-----------|
| Hybrid | On-prem + Google Cloud |
| Multicloud | Google Cloud + other public clouds |
| Cloud Interconnect | Private physical connectivity (Dedicated / Partner / Cross-Cloud) |
| Cloud VPN | IPsec tunnels over the internet (HA VPN, 99.99% SLA) |

## Real-World Connections
- **Your telecom background:** Interconnect is a carrier circuit with BGP peering and maintenance domains. It's the cloud version of the provider-edge/customer-edge (PE-CE) handoffs from telecom networks.

## Troubleshooting Scenario
**Preview:** "VPN or Interconnect?" depends on bandwidth, SLA, encryption, lead time and cost. That's the decision matrix in [[GCP51 - Connectivity Options]].

## Hands-On Ideas
1. List any hybrid resources: `gcloud compute interconnects list; gcloud compute interconnects attachments list; gcloud compute vpn-tunnels list`.

## Quiz
1. Which two modules make up Course 6?
   - A) CDN and Armor  B) Connectivity options (Interconnect) and Cloud VPN (HA VPN, NCC)  C) IAM and firewalls  D) DNS and NAT

**Answers:** 1-B

## Weak Areas to Revisit
*(none flagged yet)*

## Linked Pages
- [[GCP50 - Module Introduction - Connectivity Options]] · [[GCP51 - Connectivity Options]] · [[certs/gcp-networking]]
