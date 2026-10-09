---
id: NQ02
title: "Practice Quiz: Networking Concepts"
course: CompTIA Network+
cert_track: network-plus
covers: L01–L08
date: 2026-06-14
status: Practice
score: --
tags: [quiz, network-plus, osi, appliances, cloud, ports, media, topologies, ip, modern-networks]
---

# NQ02 — Practice Quiz: Networking Concepts

Second pass over Module 1 content. Harder scenarios, different angles than NQ01.

---

## Questions

**Q1.** What layer of the OSI model is responsible for logical addressing and routing?
- A) Data Link
- B) Transport
- C) Network
- D) Session

---

**Q2.** During encapsulation, what is added at the Data Link layer?
- A) IP header and destination IP
- B) Port numbers and sequence numbers
- C) MAC address frame header and trailer
- D) Application session tokens

---

**Q3.** A device receiving data strips headers as it moves up the OSI stack. This process is called:
- A) Encapsulation
- B) Decapsulation
- C) Multiplexing
- D) Fragmentation

---

**Q4.** Which device operates at Layer 1 only and rebroadcasts all traffic to every connected port?
- A) Switch
- B) Hub
- C) Router
- D) Bridge

---

**Q5.** A network administrator needs to inspect encrypted HTTPS traffic for malware. Which device type provides this capability?
- A) Stateful firewall
- B) Packet filter
- C) Next-generation firewall
- D) IDS

---

**Q6.** Which proxy type sits between internet users and internal web servers, protecting servers from direct internet exposure?
- A) Forward proxy
- B) Transparent proxy
- C) Reverse proxy
- D) Anonymous proxy

---

**Q7.** What is the purpose of TTL in a network packet?
- A) Encrypt packet contents in transit
- B) Prevent packets from circulating indefinitely on the network
- C) Prioritize latency-sensitive traffic
- D) Authenticate the packet sender

---

**Q8.** A company wants to run multiple operating systems on a single physical server. Which technology enables this?
- A) SDN
- B) NFV
- C) Hypervisor
- D) VXLAN

---

**Q9.** Which of the five NIST cloud characteristics refers to the ability to rapidly scale resources up or down?
- A) On-demand self-service
- B) Broad network access
- C) Resource pooling
- D) Rapid elasticity

---

**Q10.** A developer needs a platform to deploy applications without managing the underlying OS or hardware. Which cloud model fits?
- A) IaaS
- B) PaaS
- C) SaaS
- D) Private cloud

---

**Q11.** Which cloud security control is stateless, applies at the subnet level, and evaluates both inbound and outbound traffic?
- A) Security Group
- B) Network ACL
- C) WAF
- D) Next-generation firewall

---

**Q12.** Which port does DNS use?
- A) 25
- B) 443
- C) 67
- D) 53

---

**Q13.** An administrator needs to transfer files securely to a remote server. Which protocol and port should they use?
- A) FTP — port 21
- B) TFTP — port 69
- C) SFTP — port 22
- D) SMB — port 445

---

**Q14.** IPSec in transport mode differs from tunnel mode in that:
- A) Transport mode encrypts the entire original packet including the IP header
- B) Transport mode only encrypts the payload, leaving the original IP header intact
- C) Tunnel mode only works with AH, not ESP
- D) Transport mode is used exclusively for site-to-site VPNs

---

**Q15.** Which ICMP type code represents an echo request (used by ping)?
- A) Type 0
- B) Type 3
- C) Type 8
- D) Type 11

---

**Q16.** What is the maximum cable run length for 1000BASE-T over Cat5e?
- A) 55 meters
- B) 100 meters
- C) 150 meters
- D) 25 meters

---

**Q17.** How many non-overlapping channels exist in the 2.4 GHz Wi-Fi band?
- A) 11
- B) 6
- C) 14
- D) 3

---

**Q18.** Which fiber type is best suited for long-distance connections exceeding 10 km?
- A) Multimode Fiber (MMF)
- B) Cat6a shielded
- C) Single Mode Fiber (SMF)
- D) Coaxial cable

---

**Q19.** Which cable type must be used in plenum spaces (air-handling areas above drop ceilings)?
- A) PVC-jacketed cable
- B) Direct burial cable
- C) Armored cable
- D) Plenum-rated cable

