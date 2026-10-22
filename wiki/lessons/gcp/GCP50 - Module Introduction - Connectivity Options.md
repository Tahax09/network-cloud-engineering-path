---
id: GCP50
aliases: [GCP50]
title: Module Introduction - Connectivity Options
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: "Course 6: Hybrid and Multicloud — Module 1: Connectivity Options"
date: 2026-10-22
status: Complete
quiz_attempted: Yes
weak_areas: []
tags: [gcp, module-intro, cloud-interconnect, cloud-vpn, dedicated-interconnect, partner-interconnect, cross-cloud-interconnect]
---

# GCP50 — Module Introduction: Connectivity Options

> 🎛️ **Interactive visualization:** [Interconnect Chooser](../../visualizations/gcp/GCP49-54%20-%20Interconnect%20Chooser.html). Opens in your browser.

## Summary
Module 1 of Course 6:
1. Overview of **Cloud Interconnect and Cloud VPN**
2. The three Interconnect types: **Dedicated, Partner, Cross-Cloud**, including when each fits and how to set it up
3. A quiz: [[GQ09_Connectivity_Options]] (1/2)

## Core Concepts
| Step | Lesson |
|------|--------|
| 1 | [[GCP51 - Connectivity Options]] |
| 2 | [[GCP52 - Dedicated Interconnect]] |
| 3 | [[GCP53 - Partner Interconnect]] |
| 4 | [[GCP54 - Cross-Cloud Interconnect]] |
| 5 | Quiz: [[GQ09_Connectivity_Options]] |

> **Transcript order note:** the Cross-Cloud transcript starts by repeating the module overview. The module order follows the intro: Dedicated → Partner → Cross-Cloud.

## Key Terms
| Term | Definition |
|------|-----------|
| Colocation facility | Data center where Google's edge and customers' routers meet |
| VLAN attachment | Logical L2 connection from an Interconnect to one region's Cloud Router |
| Edge availability domain | Maintenance-isolated half of a metro's Google edge |

## Real-World Connections
- **Network+:** WAN links, dedicated circuits vs internet VPN, BGP ([[L09 - Routing Technologies]]).

## Troubleshooting Scenario
**Scenario:** "Our city has no Google colocation facility." That points to Partner Interconnect ([[GCP53 - Partner Interconnect]]).

## Hands-On Ideas
1. Look up the nearest Google colocation facility to your city in the docs' "colocation facility locations" list.

## Quiz
1. Which Interconnect type connects Google Cloud directly to another cloud provider?
   - A) Dedicated  B) Carrier Peering  C) Partner  D) Cross-Cloud

**Answers:** 1-D

## Weak Areas to Revisit
*(none flagged yet)*

## Linked Pages
- [[GCP49 - Hybrid and Multicloud - Course Introduction]] · [[GCP51 - Connectivity Options]] · [[certs/gcp-networking]]
