# Activity Log

<!-- Format: ## [YYYY-MM-DD] action | description -->

## [2026-10-09] refactor | Quizzes split into per-course folders
Moved quizzes into wiki/quizzes/network-plus/ (NQ01–NQ06, PE01, WD01), wiki/quizzes/gcp/ (GQ01), wiki/quizzes/interview/ (IQ01). Split quiz tables in index.md and README.md by course. Updated CLAUDE.md structure (one subfolder per course, never mix).

## [2026-10-09] create | IQ01 — CCNA-level interview questions self-test (Q1–Q80)
Filed a third-party "Top 100 CCNA Interview Questions (2026)" list as a self-test: questions paraphrased, answers condensed and written fresh, each linked to its wiki lesson. Corrected source errors (ARP always used — gateway for remote hosts; DHCP client ARP probe happens after ACK; PPPoE overhead 8 bytes; TACACS+ is RFC 8907; Diffie-Hellman is key exchange not encryption; missing Wi-Fi 7). Listed CCNA-depth gaps not yet in wiki (OSPF internals, EIGRP DUAL, GRE/DMVPN, PPPoE, FTP modes, FlexConnect). Q81–Q100 pending — source paste truncated.

## [2026-10-09] refactor | Lesson files renamed to "ID - Title.md"
Renamed all 25 Network+ and 11 GCP lesson files from bare IDs (L01.md, GCP08.md) to "<ID> - <Title>.md" (e.g. "L01 - The OSI Model.md", "GCP08 - Private Google Access.md"). Added aliases: [ID] to each lesson's frontmatter. Rewrote all wikilinks and markdown links across the wiki (log history left as-is). Fixed pre-existing broken [[NQ04]] links and [[GCP05]] → [[gcp-geolocation-lab]]. Updated CLAUDE.md naming convention.

## [2026-10-09] ingest | GCP07–GCP12 — Private Connection Options module + GQ01 quiz
Ingested six GCP transcripts. GCP07 overview (option map, PSC vs PSA). GCP08 Private Google Access (per-subnet, external-IP rule, on-prem via VPN + DNS + Cloud Router custom advertisement + default-internet-gateway route, private vs restricted VIPs, no partial overrides, IPv6 /96). GCP09 Private Service Connect (endpoints/backends, service attachments, PSC NAT subnet, overlapping IPs, consumer-initiated, PSC interface + network attachment transitive, service connection policies, caveats). GCP10 Private Services Access (peering + allocated range, Service Networking API, one range per producer, disable ≠ delete, on-prem custom route export). GCP11 Cloud NAT (distributed outbound-only, Cloud Router control plane, manual vs auto IPs, PGA interplay, public vs private/inter-VPC NAT, port exhaustion). GCP12 module debrief. New concept page gcp-private-access. GQ01 graded quiz (3 Q) saved with answer key. Corrected transcript: PSC needs no route import/export (peering concept), regional endpoints = in-transit residency, PSC consumer chooses its own endpoint IP, region not zone. Raw transcripts stored in raw/transcripts/gcp/.

## [2026-10-08] create | PE01 practice exam + WD01 weak-area drill
Generated PE01 — 90 original questions across all N10-009 objectives, weighted to official domain split (23/20/19/14/24 → 21/18/17/13/21 Q), 90-minute format, command-output exhibits, answer key with objective + lesson links, per-domain score sheet. Generated WD01 — 12-question drill targeting NQ06 misses (verify vs establish theory, step 1 sub-tasks, NIC upgrade cable type). Corrected cert page domain weights (Domain 4 = 14%, Domain 5 = 24%; were 16%/22%).

## [2026-10-08] grade | NQ06 — Network Troubleshooting scored 17/20
Missed Q11 (port security — wrong; likely cable type mismatch, unconfirmed), Q13 (establish theory — wrong; correct by elimination: verify functionality), Q16 (ambiguous single-select, correct option not revealed). Q14 "Document" confirmed. Updated NQ06 answer key, exam traps, cert page weak areas, and L21 weak areas.