---

**Q20.** Which Wi-Fi standard introduced MU-MIMO and operates exclusively on the 5 GHz band?
- A) 802.11n
- B) 802.11g
- C) 802.11ac
- D) 802.11ax

---

**Q21.** In a spine-leaf architecture, which statement correctly describes the connection rule?
- A) Each leaf connects to every other leaf directly
- B) Each leaf connects to every spine
- C) Spines connect directly to end devices
- D) Each spine connects to only two leaf switches

---

**Q22.** A company has 8 branch offices and needs every office to communicate directly with every other. How many links are required in a full mesh topology?
- A) 8
- B) 16
- C) 28
- D) 56

---

**Q23.** Which SDN interface connects the controller to network applications and business logic above it?
- A) Southbound API
- B) Eastbound API
- C) Westbound API
- D) Northbound API

---

**Q24.** What is the valid host range for the subnet 192.168.10.64/26?
- A) 192.168.10.64 – 192.168.10.127
- B) 192.168.10.65 – 192.168.10.128
- C) 192.168.10.65 – 192.168.10.126
- D) 192.168.10.1 – 192.168.10.62

---

**Q25.** Which type of NAT maps multiple private IPs to a single public IP using port numbers to track sessions?
- A) Static NAT
- B) Dynamic NAT
- C) PAT (Port Address Translation)
- D) Proxy ARP

---

**Q26.** How many usable host addresses does a /28 subnet provide?
- A) 16
- B) 30
- C) 6
- D) 14

---

**Q27.** VXLAN uses a 24-bit VNI. How many unique overlay network segments does this support?
- A) 4,096
- B) 65,536
- C) 1,048,576
- D) 16,777,216

---

**Q28.** Which IaC tool uses an agentless architecture and communicates over SSH using YAML playbooks?
- A) Puppet
- B) Chef
- C) Ansible
- D) Terraform

---

**Q29.** In Zero Trust architecture, what is the default trust assumption for any user or device?
- A) Trusted if connected to the corporate network
- B) Trusted after single-factor authentication
- C) Never trusted — always verify regardless of location
- D) Trusted if the device is domain-joined

---

**Q30.** Which IPv6 address type is the functional equivalent of IPv4 RFC 1918 private addresses?
- A) Global Unicast (2000::/3)
- B) Link-local (fe80::/10)
- C) Unique Local (fc00::/7)
- D) Multicast (ff00::/8)

---

**Q31.** What is the correct compressed form of 2001:0db8:0000:0000:0000:0000:0000:0001?
- A) 2001:0db8::0001
- B) 2001:db8:1
- C) 2001:db8::1
- D) 2001:db8:0:0:0:0:0:1

---

**Q32.** Which cloud deployment model is dedicated to a single organization but hosted and managed by a third-party provider?
- A) Public cloud
- B) Hybrid cloud
- C) Community cloud
- D) Private cloud

---

**Q33.** Which protocol is used for network device monitoring and can send unsolicited alerts (traps) to notify administrators of events?
- A) ICMP
- B) SSH
- C) SNMP
- D) Syslog

---

**Q34.** Which SASE component specifically provides secure web access with URL filtering and malware scanning?
- A) CASB
- B) ZTNA
- C) SD-WAN
- D) Secure Web Gateway

---

**Q35.** Which OSI layer manages session establishment, maintenance, and termination between applications?
- A) Transport
- B) Presentation
- C) Session
- D) Application

---

**Q36.** What is the key advantage of spine-leaf architecture over three-tier in modern data centers?
- A) Fewer physical connections required overall
- B) Better support for north-south (internet) traffic
- C) Consistent low-latency east-west traffic — maximum 2 hops between any two endpoints
- D) Simpler management using a collapsed core

---

**Q37.** A stateful firewall tracks which of the following to make forwarding decisions?
- A) MAC address table entries only
- B) Connection state and session information
- C) BGP routing table entries
- D) VLAN membership

---

**Q38.** Which of the following is a characteristic of a Type 1 (bare-metal) hypervisor?
- A) Runs as an application on top of a host OS
- B) Runs directly on hardware with no underlying OS
- C) Requires a Type 2 hypervisor to function
- D) Can only run a single virtual machine

