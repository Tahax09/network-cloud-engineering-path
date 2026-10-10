---
id: WS07
aliases: [WS07]
title: Coloring Traffic
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Module 2 - The Wireshark Interface
date: 2026-10-10
status: Complete
quiz_attempted: No
weak_areas: []
lab_file: raw/assets/wireshark-101/Pre-Lab-SlowNetwork.pcapng
tags: [wireshark, coloring-rules, tcp-syn, display-filter, drag-to-filter, rule-priority, bad-tcp, profiles, threat-hunting]
---

# WS07 — Coloring Traffic

## Summary
Coloring rules make important packets **jump out** while you scroll through thousands of them. A coloring rule is just **a display filter + a color**. The lesson builds a rule for **TCP SYNs (new connections)** in four steps:
1. Find the field in Packet Details (`tcp.flags.syn`).
2. **Drag it to the display filter bar** to get `tcp.flags.syn == 1`. That's your first filter, with no syntax to memorize.
3. **View → Coloring Rules → +**, paste the filter, and pick a **bright green** background.
4. **Drag the rule just below "Bad TCP"**: rules are **top-down, first match wins**, so a *retransmitted* SYN still shows as Bad TCP (black/red) while normal SYNs are green.

---

## Core Concepts

### Why color SYNs
In the default profile, the Pre-Lab's packets all look the same except the bright red **TCP RST** at the end ([[WS05 - The Default Interface]]). New connections (**SYN**, **SYN/ACK**) disappear in the crowd. With a SYN rule, every new connection stands out, even in a multi-million-packet file. The instructor uses **green, not red**, because red means bad and a SYN on its own is normal.

### Step 1: find the field (let Wireshark tell you the filter)
Select **frame 1** (the SYN) → Packet Details → **Transmission Control Protocol** → **Flags**:
```
Flags: 0x002 (SYN)
  ... .0.. .... = Acknowledgment: Not set
  ... .... ..1. = Syn: Set        ← tcp.flags.syn == 1
  ... .... ...0 = Fin: Not set
```
- Wireshark's **expert/summary lines** (the bracketed blue lines) already point out what's notable about the packet.
- **The field's filter name appears in the status bar (bottom left)** when you click it: `tcp.flags.syn`. That's the trick for never memorizing filters.

### Step 2: drag the field to the display filter bar
**Left-click and drag** the `Syn: Set` line onto the **display filter bar** and drop it when the green **+** appears. The filter becomes `tcp.flags.syn == 1`, and the Pre-Lab file shows **2 packets** (verified: frame 1 `[SYN]`, frame 2 `[SYN, ACK]`), because the SYN bit is set in both.

> Want *only* client SYNs (no SYN/ACKs)? `tcp.flags.syn == 1 && tcp.flags.ack == 0`. This lesson deliberately keeps both sides of the handshake.

**Filter bar validation:** **green** = valid syntax, **red** = invalid/incomplete (e.g., `tcp.flags.syn ==`). Same in the coloring-rule dialog. (Yellow = valid but possibly not what you meant, e.g. a deprecated operator.)

### Step 3: create the coloring rule
**View → Coloring Rules** (rules are stored in the **active profile**; the dialog title shows `Default`) → **+**:
| Field | Value |
|-------|-------|
| Name | `TCP SYNs` |
| Filter | `tcp.flags.syn == 1` |
| Background | **Bright green** (if you leave the defaults, the rows come out **white** and blend in) |
| Foreground | Black (default) |

### Step 4: priority, so place it carefully
Rules are evaluated **top to bottom; the first match colors the packet.**
```
Bad TCP          ← retransmitted SYN matches here first → black/red  ✔ error stays visible
TCP SYNs (new)   ← normal SYN / SYN-ACK → bright green
…
HTTP             ← tcp.port == 80 would otherwise catch the SYNs first
TCP SYN/FIN (default gray)
TCP
```
- **Below Bad TCP:** a SYN that had to be **retransmitted** (no answer from the server) is flagged by TCP expert analysis, so it should show as an **error**, not as a happy green SYN.
- **Above HTTP (bonus, verified):** in WS05, the default **HTTP rule (`tcp.port == 80`) outranked "TCP SYN/FIN"**, which is why the handshake was green-HTTP rather than gray. Testing the instructor's placement with a temporary colorfilters file, `tshark --color` reports **frames 1–2 = `TCP SYNs`**, frame 3 onward = `HTTP`. Placing the rule right below Bad TCP automatically puts it above the protocol rules.

