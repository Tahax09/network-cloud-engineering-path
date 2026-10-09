---
cert: CompTIA Network+
exam_code: N10-009
status: In Progress
target_date: TBD
lessons_completed: 3
---

# CompTIA Network+ N10-009

## Exam Overview
- **Format:** 90 questions, 90 minutes
- **Passing score:** 720/900
- **Question types:** Multiple choice, performance-based (drag-and-drop, simulations)
- **Domains:**

| Domain | Weight | Topic |
|--------|--------|-------|
| 1.0 | 23% | Networking Concepts |
| 2.0 | 20% | Network Implementation |
| 3.0 | 19% | Network Operations |
| 4.0 | 14% | Network Security |
| 5.0 | 24% | Network Troubleshooting |

## Progress by Objective

### Domain 1 — Networking Concepts (23%)

| Objective | Topic | Lesson | Status |
|-----------|-------|--------|--------|
| 1.0 | OSI Model | [[L01 - The OSI Model]] | ✅ Complete |
| 1.2 | Networking Appliances & Applications | [[L02 - Networking Appliances & Applications]] | ✅ Complete |
| 1.0 | Network Virtualization & Cloud | [[L03 - Network Virtualization & Cloud Technologies]] | ✅ Complete |
| 1.x | IP Addressing & Subnetting | — | Pending |
| 1.x | Routing Protocols | — | Pending |
| 1.x | Switching & VLANs | — | Pending |
| 1.x | Wireless Networking | — | Pending |

### Domain 2 — Network Implementation (20%)

| Objective | Topic | Lesson | Status |
|-----------|-------|--------|--------|
| 2.0 | Routing Technologies | [[L09 - Routing Technologies]] | ✅ Complete |
| 2.2 | Switching Technologies | [[L10 - Switching Technologies]] | ✅ Complete |
| 2.3 | Wireless Devices and Technologies | [[L11 - Wireless Devices and Technologies]] | ✅ Complete |
| 2.4 | Physical Installations | [[L12 - Physical Installations]] | ✅ Complete |

### Domain 3 — Network Operations (19%)

| Objective | Topic | Lesson | Status |
|-----------|-------|--------|--------|
| 3.1 | Organizational Processes and Procedures | [[L13 - Organizational Processes and Procedures]] | ✅ Complete |
| 3.2 | Network Monitoring | [[L14 - Network Monitoring]] | ✅ Complete |
| 3.3 | Disaster Recovery | [[L15 - Disaster Recovery]] | ✅ Complete |
| 3.4 | IP Services | [[L16 - IP Services]] | ✅ Complete |
| 3.5 | Network Access and Management | [[L17 - Network Access and Management]] | ✅ Complete |

### Domain 4 — Network Security (14%)

| Objective | Topic | Lesson | Status |
|-----------|-------|--------|--------|
| 4.1 | Basic Network Security Concepts | [[L18 - Basic Network Security]] | ✅ Complete |
| 4.2 | Network Attacks | [[L19 - Network Attacks]] | ✅ Complete |
| 4.3 | Network Defense Techniques | [[L20 - Network Defense Techniques]] | ✅ Complete |

### Domain 5 — Network Troubleshooting (24%)

| Objective | Topic | Lesson | Status |
|-----------|-------|--------|--------|
| 5.1 | Troubleshooting Methodology | [[L21 - Network Troubleshooting Methodology]] | ✅ Complete |
| 5.2 | Cabling and Physical Interface Issues | [[L22 - Troubleshooting Cabling and Physical Issues]] | ✅ Complete |
| 5.3 | Network Service Issues | [[L23 - Network Service Troubleshooting]] | ✅ Complete |
| 5.4 | Network Performance Issues | [[L24 - Troubleshooting Network Performance Issues]] | ✅ Complete |
| 5.5 | Troubleshooting Tools | [[L25 - Troubleshooting Tools]] | ✅ Complete |

## Key Exam Traps (collected from sessions)