---

**Q39.** An organization needs to allow remote workers to access cloud SaaS applications securely without routing traffic through a central data center. Which technology best addresses this?
- A) Traditional VPN concentrator
- B) MPLS circuit
- C) SASE
- D) Direct Connect

---

**Q40.** Which of the following IaC tools uses a declarative model where you define the desired end state and the tool determines the steps?
- A) Ansible (procedural)
- B) Chef (procedural)
- C) Terraform
- D) Puppet (procedural)

---

## Answer Key

| Q | Answer | Reasoning |
|---|--------|-----------|
| 1 | **C — Network** | L3 handles logical addressing (IP) and routing |
| 2 | **C — MAC address frame header and trailer** | Data Link layer adds MAC addresses; IP header = L3; ports = L4 |
| 3 | **B — Decapsulation** | Stripping headers as data moves up the stack |
| 4 | **B — Hub** | Hub = L1 only; repeats all traffic to all ports; no intelligence |
| 5 | **C — Next-generation firewall** | NGFW can perform SSL/TLS inspection to see inside encrypted traffic |
| 6 | **C — Reverse proxy** | Sits in front of servers, protects them from direct client access |
| 7 | **B — Prevent packets from circulating indefinitely** | TTL decrements at each hop; drops to 0 = packet discarded |
| 8 | **C — Hypervisor** | Hypervisor (Type 1 or Type 2) enables multiple VMs on one physical host |
| 9 | **D — Rapid elasticity** | Elasticity = scale up/down on demand; on-demand = self-service provisioning |
| 10 | **B — PaaS** | PaaS provides a managed platform; developer deploys code, not infrastructure |
| 11 | **B — Network ACL** | NACLs = stateless + subnet-level; Security Groups = stateful + instance-level |
| 12 | **D — 53** | DNS uses port 53 (both UDP and TCP) |
| 13 | **C — SFTP — port 22** | SFTP runs over SSH (port 22); FTP = 21 (insecure); TFTP = 69 (no auth) |
| 14 | **B — Transport mode only encrypts payload, leaves original IP header intact** | Tunnel mode wraps the entire original packet in a new IP header |
| 15 | **C — Type 8** | ICMP Type 8 = echo request (ping); Type 0 = echo reply; Type 3 = unreachable |
| 16 | **B — 100 meters** | 802.3 T-rule: all 1000BASE-T runs max out at 100m over Cat5e/Cat6 |
| 17 | **D — 3** | Channels 1, 6, and 11 are the only non-overlapping channels in 2.4 GHz |
| 18 | **C — Single Mode Fiber (SMF)** | SMF = 9 µm core, one light path, long distance (40 km+); MMF = short distance |
| 19 | **D — Plenum-rated cable** | Plenum spaces require low-smoke/low-toxin cable due to fire code |
| 20 | **C — 802.11ac** | 802.11ac = 5 GHz only + MU-MIMO; 802.11ax adds 2.4 GHz and 6 GHz |
| 21 | **B — Each leaf connects to every spine** | Leaf-to-leaf = always via a spine; no direct leaf-to-leaf or spine-to-spine links |
| 22 | **C — 28** | Full mesh formula: n(n-1)/2 = 8×7/2 = 28 links |
| 23 | **D — Northbound API** | Northbound = controller → apps/orchestration above; Southbound = controller → devices below |
| 24 | **C — 192.168.10.65 – 192.168.10.126** | /26 = 64 addresses; network = .64, broadcast = .127, hosts = .65–.126 |
| 25 | **C — PAT (Port Address Translation)** | PAT = many-to-one using source ports; also called NAT overload |
| 26 | **D — 14** | /28 = 4 host bits; 2⁴−2 = 14 usable hosts |
| 27 | **D — 16,777,216** | 24-bit VNI = 2²⁴ = 16,777,216 segments (vs. 4,096 for 802.1Q VLANs) |
| 28 | **C — Ansible** | Ansible = agentless, SSH, YAML; Puppet/Chef = agent-based; Terraform = infrastructure provisioning |
| 29 | **C — Never trusted — always verify** | Zero Trust eliminates implicit trust; every request verified regardless of location |
| 30 | **C — Unique Local (fc00::/7)** | ULA = private IPv6; Link-local (fe80) = same link only; GUA = internet-routable |
| 31 | **C — 2001:db8::1** | Drop leading zeros in each group; replace longest run of all-zero groups with :: |
| 32 | **D — Private cloud** | Dedicated to one org; if third-party hosted it's still private cloud (not public) |
| 33 | **C — SNMP** | SNMP monitors devices and sends traps for event notification |
| 34 | **D — Secure Web Gateway** | SWG = URL filtering + malware scanning; CASB = cloud app visibility; ZTNA = access control |
| 35 | **C — Session** | OSI Layer 5; establishes, maintains, terminates sessions between applications |
| 36 | **C — Consistent low-latency east-west traffic** | Any endpoint-to-endpoint = exactly 2 hops; three-tier can be 4–6 hops for E-W |
| 37 | **B — Connection state and session information** | Stateful = tracks established connections; packet filter = stateless (per-packet rules only) |
| 38 | **B — Runs directly on hardware with no underlying OS** | Type 1 (bare-metal): VMware ESXi, Hyper-V; Type 2 runs inside a host OS (VirtualBox) |
| 39 | **C — SASE** | SASE = cloud-delivered security + networking; avoids hairpinning traffic to a DC |
| 40 | **C — Terraform** | Terraform = declarative (desired state); Ansible/Chef/Puppet can be procedural |