### Coloring rule tips (beyond the lecture)
| Tip | How |
|-----|-----|
| **Temporarily highlight one conversation** | Right-click a packet → **Colorize Conversation** → TCP/IP/Ethernet (Ctrl+1…0 colors). Not saved; reset with View → Colorize Conversation → Reset |
| **Disable a rule without deleting it** | Uncheck it in the Coloring Rules dialog |
| **Share rules** | Coloring Rules → **Export** / **Import** (a `colorfilters` file), or ship a whole profile |
| **Where rules live** | `~/.config/wireshark/profiles/<profile>/colorfilters` (macOS/Linux); Default profile uses `~/.config/wireshark/colorfilters` once customized |
| **Check which rule matched a packet** | Packet Details → Frame → **Coloring Rule Name / String**, or `tshark --color -T fields -e frame.coloring_rule.name` |

### The instructor's profiles (`raw/assets/wireshark-101/Profiles/`)
Unzipped and inspected, these show how a seasoned analyst sets up rules:
| Profile | Notable coloring rules |
|---------|----------------------|
| **TCP Plain** (`Chris-TCPPlain.zip`) | Bad TCP → ICMP errors → **TCP Syn** → TCP Fin → TLS Handshake → DNS → ARP → ICMP → TCP RST → TTL low → checksum errors → protocol rules. SYN/FIN sit *high*, above HTTP |
| **Cybersec** (`Chris-Security.zip`) | Threat-hunting rules: **GeoIP country** in/out (`ip.geoip.src_country_iso in {…}`, needs a MaxMind DB), **High DNS Count** (`dns.count.answers > 5`), **Executable Header** (`frame contains` the PE string "This program cannot be run in DOS mode"), **4 NOP Problem** (`tcp.options contains 01:01:01:01`), **Invalid TCP** (`tcp.flags.syn==1 && tcp.window_size==0`: scanner fingerprint), **TrickbotClient** (`tls.handshake.ja3 == "6734…"`: a malware TLS fingerprint) |

> **Bug spotted in the Cybersec profile:** the country rule uses `{NK RU CN}`. The ISO 3166 code for North Korea is **KP**, not NK, so that part never matches. Fix it to `{KP RU CN}` after importing.

---

## Key Terms

| Term | Definition |
|------|-----------|
| Coloring rule | Display filter + foreground/background color, applied to the Packet List |
| Rule priority | Top-down order; the first matching rule colors the packet |
| TCP SYN | Synchronize flag; set in the first two packets of the handshake (SYN, SYN/ACK) |
| `tcp.flags.syn == 1` | Display filter matching packets with the SYN bit set |
| Drag-to-filter | Dragging a Packet Details field onto the filter bar to auto-build a filter |
| Filter bar colors | Green = valid, red = invalid, yellow = valid but suspicious |
| Bad TCP | Default rule for TCP expert-analysis problems (retransmissions, dup ACKs, zero window…) |
| Colorize Conversation | Temporary, unsaved highlighting of one conversation |
| JA3 | Fingerprint of a TLS ClientHello, used to spot specific malware/tools |

---

## Real-World Connections
- **Network+:** Rule priority is **ACL logic**: top-down, first match wins, order matters ([[L20 - Network Defense Techniques]]). SYN floods and scans are SYN-heavy ([[L19 - Network Attacks]]): with a SYN rule, a capture full of green with few replies shows a scan or flood immediately.
- **SOC / threat hunting:** The Cybersec profile turns Wireshark into a lightweight detection tool: JA3 fingerprints, PE downloads, scanner-style SYNs. That's the same idea as Suricata/Zeek signatures, applied visually.
- **k3s lab:** Make a `Tah-k3s` profile with rules for **Elasticsearch** (`tcp.port == 9200`), **Kubernetes API** (`tcp.port == 6443`), **Flannel VXLAN** (`udp.port == 8472`), and **DNS to CoreDNS**. Scroll a node capture and the traffic types separate by color.

