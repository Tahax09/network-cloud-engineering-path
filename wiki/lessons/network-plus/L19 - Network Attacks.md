---
id: L19
aliases: [L19]
title: Network Attacks
course: CompTIA Network+ N10-009
cert_track: network-plus
objective: "4.2 — Network Security: Summarize Various Types of Attacks and Their Impact to the Network"
date: 2026-10-08
status: Complete
quiz_attempted: No
weak_areas: []
tags: [dos, ddos, botnet, reflection, amplification, vlan-hopping, double-tagging, switch-spoofing, mac-flooding, arp-poisoning, dns-poisoning, rogue-dhcp, evil-twin, on-path, ssl-stripping, social-engineering, phishing, malware, ransomware, rootkit]
---

# L19 — Network Attacks

## Summary
The second Domain 4 objective, where L18's vocabulary (threat, vulnerability, exploit) gets applied to concrete attacks. There are five groups: **availability attacks** (DoS/DDoS, reflection, amplification), **Layer 2 attacks** (VLAN hopping, MAC flooding, ARP poisoning), **DNS poisoning**, **rogue devices and on-path attacks** (rogue DHCP, evil twin, SSL stripping), and **human/code attacks** (social engineering, malware). On the exam, each attack is paired with its **specific defense**. Learn them as attack → mechanism → mitigation triples.

---

## Core Concepts

### DoS vs DDoS

**Denial of Service (DoS)** attacks **availability**, the A in CIA ([[L18 - Basic Network Security]]). It works in one of two ways:
1. **Crash the system** with a flaw that hangs or reboots it. Example: the **ping of death**, an oversized ICMP packet that crashed old IP stacks. This is long patched.
2. **Exhaust resources** by sending so many requests that legitimate ones time out.

**Distributed DoS (DDoS)** comes from many sources at once: a **botnet** of compromised **zombies** directed by a **command-and-control (C2)** server. Volume is the point. If a victim has a 100 Mbps uplink and receives 300 Mbps, legitimate traffic can't get through, no matter how well-patched the server is.

#### Reflection and Amplification
```
Attacker ──(spoofed src = Victim IP, small request)──▶ Misconfigured open server (DNS, NTP, memcached)
                                                                 │
Victim ◀───────────(large response, sent to "requester")─────────┘
```
- **Reflected:** the attacker **spoofs the source IP** as the victim, so a third-party server sends its replies to the victim. This also hides the attacker.
- **Amplified:** the reply is much larger than the request. One small request produces a huge response (or, as the lecture puts it, "thousands of responses").

| Amplification vector | Protocol/port | Typical amplification factor |
|---------------------|---------------|-----------------------------|
| DNS (open resolvers, `ANY` queries) | UDP 53 | ~28–54× |
| NTP (`monlist`) | UDP 123 | ~550× |
| memcached | UDP 11211 | up to ~50,000× |

**Why UDP?** It has no handshake, so the server can't verify that the source IP is real before replying. TCP's 3-way handshake makes reflection much harder.

#### DDoS Defenses
| Defense | How it works |
|---------|-------------|
| **Upstream / ISP filtering, scrubbing centers** | Drop attack traffic before it reaches your pipe. You can't filter 300 Mbps on a 100 Mbps link at your own edge |
| **Black hole (RTBH)** | Drop *all* traffic to the targeted IP for a period. Stops collateral damage, but completes the DoS for that one IP |
| **Sinkhole** | Redirect malicious traffic or domains to a controlled destination for analysis instead of to the target |
| **Rate limiting, firewalls, secure coding, configuration management** | Handle the traditional single-source crash and exhaustion DoS |
| **Ingress filtering (BCP 38)** | ISPs drop packets with spoofed source IPs, which breaks reflection at its root |

> **Transcript clarification:** The lecture blends DNS sinkholes and black holes into one DDoS defense. Precisely: a **DNS sinkhole** answers lookups for known-malicious domains (botnet C2, malware) with a controlled IP, which cuts zombies off from their C2. **Black-hole routing** drops traffic to an attacked IP. Both usually need **upstream provider support** at DDoS scale, as the lecture says.

---

### VLAN Hopping

The goal is to send traffic into a VLAN you aren't assigned to, bypassing the segmentation from [[L10 - Switching Technologies]] and [[L18 - Basic Network Security]].

