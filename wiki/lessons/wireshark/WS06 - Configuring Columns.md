---
id: WS06
aliases: [WS06]
title: Configuring Columns
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Module 2
date: 2026-10-10
status: Complete
quiz_attempted: No
weak_areas: []
lab_file: raw/assets/wireshark-101/Pre-Lab-SlowNetwork.pcapng
tags: [wireshark, columns, delta-time, time-display-format, utc, time-of-day, ttl, apply-as-column, preferences, sorting]
---

# WS06 — Configuring Columns

## Summary
Columns put a value from **every packet** in the Packet List so you can **see and sort** it at a glance. The lesson shows **three ways to add a column**: (1) **Preferences → Columns → +** (e.g., a **Delta time displayed** column), (2) **drag a field** from Packet Details onto the column header (the "+" drop marker), (3) **right-click a field → Apply as Column**. Remove or hide one by right-clicking the column header. Two habits to build: the **Delta column, sorted**, finds the biggest delays instantly, and **UTC time of day** keeps teammates in different timezones aligned. The lesson also introduces the **IP TTL** column for judging **how far away** a device is and **where the capture was taken**.

---

## Core Concepts

### Method 1: Preferences → Columns
| OS | Path |
|----|------|
| Windows / Linux | **Edit → Preferences → Appearance → Columns** |
| **macOS** | **Wireshark → Settings…** (Preferences) → Appearance → Columns |

1. Click **+**, double-click the title and type `Delta`.
2. Set **Type** to **Delta time displayed**.
3. Drag the row up so Delta sits **next to Time**, then click OK.

Now there are two time columns: **Time** (follows View → Time Display Format, e.g. seconds since first packet: the "stopwatch") and **Delta** (time since the previous *displayed* packet).

**Sort by Delta (click the header)** to jump to the worst gaps. On the Pre-Lab file (verified with tshark):

| Frame | Delta | What it is | Matters? |
|-------|-------|-----------|----------|
| 121 | 44.99 s | TCP Keep-Alive | No: idle connection |
| 119 | 44.96 s | TCP Keep-Alive | No |
| 123 | 42.13 s | RST, ACK | No: server closing the idle connection |
| **7** | **20.59 s** | **HTTP/1.1 200 OK** | **Yes: the server delay from [[WS01 - Pre-Lesson - Your First Lab]]** |

The lesson here: **the biggest delta isn't always the problem.** Check the Info column. Keep-alives and teardown gaps are idle time, not user-facing delay.

> **Delta time vs delta time displayed (beyond the lecture):** *Delta time* measures from the previous **captured** packet; *delta time displayed* measures from the previous packet **visible under the current filter**. Displayed is the useful one. Filter on one TCP stream (`tcp.stream == 0`) and the deltas become the gaps *within that conversation*.

### Time Display Formats, revisited
| Format (View → Time Display Format) | Use |
|-------------------------------------|-----|
| **Seconds Since First Captured Packet** | The default stopwatch; used constantly |
| **Time of Day** | Wall-clock time, **converted to the viewer's system timezone** |
| **UTC Time of Day** | Same moment for everyone, so use it when collaborating across timezones or correlating with logs/SIEM |

**The timezone trap:** a packet captured at **3 p.m. in New York** shows as **12 noon** when opened on the Pacific coast in *Time of Day* mode. Same packet, different local display. **UTC** avoids that. On your Mac, the Pre-Lab's first packet shows as `13:45:30 +01:00` local and `12:45:30 Z` UTC.

> Tip (beyond the lecture): pcap timestamps are stored in UTC internally; only the *display* converts. Server logs are usually UTC too, so match them against **UTC Time of Day**.

### Method 2: Drag a field to the column bar
In Packet Details, expand a header (e.g., **Internet Protocol → Time to Live**), **left-click and drag** the field onto the Packet List header row, and drop it where the **"+"** appears. A TTL column appears for every packet.

