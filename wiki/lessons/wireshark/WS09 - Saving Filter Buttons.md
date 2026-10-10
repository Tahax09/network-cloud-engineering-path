---
id: WS09
aliases: [WS09]
title: Saving Filter Buttons
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Module 2 - The Wireshark Interface
date: 2026-10-10
status: Complete
quiz_attempted: No
weak_areas: []
lab_file: raw/assets/wireshark-101/Pre-Lab-SlowNetwork.pcapng
tags: [wireshark, filter-buttons, display-filter, tcp-analysis-flags, bad-tcp, profiles, dfilter-buttons, field-references, signatures, threat-hunting]
---

# WS09 — Saving Filter Buttons

## Summary
**Filter buttons** save a display filter as a **one-click toolbar button**, so you don't have to remember the syntax. They're stored in the **active profile** ("Wireshark 101"). The lesson builds the analyst's most-used button: **`Bad TCP` → `tcp.analysis.flags`**, which shows everything TCP expert analysis flagged. It shows how to **add** one (the **+** at the right end of the display filter bar → Label + Filter → OK) and how to **edit or remove** one (right-click a button → **Filter Button Preferences** → select → −). The payoff comes later: complex conversation, protocol and **attack-signature** filters become single clicks.

---

## Core Concepts

### Step 1: write and test the filter
Type `tcp.analysis.flags` in the display filter bar. It turns **green** (valid), and Enter applies it.

**On the Pre-Lab file (verified):** 4 matches, frames **119–122**:
| Frame | Expert flag |
|-------|-------------|
| 119 | TCP keep-alive segment |
| 120 | ACK to a TCP keep-alive segment |
| 121 | TCP keep-alive segment |
| 122 | ACK to a TCP keep-alive segment |

These keep-alives are **harmless here** (an idle connection, [[WS01 - Pre-Lesson - Your First Lab]]). The instructor notes that later labs show cases where keep-alives *do* matter. The point of `tcp.analysis.flags` is to **see everything TCP flagged first**, then judge it.

### Step 2: save it as a button
1. Click the **+** at the far **right** of the display filter bar.
2. A small form appears: **Label** = `Bad TCP`, **Filter** = `tcp.analysis.flags` (pre-filled from the bar), optional comment → **OK**.
3. Clear the filter, then click **Bad TCP**, and the filter applies instantly.

