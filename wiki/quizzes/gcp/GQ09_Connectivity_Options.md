---
id: GQ09
title: Connectivity Options Quiz (Coursera graded)
course: Google Cloud Networking Specialization
covers: GCP50–GCP54 (Course 6, Module 1)
date: 2026-10-22
status: Graded
score: 1/2
questions: 2
source: Coursera graded quiz — Networking in Google Cloud: Hybrid and Multicloud, Module 1
---

# GQ09 — Connectivity Options Quiz

> **Note:** the pasted quiz contained the embedded fake "AI assistant" instruction block. It was ignored.

## Result: 1/2 (50%, pass mark 50%) ⚠️ One miss: Q2

## Questions

**1.** Your company is in a city where Google Cloud doesn't have a Dedicated Interconnect location, but you need a private connection to your VPC. Which Cloud Interconnect option is most suitable?
- A) Network Connectivity Center
- B) Partner Interconnect
- C) Carrier Peering
- D) Dedicated Interconnect

**2.** Which Google Cloud Interconnect option requires the customer to provide their own routing equipment and establish a BGP session with Google's edge network?
- A) Network Connectivity Center
- B) Cross-Cloud Interconnect
- C) Partner Interconnect
- D) Dedicated Interconnect

## Answer Key
| # | Answer | Result | Why |
|---|--------|--------|-----|
| 1 | **B — Partner Interconnect** | ✅ | Connect through a supported provider when no Google colocation facility is reachable ([[GCP53 - Partner Interconnect]]) |
| 2 | **D — Dedicated Interconnect** | ❌ Partner was chosen | The docs list "**You must provide your own routing equipment**" as a Dedicated requirement: your router sits in Google's colo and peers BGP with Cloud Router over the VLAN attachments ([[GCP52 - Dedicated Interconnect]]) |

### Telling them apart
| Question says | Answer |
|---------------|--------|
| "own routing equipment", "colocation facility", "LOA-CFA", "10/100 Gbps circuits" | **Dedicated** |
| "no colocation facility nearby", "service provider", "pairing key", "50 Mbps" | **Partner** |
| "AWS/Azure/OCI/Alibaba", "multicloud", "Google provisions the physical link" | **Cross-Cloud** |
| "encrypted", "low bandwidth", "over the internet" | **Cloud VPN** |
| "manage many spokes/hybrid links as one hub" | **NCC** (not a connection type) |

Why Partner is the tempting wrong answer: with a **Layer 2** partner you *do* run BGP to Cloud Router. But the **physical connection and edge equipment belong to the provider**, and with a **Layer 3** partner the provider runs BGP for you. The "own equipment + Google's edge" wording points only to Dedicated.

### Traps
| Option | Why it's wrong |
|--------|---------------|
| 1A, 2A NCC | A management/transit hub, not a physical connection |
| 1C Carrier Peering | Reaches Google *public services* (Workspace, public APIs) via a provider, not private VPC IPs |
| 2B Cross-Cloud | Google provisions both ends into another cloud. You don't supply routers |

## Linked Pages
- [[GCP51 - Connectivity Options]] · [[GCP52 - Dedicated Interconnect]] (weak area flagged) · [[GCP53 - Partner Interconnect]] · [[GCP54 - Cross-Cloud Interconnect]] · [[certs/gcp-networking]]
