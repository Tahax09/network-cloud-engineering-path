---
id: PE01
title: Network+ N10-009 Practice Exam 1
course: CompTIA Network+
covers: L01–L25 (all domains)
date: 2026-10-08
status: Not attempted
questions: 90
time_limit: 90 minutes
passing: 720/900 (scaled) — target ≥ 80% raw on practice
source: Original questions written to the official N10-009 exam objectives and domain weights
---

# PE01 — Network+ N10-009 Practice Exam 1

> **Original questions** written to the official CompTIA N10-009 objectives — not taken from the real exam or braindumps. Format mirrors the real exam: scenario-based multiple choice, some with command-output exhibits. (The real exam also has a few performance-based questions — drag-and-drop/simulations — practice those with the hands-on sections of each lesson.)

## How to take it
1. Set a **90-minute** timer. Don't open the lesson pages.
2. Write your answers (e.g., `1-B, 2-D, …`) and paste them to Claude for grading → per-domain score + weak areas logged.
3. Real exam: max 90 questions, 90 minutes, passing **720** on a 100–900 scale (≈ 80%+ raw on practice exams is a safe target).

## Domain weighting

| Domain | Official weight | Questions here |
|--------|----------------|----------------|
| 1.0 Networking Concepts | 23% | 21 |
| 2.0 Network Implementation | 20% | 18 |
| 3.0 Network Operations | 19% | 17 |
| 4.0 Network Security | 14% | 13 |
| 5.0 Network Troubleshooting | 24% | 21 |
| **Total** | 100% | **90** |

---

## Questions

### Domain 1.0 — Networking Concepts

**1.** A switch forwards frames based on the destination MAC address in each frame. At which OSI layer is the switch making this decision?

- A) Layer 4 — Transport
- B) Layer 1 — Physical
- C) Layer 2 — Data Link
- D) Layer 3 — Network

**2.** Encryption and data formatting/translation (e.g., TLS, character encoding) are traditionally mapped to which OSI layer?

- A) Presentation
- B) Application
- C) Transport
- D) Session

**3.** A company needs a device that distributes incoming HTTPS requests across a pool of web servers and removes unhealthy servers from rotation automatically. Which device should be deployed?

- A) Load balancer
- B) Proxy server
- C) Layer 3 switch
- D) Intrusion detection system

**4.** A security team wants a sensor that receives a copy of traffic from a SPAN port and alerts on malicious patterns without being able to drop packets. Which solution fits?

- A) IPS
- B) Next-generation firewall
- C) Web application firewall
- D) IDS

**5.** A global company wants static website content served from servers geographically close to users to reduce latency. Which solution should be used?

- A) Content delivery network (CDN)
- B) Site-to-site VPN
- C) Storage area network (SAN)
- D) Network-attached storage (NAS)

**6.** A company runs virtual machines on an IaaS cloud provider. Under the shared responsibility model, who is responsible for patching the guest operating system?

- A) The ISP
- B) The customer
- C) The cloud provider
- D) Both, as defined only in the SLA

**7.** An organization needs consistent bandwidth between its data center and its cloud VPC without traversing the public internet. Which connectivity option meets this requirement?

- A) Site-to-site IPsec VPN
- B) Internet gateway
- C) VPC peering
- D) Dedicated private connection (e.g., AWS Direct Connect)

**8.** Instances in a private cloud subnet must download OS updates from the internet but must not accept inbound connections initiated from the internet. Which component provides this?

- A) NAT gateway
- B) Internet gateway attached to the private subnet
- C) VPC peering connection
- D) Transit gateway

**9.** An administrator must open a firewall port for secure LDAP (LDAP over TLS). Which port?

- A) TCP 3268
- B) TCP 443
- C) TCP 389
- D) TCP 636

**10.** Network devices must send SNMP traps to the network management system. Which port must be allowed to the NMS?

- A) UDP 514
- B) UDP 161
- C) UDP 69
- D) UDP 162

**11.** A help desk technician needs to connect to a Windows server's graphical desktop remotely. Which protocol and port are used by default?

- A) RDP, TCP 3389
- B) SSH, TCP 22
- C) SMB, TCP 445
- D) Telnet, TCP 23

**12.** A firewall must permit GRE tunnels between two routers. What must the rule allow?

- A) IP protocol 47
- B) IP protocol 50
- C) TCP port 47
- D) UDP port 4500

**13.** Two campus buildings are 10 km apart. Which medium is most appropriate for a 10 Gbps link between them?

