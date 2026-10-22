---
id: GCP55
aliases: [GCP55]
title: Module Introduction - Cloud VPN
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: "Course 6: Hybrid and Multicloud — Module 2: Cloud VPN"
date: 2026-10-22
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, module-intro, cloud-vpn, ipsec, ha-vpn, ncc]
---

# GCP55 — Module Introduction: Cloud VPN

> 🎛️ **Interactive visualization:** [HA VPN and BGP Paths](../../visualizations/gcp/GCP55-60%20-%20HA%20VPN%20and%20BGP%20Paths.html). Opens in your browser.

## Summary
Module 2 of Course 6: **Cloud VPN**, the service for **secure, encrypted** connections between on-prem (or other clouds) and Google Cloud. It covers what Cloud VPN is, how it works, its use cases, HA VPN topologies, BGP path selection, a lab, and **Network Connectivity Center**.

## Core Concepts
| Step | Lesson |
|------|--------|
| 1 | [[GCP56 - Cloud VPN Overview]] |
| 2 | [[GCP57 - HA VPN Topologies]] |
| 3 | [[GCP58 - Lab - Configuring Google Cloud HA VPN]] |
| 4 | [[GCP59 - Influence Best Path Selection]] |
| 5 | [[GCP60 - Network Connectivity Center]] |
| 6 | Quiz: [[GQ10_Cloud_VPN]] (answer key pending your submission) |

> **Order note:** the transcripts don't state lesson order. This follows the HA VPN flow (overview → topologies → lab → path selection → NCC). Tell me if the course orders them differently and I'll renumber.

## Key Terms
| Term | Definition |
|------|-----------|
| IPsec | Suite that encrypts and authenticates IP packets between VPN gateways |
| HA VPN | Regional gateway with two interfaces; 99.99% SLA with two tunnels and BGP |
| Classic VPN | Single-interface legacy gateway; static routing use cases |

## Real-World Connections
- **Network+:** site-to-site VPN, IPsec IKEv2, tunnel vs transport mode ([[L18 - Basic Network Security]]).

## Troubleshooting Scenario
**Preview:** a tunnel shows ESTABLISHED, but no routes are learned. Look at BGP (link-local 169.254.x.x addresses, ASNs), not IPsec ([[GCP58 - Lab - Configuring Google Cloud HA VPN]]).

## Hands-On Ideas
1. `gcloud compute vpn-gateways list; gcloud compute vpn-tunnels list` in any project to see what exists.

## Quiz
1. Cloud VPN traffic over the internet is:
   - A) Plaintext  B) Encrypted only with MACsec  C) Compressed only  D) Encrypted with IPsec

**Answers:** 1-D

## Weak Areas to Revisit
*(none flagged yet)*

## Linked Pages
- [[GCP56 - Cloud VPN Overview]] · [[GCP51 - Connectivity Options]] · [[certs/gcp-networking]]
