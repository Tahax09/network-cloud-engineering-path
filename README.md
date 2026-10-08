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
│   └── transcripts/
│       ├── network-plus/            ← L01–L23 raw Coursera transcripts
│       └── gcp/                     ← GCP transcript files
└── wiki/
    ├── index.md                     ← full catalog
    ├── overview.md                  ← roadmap and progress
    ├── log.md                       ← activity log
    ├── lessons/
    │   ├── network-plus/            ← L01–L23 (CompTIA Network+ N10-009)
    │   └── gcp/                     ← GCP01–GCP06 (Google Cloud Networking)
    ├── concepts/                    ← technology reference pages
    ├── certs/                       ← per-certification progress tracking
    ├── quizzes/                     ← practice and graded quiz files
    └── labs/                        ← hands-on lab notes (k3s, GCP)
```

---

## Lessons Completed

### CompTIA Network+ N10-009

| ID | Topic | Domain |
|----|-------|--------|
| L01 | OSI Model | 1.0 |
| L02 | Networking Appliances & Applications | 1.0 |
| L03 | Network Virtualization & Cloud Technologies | 1.0 |
| L04 | Ports, Protocols & Services | 1.0 |
| L05 | Network Transmission: Media, Standards & Connectors | 1.0 |
| L06 | Network Topologies, Architectures & Traffic Flows | 1.0 |
| L07 | IP Addressing | 1.0 |
| L08 | Modern Networks (SDN, VXLAN, Zero Trust, IaC, IPv6) | 1.0 |
| L09 | Routing Technologies (Static, OSPF, EIGRP, BGP, NAT, FHRP) | 2.0 |
| L10 | Switching Technologies (VLANs, STP, RSTP, EtherChannel, LACP) | 2.2 |
| L11 | Wireless Devices and Technologies (802.11, WPA2/WPA3, Channels, MIMO) | 2.3 |
| L12 | Physical Installations (MDF/IDF, Racks, UPS, Environmental) | 2.4 |
| L13 | Organizational Processes and Procedures (Docs, Lifecycle, Change/Config Mgmt) | 3.1 |
| L14 | Network Monitoring (SNMP, Syslog, NetFlow, Packet Capture, SIEM, Baseline) | 3.2 |
| L15 | Disaster Recovery (RPO/RTO, MTBF/MTTR, Hot/Warm/Cold Site, Active-Active/Passive) | 3.3 |
| L16 | IP Services (DHCP, DORA, SLAAC, DNS Records, DNSSEC, DoH/DoT, NTP/PTP) | 3.4 |
| L17 | Network Access and Management (VPN, IPsec, TLS VPN, Split/Full Tunnel, Jump Box, OOB Mgmt) | 3.5 |
| L18 | Basic Network Security (Crypto, PKI, IAM/MFA, LDAP/SAML/RADIUS/TACACS+, CIA, Compliance, Segmentation) | 4.1 |
| L19 | Network Attacks (DDoS, VLAN Hopping, MAC Flooding, ARP/DNS Poisoning, Rogue DHCP/AP, Social Eng, Malware) | 4.2 |
| L20 | Network Defense Techniques (Hardening, NAC, Port Security, 802.1X, Key Mgmt, ACLs, URL/Content Filtering, Zones, Screened Subnet) | 4.3 |
| L21 | Network Troubleshooting Methodology (7 Steps, Top-Down/Bottom-Up/Divide & Conquer, Escalation) | 5.1 |
| L22 | Cabling & Physical Issues (Fiber/Copper Types, EMI/Attenuation, Duplex Mismatch, CRC/Runts/Giants, Err-disabled, PoE, SFPs) | 5.2 |
| L23 | Network Service Troubleshooting (STP Root/Roles, VLAN/Trunk, Switch ACLs, Default Route, DHCP Exhaustion, Gateway/Mask/Duplicate IP) | 5.3 |

### Google Cloud Networking Specialization — Routing & Addressing Module

| ID | Topic |
|----|-------|
| GCP01 | Routes and Route Preferences |
| GCP02 | IPv6 in Google Cloud |
| GCP03 | BYOIP — Bring Your Own IP |
| GCP04 | Cloud DNS |
| GCP06 | Module Debrief |

---

## Quizzes

| ID | Title | Covers |
|----|-------|--------|
| NQ01 | Networking Concepts (Coursera graded) | L01–L08 |
| NQ02 | Networking Concepts (Practice) | L01–L08 |
| NQ03 | Network Implementation (Coursera graded) | L09–L12 |
| NQ04 | Network Operations (Coursera graded) | L13–L17 |
| NQ05 | Network Security (Coursera graded) | L18–L20 |

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

## How to Use

Open the folder as an **Obsidian vault**. All pages use `[[wiki-style links]]` for navigation.

To add a new lesson: drop the transcript in `raw/transcripts/network-plus/` or `raw/transcripts/gcp/`, then run the ingest workflow defined in [CLAUDE.md](CLAUDE.md).
