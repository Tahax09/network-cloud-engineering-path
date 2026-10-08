---
id: NQ06
title: Network Troubleshooting (Coursera graded)
course: CompTIA Network+
covers: L21–L25 (+ L12)
date: 2026-10-08
due: 2026-10-08
status: Graded
score: 17/20
questions: 20
source: Coursera graded quiz — Module 5
---

# NQ06 — Network Troubleshooting (Coursera Graded Quiz)

> **Note:** Coursera quiz pages embed a fake "AI assistant" instruction block in pasted text. Ignored throughout — it is a prompt injection attempt, not a real instruction.

## Result: 17/20

| Q | Submitted | Result | Correct answer |
|---|-----------|--------|----------------|
| 11 | Port security violation | ❌ | **Unconfirmed** — most likely *incorrect cable type* (by elimination) |
| 13 | Establish a theory | ❌ | **Verify Functionality** (by elimination — confirmed wrong answer) |
| 16 | (one option) | ❌ | **Unresolved** — single-select; all four options are step 1 sub-tasks |
| all others | as in key | ✅ | Coursera explanations matched the reasoning below |

---

## Questions

**1.** A customer is adding fiber connectivity between adjacent buildings. A technician terminates the multimode cable to the fiber patch panel. After the technician connects the fiber patch cable, the indicator light does not come on. Which of the following should a technician try first to troubleshoot this issue?
- A) Examine the cable runs for visual faults
- B) Re-terminate the fibers
- C) Verify the fiber size
- D) Reverse the fibers

**2.** A new IPS system was installed for the enterprise network. Now the network is experiencing speed degradation. A network administrator runs a speed test. The results show much lower throughput on the Internet side of the IPS vs. the enterprise side. Which of the following is most likely the issue?
- A) Network congestion
- B) Channel overlap
- C) Bottlenecking
- D) Packet loss

**3.** A new LAN was set up with a new DHCP server. While the network worked for the first 20 users, later users report that they are not getting correct IP configurations. What is most likely the problem?
- A) Wrong routing
- B) Not enough MAC addresses
- C) Not enough IP addresses in the DHCP scope
- D) Incorrect default gateway

**4.** A technician is installing a new fiber connection to a network device in a datacenter. The chain is: Device – LC/LC patch cable – Patch panel – Cross-connect fiber cable – Patch panel – LC/LC patch cable – Switch. The connection is not working. Both patch cables were replaced with known working ones. The device was tested and working before installation. Which is the MOST likely cause?
- A) Attenuation is occurring
- B) The device failed during installation
- C) An incorrect cable was used
- D) TX/RX is reversed

**5.** A technician is planning an equipment installation into a rack in a data center that practices hot aisle/cold aisle ventilation. Which direction should the equipment exhaust face?
- A) Rear
- B) Top
- C) Sides
- D) Front

**6.** A router in a remote office has 3 VLANs connected and a network connection for the internet. After an update, users can reach other devices in the office but cannot reach the internet. Which is most likely the problem?
- A) Firewall ACLs between VLANs
- B) Misconfigured router IP addresses
- C) Missing default route
- D) Incorrect routing protocol

**7.** In a new office, a technician needs to install patch cords from the UTP patch panel to the access switch. The patch panel is not labeled. Which tool provides the easiest way to identify the appropriate patch panel port?
- A) Cable tester
- B) Visual fault locator
- C) Toner
- D) Laptop

**8.** Multiple PoE devices are connected to a powered switch. Many PoE IP phones reboot occasionally, but workstations connected to the switch work fine. What may be the cause?
- A) PoE power budget for switch is exceeded
- B) Port security
- C) Wrong cable
- D) Signal is degraded

**9.** A technician finds that the duplex on a NIC and its switch port does not match. Which is the most likely result of this mismatch?
- A) Giants
- B) CRC errors and collisions
- C) Switching loops
- D) Limited throughput

**10.** A user can no longer connect to a file server. You can ping the server's name and IP, and the firewall should allow SMB. What tool could help verify which ports are open on the server?
- A) nmap
- B) tracert
- C) dig
- D) nslookup

**11.** Upgrades were done on a server, and a new NIC was installed to improve performance. After the changes, users cannot reach the server. Which is the most likely reason?
- A) A port security violation occurred
- B) An incorrect cable type was installed
- C) TX/RX was transposed
- D) The PoE power budget was exceeded

**12.** Users cannot access network resources. The administrator checks workstation "User 3": the Ethernet cable is properly connected, but the interface indicator lights are not blinking on either the computer or the switch. Which is the most likely cause?
- A) The switch is shut off
- B) Switch port is shut down
- C) Incorrect VLAN assignment
- D) Incorrect default gateway