- A) Single-mode fiber
- B) Cat6a UTP
- C) RG-6 coaxial
- D) Multimode fiber

**14.** Network cable will be run through the space above a drop ceiling that is used for air circulation. Which cable jacket type is required by fire code?

- A) Riser-rated
- B) PVC
- C) Plenum-rated
- D) Direct-burial

**15.** In a data center design, every access-layer switch connects to every core-layer switch, providing equal-cost paths between any two servers. What is this architecture?

- A) Point-to-point
- B) Spine and leaf
- C) Hub and spoke
- D) Three-tier hierarchical

**16.** Traffic flowing between two application servers inside the same data center is best described as which type of traffic flow?

- A) Ingress
- B) Inbound
- C) East-west
- D) North-south

**17.** How many usable host addresses are available in a /27 subnet?

- A) 32
- B) 62
- C) 30
- D) 14

**18.** Which of the following is a valid host address in the 192.168.10.64/26 network?

- A) 192.168.10.100
- B) 192.168.10.64
- C) 192.168.10.130
- D) 192.168.10.127

**19.** Which of the following is an RFC 1918 private address?

- A) 172.20.5.4
- B) 169.254.10.1
- C) 100.64.1.1
- D) 172.32.1.1

**20.** Which architecture removes implicit trust based on network location and requires every request to be authenticated and authorized?

- A) Defense in depth
- B) Zero trust
- C) Perimeter-based security
- D) Screened subnet

**21.** IPv6-only clients must reach a legacy IPv4-only server. Which technology translates between the two?

- A) 6to4 tunneling
- B) Dual stack
- C) NAT64
- D) SLAAC

### Domain 2.0 — Network Implementation

**22.** A router has these routes: 10.0.0.0/8 via OSPF, 10.1.0.0/16 via a static route, and 10.1.1.0/24 via EIGRP. Which route is used for a packet to 10.1.1.20?

- A) 10.0.0.0/8 via OSPF
- B) 10.1.0.0/16 static
- C) The packet is load-balanced across all three
- D) 10.1.1.0/24 via EIGRP

**23.** A router learns the exact same prefix from OSPF (AD 110) and EIGRP (AD 90). Which route is installed in the routing table?

- A) Both, load-balanced
- B) The one with the lower metric
- C) The OSPF route
- D) The EIGRP route

**24.** Which routing protocol is used to exchange routes between different autonomous systems on the internet?

- A) BGP
- B) RIP
- C) OSPF
- D) EIGRP

**25.** Two routers share a single virtual IP address that hosts use as their default gateway, so a router failure is transparent to hosts. Which technology is this?

- A) First hop redundancy protocol (e.g., VRRP/HSRP)
- B) Link aggregation (LACP)
- C) Port address translation
- D) Spanning tree

**26.** Two hundred internal hosts reach the internet using a single public IPv4 address, distinguished by source port numbers. What is this called?

- A) NAT64
- B) PAT (NAT overload)
- C) Static NAT
- D) 1:1 NAT

**27.** A single router interface must route between VLANs 10, 20, and 30 arriving on one trunk link from a switch. What must be configured on the router?

- A) A separate physical interface per VLAN, all untagged
- B) A static route per VLAN to the switch
- C) Subinterfaces with 802.1Q encapsulation
- D) Spanning tree on the router

**28.** Untagged frames received on an 802.1Q trunk port are placed into which VLAN?

- A) VLAN 4095
- B) The voice VLAN
- C) The native VLAN
- D) The management VLAN

**29.** An administrator wants to combine four 1 Gbps links between two switches into one logical 4 Gbps link with redundancy. Which protocol should be used?

- A) LACP
- B) CDP
- C) STP
- D) VTP

**30.** An iSCSI storage network needs better efficiency by reducing per-frame header overhead. Which switch setting helps?

- A) Jumbo frames (MTU ~9000)
- B) PortFast
- C) PoE+
- D) Port mirroring

**31.** An IP phone and a PC share one switch port. Phone traffic must be placed in a separate VLAN for QoS. Which feature is used?

- A) Private VLAN
- B) Trunk pruning
- C) Native VLAN
- D) Voice VLAN

**32.** A link between two switches must carry traffic for VLANs 10, 20, and 30. How should the switch ports be configured?

- A) As access ports in VLAN 10
- B) As port-mirroring destinations
- C) As 802.1Q trunk ports
- D) As routed ports