| Trap | Correct Answer |
|------|---------------|
| "Connectionless protocol" | UDP (L4), not IP — even though IP is also connectionless |
| "Handles traffic spikes" cloud characteristic | Rapid Elasticity (not On-Demand) |
| Frame destination MAC when sending to remote host | Your default gateway's MAC, not the remote server |
| MPLS VPN = encrypted? | No — MPLS is logical separation, NOT encryption |
| "Community cloud" | Shared by a group/industry, not public or private |
| AWS Security Groups vs Network ACLs | SGs = stateful; ACLs = stateless (need both directions) |
| VPN encrypted by default? | IPsec VPN yes; MPLS VPN no |
| Frame destination when routing | Always the next-hop router's MAC, never the final destination's MAC |
| Longest prefix match vs AD | Longest prefix match is checked FIRST — AD only applies when same prefix from two sources |
| Static NAT vs PAT | Static = 1:1 fixed; PAT = many:1 using ports |
| HSRP vs VRRP | HSRP = Cisco proprietary; VRRP = open standard; both do active/standby |
| EIGRP feasible successor | Pre-computed backup — instant failover. Not all routes have one |
| BGP port | TCP 179; application layer; explicit neighbor config required |
| STP root bridge | Lowest Bridge ID (priority + MAC) wins; default priority = 32768 |
| PortFast + BPDU Guard | Always pair them — PortFast skips STP delay; BPDU Guard shuts port if switch connected |
| Native VLAN | Frames cross trunk untagged; mismatch = security risk + STP issues |
| EtherChannel requirements | Speed, duplex, mode, VLAN, native VLAN, STP must all match on member ports |
| RSTP alternate port | Pre-computed backup to root — instant failover, no re-election |
| Jumbo frames | > 1518 bytes; must be enabled end-to-end on every device in path |
| 2.4 GHz non-overlapping channels | Only 1, 6, 11 — any other channel selection causes interference |
| WPA2-PSK vs WPA2-Enterprise | PSK = shared passphrase (brute-forceable); Enterprise = 802.1X + EAP + RADIUS |
| WPA3 vs WPA2 cipher | WPA3 uses AES-GCMP; WPA2 uses AES-CCMP |
| WPA3 SAE | Prevents offline dictionary attacks — can't crack a captured handshake |
| Autonomous vs lightweight AP | Autonomous = standalone; lightweight = WLAN controller managed (CAPWAP) |
| MDF vs IDF | MDF = carrier/ISP handoff + aggregation; IDF = per-floor, patch panels + switches |
| UPS purpose | Graceful shutdown only — not for extended operation; generator handles that |
| Low humidity risk | Electrostatic discharge (ESD); high humidity risk = condensation/shorts |
| MDF fire suppression | Clean-agent (FM-200, Novec, CO₂) — never water-based near electrical equipment |
| End of Life vs End of Support | EoL = no longer sold; EoS = no more patches — EoS is the security risk |
| Physical vs logical diagram | Physical shows cable connections; logical shows VLANs/IPs — same switch ≠ same network |
| Change management vs config management | Change = process for approving/documenting changes; Config = storing the actual device config |
| Legacy equipment | Past EoS — new CVEs are permanently unpatched; must be replaced or isolated |
| SNMP ports | GET/SET = UDP 161; Trap/Inform = UDP 162 |
| SNMPv1/v2 vs v3 | v1/v2 = clear text community strings; v3 = encrypted + authenticated |
| SNMP Get vs Trap | Get = manager polls agent; Trap = device pushes unsolicited event notification |
| Flow data vs packet capture | Flow = metadata (who talked to whom, how much); Packet capture = full content |
| Syslog UDP port | UDP 514; severity 0 (Emergency) to 7 (Debug) |
| Port Mirroring / SPAN | Copies switch port traffic to a monitor port; does NOT disrupt live traffic |
| NetFlow flow definition | src IP + dst IP + src port + dst port + L3 protocol + ToS + ingress interface |
| Baseline | Defined "normal" profile; compare live metrics to detect anomalies; rebuild after major changes |
| RPO vs RTO | RPO = data loss (how old can restore point be?); RTO = downtime (how long can systems be down?) |
| MTBF vs MTTR | MTBF = uptime between failures (maximize); MTTR = repair time (minimize) |
| Hot vs warm vs cold site | Hot = live mirror (fast/expensive); Warm = hardware ready, data not live (medium); Cold = empty space (slow/cheap) |
| Active-Active vs Active-Passive | Active-Active = all nodes process live traffic; Active-Passive = standby node waits for failure |
| Tabletop vs validation test | Tabletop = discussion walkthrough, no systems moved; Validation = real-world failover/restore test |
| DHCP reservation vs exclusion | Reservation = always assigns that IP to a specific MAC; Exclusion = never assigns that IP from the pool |
| DHCP Discover broadcast | Discover is broadcast — won't cross routers without a relay agent (ip helper-address) |
| PTR record format | Reverse octet order + `.in-addr.arpa` (e.g., 100.1.168.192.in-addr.arpa for 192.168.1.100) |
| CNAME vs A record | CNAME is an alias pointing to another name; A record maps name to IP — CNAME can't be at zone apex |
| MX priority | Lower MX priority number = preferred mail server (try lowest first) |
| DNSSEC vs DoH/DoT | DNSSEC = signs records for authenticity verification; DoH/DoT = encrypts DNS traffic in transit |
| DoH port vs DoT port | DoH = port 443 (inside HTTPS); DoT = port 853 (dedicated TLS) |
| NTP vs PTP | NTP = UDP 123, millisecond accuracy; PTP = UDP 319/320, sub-microsecond for finance/5G |
| /etc/hosts file | Checked before DNS; static entries; loopback always present (127.0.0.1 localhost) |
| Authoritative vs non-authoritative DNS | Authoritative = from the actual name server; non-authoritative = from a resolver's cache |
| Site-to-site vs client-to-site VPN | Site-to-site = gateway-to-gateway (IPsec); client-to-site = individual device to corp (TLS or IPsec) |
| Clientless VPN | Browser-based TLS VPN portal; no software install; limited to web traffic |
| Full tunnel vs split tunnel | Full = all traffic through VPN (more secure); Split = only corp traffic through VPN (less bandwidth, more risk) |
| IPsec AH vs ESP | AH = authentication only (no encryption); ESP = authentication + encryption (use ESP) |
| IPsec tunnel mode vs transport mode | Tunnel = wraps entire packet (hides IPs, used for site-to-site); Transport = preserves original IP headers |
| Jump box / bastion host | Single hardened public entry point to internal resources; concentrate monitoring here |
| In-band vs out-of-band management | In-band = mgmt traffic shares user data path; Out-of-band = dedicated management VLAN/network |
| Password + PIN = MFA? | No — both "something you know"; MFA needs two *different* factor types |
| Non-repudiation | Requires signing with sender's private key; symmetric (AES) can't provide it |
| RADIUS vs TACACS+ | RADIUS = UDP 1812/1813, encrypts password only, network access; TACACS+ = TCP 49, full encryption, per-command authZ, device admin |
| LDAP vs LDAPS port | LDAP = TCP 389; LDAPS = TCP 636 |
| PCI DSS a law? | No — industry standard enforced contractually by card brands/banks |
| GDPR scope | Any org processing EU residents' personal data, wherever the org is located; opt-in consent |
| Cameras as a control | Detective/audit — don't prevent access |
| Honeypot vs honeynet | Honeypot = single decoy system; honeynet = whole decoy network |
| MAC flooding defense | Port security (limit MACs per port) — not DHCP snooping |
| ARP poisoning defense | Dynamic ARP Inspection (uses DHCP snooping binding table) |
| Rogue DHCP defense | DHCP snooping (trusted/untrusted ports) |
| Double tagging vs switch spoofing | Double tag = native VLAN abuse, one-way → change/tag native VLAN; switch spoofing = DTP trunk → `nonegotiate` |
| Reflected vs amplified | Reflected = spoofed source sends replies to victim; amplified = response much larger than request (DNS, NTP, memcached) |
| Evil twin vs rogue AP | Rogue AP = any unauthorized AP; evil twin = rogue AP impersonating legit SSID |
| SSL stripping defense | HSTS |
| Tailgating vs piggybacking | Tailgating = without consent; piggybacking = victim knowingly lets you in |
| Whaling vs spear phishing | Whaling = spear phishing targeting executives (subset) |
| Virus vs worm vs trojan | Virus needs execution; worm self-propagates; trojan = disguised delivery |
| 802.1X roles | Supplicant = client; authenticator = switch/AP; authentication server = RADIUS |
| Unauthorized 802.1X port passes… | EAPOL only |
| MAB / MAC filtering weakness | MACs sent in cleartext → easily spoofed/cloned |
| Port security defaults | Max 1 MAC; violation = shutdown (err-disabled); protect = silent drop |
| ACL processing | Top-down, first match wins, implicit deny at end — deny-only ACL blocks everything |
| Wildcard mask | Inverse of subnet mask: /24 → 0.0.0.255 |
| Standard vs extended ACL placement | Standard near destination; extended near source |
| URL vs content filtering | URL = where you go; content = what's inside (needs proxy/TLS inspection; DLP) |
| Drop vs reject | Drop = silent (reveals nothing); reject = ICMP unreachable/RST back |
| Screened subnet | Former "DMZ" — public services isolated from trusted internal network |
| Key escrow | Stored key copy for recovery (e.g., lost BitLocker key) |
| 802.1X vs 802.1Q | X = port authentication (NAC); Q = VLAN tagging |
| Dictionary vs brute force | Wordlist = dictionary; every combination = brute force; rainbow table defeated by salting |
| Vulnerability notice vs EoS | Patch available → patch; vendor no longer supports → replace |
| Troubleshooting step order | Identify → Theory → Test → Plan → Implement/escalate → Verify + preventive → Document |
| Theory not confirmed → next? | Establish new theory OR escalate (not implement) |
| Theory confirmed → next? | Establish plan of action (rollback, effects on other systems) |
| "What changed?" / duplicate issue | Step 1 — Identify the problem |
| Preventive measures | Step 6 (with verify), not step 4 or 7 |
| Reachable by IP, not by name | DNS |
| Speed vs duplex mismatch | Speed mismatch = link DOWN; duplex mismatch = link UP but slow + errors |
| Duplex mismatch counters | Half side = late collisions; full side = CRC/FCS + runts |
| Runt vs giant | Runt < 64 bytes; giant > 1518 bytes (giants → MTU/jumbo mismatch) |
| Cat5 for gigabit? | No — Cat5e minimum for 1 Gbps |
| EMI vs attenuation | EMI = near motors/power (copper only, fix: reroute/fiber); attenuation = too long (both media) |
| Err-disabled causes | Port security violation, BPDU guard, link flapping; recover with shut/no shut or errdisable recovery |
| PoE standards | 802.3af 15.4 W; 802.3at PoE+ 30 W; 802.3bt 60–100 W |
| ST connector | Bayonet (twist-lock); SC/LC push-pull; LC = duplex in one connector |
| STP root election | Lowest Bridge ID (priority 32768 default + MAC) → lowest MAC wins if untuned; set priority on core |
| RSTP states vs roles | States: discarding/learning/forwarding. Roles: root/designated/alternate/backup; all root-bridge ports = designated |
| Static IP on wrong VLAN vs DHCP on wrong VLAN | Static = nothing reachable; DHCP = gets other VLAN's IP |
| Internet unreachable, internal OK | Router: missing default route (0.0.0.0/0). Single host: wrong default gateway |
| APIPA 169.254.x.x | DHCP failed — pool exhausted, ACL blocking UDP 67/68, wrong VLAN, no relay |
| DHCP exhaustion fix | Shorten lease, expand scope, clear stale leases |
| Wrong subnet mask | Host misjudges on-net vs off-net → ARPs for remote hosts or routes local ones |
| Subnet range | Defined by mask, not third octet (/22 = 192.168.100.0–103.255) |
| Bandwidth vs throughput | Bandwidth = max capacity (tech or contract); throughput = actual delivered rate |
| 1 Gbps port, 200 Mbps delivered | Contracted CIR, not interface speed |
| Latency vs jitter vs loss (VoIP) | Talk-over/echo = latency (≤150 ms); choppy/robotic = jitter (<30 ms); gaps = loss (<1%) |
| Wi-Fi access method | CSMA/CA (avoidance); wired half-duplex = CSMA/CD (detection) |
| Legacy client slowing whole AP | Disable low data rates (1/2/5.5/11 Mbps) — airtime hog |
| Microwave interference | 2.4 GHz; non-overlapping channels 1/6/11 |
| 2.4 vs 5 vs 6 GHz | 2.4 = best range/penetration, slowest; 6 = fastest, shortest range |
| Calls drop walking between APs | Roaming misconfig (association instead of reassociation, VLAN mismatch) → 802.11r |
| Sticky client | Client refuses to roam from weak AP; 802.11k/v help |
| ping vs service check | ping = L3 only (ICMP); service up? → netstat/ss on server, nmap/nc from outside |
| traceroute mechanism | Increasing TTL → ICMP Time Exceeded; Windows tracert = ICMP, Linux traceroute = UDP |
| `* * *` mid-path, later hops OK | Hop filters probes — not a failure |
| Nmap filtered vs closed | Filtered = firewall dropping; closed = host RST, nothing listening |
| CDP vs LLDP | CDP = Cisco proprietary; LLDP = vendor-neutral 802.1AB |
| Toner vs cable tester | Toner = find/trace which cable; tester = verify wiring (open/short/cross) |
| TDR vs OTDR vs VFL | TDR = copper fault distance; OTDR = fiber fault distance/loss; VFL = red light shows fiber break |
| TAP vs SPAN | TAP = passive hardware, no drops, errored frames; SPAN = switch mirror, may drop |
| Find device from IP | show ip arp (IP→MAC) → show mac address-table (MAC→port) |
| running vs startup config | Running = RAM (current); startup = NVRAM (boot) — unsaved changes lost on reload |
| Windows vs Linux ping | Windows = 4 packets (-n); Linux = continuous (-c) |
| New fiber link, no light — try FIRST | Reverse the fibers (TX/RX polarity) before re-terminating |
| New NIC → server unreachable | Coursera: NOT port security — likely cable type mismatch with faster NIC (Cat6a / fiber) |
| "Check each OSI level is functioning" | Verify Functionality (step 6) — not Establish a theory |
| No link lights either end, cable seated | Switch port administratively shut down / err-disabled |
| Hot aisle / cold aisle | Equipment intake front (cold), exhaust rear (hot) |
| 2.4 GHz channel width (dense) | 20 MHz — only 3 non-overlapping channels |

## Weak Areas to Revisit
*(populated as sessions progress)*

| Area | Flagged In | Priority |
|------|-----------|----------|
| Methodology: "check each OSI layer works" = Verify functionality, not theory | NQ06 Q13 | High |
| Methodology: step 1 sub-task single-select questions (identify symptoms vs gather info) | NQ06 Q16 | Medium |
| NIC upgrade → cable/interface mismatch (not port security) | NQ06 Q11 | Medium |

## Study Resources
- Coursera: CompTIA Network+ N10-009 (transcript-based study)
- Professor Messer (recommended for practice tests)
- raw/transcripts/ — all lesson transcripts

## Notes
- Connect every concept to the k3s lab — you're running L2–L7 networking live
- Use OSI-based troubleshooting in every scenario question (bottom-up or top-down)
- Performance-based questions will show network diagrams — know device placement cold
