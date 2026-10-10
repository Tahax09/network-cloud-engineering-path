---
id: WS05
aliases: [WS05]
title: The Default Interface
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Module 2
date: 2026-10-10
status: Complete
quiz_attempted: No
weak_areas: []
lab_file: raw/assets/wireshark-101/Pre-Lab-SlowNetwork.pcapng
tags: [wireshark, gui, layout, packet-list, packet-details, packet-bytes, status-bar, profile, coloring-rules]
---

# WS05 — The Default Interface

## Summary
**Module 2** starts here (after the *Introduction to Wireshark* module and its quiz). The goal is to **customize Wireshark so analysis is faster**. First you need to know the stock layout. The **status bar (bottom right)** shows the active **profile**, `Default`. Every change you make is saved to whichever profile is active. Wireshark 4.0+ has **three panes**: **Packet List** (top), **Packet Details** (bottom left, the protocol headers of the selected packet), and **Packet Bytes** (bottom right, hex + ASCII). **Coloring rules** are on by default and toggle with one toolbar button.

**Lab file:** `Pre-Lab-SlowNetwork.pcapng` (from [[WS01 - Pre-Lesson - Your First Lab]]).

---

## Core Concepts

### The three panes (Wireshark 4.x default layout)
```
┌───────────────────────────────────────────────────────────┐
│ Toolbar · Display filter bar                              │
├───────────────────────────────────────────────────────────┤
│ PACKET LIST: No. | Time | Source | Destination |          │
│              Protocol | Length | Info  (one row per frame) │
├──────────────────────────────┬────────────────────────────┤
│ PACKET DETAILS               │ PACKET BYTES               │
│ ▸ Frame 7: 572 bytes…        │ 0000  00 1a … ..           │
│ ▸ Ethernet II                │ hex offset │ hex │ ASCII   │
│ ▸ Internet Protocol v4       │                            │
│ ▸ Transmission Control Prot. │                            │
│ ▸ Hypertext Transfer Prot.   │                            │
├──────────────────────────────┴────────────────────────────┤
│ Status bar: expert info · file · Packets/Displayed · Profile: Default │
└───────────────────────────────────────────────────────────┘
```

| Pane (official name) | Instructor's name | Shows | Use it to |
|----------------------|-------------------|-------|-----------|
| **Packet List** | "packet summary view" | One row per frame: number, time, source, destination, protocol, length, info | Scan the conversation, spot gaps (Time) and problems (color, Info) |
| **Packet Details** | "packet detail view" | Expandable header tree for the **selected** packet: Frame → Ethernet → IP → TCP/UDP → application | Read any field. **Right-click a field → Apply as Column / Apply as Filter** (next lessons) |
| **Packet Bytes** | "hexadecimal and ASCII view" | Raw bytes in hex and ASCII; clicking a field highlights its bytes | Check what's actually on the wire; spot cleartext strings |

- The panes are **resizable and rearrangeable**: Edit → Preferences → Appearance → **Layout**. The default is `layout_type 2` = list on top, details and bytes side by side below. (This side-by-side split is new in 4.0; older versions stacked all three.) Panes can be hidden from the **View** menu.
- The layout is **per profile**. Your Mac currently has only the `Default` profile; the instructor's profiles haven't been imported yet ([[WS00 - Introduction]]).

### The status bar
| Area | Meaning |
|------|---------|
| Left: **expert info** indicator (colored dot) | Highest-severity expert message in the file. Click for Analyze → Expert Information |
| Middle | File name/size, **Packets** (total) vs **Displayed** (after the display filter) |
| Right: **Profile** | Active configuration profile. Click to switch, create, or import |