**33.** Which IEEE standard is marketed as Wi-Fi 6?

- A) 802.11ax
- B) 802.11n
- C) 802.11ac
- D) 802.11be

**34.** A company wants each employee to authenticate to Wi-Fi with individual credentials verified by a RADIUS server. Which security mode should be configured?

- A) WPA3-Personal (SAE)
- B) WPA3-Enterprise
- C) Open with captive portal
- D) WPA2-PSK

**35.** Two buildings 300 m apart need a wireless bridge. Which antenna type is most appropriate?

- A) Internal PCB antenna
- B) Omnidirectional
- C) Dipole
- D) Directional (e.g., Yagi or parabolic)

**36.** Some 5 GHz channels require access points to detect radar and move off the channel when radar is present. What is this feature called?

- A) Beamforming
- B) Band steering
- C) MU-MIMO
- D) Dynamic frequency selection (DFS)

**37.** Network equipment must keep running for the few minutes between a utility power failure and the backup generator coming online. Which device provides this?

- A) Automatic transfer switch only
- B) Surge protector
- C) UPS
- D) PDU

**38.** A switch is listed as 2U. How tall is it?

- A) 2 inches
- B) 1.75 inches
- C) 3.5 inches
- D) 4 inches

**39.** In a multi-floor building, where are the ISP demarcation point and core switching equipment typically located?

- A) The screened subnet
- B) Main distribution frame (MDF)
- C) Intermediate distribution frame (IDF)
- D) A floor's wiring closet

### Domain 3.0 — Network Operations

**40.** Which document defines what employees may and may not do when using company network resources?

- A) Non-disclosure agreement (NDA)
- B) Service-level agreement (SLA)
- C) Memorandum of understanding (MOU)
- D) Acceptable use policy (AUP)

**41.** A contract with an ISP guarantees 99.99% uptime and a four-hour repair window. What is this document?

- A) Memorandum of understanding
- B) Acceptable use policy
- C) Statement of work
- D) Service-level agreement (SLA)

**42.** An administrator wants to detect configuration drift on routers after unauthorized changes. What should running configurations be compared against?

- A) The rack diagram
- B) The baseline (golden) configuration
- C) The physical network diagram
- D) The IP address management export

**43.** A vendor announces that it will no longer release security patches for a firewall model. Which lifecycle stage is this?

- A) Warranty expiration
- B) Decommissioning
- C) End of support
- D) End of sale

**44.** A switch sends an unsolicited message to the NMS when a port goes down. Which SNMP message type is this?

- A) Get
- B) Trap
- C) Set
- D) Walk

**45.** A network team needs to identify top talkers and application usage across the WAN without capturing full packet payloads. Which technology should they use?

- A) SNMP gets of interface counters
- B) Port mirroring with full packet capture
- C) Syslog
- D) NetFlow / IPFIX

**46.** Which syslog severity level indicates that the system is unusable?

- A) 0 — Emergency
- B) 3 — Error
- C) 4 — Warning
- D) 7 — Debug

**47.** Which SNMP version provides authentication and encryption of management traffic?

- A) SNMPv2 with community strings
- B) SNMPv3
- C) SNMPv2c
- D) SNMPv1

**48.** Leadership states that no more than 15 minutes of transaction data can be lost after an outage. Which metric does this define?

- A) Mean time to repair (MTTR)
- B) Recovery point objective (RPO)
- C) Recovery time objective (RTO)
- D) Mean time between failures (MTBF)

**49.** A recovery site has power, network connectivity, and hardware installed, but data must be restored from backups before operations resume. Which type of site is this?

- A) Hot site
- B) Mobile site
- C) Cold site
- D) Warm site

**50.** The IT team gathers in a conference room and talks through its disaster recovery plan step by step without touching any systems. What is this?

- A) Penetration test
- B) Failover test
- C) Validation test
- D) Tabletop exercise

**51.** Which DNS record type maps a hostname to an IPv6 address?

- A) PTR
- B) CNAME
- C) AAAA
- D) A

**52.** A network printer must always receive the same IP address from DHCP. What should be configured?

- A) A DHCP exclusion range
- B) A DHCP reservation for the printer's MAC address
- C) A shorter lease time
- D) A DHCP relay agent

**53.** Clients in VLAN 30 cannot obtain addresses from a DHCP server located in VLAN 10. What should be configured on the VLAN 30 gateway interface?