### Method 3: Right-click → Apply as Column
Right-click any field in Packet Details → **Apply as Column** (also *Ctrl/⌘+Shift+I*). Same result as dragging.

### Managing columns
Right-click any **column header** to:
- **uncheck** a column to hide it temporarily (it stays in the list, ready to re-enable)
- **Remove This Column** to delete it
- **Edit Column** to rename it or change its field (e.g., retitle `Time to Live` → `TTL`)
- set alignment, or resize to contents

Columns are saved in the **active profile** ([[WS05 - The Default Interface]]).

### Why TTL is a great column
TTL = **number of router (Layer 3) hops** a packet may cross; each router or L3 switch **decrements it by 1** and drops it at 0 (sending ICMP *Time Exceeded*, the basis of traceroute, [[L25 - Troubleshooting Tools]]). It's not seconds.

**Typical starting values (beyond the lecture):**
| OS / device | Initial TTL |
|-------------|-------------|
| Linux, macOS, Android, iOS | **64** |
| Windows | **128** |
| Network gear (Cisco IOS etc.) | **255** |

**Hops away = nearest initial value above the observed TTL − observed TTL.**

**On the Pre-Lab file** (`tshark -T fields -e ip.src -e ip.ttl`):
| Source | Observed TTL | Likely initial | Hops away | Conclusion |
|--------|-------------|----------------|-----------|------------|
| 172.16.0.13 (client) | **128** | 128 (Windows) | **0** | The capture was taken **on the client itself**, matching the 1 ms client ACK seen in [[WS01 - Pre-Lesson - Your First Lab]] |
| 10.0.0.100 (server) | **111** | 128 (Windows) | **17** | The server is ~17 L3 hops away, consistent with a WAN path and 105 ms RTT |

**TTL uses for analysis:**
- **Capture position:** an unchanged initial TTL means the capture is next to (or on) that host.
- **Distance / path changes:** a TTL that shifts mid-conversation means the route changed (or traffic came via a different path/load balancer).
- **Spoofing or a middlebox:** a "server" packet with an unexpected TTL (e.g., a RST with TTL 64 while real server packets have 111) points to a **firewall, IPS or attacker injecting packets** ([[L19 - Network Attacks]]).

---

## Key Terms

| Term | Definition |
|------|-----------|
| Column | A per-packet field shown in the Packet List; sortable |
| Delta time displayed | Seconds since the previous *displayed* packet |
| Delta time | Seconds since the previous *captured* packet |
| Time Display Format | View setting controlling the Time column (since first packet, time of day, UTC…) |
| Time of Day | Wall-clock capture time shown in the viewer's local timezone |
| UTC Time of Day | Capture time in UTC, the same for every viewer |
| Apply as Column | Right-click action turning a Packet Details field into a column |
| TTL (Time to Live) | IPv4 hop counter decremented by each L3 device (IPv6: Hop Limit) |
| Initial TTL | OS default starting value (64 / 128 / 255) used to estimate hop count |

---

## Real-World Connections
- **Network+:** TTL and traceroute ([[L25 - Troubleshooting Tools]]). TTL-based loop detection: a routing loop shows the same packet with TTL dropping by 1 each pass ([[L23 - Network Service Troubleshooting]]). NTP/UTC for log correlation ([[L16 - IP Services]]).
- **k3s lab:** Pod → pod on the **same node** is bridged through `cni0` (L2), so the receiver sees **TTL 64**. Pod → pod **across nodes** is routed by the sending node's kernel into `flannel.1` and by the receiving node's kernel out to `cni0`, so expect about **TTL 62** on the inner packet. Capture both cases and add a TTL column to see the hop count. A TTL column in Kubeshark or Wireshark tells you whether a packet crossed a routing hop.
- **SOC:** Correlating a PCAP with SIEM alerts? Switch to **UTC Time of Day** first. Unexpected TTLs on RST packets are a classic sign of **IPS/firewall-injected resets** (covered with `Lab 6-TCPResets` later).

