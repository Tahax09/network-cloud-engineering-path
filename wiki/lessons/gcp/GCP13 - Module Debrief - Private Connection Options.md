---
id: GCP13
aliases: [GCP13]
title: "Module Debrief: Private Connection Options"
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Private Connection Options
date: 2026-10-09
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, private-google-access, private-service-connect, private-services-access, cloud-nat, reference]
---

# GCP13 — Module Debrief: Private Connection Options

## Purpose
A quick reference for the second module of *Networking in Google Cloud: Routing and Addressing*. Full detail is in [[GCP07 - Private Connection Options - Overview]]–[[GCP11 - Cloud NAT]]. The module ended with the lab [[GCP12 - Lab - Implement Private Google Access and Cloud NAT]] and a quiz ([[GQ01_Private_Connection_Options]]).

**Lecture recap:** PGA → Google APIs/services. PSC → Google APIs **plus other configured resources** (third-party, your own). PSA → simplifies creating a **VPC Network Peering** connection between consumer and producer, but **only for some Google products/services**. Cloud NAT → **public internet** access for resources without public IPs.

---

## One-Glance Decision Table

| I need… | Use | Mechanism | Remember |
|---------|-----|-----------|----------|
| Internal-only VM → **Google APIs** | **Private Google Access** | Per-**subnet** toggle | No effect on VMs with external IPs |
| On-prem → **Google APIs** privately | **PGA for on-prem** | VPN/Interconnect + DNS to `private` (199.36.153.8/30) / `restricted` (199.36.153.4/30) + Cloud Router custom advertisement + VPC route → `default-internet-gateway` | `restricted` = VPC-SC APIs only; no partial overrides in Cloud DNS |
| Consume a **3rd-party / own / Google** service at an internal IP I choose; **overlap OK** | **Private Service Connect** endpoint/backend | Endpoint → **service attachment** | Consumer-initiated only; not reachable from peered VPCs; can't be in the same VPC as the service |
| Producer must reach **into** consumer network | **PSC interface** + network attachment | Producer-initiated | **Transitive** |
| **Cloud SQL private IP**, Memorystore, Filestore, Apigee, TPU | **Private Services Access** | **VPC peering** + allocated range | One range per producer, one connection per producer; non-transitive (export custom routes for on-prem) |
| Cloud Run/Functions → VPC | Serverless VPC Access / Direct VPC egress | Connector | Not covered in module |
| Internal-only VM → **internet** (outbound) | **Cloud NAT** (Public NAT) | Cloud Router config, distributed data plane | **No inbound**; manual IPs for allowlists; PGA forced on for NAT'd ranges |
| Overlapping VPCs on an NCC hub talk | **Private NAT** (inter-VPC NAT) | NAT between private ranges | |

---

## Exam-Style Traps

| Trap | Correct |
|------|---------|
| PGA scope | **Subnet**, not VPC/VM |
| PGA + external IP | PGA irrelevant; VM uses its external IP |
| PGA for apt/GitHub | No: Google APIs only → **Cloud NAT** |
| restricted vs private googleapis | restricted 199.36.153.**4**/30 = VPC-SC only; private 199.36.153.**8**/30 = most APIs |
| Overlapping IPs with a producer | **PSC** (PSA/peering can't overlap) |
| PSC route exchange | **None**. Route import/export is a peering (PSA) concept |
| PSC endpoint from peered VPC | Not reachable; create an endpoint in that VPC |
| Who initiates over a PSC endpoint? | **Consumer only** (interface = producer, transitive) |
| PSA ranges | One **per producer network**; one connection serves all that producer's services |
| PSA disable | Doesn't delete the peering or release the range |
| PSA from on-prem | Advertise the allocated range + **export custom routes** to the producer |
| Cloud NAT inbound | Never |
| Cloud NAT & Google APIs | Not NAT'd; PGA auto-enabled and **can't be disabled** on NAT'd ranges |
| Cloud NAT location | Configured on **Cloud Router**; no proxy in the data path |
| Fixed egress IPs for partners | **Manual** NAT IP allocation |

---

## AWS Rosetta Stone
| GCP | AWS |
|-----|-----|
| Private Google Access | Gateway endpoints (S3/DynamoDB) / interface endpoints |
| Private Service Connect | PrivateLink |
| Private Services Access | (managed services live in your subnets) |
| Cloud NAT | NAT Gateway |
| Private NAT / inter-VPC NAT | Private NAT Gateway |

---

## Linked Pages
- [[GCP07 - Private Connection Options - Overview]] Overview · [[GCP08 - Private Google Access]] PGA · [[GCP09 - Private Service Connect]] PSC · [[GCP10 - Private Services Access]] PSA · [[GCP11 - Cloud NAT]] Cloud NAT
- [[GCP06 - Module Debrief - Routing & Addressing]] — Previous module debrief (Routing & Addressing)
- [[concepts/gcp-private-access]]
- [[certs/gcp-networking]]