- A) DNS forwarding
- B) A second DHCP scope on the client
- C) DHCP relay (IP helper address)
- D) Port security

**54.** Logs from network devices have inconsistent timestamps, which makes correlation difficult. Which service and port should be configured on all devices?

- A) SNMP, UDP 161
- B) PTP, UDP 319
- C) NTP, UDP 123
- D) Syslog, UDP 514

**55.** Remote employees should send only corporate-bound traffic through the VPN, while their internet traffic goes directly out their home connection. Which configuration is this?

- A) Site-to-site VPN
- B) Split tunnel
- C) Clientless VPN
- D) Full tunnel

**56.** Network devices are managed through a dedicated network that is separate from production user traffic, so admins can reach them even when the production network is down. What is this?

- A) In-band management
- B) Split tunnel
- C) Jump box
- D) Out-of-band management

### Domain 4.0 — Network Security

**57.** An administrator generates a SHA-256 value for a firmware file and compares it to the vendor's published value. Which security goal does this support?

- A) Non-repudiation only
- B) Confidentiality
- C) Integrity
- D) Availability

**58.** Which of the following combinations is multifactor authentication?

- A) Fingerprint and retina scan
- B) Password and a security question
- C) Password and a PIN
- D) Password and a smart card

**59.** A contractor is granted access only to the single application needed for the project, and nothing else. Which principle is applied?

- A) Implicit deny
- B) Separation of duties
- C) Least privilege
- D) Defense in depth

**60.** Network administrators logging in to routers need per-command authorization and full encryption of the AAA exchange. Which protocol should be used?

- A) RADIUS
- B) LDAP
- C) SAML
- D) TACACS+

**61.** An online retailer stores and processes customer credit card numbers. Which standard must it comply with?

- A) PCI DSS
- B) HIPAA
- C) SOX
- D) GDPR

**62.** A security team deploys a decoy server that appears vulnerable, with no production purpose, to detect and study attackers. What is it?

- A) Honeypot
- B) Jump box
- C) Screened subnet
- D) Bastion host

**63.** An attacker connected to an access port in the native VLAN sends frames with two 802.1Q tags to reach a different VLAN. What is this attack?

- A) VLAN hopping (double tagging)
- B) Switch spoofing via DTP
- C) ARP poisoning
- D) MAC flooding

**64.** An attacker floods a switch with frames from thousands of random source MAC addresses, causing it to forward all traffic out every port. What is this attack?

- A) MAC flooding
- B) VLAN hopping
- C) DNS poisoning
- D) ARP spoofing

**65.** An attacker sets up an access point broadcasting the company's SSID in the parking lot to capture employee traffic. What is this?

- A) Bluesnarfing
- B) War driving
- C) Rogue DHCP server
- D) Evil twin

**66.** A targeted email impersonating the board chair is sent to the CFO requesting an urgent wire transfer. What is the most specific term?

- A) Whaling
- B) Vishing
- C) Phishing
- D) Smishing

**67.** Users are receiving IP addresses and a default gateway from an unauthorized device on the LAN. Which switch feature prevents this?

- A) BPDU guard
- B) Storm control
- C) Port mirroring
- D) DHCP snooping

**68.** A switch port allows only EAPOL traffic until the connected device authenticates through a RADIUS server. Which technology is in use?

- A) Port mirroring
- B) 802.1Q
- C) 802.1X
- D) MAC filtering

**69.** A company hosts a public web server and wants to make sure a compromise of that server does not give direct access to internal databases. Where should the web server be placed?

- A) Out-of-band management network
- B) Guest VLAN
- C) Trusted internal zone
- D) Screened subnet

### Domain 5.0 — Network Troubleshooting

**70.** A technician tests a theory of probable cause and confirms it. What is the next step in the troubleshooting methodology?

- A) Establish a new theory
- B) Document findings
- C) Establish a plan of action to resolve the problem and identify potential effects
- D) Implement the solution

**71.** A technician has implemented a fix for a network outage. What should the technician do next?

- A) Escalate to the vendor
- B) Document findings, actions, and outcomes
- C) Establish a theory of probable cause
- D) Verify full system functionality and, if applicable, implement preventive measures

**72.** A technician asks a user whether any software was installed or settings were changed before the problem began. Which troubleshooting step is the technician performing?

- A) Test the theory
- B) Establish a theory of probable cause
- C) Establish a plan of action
- D) Identify the problem

**73.** A junior technician's theory is not confirmed, and the remaining possible causes are in systems the technician cannot access. What should the technician do?