---

## Troubleshooting Scenario
**Scenario:** An analyst added a green "TCP SYNs" rule at the **top** of the list. Later, investigating a "can't connect" ticket, they scroll the capture and see only healthy-looking green SYNs, yet users can't connect.
**Analysis:** With the SYN rule *above* Bad TCP, **retransmitted SYNs** (the client sending SYN again and again because the server never answers) are also painted green. The failure signature is hidden by the color.
**Fix:** Move "TCP SYNs" **below Bad TCP**. The repeated SYNs now show as **black/red** retransmissions, the classic "server unreachable / firewall dropping" pattern. Confirm with `tcp.analysis.retransmission && tcp.flags.syn == 1`.

---

## Hands-On Ideas
1. On the Pre-Lab file, build the **TCP SYNs** rule exactly as shown: drag `Syn: Set` to the filter bar, copy the filter, View → Coloring Rules → +, green background, drag it below Bad TCP. Clear the filter and confirm frames 1–2 are green.
2. Move the rule **below HTTP** and watch the SYNs turn HTTP-green again. That's the priority lesson.
3. Add a **TCP FIN** rule (`tcp.flags.fin == 1`) in a different color (the instructor's TCP Plain profile does this) to see connection ends.
4. Import `Chris-Security.zip`, **fix `NK` → `KP`**, and browse its rules. Open Lab 6/7 later and see which ones fire.
5. Use **Colorize Conversation** (right-click → Colorize Conversation → TCP → Color 1) on one TCP stream.
6. Verify from the CLI which rule each frame matched: `tshark -r Pre-Lab-SlowNetwork.pcapng --color -T fields -e frame.number -e frame.coloring_rule.name | head`.

---

## Quiz

1. What is a Wireshark coloring rule made of?
   - A) A display filter and a color  B) A capture filter and a font  C) A column and a profile  D) A BPF expression

2. Which display filter matches packets with the TCP SYN bit set?
   - A) `tcp.syn`  B) `tcp.flag = SYN`  C) `tcp.flags.syn == 1`  D) `syn == true`

3. On the Pre-Lab file, `tcp.flags.syn == 1` shows two packets. Why two?
   - A) Two clients connected  B) The SYN bit is set in both the SYN and the SYN/ACK  C) The SYN was retransmitted  D) A bug

4. What's the easiest way to build a filter without memorizing syntax?
   - A) Read the manual  B) Use a capture filter  C) Type random text  D) Drag the field from Packet Details onto the display filter bar (or right-click → Apply as Filter)

5. The filter bar turns red. What does that mean?
   - A) The filter matched packets  B) The filter syntax is invalid or incomplete  C) Coloring is disabled  D) The capture has errors

6. Why place the "TCP SYNs" rule *below* "Bad TCP"?
   - A) Alphabetical order  B) So retransmitted SYNs still show as errors (first match wins)  C) Bad TCP must always be last  D) It doesn't matter

7. In the default profile, why do port-80 SYNs not show the gray "TCP SYN/FIN" color?
   - A) Gray is disabled  B) The HTTP rule (`tcp.port == 80`) is higher in the list and matches first  C) SYNs are never colored  D) Port 80 is encrypted

8. How do you temporarily highlight a single conversation without creating a rule?
   - A) Right-click → Colorize Conversation  B) Edit the colorfilters file  C) Apply a capture filter  D) Change profiles

**Answers:** 1-A, 2-C, 3-B, 4-D, 5-B, 6-B, 7-B, 8-A

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[WS05 - The Default Interface]] — Default coloring rules and the HTTP-vs-SYN/FIN priority finding
- [[WS06 - Configuring Columns]] — Customizing the Packet List
- [[WS00 - Introduction]] — Instructor profiles
- [[wireshark]] — Wireshark reference · [[wireshark-101]] — Course progress
- [[L20 - Network Defense Techniques]] — First-match ACL logic · [[L19 - Network Attacks]] — SYN floods/scans