## [2026-10-08] quiz | NQ06 — Network Troubleshooting (Coursera graded, 20 questions)
Saved Module 5 graded quiz with answer key and reasoning. Covers L21–L25 plus L11/L12 crossovers. Key topics: fiber polarity, cross-connect TX/RX, DHCP exhaustion, missing default route, toner, PoE budget, duplex counters, nmap, port security after NIC swap, admin-down port, methodology steps, jitter, tracert, broadcast storm, 2.4 GHz 20 MHz channels, hot/cold aisle. Flagged Q14 (verify not offered) and Q16 (all options are step 1 sub-tasks) as flawed/ambiguous. Added 5 exam traps to cert page.

## [2026-10-08] ingest | L25 — Troubleshooting Tools (CompTIA Network+ N10-009, Objective 5.5)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L25.md covering protocol analyzers (Wireshark, TLS decryption, statistics), ping (Windows vs Linux options), traceroute/tracert (TTL + ICMP Time Exceeded, UDP vs ICMP probes, pathping/mtr), nslookup/dig, tcpdump/tshark, ip/ifconfig/ipconfig, netstat/ss, arp, Nmap (open/closed/filtered), CDP vs LLDP, iperf/speed tests, hardware tools (toner, cable tester/certifier, TAP vs SPAN, Wi-Fi analyzer, TDR, OTDR, VFL), and Cisco show commands (mac address-table, ip route, interfaces, running vs startup config, vlan, ip arp, power inline). Added "which tool?" cheat sheet and IP→MAC→port workflow. Corrected transcript: netstat port 53 = DNS (not DHCP), toner generator/probe roles reversed, TAPs not legacy, Linux traceroute uses UDP. Added 12 exam traps. Completes Domain 5 and all Network+ N10-009 objectives (L01–L25).

## [2026-10-08] ingest | L24 — Troubleshooting Network Performance Issues (CompTIA Network+ N10-009, Objective 5.4)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L24.md covering congestion/contention (CSMA/CD vs CSMA/CA, airtime, disabling low data rates), bottlenecks, bandwidth vs throughput (contracted CIR), QoS metrics (latency ≤150 ms, jitter <30 ms, loss <1%, RTP/UDP, jitter buffer, DSCP EF/LLQ), wireless interference (microwave, co-channel, SNR, noise floor), signal degradation (absorption, 2.4/5/6 GHz trade-offs, RSSI −67 dBm), insufficient coverage (site survey, heat maps, TX power), roaming (reassociation vs association), disassociation, sticky clients, and 802.11k/v/r/w. Added symptom→cause cheat sheet and shape-to-CIR QoS scenario. Added 10 exam traps to cert page.

## [2026-10-08] ingest | L23 — Network Service Troubleshooting (CompTIA Network+ N10-009, Objective 5.3)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L23.md covering STP faults (root bridge election by Bridge ID, 802.1D vs RSTP states/roles, path cost, broadcast storms), incorrect VLAN assignment (static vs DHCP symptoms), trunk allowed-VLAN isolation, switch ACLs blocking DHCP/IPv6 ND, routing table sources (connected/static/dynamic/default), missing default route, unreachable next hop, routes not learned, DHCP pool exhaustion (stale leases, lease time, scope sizing), wrong default gateway, duplicate IP, wrong subnet mask and on-net/off-net decision. Clarified transcript on default-route "wildcard" (0.0.0.0/0), static vs dynamic route withdrawal, DHCP lease reclamation, and subnetting being in scope (obj 1.7). Added 9 exam traps to cert page.