---

## Troubleshooting Scenario
**Scenario:** An analyst in Berlin and a colleague in Seattle review the same PCAP over a call. One says "the error is at 14:05," the other sees nothing at 14:05. They also disagree on which delay matters, because the biggest Delta value points to a 45 s gap.
**Fix:**
1. Both switch to **View → Time Display Format → UTC Time of Day**, so timestamps match.
2. In the sorted Delta column, check the **Info** for each top gap. The 45 s gaps are **TCP keep-alives** (idle). The real problem is the 20.6 s gap before a **200 OK** (server response).
3. Add a **TTL** column to confirm where the capture was taken (TTL 128 from the client means a client-side capture), so they know the gaps reflect what the user experienced.

---

## Hands-On Ideas
1. Add the **Delta** column (Wireshark → Settings → Appearance → Columns on your Mac), place it next to Time, and sort descending on the Pre-Lab file. Confirm frames 121, 119, 123, 7 are at the top.
2. Drag **ip.ttl** to the column bar, then rename it to `TTL` via right-click → Edit Column.
3. Add **tcp.stream** and **tcp.len** (TCP payload length) with Apply as Column. You'll use these constantly in the TCP labs.
4. Toggle Time between *Seconds Since First Captured Packet*, *Time of Day*, and *UTC Time of Day*.
5. CLI version of your columns:
   ```bash
   tshark -r Pre-Lab-SlowNetwork.pcapng -T fields -E header=y \
     -e frame.number -e frame.time_relative -e frame.time_delta_displayed -e ip.src -e ip.ttl -e _ws.col.Info
   ```
6. Find your own OS's TTL: `ping -c1 127.0.0.1` on macOS shows `ttl=64`; ping a Windows box and you'll see 128.

---

## Quiz

1. On macOS, where do you find Wireshark's Preferences to add a column?
   - A) Edit → Preferences  B) Capture → Options  C) View → Columns  D) The Wireshark (app) menu → Settings/Preferences

2. What does a "Delta time displayed" column show?
   - A) Time since the previous displayed packet  B) Time since the first packet  C) Wall-clock time  D) TTL

3. Sorting the Delta column, the top values are 45 s gaps labeled TCP Keep-Alive. What should you conclude?
   - A) That is the user-facing delay  B) These are idle-connection gaps; check further down for real delays such as a slow response  C) The capture is corrupt  D) The network is congested

4. A PCAP captured at 3 p.m. in New York is opened in Los Angeles with "Time of Day" format. What time is displayed?
   - A) 3 p.m.  B) 6 p.m.  C) 12 noon  D) Midnight

5. Which time format should a globally distributed team use when discussing the same capture?
   - A) Seconds since first packet  B) Time of Day  C) UTC Time of Day  D) Seconds since epoch

6. Which of these is NOT a way to add a column, according to the lesson?
   - A) Preferences → Columns → +  B) Drag a field to the column header  C) Right-click a field → Apply as Column  D) Statistics → Conversations

7. Packets from a server arrive with TTL 111. If the server is Windows, how many router hops away is it?
   - A) 11  B) 17  C) 111  D) 128

8. Client packets in a capture show TTL 128 (Windows). What does that tell you?
   - A) The capture was taken on or right next to the client  B) The client is 128 hops away  C) There's a routing loop  D) The packets are spoofed

**Answers:** 1-D, 2-A, 3-B, 4-C, 5-C, 6-D, 7-B, 8-A

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[WS05 - The Default Interface]] — Panes and profiles
- [[WS01 - Pre-Lesson - Your First Lab]] — The 20 s gap found again with the Delta column
- [[wireshark]] — Wireshark reference · [[wireshark-101]] — Course progress
- [[L25 - Troubleshooting Tools]] — TTL and traceroute · [[L16 - IP Services]] — NTP/UTC
