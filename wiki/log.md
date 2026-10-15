# Activity Log

<!-- Format: ## [YYYY-MM-DD] action | description -->

## [2026-10-15] ingest+verify | GCP29–GCP30 — Controlling Access module intro + IAM Roles
Module 2 intro (control plane IAM vs data plane firewall) and IAM roles (hierarchy inheritance, union, deny-first, permission anatomy, Network Viewer/Admin/Security Admin, custom role, Karen scenario mapping). Verified against IAM deny docs + quotas: deny policies per resource = 500 (lecture said 5). New in-repo viz GCP29-30 IAM Access Explorer; command anatomy §12 IAM.

## [2026-10-15] quiz | GQ04 — DDoS Protection Module Quiz 2/2
Saved Coursera graded Module 1 (Course 4) quiz with answer key, wrong-option traps, exam keyword map. Embedded prompt-injection block ignored. Marked GCP24–27 quiz_attempted.

## [2026-10-15] visualize+reference | Local visualizations, GCP01–03 viz, command anatomy
Visualizations now live in-repo (wiki/visualizations/gcp/*.html, linked from lessons; no external links). Added GCP01–03 "VPC Route Selection" (routing order per docs, IPv6 stack builder, BYOIP BGP). Fact-check: IPv6-only subnets now supported (GCP02 note). New concept page gcp-command-anatomy covering all gcloud commands used in GCP lessons/labs.

## [2026-10-15] visualize | GCP28 — Cloud Armor at the Edge (interactive)
Published interactive artifact https://claude.ai/artifact/HUYW5ZyNyEmr5ZnnWx1Wvj (source wiki/visualizations/gcp/). New standing rule: every GCP lesson gets an interactive visualization; backfill GCP01–GCP27 pending.

## [2026-10-15] update | gcp-product-map diagrams
Added 5 Mermaid diagrams: request-path big picture (edge → compute → data → APIs), compute decision flow, storage/DB decision flow, networking connectivity map (+ need→product table), resource hierarchy GCP vs AWS.

## [2026-10-15] ingest | GCP28 — Lab: Configuring Traffic Blocklisting with Google Cloud Armor
Lab page from intro transcript + pasted lab (credentials not stored): verify web-backend/web-rule, access-test VM, blocklist-access-test policy (default allow, P1000 deny-404 on VM external IP, target web-backend), policy logs. Added gcloud equivalents, 404-vs-403 rationale, fact-check on lab's VPC Flow Logs suggestion (proxy LB → backends see GFE IPs; use LB logs remoteIp / XFF). Module 1 map complete.

## [2026-10-15] query | Google Cloud product map
Filed concept page gcp-product-map: compute, storage, DB/analytics, networking (with lesson links), security, ops, DevOps, integration, AI, hybrid/migration — each with AWS (and Azure) equivalents, plus GCP-vs-AWS conceptual differences table.

## [2026-10-15] ingest | GCP27 — Types of Complementary Partner Products
Five partner security categories with native-counterpart comparison, reasons to choose partners (hybrid consistency, BYOL, gaps), multi-NIC NVA + internal NLB next-hop insertion pattern, NVA troubleshooting scenario (health checks, symmetric routing, can-ip-forward). Updated GCP24 module map links.

## [2026-10-15] ingest+verify | GCP26 — Google Cloud DDoS Mitigations
Ingested the six mitigation strategies (Cloud Load Balancing built-in defense incl. L7 vs Slowloris, attack-surface reduction, internal isolation via VPN/Interconnect, Cloud CDN, Apigee, Cloud Armor). Verified Cloud Armor against docs (overview + Enterprise): Adaptive Protection requires Enterprise (lecture implied general), Standard vs Enterprise feature table, bill protection/DDoS response team Annual-only, supported LBs incl. Cloud CDN/Media CDN, network edge policies for VM public IPs. Added layered-defense diagram, defensive policy example (preconfigured WAF, rate-based ban, Adaptive Protection), WAF false-positive scenario.

## [2026-10-15] ingest | GCP24–GCP25 — DDoS Protection module intro + How DDoS attacks work
GCP24: module map (attacks → Google mitigation → partner products → lab). GCP25: DDoS definition, botnets, attack categories (volumetric / protocol / L7) with defenses, Google bisection bandwidth (~1,300 Tbps DC vs ~200 Tbps internet), record L7 attacks (46M rps 2022, 398M rps Aug 2023). Fact-check note: Google's public figure for its largest 2017 attack is ~2.5 Tbps vs lecture's ~4 Tbps. Hands-on kept defensive (LB logging, Cloud Armor deny rule, PCAP review).

## [2026-10-15] ingest | GCP23 — Network Security: Course Introduction (Course 4 starts)
Ingested intro to Course 4 (DDoS protection; controlling access to VPC networks; advanced security monitoring and analysis). Added course-position map, preview of likely services per module (Cloud Armor, firewall policies/IAM/IAP/VPC-SC/NGFW, Cloud IDS/Packet Mirroring/Flow Logs/Firewall Insights) flagged as preview, links to security ideas already covered, Network+ Domain 4 and AZ-500/KCSA/CKS mapping. Marked Course 3 complete on cert page (GQ02 pending).

## [2026-10-15] resources | GCP course decks 05 (Private Connection Options) + 06 (Introduction to Network Architecture)
Downloaded both official ILT PDFs to raw/assets/gcp-networking/ and extracted text (pypdf). Added from deck 06: Shared VPC / VPC Network Peering / centralized appliances (GCP15), landing zones + IAM + scaling detail and the Module 1 practice quiz (GCP16); flagged invalid overlapping subnet ranges on slide 17. From deck 05: official quiz explanations confirm GQ01; flagged slide-52 Gemini sample command as invalid (PSC/PSA confusion) in GCP11. Added Course Resources sections to index and cert page.

## [2026-10-15] quiz | GQ03 — Network Topologies quiz (Course 3, Module 2) — 2/2
Saved graded quiz (gated ingress and egress for strict isolation; hub-and-spoke for central management of isolated regional segments) with keyword cheat sheet. GQ02 reserved for the Module 1 quiz (not yet received). Also applied verified PSA doc facts to GCP10 (producer /29–/24 subnets from allocated range, subnet routes only, producer default route, not available to peered VPCs → NCC producer VPC spokes).

## [2026-10-15] ingest | GCP22 — Best Practices for Connectivity and Topologies (end of Module 2)
Ingested best practices (SLAs; centralized hybrid hub VPC + peering with custom routes; APIs via API gateway/LB/Apigee; LB application capacity optimization; two authoritative DNS systems; Gemini prompts). Cross-checked the Architecture Center "general best practices" page and added the full list (Shared VPC, common identity, IPv6 early, SA-based firewall/policies/geo/Threat Intelligence, multilayer security, MACsec/HA VPN over Interconnect, active/active HA VPN, Interconnect for high egress, Interconnect vs Direct/Carrier Peering). Flagged nuance: centralized peering hub gives spoke↔on-prem, not spoke↔spoke. Added hub-VPC and hybrid DNS Mermaid diagrams.

## [2026-10-15] ingest+verify | GCP21 — Getting Topology Data; Google Cloud docs cross-check
Ingested Network Topology lesson (graph of entities/connections, infra + GKE Enterprise views, 6-week history in hourly snapshots, base vs hierarchical entities, protocols TCP/UDP/ICMP/ICMPv6/ESP/GRE, paths incl. VPN/Interconnect/router appliances, Charlie LB latency case as methodology workflow). Fetched docs.cloud.google.com pages (Network Topology overview, NCC overview, gated patterns ×4, Configure PGA, PSA, NAT overview/ports) and applied verified facts: full VM hierarchy (region > network > subnet > zone > instance group > instance), metrics + limitations (nic0 only, unsupported managed services); PGA IPv6 ranges are /56 (fixed from /64) and private/restricted VIPs also need routes/firewall for 34.126.0.0/18; NCC has 4 spoke types (incl. producer VPC, NCC Gateway), hybrid IPv6 now Preview (lecture said unsupported), export/import filters; gated patterns precise initiation rules + Cloud NGFW Enterprise IPS/Apigee; NAT 64,512 ports/IP, Public NAT static vs Private NAT dynamic default. Updated GCP08, GCP11, GCP18, GCP20, GS01, GS02, GPL01, concept pages.

## [2026-10-15] create | GCP scenario walkthroughs (GS01–GS03) + practice labs (GPL01–GPL02)
GS01: 8 private-connectivity scenarios (PGA use case, PGA vs NAT, on-prem PGA advertisement, PSC partner API with overlap, PSC from peered VPC, PSA hybrid both directions, NAT port exhaustion, NAT subnet mapping) — each with Mermaid architecture + diagnosis workflow, commands, why, verify. GS02: topology guide (hub-and-spoke incl. NCC star, full mesh, partial mesh + failover sequence, mirrored, gated egress/ingress) with how-it-works, when, pros/cons, implementation commands, comparison table. GS03: 8 architecture/topology scenarios (Cymbal global 3-tier, cloned IPs, IPAM/compliance, peering sprawl → NCC migration, non-transitive peering hub fixes, NCC spoke3 debug, DR mirror, Nur's hybrid estate). GPL01/GPL02: 11 new practice labs + design challenge with hidden solutions. New folders scenarios/gcp/ and labs/practice/ (CLAUDE.md updated).

## [2026-10-15] ingest | GCP20 — Other Topologies: Mesh, Mirrored, Gated (Course 3, Module 2)
Ingested mesh (full vs partial; GKE Enterprise microservices; cross-region failover; HA/scalability/performance/no central point), mirrored (DR standby, isolated dev/test, nearby-region performance), gated (egress, ingress, ingress+egress for hybrid/multicloud). Added trade-offs (n(n−1)/2, no central inspection → per-node policy), mirror-layout-not-addresses rule, implementation mapping per gated pattern, full Module 2 topology comparison table. Filled the pending mesh/mirrored/gated rows on gcp-network-architecture concept page and GCP17 module map.

## [2026-10-15] ingest | GCP16–GCP19 — Network Architecture: end of Module 1 + Module 2 start (hub-and-spoke, NCC lab)
GCP16 Getting Started (considerations incl. compliance; stakeholders → requirements → HLD → LLD → BoM/CapEx/OpEx; best practices and constraints). GCP17 Module 2 intro (hub-and-spoke, mesh, mirrored, gated). GCP18 Hub-and-Spoke (features, peering vs Cloud VPN vs NCC, transitivity trap, VPC/hybrid spokes, router appliances, no overlap/no IPv6 hybrid/PUPI ok, mesh vs star presets). GCP19 lab page (wiki/labs) from lab intro + pasted instructions: two isolated VPCs, pre-built ICMP firewall, NCC mesh hub with VPC spokes, ping before/after, Network Topology; gcloud equivalents; noted intro says three VPCs but lab has two (hub is NCC, not a VPC). New concept page gcp-network-architecture. Qwiklabs credentials not stored. Module 1 quiz pending as GQ02.

## [2026-10-14] ingest | GCP15 — Cloud Network Architecture Overview (Course 3, Module 1)
Ingested overview: shared responsibility for virtual network, design process (assess → quantifiable metrics → select services), toolkit (VPC/subnets, firewall policies, Cloud VPN, Interconnect, HA VPN over Interconnect, Cloud Router BGP + NAT control plane, load balancers, Cloud CDN, Network Service Tiers Premium vs Standard), Cymbal Bank 3-VM → multi-region, tier-per-subnet, load-balanced redesign with unique ranges. Clarified: policy-based routes are a VPC feature, not Cloud Router. Added service-account firewall design note, AWS mapping (zonal vs regional subnets), k3s NetworkPolicy equivalent.

## [2026-10-14] ingest | GCP14 — Network Architecture: Course Introduction (Course 3 starts)
Ingested intro to Course 3 of Networking in Google Cloud: Module 1 Introduction to Network Architecture (subnets, routes, firewalls, load balancing), Module 2 Network Topologies (strengths/weaknesses). Added course-position map, building-block review linking Course 2 lessons, topology preview (Shared VPC, hub-and-spoke/NCC, mesh, hybrid) flagged as to-be-confirmed, mesh-scaling scenario. Marked Course 2 complete on cert page. "Cloud network architecture overview.txt" not yet in Downloads — pending as GCP15.

## [2026-10-14] lab | GCP12 — Lab: Implement Private Google Access and Cloud NAT
Ingested lab intro transcript + lab instructions as wiki/labs/GCP12 (course order: lab precedes debrief, so Module Debrief renumbered GCP12 → GCP13 with all references updated). Lab page covers privatenet VPC/subnet, IAP-only SSH firewall (35.235.240.0/20), vm-internal with no external IP, staged tests (isolated → PGA → Cloud NAT), NAT logging to Cloud Logging, gcloud equivalents for every console step, expected-results matrix, lab check-in answers, Terraform-style troubleshooting scenario, k3s-on-GCE and AWS (SSM/S3 endpoint/NAT GW) mappings. Qwiklabs temporary credentials deliberately not stored.

## [2026-10-10] ingest | WS10 — Wireshark 101: The Statistics View (end of Module 2)
Ingested lesson on Statistics → Conversations (per-layer pairs, A→B/B→A, Rel Start, Duration, timeline) and Statistics → Endpoints (unique MAC/IP/port, GeoIP preview), plus unticking unused protocols. Verified on Pre-Lab with tshark -z: 1 TCP / 1 IPv4 / 2 Ethernet conversations; client sends to next-hop MAC f2:b0:cd… but receives from f2:fb:fa… (asymmetric path / FHRP); all MACs f2: = locally administered → likely anonymized trace. Protocol hierarchy: 78 HTTP frames, 51 truncated. Added other Statistics menus and CLI equivalents. Caption note: "FIDI" = FDDI. Module 2 "The Wireshark Interface" complete.

## [2026-10-10] ingest | WS09 — Wireshark 101: Saving Filter Buttons
Ingested lesson on filter buttons: Bad TCP = tcp.analysis.flags (verified 4 hits on Pre-Lab: keep-alives/ACKs, frames 119–122), add via + on filter bar, edit/remove via Filter Button Preferences, saved in profile dfilter_buttons. Inspected instructor profiles: grouped labels (A//B), field references ${tcp.stream}, ~50 security signature buttons. Tested all 81 instructor buttons with tshark 4.6: 6 fail on 4.x syntax (unquoted strings, single regex backslash, space-separated set) — fixes verified and documented. Flagged mislabeled Cybersec "SYNs and SYNACKs" button. Corrected own table entry (tls.handshake.type in {1, 2}).

## [2026-10-10] ingest | WS08 — Wireshark 101: Creating Profiles
Ingested lesson on configuration profiles: profile = collection of columns, coloring rules, filter buttons, fonts, layout, prefs; create "Wireshark 101" via status bar → New; new profile starts stock; Coloring Rules → Copy from → Default (remove duplicates); re-add Delta via Preferences or by dragging Frame → "Time delta from previous displayed frame"; Frame section is metadata (epoch = 1970-01-01 UTC). Instructor's profiles: TCP Plain (daily, filter buttons), TCP Advanced, DNS, Cybersec. Added profile storage paths, Copy/Export/Import, auto-switch filter, tshark -C; verified frame.* field names and tshark -C with tshark -G. Added Profiles section to wireshark concept page.

## [2026-10-10] ingest | WS07 — Wireshark 101: Coloring Traffic
Ingested lesson on building a TCP SYN coloring rule: find tcp.flags.syn in Packet Details, drag to filter bar (first display filter), View → Coloring Rules → + with bright green, place below Bad TCP (first match wins; retransmitted SYNs stay red). Verified: filter matches frames 1–2 on Pre-Lab; with a temp colorfilters placing the rule below Bad TCP, tshark --color reports frames 1–2 = TCP SYNs (above HTTP). Inspected instructor profiles TCP Plain and Cybersec (GeoIP, DNS count, PE header, NOP options, SYN+window 0, Trickbot JA3) and flagged bug: country code NK should be KP. Caption notes: "SIN/SYNX" = SYN / SYN-ACK.

## [2026-10-10] update | Wireshark 101 module structure
Per course structure: WS00–WS04 + WQ01 belong to Module 1 "Introduction to Wireshark"; WS05–WS06 belong to Module 2 "The Wireshark Interface". Updated module frontmatter, WS04/WS05 summaries, and rebuilt the course page Lessons section grouped by module. Fixed a bug where WS02–WS06 rows had been inserted into the Lab Files table instead of the Lessons table.

## [2026-10-10] ingest | WS06 — Wireshark 101: Configuring Columns
Ingested lesson on adding/removing columns (Preferences → Columns, drag-and-drop, Apply as Column; macOS Preferences under the Wireshark menu), Delta time displayed column + sorting, Time of Day vs UTC Time of Day, and TTL column. Verified on Pre-Lab file: top deltas = keep-alives 44.99/44.96 s, RST 42.13 s, 200 OK 20.59 s; TTL client 128 (capture on client) vs server 111 (≈17 hops from a Windows default 128). Added initial-TTL table, delta vs delta-displayed distinction, TTL-based injection/loop detection, k3s same-node vs cross-node TTL. Added Essential Columns section to wireshark concept page.

## [2026-10-10] ingest | WS05 — Wireshark 101: The Default Interface (Lesson 2 starts)
Ingested lesson on the 4.x default layout: Packet List / Packet Details / Packet Bytes panes, profile indicator in status bar, coloring rules toggle. Added official pane names, status bar areas, default coloring-rule table (read from Wireshark 4.6 colorfilters), and verified coloring on Pre-Lab file with tshark --color (122 HTTP, 1 TCP RST — HTTP rule outranks TCP SYN/FIN, so handshakes are green). Noted default layout_type 2 and that only the Default profile exists locally (course profiles not yet imported). Added Interface section to wireshark concept page.

## [2026-10-10] quiz | WQ01 — Introduction to Wireshark Quiz (5 questions)
Saved Coursera quiz with answer key (1-A editcap/rawshark/mergecap, 2-C profile = custom settings, 3-C capture driver = live capture, 4-A macOS/Windows/Linux, 5-D all). Flagged gap: installation video (OS support, Npcap, bundled CLI tools) not ingested. Added key facts to course page.

## [2026-10-10] ingest | WS04 — Wireshark 101: Packet Capture - Best Practices (end of Lesson 1)
Ingested best-practices recap: no capture filter at start (assumptions miss HTTPS/DNS/ICMP), start on the client with apps closed (dependency mapping), ring buffer for long-term, confirm the problem occurred in time and place, invest in a TAP, capture client + server simultaneously. Added NTP sync / mergecap / editcap time-shift for multi-point captures, PCAP data-sensitivity handling, checklist, k3s Kibana↔ES dual-capture example. Set module "Lesson 1 - Packet Capture" on WS02–WS04. Caption note: "Fireshark" = Wireshark.

## [2026-10-10] ingest | WS03 — Wireshark 101: Where to Capture on the Network
Ingested lesson on capture points: endpoint (quick, adds load), SPAN/port mirroring (on-wire view; oversubscription example 3 × 1G full-duplex = 6 Gbps into 1G → drops → "Previous segment not captured"), TAP (passive, sees all, cost; "make the network analyzable" with TAPs at key links), servers/cloud (provider virtual TAP or capture VM; server install = last resort, CLI only). Added: NIC offload artifacts, SPAN caveats (errored frames, priority), aggregating-TAP oversubscription nuance, AWS/GCP/Azure mirroring, Kubernetes capture points (kubectl debug netshoot, cni0/veth, VXLAN 8472, Kubeshark). Caption note: "Rubo" = Aruba. Added Where to Capture section to wireshark concept page.

## [2026-10-10] ingest | WS02 — Wireshark 101: Your First Packet Capture
Ingested lesson on choosing an interface (sparklines, extcap), live capture/stop, temp file vs Save As, pcapng vs pcap/legacy formats, Capture Options (input/output/options), and ring buffers (20 × 500 MB = 10 GB cap) for intermittent problems. Added dumpcap/tcpdump ring-buffer equivalents, macOS ChmodBPF/access_bpf note (verified local interfaces with dumpcap -D), SSH remote capture to the k3s node. Caption notes: pcapng = PCAP Next Generation; "500-byte" = 500 MB. Transcript converted from RTF to TXT (Coursera UI preamble left in raw copy). Added Capturing section to wireshark concept page.

## [2026-10-10] ingest | WS00 — Wireshark 101: Introduction (Pre-Lesson renumbered to WS01)
Ingested course introduction (instructor Chris Greer, packet analysis use cases, "packets don't lie", course files from Packet Pioneer GitHub, configuration profiles). Converted Introduction.rtf → TXT and de-duplicated (export contained the transcript twice). Renumbered Pre-Lesson from WS00 to WS01 so IDs follow course order; updated all references (log history unchanged).

## [2026-10-10] ingest | WS00 — Wireshark 101: Pre-Lesson - Your First Lab (new course)
Started new track "Wireshark 101: Packet Analysis Essentials". Created lessons/wireshark/, quizzes/wireshark/, raw/transcripts/wireshark/, raw/assets/wireshark-101/ (8 course PCAPs + instructor profiles). WS00 covers client vs network vs server diagnosis on Pre-Lab-SlowNetwork.pcapng: client 172.16.0.13 → server 10.0.0.100:80, handshake RTT 105.6 ms, server ACK of GET at 1 RTT, 20.586 s server think time before 200 OK (frames 6→7). Verified with tshark and extended: 13 serialized follow-up GETs (7× 304 Not Modified, ~1 RTT each, page done ≈24 s), TCP keep-alives every ~45 s, server RST at 156.5 s, 96-byte snaplen (no http.time). Transcript converted from RTF to TXT and de-duplicated (export contained it twice). New concept page wireshark.md and course page certs/wireshark-101.md.

## [2026-10-10] maintenance | README rebuild, LICENSE, repo metadata
Rebuilt README.md (generated tables linking every lesson, quiz, concept and lab; layout; lesson format; certification roadmap incl. KCNA → CKA → AZ-500 → KCSA → CKS, PCA). Added LICENSE (CC BY-NC-SA 4.0 for original wiki content; raw/ third-party material excluded). Corrected objective numbers L01 → 1.1, L03 → 1.3, L09 → 2.1 and replaced stale "Pending" Domain 1 rows on the Network+ cert page. Untracked raw/transcripts/.DS_Store and extended .gitignore. Updated GitHub repo description and topics.

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