### Coloring rules
- **On by default.** The toolbar button with colored lines (**View → Colorize Packet List**) turns them off for plain black and white. The instructor prefers them on.
- Rules are evaluated **top to bottom, first match wins** (the same logic as ACLs, [[L20 - Network Defense Techniques]]). Edit them at **View → Coloring Rules**.
- **Default rules worth recognizing** (from Wireshark 4.6's `colorfilters`):

| Rule | Filter (simplified) | Look |
|------|--------------------|------|
| **Bad TCP** | `tcp.analysis.flags && !window_update && !keep_alive` | **Black background, red text** (retransmissions, dup ACKs, zero window…) |
| **ICMP errors** | `icmp.type in {3..5, 11}` | Black background, green text |
| **TCP RST** | `tcp.flags.reset eq 1` | **Dark red background**, yellow text |
| Checksum errors | `*.checksum.status == "Bad"` | Black / red |
| HSRP / STP / OSPF state change | e.g. `stp.type == 0x80` | Black / yellow |
| TCP SYN/FIN | `tcp.flags & 0x02 \|\| tcp.flags.fin == 1` | **Gray** |
| HTTP | `http \|\| tcp.port == 80` | Light green |
| TCP | `tcp` | Light purple |
| UDP | `udp` | Light blue |
| ARP | `arp` | Light tan |

**Verified on the Pre-Lab file** (`tshark --color -T fields -e frame.coloring_rule.name`): **122 frames = HTTP (green)**, **1 frame = TCP RST (dark red)**. Even the SYN/SYN-ACK are green, not gray: the **HTTP rule (`http || tcp.port == 80`) sits above "TCP SYN/FIN"** in the list, and first match wins. The keep-alives aren't flagged "Bad TCP" because that rule explicitly excludes them. **Rule order changes what you see**, so if you want handshakes gray on web traffic, move TCP SYN/FIN above HTTP.

---

## Key Terms

| Term | Definition |
|------|-----------|
| Packet List pane | Top pane, one summary row per frame |
| Packet Details pane | Expandable protocol header tree of the selected packet |
| Packet Bytes pane | Hex + ASCII dump of the selected packet |
| Profile | Named set of all customizations (layout, columns, colors, filters); shown in the status bar |
| Default profile | Stock profile active until you create or switch to another |
| Coloring rules | Ordered filter → color mappings applied to the packet list; first match wins |
| Colorize Packet List | Toggle that turns coloring on/off |
| Expert info indicator | Status-bar dot showing the most severe expert finding |
| Displayed vs Packets | Count after the display filter vs total captured |

---

## Real-World Connections
- **Network+:** The Packet Details tree **is the OSI/encapsulation model** ([[L01 - The OSI Model]]): Frame (L1 metadata) → Ethernet (L2) → IP (L3) → TCP (L4) → HTTP (L7). Expanding it in order is bottom-to-top troubleshooting ([[L21 - Network Troubleshooting Methodology]]).
- **k3s / Kubeshark:** Kubeshark's UI has the same split: request list, protocol detail, raw payload. The habits carry over.
- **SOC work:** The **Packet Bytes** pane is where cleartext credentials and suspicious strings jump out (e.g., `USER`/`PASS` in FTP, HTTP Basic auth). Security profiles often add coloring rules for those protocols.

---

## Troubleshooting Scenario
**Scenario:** A colleague says "Wireshark looks broken": the bytes pane is missing, there are no colors, and their columns differ from the training videos.
**Analysis:** None of that is a bug; it's **profile state**. Check the status bar: they're on a profile someone else changed (or Default with coloring toggled off).
**Fix:** Toggle **Colorize Packet List** back on. Restore the panes via **View → Packet Bytes** (or Edit → Preferences → Appearance → Layout). Better still, **create a new profile** for experiments so `Default` stays clean, and import the course profiles to match the videos.

---

## Hands-On Ideas
1. Open `raw/assets/wireshark-101/Pre-Lab-SlowNetwork.pcapng`. Click **frame 7** (the 200 OK). Expand Frame → Ethernet → IP → TCP → HTTP in Packet Details, and click each layer to see its bytes highlighted in Packet Bytes.
2. Toggle **Colorize Packet List** off and on. Then open **View → Coloring Rules** and find which rule colors frame 123 (the RST).
3. Change the layout under Preferences → Appearance → Layout, in a **new profile** named `Tah-Lab` (status bar → right-click Profile → New), and leave Default untouched.
4. CLI equivalent of the three panes:
   ```bash
   tshark -r Pre-Lab-SlowNetwork.pcapng -c 7                 # packet list
   tshark -r Pre-Lab-SlowNetwork.pcapng -Y frame.number==7 -V # packet details (verbose tree)
   tshark -r Pre-Lab-SlowNetwork.pcapng -Y frame.number==7 -x # packet bytes (hex + ASCII)
   tshark -r Pre-Lab-SlowNetwork.pcapng --color -T fields -e frame.number -e frame.coloring_rule.name   # which rule colored each frame
   ```

---

## Quiz

1. Where in the Wireshark window do you see which profile is active?
   - A) Title bar  B) Bottom-right of the status bar  C) Packet Details pane  D) The Capture menu

2. Which pane shows the expandable protocol headers of the selected packet?
   - A) Packet List  B) Packet Bytes  C) Packet Details  D) Status bar

3. In the default Wireshark 4.x layout, where is the hex/ASCII view?
   - A) Top  B) Bottom left  C) Bottom right  D) Hidden by default

4. You change columns and colors. Where are those changes saved?
   - A) In the capture file  B) In the currently active profile  C) In the Windows registry only  D) Nowhere, they reset on restart

5. How do you quickly turn off packet coloring?
   - A) Delete the colorfilters file  B) The Colorize Packet List toolbar button (View → Colorize Packet List)  C) Apply a display filter  D) Restart Wireshark

6. By default, how are packets matching the "Bad TCP" coloring rule (e.g., retransmissions) shown?
   - A) Green background  B) Black background with red text  C) Gray background  D) Light blue

7. How are coloring rules evaluated?
   - A) All matching rules blend their colors  B) Top to bottom; the first matching rule wins  C) Alphabetically  D) Randomly

**Answers:** 1-B, 2-C, 3-C, 4-B, 5-B, 6-B, 7-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[WS00 - Introduction]] — Profiles
- [[WS01 - Pre-Lesson - Your First Lab]] — Lab file used here
- [[wireshark]] — Wireshark reference · [[wireshark-101]] — Course progress
- [[L01 - The OSI Model]] — Packet Details = encapsulation layers