### Step 3: edit / remove a button
Right-click any button for: **Filter Button Preferences…** · Edit · **Disable** · **Remove** · Copy.
In *Filter Button Preferences*, the list shows every button and its filter. Select the mistaken one (the lesson's `HTTP` example) → **−** → OK.

Buttons are saved in the profile folder as `dfilter_buttons` (`~/.config/wireshark/profiles/Wireshark 101/dfilter_buttons`), one line per button: `"TRUE","Label","filter","comment"`. `FALSE` = disabled.

### `tcp.analysis.flags` vs the "Bad TCP" coloring rule (beyond the lecture)
The **default Bad TCP coloring rule** ([[WS05 - The Default Interface]]) is more selective than the lesson's button:
| Filter | Includes keep-alives & window updates? |
|--------|---------------------------------------|
| `tcp.analysis.flags` (lesson button) | **Yes**: everything flagged |
| `tcp.analysis.flags && !tcp.analysis.window_update && !tcp.analysis.keep_alive && !tcp.analysis.keep_alive_ack` (default coloring rule) | No: only problem flags |
| `tcp.analysis.flags && !tcp.analysis.window_update` (instructor's TCP Plain button) | Keep-alives yes, window updates no |

Use the broad one to see everything on first look; save the narrow one as a second button ("TCP Problems") to cut noise on busy captures.

---

## Power Features Found in the Instructor's Profiles (beyond the lecture)
Unzipped from `raw/assets/wireshark-101/Profiles/` (`dfilter_buttons`):

### 1. Grouped buttons with `//`
A label like `TCP Conn//SYNs` creates a **dropdown button "TCP Conn"** containing **"SYNs"**. That keeps a toolbar with 30+ buttons usable.
- **TCP Plain** groups: `TCP Conn//` (Bad TCP, TCP RST, Low RWin, SYNs, Full Handshake, Retransmissions, Slow iRTT), `Slow Stuff//` (Slow HTTP, TCP Delta, DNS), `Bcast//`, `Signatures//`.
- **Cybersec** groups: `TCP//`, `NMAP//`, `Signatures//`, `Countries//`, `HTTP//`, `Malware//`, `Naming//`, `Ping//`, `DNS//`.

### 2. Field references to the selected packet: `${field}`
`tcp.stream == ${tcp.stream}` ("#TCP Stream" / "TCP Stream" button) **filters on the stream of whichever packet you've selected**. One button isolates any conversation. Supported since Wireshark 4.0 (*display filter field references*).

### 3. Useful buttons to copy into Wireshark 101
| Label | Filter | Use |
|-------|--------|-----|
| Bad TCP | `tcp.analysis.flags` | First look (the lesson) |
| Retransmissions | `tcp.analysis.retransmission` | Loss |
| Slow iRTT | `tcp.analysis.initial_rtt > 1` | Slow handshakes / far servers |
| Low RWin | `tcp.window_size <= 2920` | Receiver window nearly full |
| TCP Delta | `tcp.time_delta > 1` | Gaps within a TCP stream |
| Slow HTTP | `http.time > 2` | Slow web responses ([[WS01 - Pre-Lesson - Your First Lab]]) |
| Slow DNS | `dns.time > 0.05` | Slow name resolution |
| Full Handshake | `tcp.flags.syn==1 or (tcp.seq==1 and tcp.ack==1 and tcp.len==0)` | SYN, SYN/ACK + the final ACK |
| TCP Stream | `tcp.stream == ${tcp.stream}` | Isolate the selected conversation |
| No Broadcast/Chatter | `!(eth.addr == ff:ff:ff:ff:ff:ff or arp or icmp or stp)` | Remove LAN noise |
| TLS Handshake | `tls.handshake.type in {1, 2}` | ClientHello / ServerHello |

### 4. Security signature buttons (Cybersec)
| Signature | Filter | Detects |
|-----------|--------|---------|
| Nmap SYN scan | `tcp.window_size_value == 1024 and tcp.flags.syn==1` | Nmap's default SYN-probe window |
| Null / Xmas scans | `tcp.flags == 0` / `tcp.flags == 0x02b` | Stealth scan techniques ([[L19 - Network Attacks]]) |
| SYN without options | `(tcp.flags == 0x002) && (tcp.hdr_len == 20)` | Crafted/tool SYNs (real OS stacks send MSS, SACK, WS) |
| Password strings | `frame matches "(?i)(pass)"` | Cleartext credentials (FTP, HTTP Basic, Telnet) |
| Old TLS | `tls.handshake.version in {0x0300,0x0301,0x0302}` | SSLv3 / TLS 1.0 / 1.1 |
| log4j | `frame contains "jndi"` *(profile has `ip contains jndi`, broken in 4.x)* | Log4Shell (`${jndi:…}`) exploitation attempts |
| Gobuster | `http.user_agent contains "gobuster"` | Directory brute-forcing |
| MZ offset | `eth[0xe9:2] == 4d:5a` | "MZ" Windows executable header at a fixed offset |
| Trickbot JA3 | `tls.handshake.ja3 == "6734f374…"` | Known malware TLS fingerprint |
| Inbound connections | `(tcp.flags == 0x012) and (ip.src == RFC1918)` | Internal hosts *answering* SYNs, i.e. listening services |

### 5. ⚠️ Six instructor buttons fail on Wireshark 4.6 (verified)
All 81 buttons in both profiles were tested with `tshark -Y` (excluding the `${…}` field-reference ones, which need a selected packet). These six fail because 4.x tightened display-filter syntax:
| Profile | Button | Original (fails) | Error | Fixed (verified) |
|---------|--------|------------------|-------|------------------|
| TCP Plain | Bcast//Any IP Broadcast | `ip.host matches "\.255$"` (stored as single `\`) | `\. is not a valid character escape sequence` | `ip.host matches "\\.255$"` |
| TCP Plain | TLS Handshake | `tls.handshake.type in {1 2}` | `"2" was unexpected` | `tls.handshake.type in {1, 2}` |
| TCP Plain | Any HTTP Host | `http.host matches "\.(org\|com\|net)"` | invalid escape | `http.host matches "\\.(org\|com\|net)"` |
| Cybersec | Signatures//log4j | `ip contains jndi` | `"jndi" is not a valid byte string` | `frame contains "jndi"` |
| Cybersec | DNS//WhatIsMy | `frame matches whatismy` | `Matches requires a double quoted string` | `frame matches "whatismy"` |
| Cybersec | NMAP//UserAgentNmap | `http.user_agent matches nmap` | same | `http.user_agent matches "nmap"` |

**4.x syntax rules behind these:** strings must be **double-quoted**, regex backslashes must be **doubled** inside quotes (`"\\."`), and set members are **comma-separated** (`{1, 2}`). After importing the profiles, fix these via right-click a button → Edit.

> **Bug spotted:** Cybersec's button **`TCP//SYNs and SYNACKs`** uses `tcp.flags.syn==1 and tcp.flags.ack==1`, which matches **SYN/ACKs only**. For both, use `tcp.flags.syn == 1` ([[WS07 - Coloring Traffic]]). (Also note the `NK` → `KP` country-code bug in this profile's coloring rules.)

---

## Key Terms

| Term | Definition |
|------|-----------|
| Filter button | Saved display filter on the toolbar; one click applies it |
| `tcp.analysis.flags` | Matches any packet flagged by TCP expert analysis (retransmissions, dup ACKs, zero window, keep-alives, window updates…) |
| Filter Button Preferences | Dialog listing all buttons; add, edit, remove |
| `dfilter_buttons` | Profile file storing the buttons |
| Grouped button (`A//B`) | Label syntax creating a dropdown menu A with item B |
| Field reference `${field}` | Inserts the selected packet's field value into a filter (4.0+) |
| Signature filter | Filter matching a known attack/tool/malware pattern |

---

## Real-World Connections
- **Network+:** The scan signatures (Null, Xmas, SYN scans) are the reconnaissance from [[L19 - Network Attacks]]. "Old TLS" and "Password strings" are the weak-crypto and cleartext-credential issues from [[L18 - Basic Network Security]].
- **SOC / CKS path:** Signature buttons are a manual version of IDS rules (Suricata/Snort). Clicking through them on a suspicious PCAP is a quick triage routine. Falco alerts in Kubernetes (CKS) often lead to "capture the pod and check": these buttons speed that up.
- **k3s lab:** Add a `k3s//` group to Wireshark 101: `k3s//ES` = `tcp.port == 9200`, `k3s//API` = `tcp.port == 6443`, `k3s//VXLAN` = `udp.port == 8472`, `k3s//CoreDNS` = `dns && ip.addr == 10.43.0.10`.

---

## Troubleshooting Scenario
**Scenario:** On a 2-million-packet capture, an analyst clicks **Bad TCP** (`tcp.analysis.flags`) and gets 40,000 hits, mostly **window updates** and **keep-alives**, so the real retransmissions are buried.
**Fix:** Add a second, narrower button **`TCP Problems`** = `tcp.analysis.flags && !tcp.analysis.window_update && !tcp.analysis.keep_alive && !tcp.analysis.keep_alive_ack`, plus **`Retransmissions`** = `tcp.analysis.retransmission`. Use Bad TCP for a quick overview and TCP Problems for triage. Group them under `TCP//` to keep the toolbar tidy.

---

## Hands-On Ideas
1. In **Wireshark 101**, add **Bad TCP** (`tcp.analysis.flags`) exactly as in the lesson. Click it on the Pre-Lab file and confirm frames 119–122.
2. Add an **HTTP** button, then remove it via right-click → Filter Button Preferences → −.
3. Add a grouped set: `TCP//Bad TCP`, `TCP//Problems`, `TCP//Retransmissions`, `TCP//Stream` (`tcp.stream == ${tcp.stream}`). Select a packet and click `TCP//Stream`.
4. Look at the saved file: `cat ~/.config/wireshark/profiles/"Wireshark 101"/dfilter_buttons`.
5. Import the instructor's profiles and click through the **Cybersec → NMAP//** buttons on later labs (e.g., Lab 6 resets).
6. CLI equivalent: `tshark -r Pre-Lab-SlowNetwork.pcapng -Y tcp.analysis.flags -T fields -e frame.number -e _ws.expert.message`.

---

## Quiz

1. What is the main benefit of filter buttons?
   - A) One-click reuse of saved display filters without retyping the syntax  B) Faster captures  C) They encrypt traffic  D) They replace capture filters