**1. Double tagging** (needs the attacker to be on the **native VLAN**):
```
Attacker (access port, native VLAN 10) sends frame with TWO 802.1Q tags: [outer: 10][inner: 20][payload]
        │
Switch 1: outer tag = native VLAN → strips it, forwards untagged on trunk… frame now shows [20]
        │
Switch 2: sees tag 20 → delivers into VLAN 20  ✔ hopped
```
- This is **one-way only**: replies can't come back the same way. It's good for blind attacks such as DoS or one-way payloads.
- It only works because native-VLAN traffic crosses trunks **untagged**.

**2. Switch spoofing:** The attacker's device pretends to be a switch and negotiates a **trunk** using **DTP** (Dynamic Trunking Protocol). A trunk carries **all VLANs**, so the attacker can then tag frames for any VLAN.

**Defenses:**
```
switchport mode access                 ! hard-code user ports as access ports
switchport nonegotiate                 ! disable DTP (stops switch spoofing)
switchport trunk native vlan 999       ! move native VLAN to an unused, empty VLAN
vlan dot1q tag native                  ! or tag native VLAN traffic too
```
Also shut down unused ports and place them in a dead "parking" VLAN.

---

### MAC Flooding

A switch learns `MAC → port` mappings in its **CAM table** (Content Addressable Memory), which has a fixed size.

**Attack:** Flood the switch with frames from thousands of random source MACs (the lecture says ~4,000+ on old switches). Once the table is full, the switch can't learn new mappings, so it **fails open**: frames for unknown destinations are **flooded out all ports**, and the switch behaves like a **hub**. The attacker then sniffs everyone's traffic.

**Defense: port security.** Limit how many MACs can be learned per port.
```
switchport port-security
switchport port-security maximum 2
switchport port-security violation shutdown   ! or restrict / protect
switchport port-security mac-address sticky
```

> **Transcript correction:** The lecture says "computer addressable memory." It's **Content Addressable Memory (CAM)**: memory searched by content (the MAC) rather than by address.

---

### Spoofing and Poisoning (the Common Thread)

Every poisoning attack has the same shape: **send forged information so a device stores a false mapping, then use it to redirect traffic.**

| Target cache | Forged mapping | Result |
|-------------|----------------|--------|
| Switch **CAM table** | Victim's MAC → attacker's port (MAC spoofing) | Switch delivers the victim's frames to the attacker |
| Host **ARP cache** | Gateway IP → attacker's MAC | Host sends off-subnet traffic to the attacker |
| **DNS cache** | `bank.com` → attacker's IP | Users resolve to the attacker's server |

#### ARP Poisoning → On-Path
ARP maps IP → MAC on the local segment ([[L01 - The OSI Model]], [[L07 - IP Addressing]]). It has **no authentication**, and hosts accept **unsolicited (gratuitous) ARP replies**.

```
Normal:   Victim ──▶ Gateway 10.0.0.1 (MAC aa:aa) ──▶ Internet
Poisoned: Attacker sends "10.0.0.1 is at ee:ee (me)" to Victim
          and        "10.0.0.50 (Victim) is at ee:ee" to Gateway
          Victim ──▶ Attacker ee:ee ──(forwards)──▶ Gateway ──▶ Internet
                     └── reads / modifies everything: on-path
```

View the cache with `arp -a` (Windows/Linux/macOS) or `ip neigh` (modern Linux).

**Defense: DHCP snooping + Dynamic ARP Inspection (DAI).**
- **DHCP snooping** marks ports as trusted or untrusted, blocks DHCP *offers* from untrusted ports, and builds a **binding table** of `IP ↔ MAC ↔ port ↔ VLAN` from real DHCP leases.
- **DAI** checks every ARP packet against that binding table and **drops ARP replies that don't match**.
```
ip dhcp snooping
ip dhcp snooping vlan 10
interface Gi1/0/48
 ip dhcp snooping trust          ! uplink toward the real DHCP server
ip arp inspection vlan 10
```

> **Transcript corrections:** The lecture's "ARP TAC-A" command is `arp -a`. "DHC snooping" is **DHCP snooping**. The lecture also tries to separate "ARP spoofing" (tricking the switch) from "ARP poisoning" (corrupting the host cache). In practice and on the exam, the terms are used **interchangeably** for forging ARP replies to become on-path. Tricking the switch's CAM table is **MAC spoofing**.

#### DNS Cache Poisoning
The attacker injects a forged DNS response so a resolver or client caches `name → wrong IP`. Users who type the right URL land on the attacker's site.

