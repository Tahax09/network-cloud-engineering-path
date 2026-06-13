---
id: NQ01
title: "Quiz: Networking Concepts"
course: CompTIA Network+
cert_track: network-plus
covers: L01–L08
date: 2026-06-14
status: Reviewed
score: --
tags: [quiz, network-plus, osi, appliances, cloud, ports, media, topologies, ip, modern-networks]
---

# NQ01 — Quiz: Networking Concepts

Module 1 graded quiz covering all content from L01–L08.

## Answer Key

| Q | Answer | Key Reasoning |
|---|--------|--------------|
| 1 | **Datagram** | UDP (connectionless) PDU at transport layer = datagram; TCP (connection-oriented) = segment |
| 2 | **It relies upon other layers for packet delivery** | L7 uses all layers below for transmission — doesn't do address translation, encryption, or independent error-checking |
| 3 | **Switch** | Operates at L2 + L3; hub and transceiver are L1-only |
| 4 | **Datagram** | Same as Q1 — connectionless transport = datagram |
| 5 | **Switch** | Switches support jumbo frame MTU configuration (up to 9000 bytes) |
| 6 | **Access point** | APs extend WLAN coverage for wireless clients |
| 7 | **IPS** | Inline + blocks = IPS; IDS is passive/out-of-band (detects and alerts only) |
| 8 | **Layer 3 switch** | Access ports (L2) + routing capability (L3) — both described in the question |
| 9 | **QoS** | Quality of Service classifies and prioritizes traffic by type |
| 10 | **Quality of service** | QoS gives priority to latency-sensitive real-time traffic (voice/video) |
| 11 | **CDN** | Content Delivery Network = geographically distributed PoPs, reduced latency |
| 12 | **Hybrid** | On-prem for key services + cloud for others = hybrid deployment model |
| 13 | **Direct Connect** | Dedicated physical link to cloud provider with no built-in encryption (VPN adds encryption) |
| 14 | **Infrastructure as a Service** | IaaS requires the most migration effort — you manage VMs, OS, and storage yourself |
| 15 | **Network Security Group** | NSGs apply to specific VMs/NICs; NACLs apply to whole subnets |
| 16 | **Network Function Virtualization (NFV)** | NFV = virtual appliances replacing physical network devices |
| 17 | **IPSec VPN** | Encrypted tunnel over public internet between offices with different ISPs |
| 18 | **ESP (Encapsulating Security Payload)** | ESP encrypts payload; AH provides only integrity/authentication — no encryption |
| 19 | **SSH** | TCP port 22, encrypted remote access |
| 20 | **Multicast** | Multicast = group of nodes (some); anycast = nearest one; broadcast = all |
| 21 | **587** | SMTP submission with TLS; 25 = relay, 110 = POP3, 143 = IMAP |
| 22 | **ICMP** | Ping and unreachable messages; not TCP/UDP (transport), not SNMP (management) |
| 23 | **802.11 WiFi technologies** | 802.11n/ac/ax all support both 2.4 GHz and 5 GHz |
| 24 | **Heat Map** | Wireless heat map shows signal coverage to choose optimal AP placement |
| 25 | **802.11ax (Wi-Fi 6)** | Highest user density (OFDMA, MU-MIMO) + supports 2.4, 5, AND 6 GHz |
| 26 | **Multimode Fiber** | MMF large core = light reflects at multiple angles off cladding; SMF uses single straight path |
| 27 | **RJ45** | Standard NIC connector for copper Ethernet (if fiber NIC: LC; GBIC is a transceiver module, not a connector) |
| 28 | **SFP** | Small Form-factor Pluggable = modular, hot-swappable, supports multiple media types and speeds |
| 29 | **Mesh** | Full mesh = direct link between every node |
| 30 | **Access, Distribution, Core** | End-stations connect at access → aggregate at distribution → backbone at core |
| 31 | **Flow of data from the internet into and out of the cloud** | North-south = external ↔ internal; east-west = server ↔ server inside the DC |
| 32 | **255.255.255.248** | /29 = 2³−2 = exactly 6 usable hosts — minimum waste; /30 gives 2, /28 gives 14 |
| 33 | **The IP address is reserved and will not route through the internet** | 10.0.0.0/8 is RFC 1918 private; .97 is a valid host (not APIPA, not loopback) |
| 34 | **Control Plane** | SDN centralizes routing decisions in a controller; data plane stays distributed in devices |
| 35 | **Software Defined Networking** | SDN controller pushes forwarding logic to devices programmatically |
| 36 | **VXLAN** | L2 overlay over L3 IP; 24-bit VNI supports 16 million segments |
| 37 | **Mutual Authentication + Micro-segmentation** | Zero Trust = verify identity (mutual auth) + restrict lateral movement (micro-seg); IPS/IDS are not ZT concepts |
| 38 | **Least privilege** | Minimum access needed for the task |
| 39 | **CASB** | Cloud Access Security Broker = visibility and policy enforcement across SaaS/IaaS |
| 40 | **All of the above** | SSE (Security Service Edge) includes CASB, SWG, ZTNA, and Firewall-as-a-Service |
| 41 | **API** | APIs enable programmatic/automated management; SSH/CLI/GUI are manual |
| 42 | **Conflict resolution** | Resolves the problem of conflicting changes to the same code from multiple contributors |
| 43 | **Tunneling** | Encapsulates IPv4 inside IPv6 packets; dual stack runs both simultaneously (different use case) |

## Exam Traps in This Quiz

| Trap | Detail |
|------|--------|
| Q1 = Q4 | Same question, different wording — both answer is Datagram |
| AH vs ESP | AH = integrity only; ESP = encryption + integrity. Exam loves this |
| Direct Connect = no encryption | Dedicated link but no built-in crypto — VPN adds that layer |
| Anycast ≠ "some nodes" | Anycast = nearest single node; Multicast = group of nodes |
| /29 for 6 hosts | 2³−2 = 6 exactly — don't over-provision to /28 (14 hosts) |
| IPS/IDS are not Zero Trust | ZT = mutual auth + micro-segmentation. IPS/IDS are detection/prevention tools |
| NFV vs SDN | NFV = virtual appliances; SDN = centralized control plane. Different concepts |
| North-south vs East-west | North-south = in/out of DC (internet); East-west = within DC (server-to-server) |

## Weak Areas to Revisit

*(fill in after submission)*

## Lesson Cross-Reference

| Questions | Lesson |
|-----------|--------|
| Q1–Q4 | [[L01]] — OSI Model, PDUs, encapsulation |
| Q5–Q11 | [[L02]] — Networking Appliances (switch, AP, IPS, QoS, CDN) |
| Q12–Q18 | [[L03]] — Cloud Technologies (deployment models, NSG, NFV, VPN, IPSec) |
| Q19–Q22 | [[L04]] — Ports, Protocols, Services (SSH 22, SMTP 587, ICMP) |
| Q23–Q28 | [[L05]] — Network Media (802.11, fiber types, SFP) |
| Q29–Q31 | [[L06]] — Topologies (mesh, three-tier, traffic flow) |
| Q32–Q33 | [[L07]] — IP Addressing (subnetting, RFC 1918) |
| Q34–Q43 | [[L08]] — Modern Networks (SDN, VXLAN, Zero Trust, CASB, SSE, IPv6) |
