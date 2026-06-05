# Network & Cloud Engineering Path

A structured, LLM-assisted knowledge base for self-study in **Networking, Cloud Computing, and Infrastructure Engineering** — built as an Obsidian vault.

Covers CompTIA Network+, Google Cloud Networking, AWS, and Kubernetes/DevOps. Each lesson is processed from course transcripts into a consistent wiki format: summary, core concepts, key terms, real-world examples, troubleshooting scenarios, hands-on labs, and quiz questions.

---

## What's Inside

```
Networking + Cloud/
├── CLAUDE.md                        ← wiki schema and ingest workflow
├── raw/
│   ├── course_tracker.md            ← lesson tracking table
│   └── transcripts/                 ← raw course transcript files
└── wiki/
    ├── index.md                     ← full catalog
    ├── overview.md                  ← roadmap and progress
    ├── log.md                       ← activity log
    ├── lessons/
    │   ├── network-plus/            ← L01–L08 (CompTIA Network+ N10-009)
    │   └── gcp/                     ← GCP01–GCP06 (Google Cloud Networking)
    ├── concepts/                    ← technology reference pages
    ├── certs/                       ← per-certification progress tracking
    └── labs/                        ← hands-on lab notes (k3s, AWS, GCP)
```

---

## Lessons Completed

### CompTIA Network+ N10-009 — Domain 1 (Networking Concepts)

| ID | Topic | Objective |
|----|-------|-----------|
| L01 | OSI Model | 1.1 |
| L02 | Networking Appliances & Applications | 1.2 |
| L03 | Network Virtualization & Cloud Technologies | 1.3 |
| L04 | Ports, Protocols & Services | 1.4 |
| L05 | Network Transmission: Media, Standards & Connectors | 1.5 |
| L06 | Network Topologies, Architectures & Traffic Flows | 1.6 |
| L07 | IP Addressing | 1.7 |
| L08 | Modern Networks (SDN, VXLAN, Zero Trust, IaC, IPv6) | 1.8 |

### Google Cloud Networking Specialization — Routing & Addressing Module

| ID | Topic |
|----|-------|
| GCP01 | Routes and Route Preferences |
| GCP02 | IPv6 in Google Cloud |
| GCP03 | BYOIP — Bring Your Own IP |
| GCP04 | Cloud DNS |
| GCP05 | Lab: Traffic Steering Using Geolocation |
| GCP06 | Module Debrief |

---

## Concept Reference Pages

- OSI Model — layers, encapsulation, TCP vs UDP
- Network Devices — router, switch, firewall, IDS/IPS, load balancer, proxy, CDN
- Cloud Fundamentals — IaaS/PaaS/SaaS, deployment models, SDN/NFV
- VPC & Subnets — design, security groups, NAT, connectivity options
- Ports & Protocols — port table, IPsec, ICMP, traffic types
- Network Media — Wi-Fi standards, Ethernet, fiber, connectors, plenum
- Network Topologies — three-tier, spine-leaf, north-south/east-west
- IP Addressing — classes, RFC 1918, CIDR, VLSM, NAT
- Modern Networks — SDN, VXLAN, Zero Trust, SASE, IaC tools, IPv6
- GCP Routing — route types, Cloud Router, BGP, multi-NIC, BYOIP
- GCP DNS — zones, server/response/routing policies

---

## Certification Tracks

| Cert | Status |
|------|--------|
| CompTIA Network+ N10-009 | In Progress |
| Google Cloud Networking Specialization | In Progress |
| AWS Cloud Practitioner | Planned |
| AWS Solutions Architect – Associate | Planned |

---

## Lab Environment

All networking concepts are mapped to a live **k3s observability lab** running on ARM64 Ubuntu 22.04:

- **Kubeshark** — packet capture (promiscuous mode / IDS equivalent)
- **Elasticsearch** — log/metrics storage
- **Kibana** — visualization

Every OSI layer is active and inspectable in this cluster — from VXLAN overlays (L2) to HTTP traces (L7).

---

## How to Use

Open the folder as an **Obsidian vault**. All pages use `[[wiki-style links]]` for navigation.

To add a new lesson: drop the transcript in `raw/transcripts/`, then run the ingest workflow defined in [CLAUDE.md](CLAUDE.md).