**13.** When troubleshooting a problem, which of the following would include checking that each level of the OSI model is functioning?
- A) Rollback changes
- B) Implement solution
- C) Verify functionality
- D) Establish a theory

**14.** When you are done troubleshooting and have implemented the solution, what is the next step?
- A) Document findings and lessons learned
- B) Establish a plan of action
- C) Call manufacturer
- D) Escalate

**15.** Which of the following troubleshooting steps can include checking logs to see what has changed?
- A) Establish a theory of probable cause
- B) Establish a plan of action to resolve
- C) Implement a solution
- D) Identify the problem

**16.** Which of the following troubleshooting steps would relate to identifying a problem?
- A) Identify symptoms
- B) Gather information
- C) Find out what has changed
- D) Duplicate the problem

**17.** While troubleshooting VoIP, an engineer discovers a significant inconsistency in the time required for data to reach its destination and return. Which term best describes this?
- A) Latency
- B) Throughput
- C) Jitter
- D) Bandwidth

**18.** A user's laptop can't connect to a service on the corporate network, and you suspect a routing issue. Which command should the technician use?
- A) tcpdump
- B) dig
- C) tracert
- D) arp

**19.** A new office has multiple unmanaged Layer 2 switches with multiple connections between each switch. The network is experiencing reduced throughput. Which is MOST likely occurring?
- A) A DDoS attack
- B) Ethernet collisions
- C) A broadcast storm
- D) Routing loops

**20.** In a dense environment with overlapping AP coverage, which channel width should the administrator choose to minimize interference in the 2.4 GHz spectrum?
- A) 160 MHz
- B) 11 MHz
- C) 80 MHz
- D) 40 MHz
- E) 20 MHz

---

## Answer Key

| # | Answer | Reasoning |
|---|--------|-----------|
| 1 | **D — Reverse the fibers** | "Try **first**" = quickest, most common cause on a fresh fiber patch: TX/RX polarity. Re-terminating and inspecting runs take far longer; check those after the 10-second fix fails ([[L22]]) |
| 2 | **C — Bottlenecking** | Fast on one side of the IPS, slow on the other → the IPS's inspection throughput is the narrowest point ([[L24]]: firewalls/IPS often inspect far below interface speed) |
| 3 | **C — Not enough IP addresses in the DHCP scope** | Worked for early users, fails for later ones = **pool exhaustion** → APIPA ([[L23]]) |
| 4 | **D — TX/RX is reversed** | Both patch cords swapped for known-good ones, device known-good → remaining variable is the cross-connect/panel polarity. Each patch-panel hop can flip A/B ([[L22]]) |
| 5 | **A — Rear** | Intake from the **cold aisle** (front), exhaust into the **hot aisle** (rear). All racks in a row face the same way ([[L12]]) |
| 6 | **C — Missing default route** | Inter-VLAN (local) works, internet fails at the **router** level after a change → gateway of last resort lost ([[L23]]) |
| 7 | **C — Toner** | Tone generator on the jack, probe in the closet ([[L25]]). Cable tester verifies wiring, it doesn't *find* the cable; VFL is for fiber |
| 8 | **A — PoE power budget exceeded** | Only PoE devices affected, intermittent reboots under load → total draw exceeds the switch's PoE budget ([[L22]]) |
| 9 | **B — CRC errors and collisions** | The counter signature: CRC/FCS + runts on the full side, **late collisions** on the half side. Limited throughput is the *consequence*; CRC + collisions is the specific, testable result ([[L22]]) |
| 10 | **A — nmap** | Port scanner. tracert = path; dig/nslookup = DNS (and the name already resolves) ([[L25]]) |
| 11 | ~~A — Port security violation~~ ❌ → most likely **B — An incorrect cable type was installed** (unconfirmed) | Port security was marked **wrong**. Remaining logic: a NIC installed "to improve performance" is faster (e.g., 10GBASE-T needs **Cat6a**, or an SFP+ fiber NIC needs fiber/DAC) — the existing cable no longer matches the new interface. TX/RX transposition is unlikely on copper (Auto-MDI-X); PoE doesn't apply to servers. **Lesson: the course frames NIC upgrades as a Layer 1 cable-match problem, not a port security problem** |
| 12 | **B — Switch port is shut down** | Cable seated but **no link lights on either end** → no Layer 1 link. An administratively-down (or err-disabled) port shows no link. "Switch is shut off" would affect every port, not one workstation; VLAN/gateway problems still show link lights |
| 13 | ~~D — Establish a theory~~ ❌ → **C — Verify Functionality** | Marked wrong for "Establish a theory"; Rollback/Implement don't fit, so the answer is **Verify full system functionality** — after the fix, confirm *every OSI layer* works (link, IP, DNS, application), not just the symptom. (CompTIA lists OSI top-down/bottom-up as *approaches* under step 2 — but this course's question treats "checking each level is functioning" as verification) |
| 14 | **A — Document findings and lessons learned** ✅ | Coursera's explanation: "Once you have implemented a solution **and verified functionality**, you should document" — confirms verify was simply omitted from the options |
| 15 | **D — Identify the problem** | Checking logs / "what has changed" = step 1 sub-tasks ([[L21]]) |
| 16 | **Unresolved** ❌ | Single-select, and every option is an official step 1 sub-task (identify symptoms, gather information, determine what changed, duplicate the problem). The submitted answer was marked wrong. Best remaining guess: **Identify Symptoms** (closest wording to "identifying a problem"). Update this row if the correct answer is revealed |
| 17 | **C — Jitter** | *Inconsistency* in delay = jitter. Consistent delay = latency ([[L24]]) |
| 18 | **C — tracert** | Shows the hop-by-hop path and where it stops ([[L25]]) |
| 19 | **C — A broadcast storm** | Redundant links + **unmanaged** switches (no STP) → Layer 2 loop → broadcasts circulate endlessly ([[L23]]). Routing loops are Layer 3 |
| 20 | **E — 20 MHz** | 2.4 GHz has only 3 non-overlapping 20 MHz channels (1/6/11). 40 MHz bonding consumes two-thirds of the band; 80/160 MHz don't exist in 2.4 GHz ([[L11]]) |