| Defense | What it protects |
|---------|-----------------|
| **DNSSEC** | Signs DNS records, so a validating resolver **rejects forged answers**. This is the direct fix for cache poisoning ([[L16 - IP Services]]) |
| **DoH / DoT** | Encrypts the client ↔ resolver leg, so an on-path attacker can't see or tamper with queries on that hop |
| **HTTPS + certificates** | Even if DNS is poisoned, the fake site can't present a valid certificate for `bank.com`, so the browser warns ([[L18 - Basic Network Security]] PKI) |

---

### Rogue Devices and On-Path Attacks

**On-path attack** (formerly *man-in-the-middle*): the attacker sits in the traffic path and can **read or modify** it. Most of this lesson's attacks are ways to *get* on-path.

| Way to get on-path | Mechanism | Defense |
|-------------------|-----------|---------|
| **Rogue DHCP server** | The DHCP DISCOVER is a **broadcast**, so anyone can answer. The attacker hands out its own IP as the **default gateway** (and its own DNS) | **DHCP snooping** |
| **Rogue AP / evil twin** | An unauthorized AP broadcasting the **corporate SSID**. Clients connect, and all their Wi-Fi traffic passes through the attacker | WIPS/WIDS detects unknown BSSIDs (not on the managed-AP list); 802.1X/EAP-TLS so clients verify the server's certificate ([[L11 - Wireless Devices and Technologies]]) |
| **ARP poisoning** | Above | DAI |
| **DNS poisoning** | Above | DNSSEC, HTTPS |
| **SSL stripping** | The on-path attacker downgrades the victim's HTTPS to plain HTTP while keeping HTTPS to the server, then reads the plaintext | **HSTS** (and HSTS preload): the browser refuses HTTP for that domain |
| **Wi-Fi eavesdropping** | Sniffing open or weakly encrypted wireless | WPA3, encryption |