## [2026-10-08] ingest | L22 — Troubleshooting Cabling and Physical Issues (CompTIA Network+ N10-009, Objective 5.2)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L22.md covering SMF vs MMF optics matching, fiber connectors (ST/SC/LC/MPO), copper categories, crosstalk/EMI/attenuation, termination faults (open/short/cross/split pair), TX/RX transposition and Auto-MDI-X, speed vs duplex mismatch with counter signatures, CRC/runts/giants/late collisions/drops, port status (admin down, err-disabled causes and recovery, suspended), PoE (PSE/PD, 802.3af/at/bt, power budget), and transceivers (type, cleanliness, seating, DOM signal strength). Added symptom→cause cheat sheet. Clarified caption artifacts: runts (captioned "runs"), Auto-MDI-X; parallel-detection duplex fallback. Added 8 exam traps to cert page.

## [2026-10-08] ingest | L21 — Network Troubleshooting Methodology (CompTIA Network+ N10-009, Objective 5.1)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L21.md covering CompTIA 7-step methodology (identify, theory, test, plan, implement/escalate, verify + preventive measures, document + lessons learned), step 1 sub-tasks, question-the-obvious, top-to-bottom / bottom-to-top / divide-and-conquer approaches, APIPA test example, escalation, rollback planning, and a step-identification cheat sheet for narrative exam questions. Starts Domain 5 (Network Troubleshooting).

## [2026-10-08] quiz | NQ05 — Network Security (Coursera graded, 19 questions)
Saved Module 4 graded quiz with answer key and reasoning. Covers L18–L20 (Basic Network Security, Network Attacks, Defense Techniques). Key topics: hardening, DNS poisoning vs ARP spoofing, 802.1X vs 802.1Q, rogue DHCP, VLAN hopping, honeypot, data at rest, SAML, GDPR, OT segmentation, port security, PKI, RADIUS, content filtering. Gaps found: password attacks (dictionary/brute force/rainbow table/pass-the-hash) not in L19 transcript — reference table added to quiz page; patch-vs-replace response hierarchy. Added 3 exam traps to cert page.

## [2026-10-08] ingest | L20 — Network Defense Techniques (CompTIA Network+ N10-009, Objective 4.3)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L20.md covering device hardening (unused ports/services, default credentials, CIS/STIG baselines), NAC with posture checks, port security (max/sticky/static, violation modes), 802.1X (supplicant/authenticator/auth server, EAPOL, EAP-TLS, MAB), MAC filtering weaknesses, key management (symmetric rotation, private key protection, revocation, escrow, HSM/KMS), network ACLs (first match, implicit deny, wildcard masks, standard vs extended placement), URL vs content filtering/DLP/TLS inspection, zones (trusted/untrusted, drop vs reject), and screened subnet. Corrected transcript's misread wildcard mask (0.255.255.255 = 10.0.0.0/8). Added 11 exam traps to cert page.

## [2026-10-08] ingest | L19 — Network Attacks (CompTIA Network+ N10-009, Objective 4.2)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L19.md covering DoS vs DDoS (botnet/C2, reflection, amplification vectors DNS/NTP/memcached, black hole vs sinkhole, BCP 38), VLAN hopping (double tagging, switch spoofing/DTP), MAC flooding (CAM table fail-open, port security), ARP poisoning (DHCP snooping + DAI), DNS cache poisoning (DNSSEC, DoH/DoT, HTTPS), rogue DHCP, rogue AP vs evil twin, on-path attacks and SSL stripping (HSTS), social engineering (phishing/spear/whaling/smishing/vishing, tailgating vs piggybacking), and malware (virus subtypes, worm, trojan, RAT, rootkit, ransomware, logic bomb). Added attack→defense cheat sheet. Flagged transcript errors: CAM = Content Addressable Memory, "ARP TAC-A" = arp -a, ARP spoofing/poisoning used interchangeably. Added 11 exam traps to cert page.

