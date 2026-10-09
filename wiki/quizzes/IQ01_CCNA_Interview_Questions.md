---
id: IQ01
title: CCNA-Level Interview Questions — Self-Test
course: Interview prep (CCNA / Network+ overlap)
covers: L01–L25 (+ topics beyond Network+)
date: 2026-10-09
status: Q1–Q80 filed; Q81–Q100 pending (source paste truncated)
questions: 80
source: "Top 100 CCNA Interview Questions for Network Engineers (2026)" (third-party article). Questions paraphrased; answers are condensed and written fresh, with corrections where the source is wrong or outdated
---

# IQ01 — CCNA-Level Interview Questions (Self-Test)

> **How to use:** cover the *Short answer* column and answer out loud, as in an interview. Then check yourself and follow the lesson link. ⚠️ marks a correction to the source article. 🆕 marks a topic **beyond Network+**: CCNA-depth material not yet in the wiki (see *Gaps* at the bottom).

---

## 1. OSI & Fundamentals (Q1–Q10)

| # | Question | Short answer | Lesson |
|---|----------|--------------|--------|
| 1 | Seven OSI layers? | Physical, Data Link, Network, Transport, Session, Presentation, Application. PDUs: bits, frames, packets, segments, data. Switch = L2 (L3 if it routes); router = L3 | [[L01 - The OSI Model]] |
| 2 | TCP vs UDP? | TCP: handshake, ACKs, retransmission, ordering. UDP: no handshake, no reliability, low overhead (DNS queries, VoIP, DHCP, SNMP) | [[L04 - Ports, Protocols & Services]] |
| 3 | TCP three-way handshake? | SYN → SYN-ACK → ACK (exchange initial sequence numbers). Teardown FIN/ACK in each direction | [[L04 - Ports, Protocols & Services]] |
| 4 | Type a URL and press Enter: what happens? | Cache → DNS (UDP 53) → ARP → TCP handshake to 443 → TLS handshake → HTTP GET → response. ⚠️ The source says ARP is involved "if the destination is on the same subnet." **ARP happens every time**: for a remote server, the host ARPs for its **default gateway** | [[L23 - Network Service Troubleshooting]] |
| 5 | What is ARP? | Maps IP → MAC within a broadcast domain: broadcast request, unicast reply, cached. Routers don't forward ARP | [[L07 - IP Addressing]] |
| 6 | Broadcast vs collision domain? | Each switch port = its own collision domain; a switch (per VLAN) = one broadcast domain; router interfaces and VLANs separate broadcast domains | [[L10 - Switching Technologies]] |
| 7 | Hub vs switch vs router? | Hub L1 repeats to all ports (half-duplex); switch L2 forwards by MAC; router L3 forwards by IP between networks | [[L02 - Networking Appliances & Applications]] |
| 8 | Encapsulation? | Each layer adds a header (+ L2 trailer) on the way down; the receiver strips them on the way up | [[L01 - The OSI Model]] |
| 9 | MAC address structure? | 48 bits: 24-bit **OUI** (vendor) + 24-bit device ID; broadcast FF:FF:FF:FF:FF:FF; rewritten at every L3 hop. (Note: MACs *can* be changed/randomized, so they aren't truly fixed) | [[L07 - IP Addressing]] |
| 10 | Full vs half duplex? | Half: send *or* receive, CSMA/CD. Full: both at once, no collisions. Mismatch = link up, slow, CRC + late collisions | [[L22 - Troubleshooting Cabling and Physical Issues]] |

## 2. IP Addressing & Subnetting (Q11–Q20)

| # | Question | Short answer | Lesson |
|---|----------|--------------|--------|
| 11 | IPv4 classes, private ranges? | A 1–126 /8, B 128–191 /16, C 192–223 /24, D 224–239 multicast, E reserved. Private 10/8, 172.16/12, 192.168/16; loopback 127/8; APIPA 169.254/16 | [[L07 - IP Addressing]] |
| 12 | Subnet a /24 for ≥30 hosts? | 2⁵−2 = 30 → **/27** (255.255.255.224), 8 subnets in blocks of 32 (.0, .32, .64…) | [[L07 - IP Addressing]] |
| 13 | VLSM? | Different mask sizes per subnet to avoid waste (100/50/10 hosts → /25, /26, /28). Needs classless protocols (OSPF, EIGRP, RIPv2, BGP) | [[L07 - IP Addressing]] |
| 14 | CIDR vs classful? | Any prefix /0–/32 (1993); enables summarization/supernetting | [[L07 - IP Addressing]] |
| 15 | Network/broadcast/range for 172.16.45.14/20? | Block 16 in the 3rd octet → network **172.16.32.0**, broadcast **172.16.47.255**, hosts .32.1–.47.254 (4,094) | [[L07 - IP Addressing]] |
| 16 | Route summarization? | Advertise one aggregate (10.1.0.0/24–10.1.15.0/24 → 10.1.0.0/20). Smaller tables, faster convergence; risk: the summary attracts traffic for down subnets | [[L09 - Routing Technologies]] |
| 17 | Loopback interface? | Software interface, always up; stable OSPF/BGP router ID and management address; usually /32 | 🆕 |
| 18 | Unicast / multicast / broadcast? | One-to-one / one-to-group (224.0.0.0/4; OSPF 224.0.0.5/.6) / one-to-all (255.255.255.255 limited, subnet-directed) | [[L04 - Ports, Protocols & Services]] |
| 19 | /30 usage? | 2 usable hosts, point-to-point links; /31 (RFC 3021) also valid for P2P; /32 = host route/loopback | [[L07 - IP Addressing]] |
| 20 | Purpose of the subnet mask? | IP AND mask = network; decides on-net (ARP the host) vs off-net (send to the gateway) | [[L23 - Network Service Troubleshooting]] |

## 3. VLANs & Switching (Q21–Q30)

| # | Question | Short answer | Lesson |
|---|----------|--------------|--------|
| 21 | Why VLANs? | Separate broadcast domains on shared hardware; security isolation; less broadcast traffic (802.1Q) | [[L10 - Switching Technologies]] |
| 22 | How does 802.1Q trunking work? | 4-byte tag after the source MAC; 12-bit VLAN ID (1–4094 usable); native VLAN untagged | [[L10 - Switching Technologies]] |
| 23 | How does a switch learn MACs? | Records the **source** MAC + port in the CAM table; unknown unicast floods; aging 300 s (Cisco) | [[L10 - Switching Technologies]] |
| 24 | Why STP? | Prevents L2 loops → broadcast storms and MAC flapping; blocks redundant paths; RSTP/Rapid PVST+ converges in ~1–2 s vs 30–50 s | [[L23 - Network Service Troubleshooting]] |
| 25 | Root bridge election? | Lowest Bridge ID = priority (32768 + VLAN ID in PVST+) + MAC; set `spanning-tree vlan X root primary` or priority 4096 | [[L23 - Network Service Troubleshooting]] |
| 26 | PortFast? | Edge ports skip listening/learning; only on host ports; pair with **BPDU Guard** (err-disable if a BPDU arrives) | [[L10 - Switching Technologies]] |
| 27 | EtherChannel protocols? | LACP (802.3ad, active/passive) vs PAgP (Cisco, desirable/auto); members must match speed, duplex, VLAN, trunk settings | [[L10 - Switching Technologies]] |
| 28 | Inter-VLAN routing on an L3 switch? | **SVI** per VLAN as the gateway + `ip routing`; hardware-speed routing. Alternative: router-on-a-stick (subinterfaces, can bottleneck) | [[L09 - Routing Technologies]] |
| 29 | DTP: leave it on? | No. Attackers can negotiate a trunk (switch spoofing). Hard-code access/trunk + `switchport nonegotiate` | [[L19 - Network Attacks]] |
| 30 | DHCP snooping? | Trusted/untrusted ports; untrusted can't send OFFER/ACK; builds the binding table used by DAI and IP Source Guard | [[L20 - Network Defense Techniques]] |

## 4. Routing Protocols (Q31–Q40)

| # | Question | Short answer | Lesson |
|---|----------|--------------|--------|
| 31 | Administrative distance? | Trust of a route source; lowest wins for the same prefix. Connected 0, static 1, eBGP 20, EIGRP 90, OSPF 110, IS-IS 115, RIP 120, iBGP 200 | [[L09 - Routing Technologies]] |
| 32 | Distance-vector vs link-state? | DV: routes learned from neighbors, loop prevention (split horizon, poisoning). LS (OSPF/IS-IS): flood LSAs, full LSDB, Dijkstra SPF, faster convergence | [[L09 - Routing Technologies]] |
| 33 | OSPF DR/BDR election? | Highest priority (default 1; 0 = never), then highest router ID; **non-preemptive** | 🆕 |
| 34 | OSPF cost? | Reference BW (100 Mbps default) / interface BW, so FE and GE both = 1. Fix: `auto-cost reference-bandwidth 10000` on all routers | 🆕 |
| 35 | OSPF neighbor states? | Down → Init → 2-Way (DR election; DROthers stay here) → ExStart → Exchange → Loading → **Full** | 🆕 |
| 36 | EIGRP successor / feasible successor? | Successor = best path. FS = backup whose **reported distance < successor's feasible distance** (loop-free). Instant failover; no FS → queries | 🆕 |
| 37 | Floating static route? | Static route with a higher AD (e.g., 250), used only when the dynamic route disappears; WAN backup | [[L23 - Network Service Troubleshooting]] |
| 38 | Best path selection? | Longest prefix match → lowest AD → lowest metric; ECMP for equal-cost routes | [[L09 - Routing Technologies]] |
| 39 | BGP basics? | Path-vector between ASes, **TCP 179**; eBGP AD 20, iBGP 200; ISP edge, multihoming, SD-WAN | [[L09 - Routing Technologies]] |
| 40 | Verify routes / OSPF? | `show ip route [ospf]`, `show ip ospf neighbor`, `show ip ospf interface brief`, `show ip eigrp topology` | [[L25 - Troubleshooting Tools]] |

## 5. Network Services (Q41–Q50)

| # | Question | Short answer | Lesson |
|---|----------|--------------|--------|
| 41 | DHCP DORA? | Discover (broadcast) → Offer → Request (broadcast) → Ack (IP, mask, gateway, DNS, lease). ⚠️ The source says the client ARP-probes "before accepting." Per RFC 2131 the client probes **after the ACK** (and sends DHCPDECLINE on conflict); servers often ping-check **before the Offer** | [[L16 - IP Services]] |
| 42 | DHCP relay? | `ip helper-address` turns the broadcast into unicast to the server on another subnet | [[L16 - IP Services]] |
| 43 | DNS resolution path? | Client → recursive resolver → root → TLD → authoritative → cache for TTL | [[L16 - IP Services]] |
| 44 | NAT types? | Static 1:1, dynamic (pool), **PAT/overload** (many → one IP by port) | [[L09 - Routing Technologies]] |
| 45 | NTP modes / stratum? | Client/server (can be both); stratum 0 reference → 1 → 2…; matters for logs, certs, auth; UDP 123 | [[L16 - IP Services]] |
| 46 | Common ports? | HTTP 80, HTTPS 443, SSH 22, DNS 53, DHCP 67/68, SNMP 161/162, SMTP 25, FTP 20/21 (+ Telnet 23, RDP 3389, LDAP 389, Syslog 514, NTP 123) | [[L04 - Ports, Protocols & Services]] |
| 47 | SNMP versions? | v1/v2c plaintext community strings; **v3** auth + encryption; UDP 161 queries / 162 traps; MIBs/OIDs | [[L14 - Network Monitoring]] |
| 48 | Syslog on IOS? | Severity 0 Emergency … 7 Debug; `logging host`, `logging trap`, `service timestamps log datetime msec` | [[L14 - Network Monitoring]] |
| 49 | FTP active vs passive? | Active: server opens data from port 20 to the client (firewalls block it). Passive: the client opens both. Secure options: SFTP (22) / FTPS | 🆕 |
| 50 | QoS models? | Best effort / IntServ (RSVP, per-flow) / **DiffServ** (DSCP marking per hop). Classify → mark → queue → police/shape; voice ≤150 ms | [[L24 - Troubleshooting Network Performance Issues]] |

## 6. WAN & VPN (Q51–Q60)

| # | Question | Short answer | Lesson |
|---|----------|--------------|--------|
| 51 | Leased line vs MPLS vs broadband? | Dedicated P2P / provider label-switched private WAN with SLAs / shared, cheap, variable; SD-WAN makes broadband enterprise-grade | [[L08 - Modern Networks]] |
| 52 | GRE? | Encapsulates any L3 protocol, carries multicast (routing protocols), **24 bytes** overhead; usually GRE over IPsec for encryption (modern alternative: IPsec VTI) | 🆕 |
| 53 | IPsec protocols? | **AH** = integrity/auth, no encryption, breaks with NAT. **ESP** = encryption + integrity, works with NAT-T (UDP 4500). IKE phase 1 (management SA) → phase 2 (data SAs) | [[L17 - Network Access and Management]] |
| 54 | Transport vs tunnel mode? | Transport keeps the original IP header (host-to-host); tunnel wraps the whole packet (site-to-site) | [[L17 - Network Access and Management]] |
| 55 | PPPoE? | PPP over Ethernet for DSL authentication; MTU **1492**. ⚠️ The source attributes this to a "6-byte PPPoE header." The full overhead is **8 bytes** (6 PPPoE + 2 PPP protocol ID) | 🆕 |
| 56 | SD-WAN? | Separated control/data plane, central policy, **application-aware path selection** based on latency, jitter, loss | [[L08 - Modern Networks]] |
| 57 | DMVPN? | Hub-and-spoke with on-demand spoke-to-spoke tunnels via **NHRP** + mGRE + IPsec | 🆕 |
| 58 | Site-to-site vs remote access VPN? | Gateway-to-gateway, transparent to users / per-user client or clientless SSL (443) | [[L17 - Network Access and Management]] |
| 59 | MTU problems in tunnels? | Headers shrink usable MTU (~1400 for GRE+IPsec); blocked ICMP 3/4 "frag needed" → black holes; fix with `ip tcp adjust-mss 1360` | [[L22 - Troubleshooting Cabling and Physical Issues]] |
| 60 | HSRP? | Virtual IP/MAC shared by Active/Standby routers; VRRP = open standard; GLBP = load-balancing | [[L09 - Routing Technologies]] |

## 7. Wireless (Q61–Q70)

| # | Question | Short answer | Lesson |
|---|----------|--------------|--------|
| 61 | 802.11 standards? | b 2.4/11 Mbps, a 5/54, g 2.4/54, n (Wi-Fi 4) 2.4+5, ac (Wi-Fi 5) 5 GHz, ax (Wi-Fi 6/6E) 2.4/5/6 + OFDMA. ⚠️ The source (dated 2026) omits **802.11be / Wi-Fi 7** (320 MHz channels, MLO, 4K-QAM) and 802.11a | [[L11 - Wireless Devices and Technologies]] |
| 62 | CSMA/CA vs CSMA/CD? | Wi-Fi avoids collisions (listen, random backoff, RTS/CTS for hidden nodes); wired half-duplex detects them | [[L24 - Troubleshooting Network Performance Issues]] |
| 63 | 2.4 vs 5 GHz? | 2.4: range, penetration, 3 channels (1/6/11), crowded. 5: faster, many channels, shorter range. 6 GHz: cleanest, shortest | [[L24 - Troubleshooting Network Performance Issues]] |
| 64 | Wireless security protocols? | WEP broken → WPA/TKIP weak → WPA2 AES-CCMP (PSK/Enterprise) → **WPA3** SAE + 192-bit Enterprise | [[L11 - Wireless Devices and Technologies]] |
| 65 | Autonomous vs controller-based AP? | Standalone config vs lightweight APs managed by a WLC over **CAPWAP** (UDP 5246 control / 5247 data) | [[L11 - Wireless Devices and Technologies]] |
| 66 | BSS / SSID / BSSID / ESS? | AP + clients / network name / radio MAC per SSID / multiple APs with the same SSID, enabling roaming | [[L11 - Wireless Devices and Technologies]] |
| 67 | 802.1X for wireless? | Supplicant → authenticator (AP/WLC) → RADIUS; EAP-TLS (certs, strongest), PEAP; per-user credentials and VLAN assignment | [[L20 - Network Defense Techniques]] |
| 68 | Interference mitigation? | Co-channel and adjacent-channel planning (1/6/11), move to 5/6 GHz, spectrum analyzer for non-Wi-Fi sources | [[L24 - Troubleshooting Network Performance Issues]] |
| 69 | Site survey? | Passive (listen) vs active (measure throughput) vs predictive; for new builds, dense or obstructed areas, complaints | [[L24 - Troubleshooting Network Performance Issues]] |
| 70 | FlexConnect vs central switching? | Central: all data tunneled to the WLC. FlexConnect: branch AP switches locally and survives a WAN outage | 🆕 |

## 8. Security (Q71–Q80)

| # | Question | Short answer | Lesson |
|---|----------|--------------|--------|
| 71 | Standard vs extended ACL? | Standard: source only, place near the destination (1–99, 1300–1999). Extended: src/dst/proto/port, place near the source (100–199, 2000–2699). Implicit deny | [[L20 - Network Defense Techniques]] |
| 72 | Firewall vs IDS vs IPS vs NGFW? | Stateful filtering / detect + alert (out-of-band) / inline block / all-in-one + app ID, URL, TLS inspection | [[L02 - Networking Appliances & Applications]] |
| 73 | AAA? | Authentication, authorization, accounting; RADIUS for network access, TACACS+ for device admin | [[L18 - Basic Network Security]] |
| 74 | RADIUS vs TACACS+? | RADIUS UDP 1812/1813, password-only encryption, combined authN/authZ. TACACS+ TCP 49, full encryption, per-command authZ. ⚠️ The source says TACACS+ is "Cisco proprietary." It was **published as RFC 8907 (2020)**, so it's now an open, informational standard, though Cisco-originated | [[L18 - Basic Network Security]] |
| 75 | A Layer 2 attack + prevention? | MAC flooding → port security; VLAN hopping → nonegotiate + unused native VLAN; ARP spoofing → DAI | [[L19 - Network Attacks]] |
| 76 | Harden a Cisco switch? | Shut unused ports, SSHv2 only, `enable secret`, AAA/TACACS+, SNMPv3, VTY timeouts, CDP off at the edge, DHCP snooping + DAI, PortFast + BPDU Guard, OOB management | [[L20 - Network Defense Techniques]] |
| 77 | Port security violation modes? | Shutdown (default, err-disabled) / Restrict (drop + log + counter) / Protect (silent drop) | [[L20 - Network Defense Techniques]] |
| 78 | CIA triad? | Confidentiality (encryption, access control) / Integrity (hashing, signatures) / Availability (redundancy, DoS protection) | [[L18 - Basic Network Security]] |
| 79 | Symmetric vs asymmetric? | Same key, fast (AES) vs key pair, slow (RSA, ECC); TLS uses asymmetric to set up a symmetric session key. ⚠️ The source lists **Diffie-Hellman** as asymmetric *encryption*: it's a **key-exchange** algorithm, it encrypts nothing. It also lists DES/3DES without noting they're **deprecated**, and says a public key "can only encrypt" (with signatures, the public key *verifies*) | [[L18 - Basic Network Security]] |
| 80 | What makes a VPN secure? | Confidentiality (AES-256), integrity (SHA-256+, not MD5), authentication (PSK/certs), anti-replay (sequence numbers); DH derives keys for IKE | [[L17 - Network Access and Management]] |

## 9–10. Troubleshooting & Management (Q81–Q100)
*Pending: the pasted article was cut off at Q81. Paste the rest to complete this section.*

---

## Gaps: CCNA Topics Not Yet in the Wiki (🆕)

These come up in interviews and on the CCNA but go beyond Network+ depth:
| Topic | Questions |
|-------|-----------|
| OSPF internals: DR/BDR, cost/reference bandwidth, neighbor states, loopback router IDs | 17, 33, 34, 35 |
| EIGRP DUAL: successor / feasible successor / feasibility condition | 36 |
| GRE, DMVPN/NHRP | 52, 57 |
| PPPoE / MTU 1492 | 55 |
| FTP active vs passive | 49 |
| Wireless FlexConnect vs central switching | 70 |

**Suggested:** if you plan to take the CCNA after Network+, these become lesson pages. Otherwise, learn them as interview talking points.