**General on-path defenses:** **encryption end to end** (an on-path attacker sees only ciphertext) and **strong identity** (mutual authentication, so you can't be fooled into talking to the attacker).

> **Note:** A **rogue AP** is any unauthorized AP (even an employee's well-meaning home router plugged into a wall jack). An **evil twin** is specifically a rogue AP that **impersonates a legitimate SSID**. The lecture uses the terms as synonyms. The exam may test the distinction.

---

### Social Engineering

Social engineering exploits **human psychology** (urgency, authority, fear, helpfulness) rather than technology. Goals: physical access, password resets, information gathering.

| Attack | Type | Description |
|--------|------|-------------|
| **Phishing** | Technical | Mass fraudulent email |
| **Spear phishing** | Technical | **Targeted** at a specific person or group, using personal details |
| **Whaling** | Technical | Spear phishing aimed at **high-value** targets (CEO, CFO). **Whaling is a subset of spear phishing** |
| **Smishing** | Technical | Phishing via **SMS** |
| **Vishing** | Technical | **Voice** phishing (phone calls, including deepfaked voices) |
| **Shoulder surfing** | Physical | Watching someone enter credentials |
| **Dumpster diving** | Physical | Searching trash for written credentials or documents. *Not strictly social engineering*: it exploits careless disposal, not psychology |
| **Tailgating** | Physical | Following someone through a secured door **without their knowledge or consent** |
| **Piggybacking** | Physical | Following someone through a secured door **with their consent**, obtained through social engineering ("hold the door, my hands are full") |

**Exam trap:** tailgating vs piggybacking. Both are following someone in. **Piggybacking = the victim knowingly lets you in.** Defenses: **access control vestibules** (mantraps), badge-per-person policy, security awareness training.

---

### Malware

| Type | Defining trait |
|------|----------------|
| **Virus** | Attaches to a host file or program and needs **user execution** to run (e.g., an Office **macro virus**) |
| **Worm** | **Self-propagating**: spreads with no user action. Fast spread makes it noisy and detectable |
| **Trojan** | A **delivery method**: malware disguised as something wanted (cracked software, video, document) |
| **RAT / backdoor** | A trojan that installs **persistent remote administrative access** |
| **Rootkit** | Hides deep in the system (often the kernel or firmware) so normal OS tools **can't see it** |
| **Ransomware** | **Encrypts the victim's data** and demands payment for the key |
| **Logic bomb** | Dormant code that runs only when a **condition** is met (a date, or an employee removed from payroll) |

**Virus subtypes:**
| Subtype | Evasion trick |
|---------|---------------|
| **Multipartite** | Uses **multiple infection vectors** (boot sector + files) |
| **Stealth** | Hides from OS components and antivirus |
| **Polymorphic** | **Mutates its code or signature** with each infection, which defeats signature-based detection |
| **Encrypted** | Encrypts its body to resist scanning |

> **Transcript clarifications:** Worms spread through **network vulnerabilities** at least as often as by email. WannaCry (2017) self-propagated over **SMB (TCP 445)** using EternalBlue. A **rootkit** isn't really a "type of virus". It's a stealth/persistence toolkit that often *accompanies* other malware.

---

## Attack → Defense Cheat Sheet

| Attack | Layer | Primary defense |
|--------|-------|-----------------|
| DDoS / amplification | L3–L7 | Upstream scrubbing, black-hole routing, rate limiting, BCP 38 |
| VLAN hopping (double tag) | L2 | Change native VLAN to an unused one / tag native |
| VLAN hopping (switch spoofing) | L2 | Disable DTP (`nonegotiate`), access mode on user ports |
| MAC flooding | L2 | **Port security** (max MACs per port) |
| ARP poisoning | L2/L3 | **Dynamic ARP Inspection** + DHCP snooping |
| Rogue DHCP | L7 (affects L3) | **DHCP snooping** |
| DNS poisoning | L7 | **DNSSEC**, DoH/DoT, HTTPS |
| Evil twin / rogue AP | L1/L2 | WIPS, 802.1X with server certificate validation |
| SSL stripping | L7 | **HSTS** |
| Tailgating / piggybacking | Physical | Access control vestibule, training |
| Phishing family | Human | Training, email filtering, MFA |
| Malware | Host | EDR/anti-malware, patching, least privilege, backups (ransomware) |

---

## Key Terms

| Term | Definition |
|------|-----------|
| DoS | Attack on availability by a single source: crash or resource exhaustion |
| DDoS | DoS from many sources (a botnet) |
| Botnet / zombie / C2 | Network of compromised hosts / one compromised host / attacker's control server |
| Reflected attack | Spoofed source IP makes a third party send responses to the victim |
| Amplified attack | Small request produces a much larger response toward the victim |
| Ping of death | Legacy oversized ICMP packet that crashed IP stacks |
| Black hole | Drop all traffic to an attacked destination |
| DNS sinkhole | Resolve malicious domains to a controlled IP |
| VLAN hopping | Reaching a VLAN you aren't assigned to |
| Double tagging | Two 802.1Q tags; outer stripped on native VLAN; one-way hop |
| Switch spoofing | Attacker negotiates a trunk via DTP |
| Native VLAN | VLAN carried untagged on an 802.1Q trunk |
| MAC flooding | Overflow the CAM table so the switch fails open like a hub |
| CAM table | Switch's MAC → port mapping table (Content Addressable Memory) |
| Port security | Limit or pin MACs per switch port |
| ARP poisoning / spoofing | Forged ARP replies map a legitimate IP to the attacker's MAC |
| DHCP snooping | Blocks rogue DHCP servers; builds the IP-MAC-port binding table |
| Dynamic ARP Inspection | Drops ARP packets that don't match the DHCP snooping bindings |
| DNS cache poisoning | Forged DNS response cached as name → attacker IP |
| On-path attack | Attacker in the traffic path reads or modifies it (formerly MITM) |
| Rogue DHCP server | Unauthorized DHCP server handing out a malicious gateway/DNS |
| Rogue AP | Any unauthorized wireless access point |
| Evil twin | Rogue AP impersonating a legitimate SSID |
| SSL stripping | Downgrading HTTPS to HTTP on the victim's side of an on-path connection |
| HSTS | HTTP Strict Transport Security; browser enforces HTTPS for a domain |
| Phishing / spear phishing / whaling | Mass / targeted / executive-targeted fraudulent email |
| Smishing / vishing | SMS phishing / voice phishing |
| Shoulder surfing | Observing credential entry |
| Dumpster diving | Recovering sensitive info from trash |
| Tailgating vs piggybacking | Following in without consent vs with tricked consent |
| Virus / worm / trojan | Needs execution / self-propagates / disguised delivery |
| RAT / backdoor | Persistent remote admin access |
| Rootkit | Malware hidden from OS-level detection |
| Ransomware | Encrypts data for ransom |
| Logic bomb | Code triggered by a specific condition |
| Polymorphic | Malware that mutates its signature |

---

## Real-World Connections

**k3s lab:**
- **ARP spoofing inside clusters is real.** Container runtimes have historically granted `CAP_NET_RAW` by default, which lets a compromised pod craft raw ARP or IPv6 Router Advertisement packets against neighbors on the same node bridge. CVE-2020-10749 was exactly this: malicious IPv6 RAs from a pod could make it on-path for other pods' traffic in several CNI plugins. Mitigation: drop the capability in your pod specs:
  ```yaml
  securityContext:
    capabilities:
      drop: ["NET_RAW"]
  ```
  Pod Security Standards (`restricted` profile) enforce dropping ALL capabilities.
- **Kubeshark** can show you ARP traffic between pods and nodes. A single IP answered by two different MACs in quick succession is the signature of poisoning.
- **Ingress DoS:** your Traefik ingress (k3s default) is the DoS surface. Traefik's `RateLimit` middleware is per-source rate limiting, the lesson's "limit resource exhaustion" defense.
- Ship Suricata or Zeek alerts to Elasticsearch, and Kibana becomes your detection console for this whole lesson.

**AWS/GCP:**
- **Cloud VPCs are built to make L2 attacks impossible.** AWS and GCP don't run real broadcast L2 between instances. The hypervisor answers ARP itself and enforces **source/destination checks**, so ARP poisoning, MAC flooding, rogue DHCP, and VLAN hopping don't work in a VPC. That's one reason those attacks are "on-prem" exam topics.
- DDoS: **AWS Shield** (Standard is automatic; Advanced adds a 24/7 response team plus cost protection), **AWS WAF** rate-based rules, CloudFront absorbing volume at the edge. On GCP, **Cloud Armor**.
- DNS: **Route 53 Resolver DNS Firewall** is a managed DNS sinkhole for malicious domains. Route 53 supports DNSSEC signing.
- **GuardDuty** detects C2 callbacks, crypto-mining, and DNS exfiltration from compromised instances.

**Enterprise:** A standard access-switch hardening template bundles this whole lesson: port security, DHCP snooping, DAI, `nonegotiate`, a dead native VLAN, BPDU guard ([[L10 - Switching Technologies]]), and unused ports shut down. Add WIPS on the wireless controller and HSTS on all web apps.

**Telecom/V2X:** RF jamming is the PHY-layer DoS for sidelink (PC5). There's no handshake to rate-limit, so it can only be detected and worked around (channel hopping, fallback to Uu). A **Sybil attack** (one attacker posing as many vehicles) is the V2X analogue of spoofing, which is why IEEE 1609.2 signs every safety message ([[L18 - Basic Network Security]]). **GNSS spoofing** poisons position data the way DNS poisoning corrupts name resolution.

---

## Troubleshooting Scenario

**Scenario:** Users on VLAN 10 report intermittent connectivity and "Your connection is not private" warnings on internal HTTPS sites. Some users got IP addresses in `192.168.50.0/24` instead of the expected `10.10.10.0/24`. One user's `ipconfig /all` shows default gateway `192.168.50.1`.

**Analysis:**
1. **Wrong subnet + wrong gateway → rogue DHCP server.** Some clients received an offer from an unauthorized server before the legitimate one answered.
2. **Certificate warnings → on-path.** The rogue gateway is intercepting traffic and presenting its own certificates. PKI doing its job ([[L18 - Basic Network Security]]): it can't forge a valid certificate.
3. Intermittent failures: clients that got the legitimate lease work; clients on the rogue lease route through a device that can't forward properly.

**Find it:**
```
# On an affected client: who gave me this lease?
ipconfig /all                          # Windows: "DHCP Server" field
cat /var/lib/dhcp/dhclient.leases      # Linux: dhcp-server-identifier
# On the switch: which port has that MAC?
show mac address-table address <rogue-MAC>
```
**Fix:**
- Shut down the offending port. Physically locate the device (often an employee's consumer router plugged in backwards, LAN port to the wall).
- Enable **DHCP snooping** on VLAN 10 and trust only the uplink toward the real DHCP server.
- Enable **DAI** so the same port can't follow up with ARP poisoning.
- Have affected clients run `ipconfig /release && ipconfig /renew`.

---

## Hands-On Ideas

> Run attack demos **only on networks and devices you own**, ideally an isolated lab VLAN or VMs.

1. **Watch ARP in action:** `ip neigh` (or `arp -a`) before and after `ping 10.0.0.1`. In Wireshark, filter `arp` and see request/reply. Filter `arp.duplicate-address-detected` to see Wireshark flag IP conflicts, which is exactly what ARP poisoning looks like.
2. **ARP poisoning lab:** In two VMs on an isolated virtual network, use `arpspoof` (dsniff) or Bettercap from one to the other, then watch the victim's `ip neigh` gateway MAC change. Defend by pinning a static ARP entry (`ip neigh replace 10.0.0.1 lladdr aa:bb:cc:dd:ee:ff dev eth0 nud permanent`) and confirm the attack stops working.
3. **Switch hardening in GNS3/Packet Tracer:** Configure port security (`maximum 2`, `violation shutdown`), DHCP snooping, and DAI. Then plug in a second "DHCP server" on an untrusted port and confirm its offers are dropped.
4. **DNSSEC validation:** `dig +dnssec cloudflare.com` (look for the `ad` flag). `dig dnssec-failed.org` should return **SERVFAIL** from a validating resolver: that's a forged/broken signature being rejected.
5. **HSTS check:** `curl -sI https://github.com | grep -i strict-transport` shows the header that defeats SSL stripping.
6. **k3s:** Audit pods for `NET_RAW`: `kubectl get pods -A -o json | jq '.items[] | select(.spec.containers[].securityContext.capabilities.drop | index("NET_RAW") | not) | .metadata.name'`

---

## Quiz

1. An attacker sends small DNS queries with the victim's spoofed source IP to thousands of open resolvers, and the victim receives huge responses. What attack is this?
   - A) Ping of death  B) Reflected amplification DDoS  C) DNS cache poisoning  D) MAC flooding