2. Where are filter buttons saved?
   - A) In the capture file  B) In the active profile  C) In the Windows registry  D) In the cloud

3. Which filter shows every packet flagged by TCP expert analysis?
   - A) `tcp.errors`  B) `tcp.bad`  C) `tcp.analysis.flags`  D) `expert.tcp`

4. How do you add a new filter button?
   - A) Statistics → Buttons  B) Click the + at the right end of the display filter bar, then set a label and filter  C) Edit → Copy  D) Capture → Options → Buttons

5. On the Pre-Lab file, Bad TCP matches 4 packets. What are they?
   - A) Retransmissions  B) Zero windows  C) Resets  D) Keep-alives and their ACKs

6. How do you remove a filter button?
   - A) Delete the profile  B) Right-click a button → Filter Button Preferences (or Remove) → delete it  C) Restart Wireshark  D) Clear the display filter

7. A button labeled `TCP Conn//SYNs` creates what?
   - A) Two separate buttons  B) A "TCP Conn" dropdown containing a "SYNs" item  C) A syntax error  D) A coloring rule

8. What does `tcp.stream == ${tcp.stream}` do?
   - A) Filters on the TCP stream of the currently selected packet  B) Shows all streams  C) Shows stream 0 only  D) Creates a column

**Answers:** 1-A, 2-B, 3-C, 4-B, 5-D, 6-B, 7-B, 8-A

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[WS08 - Creating Profiles]] — The Wireshark 101 profile
- [[WS07 - Coloring Traffic]] — Drag-to-filter, SYN filter
- [[WS05 - The Default Interface]] — Default Bad TCP coloring rule
- [[wireshark]] — Wireshark reference · [[wireshark-101]] — Course progress
- [[L19 - Network Attacks]] · [[L18 - Basic Network Security]]