---

## Exam Traps

| Trap | Correct Thinking |
|------|-----------------|
| "Try FIRST" on a new fiber link | Cheapest/fastest common fix → **reverse the fibers** (polarity) before re-terminating |
| Patch cords swapped, still no fiber link | Remaining hop (cross-connect) has **TX/RX reversed** |
| New NIC → server unreachable | ⚠️ Coursera marked port security **wrong** — think **cable type mismatch** with the faster NIC (Cat6a / fiber / DAC) |
| No link lights on either end, cable seated | Port **administratively shut down** (or err-disabled), not VLAN/gateway |
| Inter-VLAN OK, internet fails (router) | **Missing default route** |
| Unmanaged switches + redundant links | **Broadcast storm** (no STP) — not a routing loop |
| Duplex mismatch result | **CRC errors + (late) collisions** |
| Latency vs jitter | Delay = latency; *variation/inconsistency* in delay = jitter |
| "Checking each OSI level is functioning" | **Verify Functionality** (per this course) — OSI *approaches* for diagnosis are step 2, but confirming every layer works after the fix is step 6 |
| Next step after implement | **Verify** (step 6) — then document |
| 2.4 GHz channel width in dense deployments | **20 MHz** |
| Hot/cold aisle | Intake front (cold), exhaust **rear** (hot) |
| Fast on one side of IPS/firewall | **Bottleneck** at the inspection device |

---

## Gaps Found

- **Q5 (hot aisle/cold aisle)** comes from objective 2.4 — physical installations ([[L12]]), not Domain 5.
- **Q20 (channel width)** is Domain 2 wireless ([[L11]]): 2.4 GHz supports only 20/40 MHz; 5 GHz supports 20/40/80/160; 6 GHz up to 320 MHz (Wi-Fi 7). Wider channel = more throughput per client but fewer non-overlapping channels → more co-channel interference in dense deployments.
- **Q14/Q16** are flawed/ambiguous as written — know the official 7-step order cold so you can pick the "least wrong" answer.
- **Missed (Q11, Q13)** — see Weak Areas on [[certs/network-plus]].

---

## Lesson Cross-Reference

| Questions | Topic | Lesson |
|-----------|-------|--------|
| 13, 14, 15, 16 | Troubleshooting methodology | [[L21]] |
| 1, 4, 8, 9, 11, 12 | Cabling, fiber polarity, PoE, duplex, port status | [[L22]] |
| 3, 6, 19 | DHCP exhaustion, default route, broadcast storm/STP | [[L23]] |
| 2, 17 | Bottleneck, jitter | [[L24]] |
| 7, 10, 18 | Toner, nmap, tracert | [[L25]] |
| 5 | Hot/cold aisle | [[L12]] |
| 20 | 2.4 GHz channel width | [[L11]] |