2. A switch starts forwarding all frames out every port after an attacker generates thousands of fake source MACs. Which control prevents this?
   - A) DHCP snooping  B) Port security  C) DNSSEC  D) HSTS

3. An attacker on the native VLAN sends a frame with two 802.1Q tags to reach VLAN 20. What is the best mitigation?
   - A) Enable DAI  B) Change the native VLAN to an unused VLAN  C) Enable WPA3  D) Configure an RTBH route

4. An attacker's laptop negotiates a trunk with an access switch port. Which feature should be disabled?
   - A) STP  B) DTP  C) LACP  D) CDP

5. Users receive IP addresses from an unexpected subnet with an unknown default gateway. Which switch feature prevents this?
   - A) Port security  B) DHCP snooping  C) BPDU guard  D) Storm control

6. Which feature validates ARP packets against the DHCP snooping binding table?
   - A) Dynamic ARP Inspection  B) Port mirroring  C) 802.1X  D) DNSSEC

7. An attacker sets up an AP broadcasting `CorpWiFi`, the company's SSID, in the parking lot. What is this?
   - A) War driving  B) Evil twin  C) Bluejacking  D) Jamming

8. An on-path attacker downgrades a user's HTTPS session to HTTP. Which server-side control best prevents this?
   - A) DNSSEC  B) HSTS  C) Port security  D) DoT