- A) Verify full system functionality
- B) Escalate
- C) Implement the solution anyway
- D) Document findings

**74.** Exhibit — `show interfaces Gi0/1`:

```
GigabitEthernet0/1 is up, line protocol is up
  Half-duplex, 100Mb/s
  0 input errors, 0 CRC, 0 runts
  1532 late collision
```

The connected server's NIC is set to 100 Mb/s full duplex. What is the problem?

- A) EMI on the cable
- B) Speed mismatch
- C) MTU mismatch
- D) Duplex mismatch

**75.** Exhibit — `show interfaces Gi1/0/5`:

```
GigabitEthernet1/0/5 is up, line protocol is up
  Full-duplex, 1000Mb/s
  0 CRC, 0 runts, 4210 giants
```

What is the most likely cause of the giants?

- A) A cable exceeding 100 m
- B) EMI from nearby equipment
- C) Duplex mismatch
- D) MTU mismatch — jumbo frames enabled on the connected device only

**76.** CRC errors are increasing on a copper cable that runs alongside a large industrial motor. Which is the most likely cause?

- A) Attenuation
- B) TX/RX transposition
- C) Duplex mismatch
- D) EMI

**77.** A user plugs a small personal switch into a wall port, and the port immediately becomes err-disabled. Which feature most likely caused this?

- A) DHCP snooping
- B) BPDU guard
- C) Auto-MDI-X
- D) Storm control logging

**78.** A workstation's gigabit NIC only links at 100 Mbps. The patch cable is labeled Cat5. What should the technician do?

- A) Replace the switch's SFP
- B) Replace the cable with Cat5e or better
- C) Hard-code the NIC to 1000/full
- D) Enable Auto-MDI-X

**79.** A copper Ethernet run between two switches is 130 m long and shows intermittent errors. What is the most likely cause?

- A) Attenuation
- B) Speed mismatch
- C) EMI
- D) Crosstalk from a split pair

**80.** Exhibit — `ipconfig` on a Windows client:

```
IPv4 Address. . . . : 169.254.33.10
Subnet Mask . . . . : 255.255.0.0
Default Gateway . . :
```

What is the most likely problem?

- A) The default route is missing on the router
- B) The client has a duplicate IP address
- C) The DNS server is unreachable
- D) The client could not obtain a lease from a DHCP server

**81.** A host is configured as 10.10.5.20/24 with default gateway 10.10.6.1. What symptom will the user see?

- A) Only DNS resolution fails
- B) Local subnet hosts are reachable, but remote networks are not
- C) Nothing is reachable, including local hosts
- D) Everything works normally

**82.** Exhibit — `show ip route` on a branch router:

```
Gateway of last resort is not set
C  10.1.10.0/24 is directly connected, Vlan10
C  10.1.20.0/24 is directly connected, Vlan20
C  203.0.113.0/30 is directly connected, Gi0/0
```

Users can reach each other across VLANs but not the internet. What should be added?

- A) Spanning tree on VLAN 10
- B) A DHCP relay agent
- C) A default route (0.0.0.0/0) toward the ISP next hop
- D) An ACL permitting inter-VLAN traffic

**83.** The entire LAN suddenly becomes unusable. Switch CPUs are at 100%, link lights are solid, and logs show MAC addresses flapping between ports. What is the most likely cause?

- A) A missing default route
- B) DHCP scope exhaustion
- C) A switching loop causing a broadcast storm
- D) A duplex mismatch on the uplink

**84.** Hosts in VLAN 20 on Switch B cannot reach VLAN 20 hosts on Switch A. All other VLANs work across the same trunk. What is most likely misconfigured?

- A) VLAN 20 is not in the trunk's allowed VLAN list
- B) The STP root bridge
- C) The PoE budget
- D) The DHCP lease time

**85.** VoIP users report that calls sound choppy and robotic. Which network metric is most likely out of tolerance?

- A) Jitter
- B) Bandwidth
- C) Throughput
- D) Latency

**86.** Wi-Fi connections in the break room drop whenever the microwave oven is running. What is the cause?

- A) Insufficient DHCP addresses
- B) Interference in the 2.4 GHz band
- C) A duplex mismatch on the AP uplink
- D) A roaming misconfiguration

**87.** A WAN interface is rated at 1 Gbps, but file transfers across it achieve only 180 Mbps due to overhead, congestion, and contract limits. What term describes the 180 Mbps value?