## [2026-10-03] ingest | L18 — Basic Network Security (CompTIA Network+ N10-009, Objective 4.1)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L18.md covering cryptography fundamentals (plaintext/ciphertext, key space, IV), symmetric vs asymmetric vs hashing, non-repudiation and digital signatures, PKI (CA/RA, root/subordinate chain of trust, self-signed certs, pinning), IAM (authN vs authZ, MFA factor types, SSO, time-based auth), AAA protocols (LDAP 389/LDAPS 636, SAML, RADIUS UDP 1812/1813, TACACS+ TCP 49), least privilege, RBAC, geofencing, physical security, deception tech (honeypot/honeynet/honeyfile), CIA triad, threat→vulnerability→exploit→risk chain, CVE, shadow IT, compliance (HIPAA, GLBA, SOX, PCI DSS, GDPR rights), data locality/sovereignty/residency, and segmentation (IoT, OT/ICS/SCADA, Stuxnet, guest, BYOD). Flagged transcript errors: TACACS+ is TCP-only, PCI DSS = Data Security Standard, SSH uses host keys not certificates. Added RADIUS/TACACS+ to ports-and-protocols concept page. Starts Domain 4 (Network Security).

## [2026-06-16] ingest | L17 — Network Access and Management (CompTIA Network+ N10-009, Objective 3.5)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L17.md covering VPN types (site-to-site IPsec, client-to-site TLS, personal VPN), VPN security properties (confidentiality/integrity/authentication/anti-replay), IPsec headers (AH vs ESP) and modes (tunnel vs transport), TLS VPN client-based vs clientless (browser portal), split tunnel vs full tunnel security tradeoffs, device access methods (console/SSH/GUI/web UI/API), jump box/bastion host pattern, and in-band vs out-of-band management. Completes Domain 3 (Network Operations) coverage.

## [2026-06-16] ingest | L16 — IP Services (CompTIA Network+ N10-009, Objective 3.4)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L16.md covering DHCP (scope/lease/reservation/exclusion, DORA 4-step, relay agent/ip helper-address), IPv6 SLAAC (FE80 link-local, router solicitation/advertisement, EUI-64, stateless DHCPv6), DNS architecture (resolver vs name server, UDP 53), all DNS record types (A, AAAA, NS, MX, CNAME, TXT, PTR/in-addr.arpa), DNS security (DNSSEC signatures, DoH port 443, DoT port 853), /etc/hosts file, NTP (UDP 123), PTP (UDP 319/320, sub-microsecond), and NTS (NTP over TLS).

## [2026-06-16] ingest | L15 — Disaster Recovery (CompTIA Network+ N10-009, Objective 3.3)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L15.md covering DR metrics (RPO vs RTO, MTBF vs MTTR), DR site types (hot/warm/cold), high availability approaches (active-active vs active-passive, fail back), and DR testing methods (tabletop exercise vs validation test). Updated index.md, course_tracker.md, certs/network-plus.md, log.md, README.md.

## [2025-05-28] ingest | L01 — The OSI Model (CompTIA Network+ N10-009)
Lesson ingested from Coursera transcript. Created wiki/lessons/L01.md, updated concepts/osi-model.md. Quiz questions generated; weak areas TBD.

## [2025-05-28] ingest | L02 — Networking Appliances & Applications (CompTIA Network+ N10-009)
Lesson ingested from Coursera transcript. Created wiki/lessons/L02.md, updated concepts/network-devices.md. K3s lab connections drawn (iptables=firewall, Ingress=reverse proxy, kube-proxy=load balancer, Kubeshark=IDS).

## [2025-05-28] ingest | L03 — Network Virtualization & Cloud Technologies (CompTIA Network+ N10-009)
Lesson ingested from Coursera transcript. Created wiki/lessons/L03.md, updated concepts/cloud-fundamentals.md, concepts/vpc-and-subnets.md.

## [2026-06-04] init | Wiki initialized
CLAUDE.md schema created, full directory structure built, all three lessons from initial session converted to wiki format.