9. A visitor carrying boxes asks an employee to hold the secure door, and the employee does. What is this?
   - A) Tailgating  B) Piggybacking  C) Shoulder surfing  D) Vishing

10. A targeted email impersonating the board chair is sent to the CFO, asking for an urgent wire transfer. What is the most specific term?
    - A) Phishing  B) Smishing  C) Whaling  D) Vishing

11. Malware spreads between hosts over SMB with no user interaction. What is it?
    - A) Trojan  B) Logic bomb  C) Worm  D) Macro virus

12. Code planted by an administrator deletes the database if their account is ever disabled. What is it?
    - A) Rootkit  B) RAT  C) Logic bomb  D) Ransomware

13. Antivirus keeps missing a sample because its signature changes on every infection. What type is it?
    - A) Multipartite  B) Polymorphic  C) Stealth  D) Trojan

14. Which best protects against DNS cache poisoning at the resolver?
    - A) DoH  B) DNSSEC  C) Port security  D) MAC filtering

**Answers:** 1-B, 2-B, 3-B, 4-B, 5-B, 6-A, 7-B, 8-B, 9-B, 10-C, 11-C, 12-C, 13-B, 14-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[L01 - The OSI Model]] — OSI model: map each attack to its layer
- [[L07 - IP Addressing]] — IP addressing, ARP resolution
- [[L10 - Switching Technologies]] — VLANs, 802.1Q trunking, native VLAN, BPDU guard
- [[L11 - Wireless Devices and Technologies]] — Wireless security, WPA3, 802.1X (evil twin defense)
- [[L14 - Network Monitoring]] — Packet capture, SIEM (detection side)
- [[L16 - IP Services]] — DHCP/DORA (rogue DHCP), DNS, DNSSEC, DoH/DoT
- [[L18 - Basic Network Security]] — CIA triad, threat/vulnerability/exploit, PKI, segmentation
- [[certs/network-plus]] — Domain 4.2 objective