- A) Throughput
- B) Bandwidth
- C) Jitter
- D) Latency

**88.** A technician knows a device's MAC address and needs to find which switch port it is connected to. Which command should be used?

- A) show vlan brief
- B) show power inline
- C) show mac address-table
- D) show ip route

**89.** Users cannot reach a web application. The server responds to ping. Which tool, run on the server itself, verifies that the web service is listening on TCP 443?

- A) arp
- B) tracert
- C) netstat (or ss)
- D) nslookup

**90.** A fiber run between two buildings has failed, and the technician needs to determine how far down the cable the break is located. Which tool should be used?

- A) Toner probe
- B) Cable tester for copper
- C) Wi-Fi analyzer
- D) OTDR

---

## Answer Key

> Don't scroll here until you've finished.

| # | Ans | Obj | Explanation | Lesson |
|---|-----|-----|-------------|--------|
| 1 | **C** | 1.1 | MAC addresses and frames are Layer 2. Routers decide on IP (L3). | [[L01]] |
| 2 | **A** | 1.1 | Layer 6 (Presentation) handles formatting, compression, and encryption in the classic OSI model. | [[L01]] |
| 3 | **A** | 1.2 | Load balancers distribute sessions and run health checks against pool members. | [[L02]] |
| 4 | **D** | 1.2 | An IDS is out-of-band (passive) — detect and alert only. An IPS sits inline and can block. | [[L02]] |
| 5 | **A** | 1.2 | A CDN caches content at edge locations near users. | [[L02]] |
| 6 | **B** | 1.3 | In IaaS the provider secures the physical host and hypervisor; the customer owns the guest OS and up. | [[L03]] |
| 7 | **D** | 1.3 | A site-to-site VPN is encrypted but still rides the internet. Direct Connect/Interconnect is a private circuit. | [[L03]] |
| 8 | **A** | 1.3 | A NAT gateway allows outbound-initiated traffic only. An internet gateway route makes a subnet public. | [[L03]] |
| 9 | **D** | 1.4 | LDAP = 389, LDAPS = 636. | [[L04]] |
| 10 | **D** | 1.4 | Agents listen on UDP 161 (get/set); the manager receives traps on UDP 162. 514 = syslog, 69 = TFTP. | [[L04]] |
| 11 | **A** | 1.4 | RDP uses TCP 3389. | [[L04]] |
| 12 | **A** | 1.4 | GRE is IP protocol 47 (not a port). ESP is IP protocol 50; UDP 4500 is IPsec NAT-T. | [[L04]] |
| 13 | **A** | 1.5 | Multimode reaches hundreds of meters; copper reaches 100 m. Kilometers = single-mode. | [[L05]] |
| 14 | **C** | 1.5 | Plenum cable resists fire and produces less toxic smoke in air-handling spaces. | [[L05]] |
| 15 | **B** | 1.6 | Spine-and-leaf: every leaf connects to every spine; consistent hop count, ideal for east-west traffic. | [[L06]] |
| 16 | **C** | 1.6 | East-west = within the data center. North-south = in/out of the data center. | [[L06]] |
| 17 | **C** | 1.7 | /27 = 32 addresses − network − broadcast = 30. | [[L07]] |
| 18 | **A** | 1.7 | /26 = blocks of 64: .64 (network) – .127 (broadcast). Hosts .65–.126. | [[L07]] |
| 19 | **A** | 1.7 | Private: 10/8, 172.16/12 (172.16–172.31), 192.168/16. 169.254 = APIPA, 100.64/10 = CGNAT. | [[L07]] |
| 20 | **B** | 1.8 | Zero trust: never trust, always verify — identity and context per request. | [[L08]] |
| 21 | **C** | 1.8 | NAT64 translates IPv6↔IPv4. Dual stack requires both stacks on the client; tunneling carries v6 over v4. | [[L08]] |
| 22 | **D** | 2.1 | Longest prefix match is evaluated first, before administrative distance. | [[L09]] |
| 23 | **D** | 2.1 | Same prefix length → lowest administrative distance wins. Metrics only compare routes from the same protocol. | [[L09]] |
| 24 | **A** | 2.1 | BGP is the exterior gateway protocol of the internet. | [[L09]] |
| 25 | **A** | 2.1 | FHRPs provide a virtual gateway IP shared by multiple routers. | [[L09]] |
| 26 | **B** | 2.1 | PAT maps many private IP:port pairs to one public IP with unique ports. | [[L09]] |
| 27 | **C** | 2.1 | Router-on-a-stick: one subinterface per VLAN, each tagged with its VLAN ID. | [[L09]] |
| 28 | **C** | 2.2 | Native VLAN traffic crosses the trunk untagged (default VLAN 1). | [[L10]] |
| 29 | **A** | 2.2 | LACP (802.3ad) negotiates link aggregation / port channels. | [[L10]] |
| 30 | **A** | 2.2 | Jumbo frames carry more payload per frame — common for storage traffic. All devices in the path must match. | [[L10]] |
| 31 | **D** | 2.2 | The voice VLAN tags phone traffic separately while the PC uses the access (data) VLAN. | [[L10]] |
| 32 | **C** | 2.2 | Trunks carry multiple tagged VLANs; access ports carry one. | [[L10]] |
| 33 | **A** | 2.3 | Wi-Fi 4 = n, Wi-Fi 5 = ac, Wi-Fi 6/6E = ax, Wi-Fi 7 = be. | [[L11]] |
| 34 | **B** | 2.3 | Enterprise mode uses 802.1X/EAP with RADIUS; Personal uses a shared passphrase. | [[L11]] |
| 35 | **D** | 2.3 | Point-to-point links focus energy in one direction. | [[L11]] |
| 36 | **D** | 2.3 | DFS channels are shared with radar systems; APs must vacate when radar is detected. | [[L11]] |
| 37 | **C** | 2.4 | A UPS bridges the gap with battery power. A PDU distributes power; it doesn't store it. | [[L12]] |
| 38 | **C** | 2.4 | 1U = 1.75 in, so 2U = 3.5 in. | [[L12]] |
| 39 | **B** | 2.4 | MDF = main equipment room / demarc; IDFs serve individual floors. | [[L12]] |
| 40 | **D** | 3.1 | The AUP sets the rules of use for users. | [[L13]] |
| 41 | **D** | 3.1 | SLAs define measurable service commitments. | [[L13]] |
| 42 | **B** | 3.1 | Baseline configurations are the approved standard; differences = drift. | [[L13]] |
| 43 | **C** | 3.1 | End of sale = can't buy it; end of support = no more patches → plan replacement. | [[L13]] |
| 44 | **B** | 3.2 | Traps are pushed by the agent; gets/sets/walks are initiated by the manager. | [[L14]] |
| 45 | **D** | 3.2 | Flow data records metadata (src/dst/ports/bytes), not payloads. | [[L14]] |
| 46 | **A** | 3.2 | Lower number = more severe; 0 is Emergency. | [[L14]] |
| 47 | **B** | 3.2 | v1/v2c use cleartext community strings; v3 adds user auth and privacy (encryption). | [[L14]] |
| 48 | **B** | 3.3 | RPO = acceptable data loss (age of the last good copy). RTO = acceptable downtime. | [[L15]] |
| 49 | **D** | 3.3 | Hot = live/replicated data; warm = equipment ready, data restore needed; cold = space and power only. | [[L15]] |
| 50 | **D** | 3.3 | A tabletop is a discussion-based walkthrough. | [[L15]] |
| 51 | **C** | 3.4 | A = IPv4, AAAA = IPv6, PTR = reverse lookup, CNAME = alias. | [[L16]] |
| 52 | **B** | 3.4 | Reservations bind an IP to a MAC. Exclusions remove addresses from the pool entirely. | [[L16]] |
| 53 | **C** | 3.4 | DHCP DISCOVER is a broadcast; a relay forwards it as unicast to the server on another subnet. | [[L16]] |
| 54 | **C** | 3.4 | NTP synchronizes clocks (UDP 123). PTP is for sub-microsecond precision needs. | [[L16]] |
| 55 | **B** | 3.5 | Split tunnel sends only corporate routes through the VPN; full tunnel sends everything. | [[L17]] |
| 56 | **D** | 3.5 | OOB = separate management path (dedicated network, console servers). | [[L17]] |
| 57 | **C** | 4.1 | Hashes detect changes → integrity. | [[L18]] |
| 58 | **D** | 4.1 | MFA requires different factor types. Password+PIN and password+question are both 'know'; fingerprint+retina are both 'are'. | [[L18]] |
| 59 | **C** | 4.1 | Least privilege = minimum access needed for the job. | [[L18]] |
| 60 | **D** | 4.1 | TACACS+ (TCP 49) encrypts the full payload and supports per-command authorization; RADIUS encrypts only the password. | [[L18]] |
| 61 | **A** | 4.1 | PCI DSS covers cardholder data (contractually enforced). | [[L18]] |
| 62 | **A** | 4.1 | A honeypot is a decoy; any interaction is suspicious. | [[L18]] |
| 63 | **A** | 4.2 | Double tagging exploits the untagged native VLAN. Switch spoofing negotiates a trunk instead. | [[L19]] |
| 64 | **A** | 4.2 | A full CAM table makes the switch fail open (act like a hub). | [[L19]] |
| 65 | **D** | 4.2 | An evil twin is a rogue AP that impersonates a legitimate SSID. | [[L19]] |
| 66 | **A** | 4.2 | Whaling = spear phishing aimed at executives. | [[L19]] |
| 67 | **D** | 4.3 | DHCP snooping blocks DHCP offers from untrusted ports. | [[L20]] |
| 68 | **C** | 4.3 | 802.1X: supplicant → authenticator (switch) → authentication server (RADIUS). | [[L20]] |
| 69 | **D** | 4.3 | The screened subnet (formerly DMZ) isolates public services behind another firewall layer. | [[L20]] |
| 70 | **C** | 5.1 | Identify → theory → test → PLAN → implement → verify → document. | [[L21]] |
| 71 | **D** | 5.1 | Verify comes before documentation. | [[L21]] |
| 72 | **D** | 5.1 | Determining what changed is a step 1 sub-task. | [[L21]] |
| 73 | **B** | 5.1 | Theory not confirmed → establish a new theory or escalate. | [[L21]] |
| 74 | **D** | 5.2 | Late collisions on the half-duplex side are the duplex-mismatch signature. A speed mismatch would bring the link down. | [[L22]] |
| 75 | **D** | 5.2 | Giants are frames larger than 1518 bytes — classic jumbo frame/MTU mismatch. | [[L22]] |
| 76 | **D** | 5.2 | Motors and power equipment induce electromagnetic interference on copper. | [[L22]] |
| 77 | **B** | 5.2 | BPDU guard err-disables a PortFast edge port when a BPDU (from another switch) arrives. | [[L22]] |
| 78 | **B** | 5.2 | Gigabit Ethernet requires at least Cat5e. | [[L22]] |
| 79 | **A** | 5.2 | Copper Ethernet is limited to 100 m; beyond that the signal weakens too much. | [[L22]] |
| 80 | **D** | 5.3 | 169.254.x.x is APIPA — DHCP failed (pool exhausted, server unreachable, wrong VLAN, no relay). | [[L23]] |
| 81 | **B** | 5.3 | A gateway outside the host's subnet can't be ARPed; local traffic still works. | [[L23]] |
| 82 | **C** | 5.3 | Only connected routes exist; without 0.0.0.0/0 the router can't forward internet-bound traffic. | [[L23]] |
| 83 | **C** | 5.3 | MAC flapping + pegged CPUs + total outage = Layer 2 loop (STP disabled or bypassed). | [[L23]] |
| 84 | **A** | 5.3 | A pruned or missing VLAN on the trunk isolates it to one switch. | [[L23]] |
| 85 | **A** | 5.4 | Choppy/robotic audio = variation in packet arrival (jitter). Latency causes talk-over and echo. | [[L24]] |
| 86 | **B** | 5.4 | Microwave ovens emit around 2.4 GHz. | [[L24]] |
| 87 | **A** | 5.4 | Bandwidth = rated capacity; throughput = actual delivered rate. | [[L24]] |
| 88 | **C** | 5.5 | The MAC address table maps MAC → port (and VLAN). | [[L25]] |
| 89 | **C** | 5.5 | netstat/ss lists listening sockets. ping only proves Layer 3 reachability. | [[L25]] |
| 90 | **D** | 5.5 | An optical time-domain reflectometer measures distance to fiber faults. A TDR is the copper equivalent. | [[L25]] |

---

## Score Sheet

| Domain | Correct | Total | % |
|--------|---------|-------|---|
| 1.0 Networking Concepts | — | 21 | — |
| 2.0 Network Implementation | — | 18 | — |
| 3.0 Network Operations | — | 17 | — |
| 4.0 Network Security | — | 13 | — |
| 5.0 Network Troubleshooting | — | 21 | — |
| **Total** | — | 90 | — |

## Missed Questions / Weak Areas
*(filled in after grading)*