## [2026-06-05] ingest | L04–L08 — CompTIA Network+ N10-009 Objectives 1.4–1.8
Five lessons ingested from study guide: Ports/Protocols, Network Media, Topologies, IP Addressing, Modern Networks. Created lessons/network-plus/L04–L08.md. Created concept pages: ports-and-protocols, network-media, network-topologies, ip-addressing, modern-networks.

## [2026-06-05] ingest | GCP01–GCP06 — Google Cloud Networking Specialization (Routing & Addressing module)
Six GCP lessons ingested: Routes, IPv6, BYOIP, Cloud DNS, Geolocation Lab, Module Debrief. Created lessons/gcp/GCP01–GCP06.md. Created concept pages: gcp-routing, gcp-dns. Created certs/gcp-networking.md.

## [2026-06-14] ingest | L10 — Switching Technologies (CompTIA Network+ N10-009, Objective 2.2)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L10.md covering VLANs, SVI, access/trunk ports, 802.1Q, speed/duplex, MTU/jumbo frames, EtherChannel/LACP, STP root bridge election, RSTP, PortFast, BPDU Guard.

## [2026-06-14] ingest | L09 — Routing Technologies (CompTIA Network+ N10-009, Objective 2.0)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L09.md covering static routing, route selection (longest match → AD → metric), subinterfaces, OSPF/EIGRP/BGP, NAT/PAT, and FHRP (HSRP/VRRP/GLBP). Updated index.md, course_tracker.md, certs/network-plus.md.

## [2026-06-16] ingest | L14 — Network Monitoring (CompTIA Network+ N10-009, Objective 3.2)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L14.md covering SNMP (agent/manager/MIB/OID, GET/SET/Trap/Inform, UDP 161/162, v1 clear text → v3 encrypted), syslog (UDP 514, severity 0–7), flow data (NetFlow/sFlow, 7-field flow definition), packet capture (tcpdump/Wireshark, TAP, port mirroring/SPAN), baseline metrics, APIs, and monitoring solution types (discovery/traffic/performance/availability/config).

## [2026-06-16] quiz | NQ04 — Network Operations (Coursera graded, 26 questions)
Saved Module 3 graded quiz. Covers L13–L17 (Process/Procedure, Network Monitoring, Disaster Recovery, IP Services, Network Access and Management). Key topics: RPO vs RTO, MTBF vs MTTR, hot/warm/cold site, SNMP traps, port mirroring vs NetFlow, PTP vs NTP, DHCP relay/reservation, DoH vs DoT, TLS VPN, split-tunnel, jump box.

## [2026-06-16] ingest | L13 — Organizational Processes and Procedures (CompTIA Network+ N10-009, Objective 3.1)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L13.md covering documentation types (physical/logical/rack/cable diagrams, asset inventory, IPAM, wireless heat maps, SLAs), lifecycle management (planning→installation→maintenance→decommissioning, EoL vs EoS, legacy equipment), change management (ticket-based process, knowledge documentation), and configuration management (baseline configs, config items, backup/restore).

## [2026-06-15] ingest | L11 — Wireless Devices and Technologies (CompTIA Network+ N10-009, Objective 2.3)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L11.md covering 802.11 channels (1/6/11), 2.4/5/6 GHz bands, BSS/ESS/captive portal, WPA2 vs WPA3 (SAE, AES-GCMP), autonomous vs controller-based APs, omnidirectional vs directional antennas, and MIMO.

## [2026-06-15] ingest | L12 — Physical Installations (CompTIA Network+ N10-009, Objective 2.4)
Lesson ingested from Coursera transcript. Created wiki/lessons/network-plus/L12.md covering MDF/IDF hierarchy, rack units (U = 1.75"), hot/cold aisles, punchdown blocks, patch panels, UPS/PDU roles, clean power, humidity/ESD/temperature controls, and non-water fire suppression for electrical rooms.

## [2026-06-05] refactor | Reorganized lessons/ into subfolders
Split wiki/lessons/ into wiki/lessons/network-plus/ and wiki/lessons/gcp/ for better organization as multiple courses accumulate.