---

## Exam Traps in This Quiz

| Trap | Detail |
|------|--------|
| Rapid elasticity vs on-demand | Elasticity = scale up/down; on-demand = self-provision without human interaction |
| NACL vs Security Group | NACLs = stateless + subnet; Security Groups = stateful + instance — opposite of what feels intuitive |
| ICMP Type 8 vs Type 0 | Type 8 = request (you send ping); Type 0 = reply (you receive pong) |
| Transport vs Tunnel mode | Transport = payload only encrypted, IP header exposed; Tunnel = whole packet wrapped |
| PAT vs Dynamic NAT | Dynamic NAT = pool of public IPs, 1:1 mapping; PAT = one public IP, uses ports to multiplex |
| MMF vs SMF | MMF = short range, large core, multi-path; SMF = long range, tiny core, single path |
| 802.11ac vs 802.11ax | ac = 5 GHz only; ax (Wi-Fi 6) = 2.4 + 5 + 6 GHz |
| Type 1 vs Type 2 hypervisor | Type 1 = bare metal (no host OS); Type 2 = app on top of host OS |
| 2²⁴ VXLAN segments | 16,777,216 — not 4,096 (that's 802.1Q VLAN limit) |
| Terraform declarative | Defines end state; Ansible/Chef/Puppet can be procedural (step-by-step) |

## Lesson Cross-Reference

| Questions | Lesson |
|-----------|--------|
| Q1–Q3 | [[L01 - The OSI Model]] — OSI Model, PDUs, encapsulation/decapsulation |
| Q4–Q7 | [[L02 - Networking Appliances & Applications]] — Networking Appliances (hub, NGFW, reverse proxy, TTL) |
| Q8–Q11 | [[L03 - Network Virtualization & Cloud Technologies]] — Cloud Technologies (hypervisor, NIST, PaaS, NACLs) |
| Q12–Q15 | [[L04 - Ports, Protocols & Services]] — Ports, Protocols, Services (DNS, SFTP, ICMP, IPsec modes) |
| Q16–Q20 | [[L05 - Network Transmission - Media, Standards & Connectors]] — Network Media (cable length, 2.4 GHz channels, SMF, plenum, 802.11ac) |
| Q21–Q23 | [[L06 - Network Topologies, Architectures & Traffic Flows]] — Topologies (spine-leaf, mesh formula, SDN APIs) |
| Q24–Q26 | [[L07 - IP Addressing]] — IP Addressing (subnetting, PAT, /28 hosts) |
| Q27–Q40 | [[L08 - Modern Networks]] — Modern Networks (VXLAN, Ansible, Zero Trust, ULA, SASE, Terraform) |
