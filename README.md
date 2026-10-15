# Network & Cloud Engineering Path

An Obsidian knowledge base for self-study in **networking, cloud, and infrastructure engineering**, maintained with an LLM study coach. Course transcripts are turned into consistent lesson pages, each tied back to a hands-on k3s lab, AWS VPC, and enterprise practice.

![Lessons](https://img.shields.io/badge/lessons-58-blue) ![Network+](https://img.shields.io/badge/Network%2B%20N10--009-course%20complete-success) ![GCP](https://img.shields.io/badge/GCP%20Networking-course%204%20of%206-yellow) ![Quizzes](https://img.shields.io/badge/quizzes-12-informational) ![License](https://img.shields.io/badge/license-CC%20BY--NC--SA%204.0-lightgrey)

---

## Contents

- [Repository Layout](#repository-layout)
- [How a Lesson Page Works](#how-a-lesson-page-works)
- [CompTIA Network+ N10-009](#comptia-network-n10-009)
- [Google Cloud Networking](#google-cloud-networking)
- [Wireshark 101](#wireshark-101-packet-analysis-essentials)
- [Scenario Walkthroughs & Practice Labs](#scenario-walkthroughs--practice-labs)
- [Quizzes & Practice Exams](#quizzes--practice-exams)
- [Concept Reference](#concept-reference)
- [Labs](#labs)
- [Certification Roadmap](#certification-roadmap)
- [Using the Vault](#using-the-vault)
- [License](#license)

---

## Repository Layout

```
.
├── CLAUDE.md                     # Wiki schema, page formats, ingest workflow
├── raw/                          # Source material, never edited
│   ├── course_tracker.md         # Lesson tracking table
│   ├── assets/wireshark-101/     # Course PCAPs + Wireshark profiles
│   └── transcripts/
│       ├── network-plus/         # Lxx_Topic.txt
│       ├── gcp/                  # GCPxx_Topic.txt
│       └── wireshark/            # WSxx_Topic.txt
└── wiki/
    ├── index.md                  # Full catalog of every page
    ├── overview.md               # Goals, roadmap, current status
    ├── log.md                    # Append-only activity log
    ├── lessons/
    │   ├── network-plus/         # "Lxx - Title.md"
    │   ├── gcp/                  # "GCPxx - Title.md"
    │   └── wireshark/            # "WSxx - Title.md"
    ├── concepts/                 # Cross-lesson reference pages
    ├── certs/                    # Per-certification progress, exam traps, weak areas
    ├── quizzes/                  # One folder per course
    │   ├── network-plus/         # NQ graded · PE practice exams · WD drills
    │   ├── gcp/                  # GQ graded
    │   ├── wireshark/            # WQ quizzes
    │   └── interview/            # IQ interview prep
    ├── scenarios/gcp/            # GSxx scenario walkthroughs + topology guide
    └── labs/                     # Hands-on lab notes; labs/practice/ = GPLxx practice labs
```

## How a Lesson Page Works

Every lesson follows the same structure, defined in [CLAUDE.md](CLAUDE.md):

| Section | Purpose |
|---------|---------|
| Summary | What the objective covers and what the exam tests |
| Core Concepts | Mechanisms, tables, diagrams, transcript corrections |
| Key Terms | Glossary for quick review |
| Real-World Connections | k3s lab, AWS/GCP equivalents, enterprise and telecom practice |
| Troubleshooting Scenario | Worked incident using the 7-step methodology |
| Hands-On Ideas | Commands and lab exercises |
| Quiz | Scenario questions with answer key |
| Weak Areas to Revisit | Filled in from graded quiz results |

When a lecture states something inaccurate (or the auto-captions garble a term), the page flags it inline as a **Transcript correction** or **Caption note**.

---

## CompTIA Network+ N10-009

All 25 lessons across the five exam domains are complete. Domain weights: 1.0 Concepts 23% · 2.0 Implementation 20% · 3.0 Operations 19% · 4.0 Security 14% · 5.0 Troubleshooting 24%.

| ID | Lesson | Obj | Key topics |
|----|--------|-----|-----------|
| [L01](wiki/lessons/network-plus/L01%20-%20The%20OSI%20Model.md) | The OSI Model | 1.1 | Layers, encapsulation, PDUs |
| [L02](wiki/lessons/network-plus/L02%20-%20Networking%20Appliances%20%26%20Applications.md) | Networking Appliances & Applications | 1.2 | Routers, switches, firewalls, IDS/IPS, LB, proxy, CDN |
| [L03](wiki/lessons/network-plus/L03%20-%20Network%20Virtualization%20%26%20Cloud%20Technologies.md) | Network Virtualization & Cloud Technologies | 1.3 | NFV, VPC, cloud service & deployment models |
| [L04](wiki/lessons/network-plus/L04%20-%20Ports%2C%20Protocols%20%26%20Services.md) | Ports, Protocols & Services | 1.4 | Port table, TCP/UDP, IP protocol types |
| [L05](wiki/lessons/network-plus/L05%20-%20Network%20Transmission%20-%20Media%2C%20Standards%20%26%20Connectors.md) | Network Transmission - Media, Standards & Connectors | 1.5 | Copper/fiber, 802.11, connectors, transceivers |
| [L06](wiki/lessons/network-plus/L06%20-%20Network%20Topologies%2C%20Architectures%20%26%20Traffic%20Flows.md) | Network Topologies, Architectures & Traffic Flows | 1.6 | Three-tier, spine-leaf, N-S / E-W traffic |
| [L07](wiki/lessons/network-plus/L07%20-%20IP%20Addressing.md) | IP Addressing | 1.7 | IPv4/IPv6, RFC 1918, CIDR, VLSM, subnetting |
| [L08](wiki/lessons/network-plus/L08%20-%20Modern%20Networks.md) | Modern Networks | 1.8 | SDN, SD-WAN, VXLAN, Zero Trust, SASE, IaC |
| [L09](wiki/lessons/network-plus/L09%20-%20Routing%20Technologies.md) | Routing Technologies | 2.1 | Static, OSPF, EIGRP, BGP, NAT/PAT, FHRP |
| [L10](wiki/lessons/network-plus/L10%20-%20Switching%20Technologies.md) | Switching Technologies | 2.2 | VLANs, 802.1Q, STP/RSTP, EtherChannel, LACP |
| [L11](wiki/lessons/network-plus/L11%20-%20Wireless%20Devices%20and%20Technologies.md) | Wireless Devices and Technologies | 2.3 | 802.11 standards, WPA2/3, channels, MIMO |
| [L12](wiki/lessons/network-plus/L12%20-%20Physical%20Installations.md) | Physical Installations | 2.4 | MDF/IDF, racks, UPS/PDU, environmental |
| [L13](wiki/lessons/network-plus/L13%20-%20Organizational%20Processes%20and%20Procedures.md) | Organizational Processes and Procedures | 3.1 | Documentation, lifecycle, change & config mgmt |
| [L14](wiki/lessons/network-plus/L14%20-%20Network%20Monitoring.md) | Network Monitoring | 3.2 | SNMP, syslog, NetFlow, packet capture, SIEM |
| [L15](wiki/lessons/network-plus/L15%20-%20Disaster%20Recovery.md) | Disaster Recovery | 3.3 | RPO/RTO, MTBF/MTTR, hot/warm/cold sites |
| [L16](wiki/lessons/network-plus/L16%20-%20IP%20Services.md) | IP Services | 3.4 | DHCP/DORA, SLAAC, DNS records, DNSSEC, NTP/PTP |
| [L17](wiki/lessons/network-plus/L17%20-%20Network%20Access%20and%20Management.md) | Network Access and Management | 3.5 | IPsec, TLS VPN, split/full tunnel, jump box, OOB |
| [L18](wiki/lessons/network-plus/L18%20-%20Basic%20Network%20Security.md) | Basic Network Security | 4.1 | Crypto, PKI, MFA, LDAP/SAML/RADIUS/TACACS+, compliance |
| [L19](wiki/lessons/network-plus/L19%20-%20Network%20Attacks.md) | Network Attacks | 4.2 | DDoS, VLAN hopping, MAC flooding, ARP/DNS poisoning, malware |
| [L20](wiki/lessons/network-plus/L20%20-%20Network%20Defense%20Techniques.md) | Network Defense Techniques | 4.3 | Hardening, 802.1X, port security, ACLs, filtering, zones |
| [L21](wiki/lessons/network-plus/L21%20-%20Network%20Troubleshooting%20Methodology.md) | Network Troubleshooting Methodology | 5.1 | 7-step methodology, OSI approaches, escalation |
| [L22](wiki/lessons/network-plus/L22%20-%20Troubleshooting%20Cabling%20and%20Physical%20Issues.md) | Troubleshooting Cabling and Physical Issues | 5.2 | Fiber/copper faults, duplex mismatch, CRC/runts/giants, PoE |
| [L23](wiki/lessons/network-plus/L23%20-%20Network%20Service%20Troubleshooting.md) | Network Service Troubleshooting | 5.3 | STP, VLAN/trunk, default route, DHCP exhaustion, subnet mask |
| [L24](wiki/lessons/network-plus/L24%20-%20Troubleshooting%20Network%20Performance%20Issues.md) | Troubleshooting Network Performance Issues | 5.4 | Bandwidth vs throughput, latency/jitter/loss, Wi-Fi issues |
| [L25](wiki/lessons/network-plus/L25%20-%20Troubleshooting%20Tools.md) | Troubleshooting Tools | 5.5 | Wireshark, ping/traceroute, dig, tcpdump, Nmap, show commands |

Progress, exam traps, and weak areas: [wiki/certs/network-plus.md](wiki/certs/network-plus.md)

---

## Google Cloud Networking

*Networking in Google Cloud* is a six-course program: Fundamentals ✅ · Routing and Addressing ✅ · Network Architecture ✅ · **Network Security** (in progress) · Load Balancing · Hybrid and Multicloud.

### Course 2: Routing and Addressing

| ID | Lesson | Key topics |
|----|--------|-----------|
| [GCP01](wiki/lessons/gcp/GCP01%20-%20Routes%20and%20Route%20Preferences.md) | Routes and Route Preferences | System/custom/dynamic routes, priority, Cloud Router |
| [GCP02](wiki/lessons/gcp/GCP02%20-%20IPv6%20in%20Google%20Cloud.md) | IPv6 in Google Cloud | Dual-stack subnets, /96 per VM, IPv6 routing |
| [GCP03](wiki/lessons/gcp/GCP03%20-%20BYOIP%20-%20Bring%20Your%20Own%20IP.md) | BYOIP - Bring Your Own IP | Bring your own public IP ranges into Google Cloud |
| [GCP04](wiki/lessons/gcp/GCP04%20-%20Cloud%20DNS.md) | Cloud DNS | Public/private zones, server & routing policies |
| [GCP06](wiki/lessons/gcp/GCP06%20-%20Module%20Debrief%20-%20Routing%20%26%20Addressing.md) | Module Debrief - Routing & Addressing | Module quick reference |
| [GCP07](wiki/lessons/gcp/GCP07%20-%20Private%20Connection%20Options%20-%20Overview.md) | Private Connection Options - Overview | Option map: PGA, PSC, PSA, Serverless VPC Access, Cloud NAT |
| [GCP08](wiki/lessons/gcp/GCP08%20-%20Private%20Google%20Access.md) | Private Google Access | Per-subnet access to Google APIs, on-prem via VPN + DNS |
| [GCP09](wiki/lessons/gcp/GCP09%20-%20Private%20Service%20Connect.md) | Private Service Connect | Endpoints, service attachments, PSC interface |
| [GCP10](wiki/lessons/gcp/GCP10%20-%20Private%20Services%20Access.md) | Private Services Access | Peering-based access to managed services (Cloud SQL) |
| [GCP11](wiki/lessons/gcp/GCP11%20-%20Cloud%20NAT.md) | Cloud NAT | Distributed outbound NAT, manual/auto IPs, private NAT |
| [GCP12](wiki/labs/GCP12%20-%20Lab%20-%20Implement%20Private%20Google%20Access%20and%20Cloud%20NAT.md) | Lab: Implement Private Google Access and Cloud NAT | Hands-on: no-external-IP VM, IAP SSH, PGA, Cloud NAT, NAT logging |
| [GCP13](wiki/lessons/gcp/GCP13%20-%20Module%20Debrief%20-%20Private%20Connection%20Options.md) | Module Debrief - Private Connection Options | Module quick reference + decision table |

### Course 3: Network Architecture

| ID | Lesson | Key topics |
|----|--------|-----------|
| [GCP14](wiki/lessons/gcp/GCP14%20-%20Network%20Architecture%20-%20Course%20Introduction.md) | Network Architecture - Course Introduction | Modules: Intro to Network Architecture (subnets, routes, firewalls, LB) · Network Topologies |
| [GCP15](wiki/lessons/gcp/GCP15%20-%20Cloud%20Network%20Architecture%20Overview.md) | Cloud Network Architecture Overview | Design process & metrics, GCP networking toolkit, Premium vs Standard tier, multi-region 3-tier redesign |
| [GCP16](wiki/lessons/gcp/GCP16%20-%20Getting%20Started%20with%20Network%20Architecture.md) | Getting Started with Network Architecture | Considerations, stakeholders → requirements → HLD → LLD → BoM, CapEx/OpEx |
| [GCP17](wiki/lessons/gcp/GCP17%20-%20Module%20Introduction%20-%20Network%20Topologies.md) | Module Introduction - Network Topologies | Hub-and-spoke, mesh, mirrored, gated; NCC lab |
| [GCP18](wiki/lessons/gcp/GCP18%20-%20Hub-and-Spoke%20Topology.md) | Hub-and-Spoke Topology | Peering vs VPN vs NCC, transitivity, VPC/hybrid spokes, router appliance |
| [GCP19](wiki/labs/GCP19%20-%20Lab%20-%20Implement%20a%20Hub-and-Spoke%20Network%20Using%20Network%20Connectivity%20Center.md) | Lab: Implement a Hub-and-Spoke Network Using NCC | Two isolated VPCs → NCC mesh hub → ping works; Network Topology |
| [GCP20](wiki/lessons/gcp/GCP20%20-%20Other%20Topologies%20-%20Mesh%2C%20Mirrored%2C%20Gated.md) | Other Topologies - Mesh, Mirrored, Gated | Full vs partial mesh, mirrored DR/dev-test, gated egress/ingress |
| [GCP21](wiki/lessons/gcp/GCP21%20-%20Getting%20Topology%20Data.md) | Getting Topology Data | Network Topology: entities/hierarchies, 6-week hourly snapshots, metrics, Charlie LB latency case |
| [GCP22](wiki/lessons/gcp/GCP22%20-%20Best%20Practices%20for%20Connectivity%20and%20Topologies.md) | Best Practices for Connectivity and Topologies | SLAs, centralized hybrid hub VPC + custom routes, APIs via Apigee/LB, capacity optimization, two authoritative DNS |

### Course 4: Network Security

| ID | Lesson | Key topics |
|----|--------|-----------|
| [GCP23](wiki/lessons/gcp/GCP23%20-%20Network%20Security%20-%20Course%20Introduction.md) | Network Security - Course Introduction | Modules: DDoS protection · Controlling access to VPC networks · Advanced security monitoring and analysis |
| [GCP24](wiki/lessons/gcp/GCP24%20-%20Module%20Introduction%20-%20DDoS%20Protection.md) | Module Introduction - DDoS Protection | Module map: attacks → Google mitigation → partner products → lab |
| [GCP25](wiki/lessons/gcp/GCP25%20-%20How%20DDoS%20Attacks%20Work.md) | How DDoS Attacks Work | Botnets, volumetric vs protocol vs L7, bisection bandwidth, 46M/398M rps records |

The geolocation traffic-steering lab lives in [wiki/labs/gcp-geolocation-lab.md](wiki/labs/gcp-geolocation-lab.md). Progress: [wiki/certs/gcp-networking.md](wiki/certs/gcp-networking.md)

---

## Wireshark 101: Packet Analysis Essentials

Lab-heavy protocol analysis course, running in parallel with Google Cloud. Course PCAPs and instructor profiles live in `raw/assets/wireshark-101/`.

| ID | Lesson | Lab file | Key topics |
|----|--------|----------|-----------|
| [WS00](wiki/lessons/wireshark/WS00%20-%20Introduction.md) | Introduction | — | Why packet analysis, course files, instructor profiles |
| [WS01](wiki/lessons/wireshark/WS01%20-%20Pre-Lesson%20-%20Your%20First%20Lab.md) | Pre-Lesson - Your First Lab | Pre-Lab-SlowNetwork.pcapng | Client vs network vs server: 105 ms RTT, 20.6 s server think time |
| [WS02](wiki/lessons/wireshark/WS02%20-%20Your%20First%20Packet%20Capture.md) | Your First Packet Capture | — (live capture) | Interfaces, save as pcapng, Capture Options, ring buffers |
| [WS03](wiki/lessons/wireshark/WS03%20-%20Where%20to%20Capture%20on%20the%20Network.md) | Where to Capture on the Network | — (concepts) | Endpoint vs SPAN vs TAP vs cloud mirroring, SPAN oversubscription |
| [WS04](wiki/lessons/wireshark/WS04%20-%20Packet%20Capture%20-%20Best%20Practices.md) | Packet Capture - Best Practices | — (concepts) | No capture filter first, client first, ring buffer, capture the problem, TAP, multi-point |
| [WS05](wiki/lessons/wireshark/WS05%20-%20The%20Default%20Interface.md) | The Default Interface | Pre-Lab-SlowNetwork.pcapng | Packet List / Details / Bytes panes, profile in status bar, coloring rules |
| [WS06](wiki/lessons/wireshark/WS06%20-%20Configuring%20Columns.md) | Configuring Columns | Pre-Lab-SlowNetwork.pcapng | Delta time column, UTC vs local time, TTL column, 3 ways to add columns |
| [WS07](wiki/lessons/wireshark/WS07%20-%20Coloring%20Traffic.md) | Coloring Traffic | Pre-Lab-SlowNetwork.pcapng | TCP SYN coloring rule, drag-to-filter, rule priority, instructor's Cybersec rules |
| [WS08](wiki/lessons/wireshark/WS08%20-%20Creating%20Profiles.md) | Creating Profiles | Pre-Lab-SlowNetwork.pcapng | Profile = settings bundle; Copy-from coloring rules; Frame metadata fields |
| [WS09](wiki/lessons/wireshark/WS09%20-%20Saving%20Filter%20Buttons.md) | Saving Filter Buttons | Pre-Lab-SlowNetwork.pcapng | Bad TCP button, grouped buttons, field references, signature buttons, 4.x syntax fixes |
| [WS10](wiki/lessons/wireshark/WS10%20-%20The%20Statistics%20View.md) | The Statistics View | Pre-Lab-SlowNetwork.pcapng | Conversations & Endpoints per layer, asymmetric next-hop MACs, tshark -z |

Progress: [wiki/certs/wireshark-101.md](wiki/certs/wireshark-101.md)

---

## Scenario Walkthroughs & Practice Labs

Step-by-step explanations of every course scenario (context → architecture diagram → diagnosis workflow → commands → why → verify), plus original practice labs with hidden solutions. Diagrams are Mermaid (render in Obsidian and GitHub).

| ID | Page | Covers |
|----|------|--------|
| [GS01](wiki/scenarios/gcp/GS01%20-%20Private%20Connectivity%20Scenarios.md) | Private connectivity scenarios — 8 walkthroughs with diagrams | GCP07–GCP13 |
| [GS02](wiki/scenarios/gcp/GS02%20-%20Network%20Topologies%20Explained.md) | Topologies explained — how, when, pros/cons, implementation | GCP17–GCP20 |
| [GS03](wiki/scenarios/gcp/GS03%20-%20Architecture%20and%20Topology%20Scenarios.md) | Architecture & topology scenarios — 8 walkthroughs with diagrams | GCP14–GCP20 |
| [GPL01](wiki/labs/practice/GPL01%20-%20Private%20Connectivity%20Practice%20Labs.md) | Practice labs: hardened VPC, PSC overlap, PSA hybrid, NAT load, PSC for APIs | GCP07–GCP13 |
| [GPL02](wiki/labs/practice/GPL02%20-%20Architecture%20and%20Topology%20Practice%20Labs.md) | Practice labs: 3-tier SA firewall, NCC star, VPN hub, failover, gated egress, mirrored IaC | GCP14–GCP20 |

---

## Quizzes & Practice Exams

### CompTIA Network+

| ID | Quiz | Covers |
|----|------|--------|
| [NQ01](wiki/quizzes/network-plus/NQ01_Networking_Concepts.md) | Networking Concepts (Coursera graded) | L01–L08 |
| [NQ02](wiki/quizzes/network-plus/NQ02_Networking_Concepts_Practice.md) | Networking Concepts (Practice) | L01–L08 |
| [NQ03](wiki/quizzes/network-plus/NQ03_Network_Implementation.md) | Network Implementation (Coursera graded) | L09 + L10–L12 |
| [NQ04](wiki/quizzes/network-plus/NQ04_Network_Operations.md) | Network Operations (Coursera graded) | L13–L17 |
| [NQ05](wiki/quizzes/network-plus/NQ05_Network_Security.md) | Network Security (Coursera graded) | L18–L20 |
| [NQ06](wiki/quizzes/network-plus/NQ06_Network_Troubleshooting.md) | Network Troubleshooting (Coursera graded) | L21–L25 |
| [PE01](wiki/quizzes/network-plus/PE01_Practice_Exam_1.md) | Practice Exam 1 — 90 Q, domain-weighted, original | L01–L25 |
| [WD01](wiki/quizzes/network-plus/WD01_Methodology_Drill.md) | Weak-Area Drill — Troubleshooting Methodology | L21–L22 |

### Google Cloud Networking

| ID | Quiz | Covers |
|----|------|--------|
| [GQ01](wiki/quizzes/gcp/GQ01_Private_Connection_Options.md) | GCP Private Connection Options (Coursera graded) | GCP07–GCP13 |
| [GQ03](wiki/quizzes/gcp/GQ03_Network_Topologies.md) | Network Topologies Quiz (Coursera graded) | GCP17–GCP22 |

### Wireshark 101

| ID | Quiz | Covers |
|----|------|--------|
| [WQ01](wiki/quizzes/wireshark/WQ01_Introduction_to_Wireshark.md) | Introduction to Wireshark Quiz (Coursera) | WS00–WS04 |

### Interview Prep

| ID | Quiz | Covers |
|----|------|--------|
| [IQ01](wiki/quizzes/interview/IQ01_CCNA_Interview_Questions.md) | CCNA-level interview questions — self-test (Q1–Q80) | L01–L25 |

Graded quizzes are saved with an answer key and reasoning. Missed questions are recorded as weak areas on the cert page and the related lesson.

---

## Concept Reference

| Page | Covers |
|------|--------|
| [osi-model](wiki/concepts/osi-model.md) | Layers, encapsulation, TCP vs UDP |
| [network-devices](wiki/concepts/network-devices.md) | Router, switch, firewall, IDS/IPS, LB, proxy, CDN |
| [cloud-fundamentals](wiki/concepts/cloud-fundamentals.md) | IaaS/PaaS/SaaS, deployment models, SDN/NFV |
| [vpc-and-subnets](wiki/concepts/vpc-and-subnets.md) | VPC design, security groups, NAT, connectivity |
| [ports-and-protocols](wiki/concepts/ports-and-protocols.md) | Port table, IPsec, ICMP, RADIUS/TACACS+ |
| [network-media](wiki/concepts/network-media.md) | Wi-Fi standards, Ethernet, fiber, connectors |
| [network-topologies](wiki/concepts/network-topologies.md) | Three-tier, spine-leaf, N-S / E-W |
| [ip-addressing](wiki/concepts/ip-addressing.md) | Classes, RFC 1918, CIDR, VLSM, NAT |
| [modern-networks](wiki/concepts/modern-networks.md) | SDN, VXLAN, Zero Trust, SASE, IaC, IPv6 |
| [gcp-routing](wiki/concepts/gcp-routing.md) | Route types, Cloud Router, BGP, multi-NIC, BYOIP |
| [gcp-dns](wiki/concepts/gcp-dns.md) | Cloud DNS zones and policies |
| [gcp-network-architecture](wiki/concepts/gcp-network-architecture.md) | Design process, service tiers, topologies, Network Connectivity Center |
| [gcp-private-access](wiki/concepts/gcp-private-access.md) | PGA, PSC, PSA, Cloud NAT + AWS mapping |
| [wireshark](wiki/concepts/wireshark.md) | Capture workflow, time formats, RTT vs server time, filters, tshark |

---

## Labs

| Lab | Description |
|-----|-------------|
| [k3s-lab](wiki/labs/k3s-lab.md) | ARM64 Ubuntu k3s cluster with Kubeshark, Elasticsearch, and Kibana, used as the hands-on reference for every lesson |
| [gcp-geolocation-lab](wiki/labs/gcp-geolocation-lab.md) | Cloud DNS geolocation routing policy for traffic steering |
| [GCP12 lab](wiki/labs/GCP12%20-%20Lab%20-%20Implement%20Private%20Google%20Access%20and%20Cloud%20NAT.md) | Private Google Access + Cloud NAT for a VM with no external IP, reached via IAP |
| [GCP19 lab](wiki/labs/GCP19%20-%20Lab%20-%20Implement%20a%20Hub-and-Spoke%20Network%20Using%20Network%20Connectivity%20Center.md) | Hub-and-spoke with Network Connectivity Center: two isolated VPCs connected as VPC spokes |

---

## Certification Roadmap

```
Now      CompTIA Network+ N10-009 ── course complete, exam prep (PE01)
Parallel Wireshark 101 (in progress)  +  Networking in Google Cloud (courses 2–6)
Next     KCNA ─▶ CKA ─▶ AZ-500 ─▶ KCSA ─▶ CKS      (PCA anywhere after CKA)
Planned  AWS Cloud Practitioner ─▶ AWS Solutions Architect – Associate
```

| Certification | Status |
|---------------|--------|
| CompTIA Network+ N10-009 | Course complete, exam prep |
| Google Cloud Networking (6-course program) | Course 4 in progress |
| Wireshark 101: Packet Analysis Essentials (course) | In progress |
| Kubernetes and Cloud Native Associate (KCNA) | Next |
| Certified Kubernetes Administrator (CKA) | Planned |
| Microsoft Azure Security Engineer (AZ-500) | Planned |
| Kubernetes and Cloud Native Security Associate (KCSA) | Planned |
| Certified Kubernetes Security Specialist (CKS) | Planned |
| Prometheus Certified Associate (PCA) | Planned |
| AWS Cloud Practitioner / Solutions Architect – Associate | Planned |

---

## Using the Vault

1. Open the repository folder as an **Obsidian vault**. Pages link to each other with `[[wiki links]]` using full file names, e.g. `[[L09 - Routing Technologies]]`.
2. Start from [wiki/index.md](wiki/index.md) (catalog) or [wiki/overview.md](wiki/overview.md) (status and roadmap).
3. **Add a lesson:** drop the transcript in `raw/transcripts/<track>/` and ask the coach to ingest it. The workflow in [CLAUDE.md](CLAUDE.md) creates the lesson page and updates the index, concept pages, cert page, log, and this README.
4. **Practice:** take a quiz from `wiki/quizzes/<course>/`, paste your answers for grading, and weak areas are logged automatically.

---

## License

The original notes, lesson pages, concept pages, quizzes, and practice exams in `wiki/` are licensed under [Creative Commons Attribution-NonCommercial-ShareAlike 4.0](LICENSE) (CC BY-NC-SA 4.0).

**Excluded:** third-party material in `raw/` (course transcripts and articles) remains the property of its original authors (Coursera course providers, CompTIA, Google Cloud, and others). It is kept only as private study reference and is **not** covered by this license. CompTIA, Network+, Google Cloud, AWS, Azure, Kubernetes, and other names are trademarks of their respective owners. This project is not affiliated with or endorsed by them.
