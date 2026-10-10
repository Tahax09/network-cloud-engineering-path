---
id: WS04
aliases: [WS04]
title: Packet Capture - Best Practices
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Lesson 1 - Packet Capture
date: 2026-10-10
status: Complete
quiz_attempted: No
weak_areas: []
tags: [wireshark, best-practices, capture-filter, display-filter, client-side-capture, ring-buffer, tap, multi-point-capture, mergecap, time-sync]
---

# WS04 — Packet Capture: Best Practices

## Summary
This lesson wraps up **Lesson 1 (packet capture)** with six rules. **The quality of the capture determines the quality of the analysis; if you never captured it, you can't analyze it.**
1. **No capture filter at the start.** Filtering assumes you already know the problem.
2. **Start on the client**, with every unrelated app closed.
3. **Use a ring buffer** for long or intermittent captures.
4. **Make sure the problem actually happened** while and where you were capturing.
5. **Own a TAP**, even a cheap USB-powered gigabit one.
6. *(Bonus)* **Capture from multiple points at once**, client and server side.

---

## Core Concepts

### 1. Don't start with a capture filter
A **capture filter** (BPF, set before capturing) **permanently discards** anything that doesn't match. A **display filter** only hides packets and can be changed at any time.

| You filter on… | …and miss |
|---------------|-----------|
| `port 80` ("it's a web app") | The app actually uses **HTTPS (443)** or a non-standard port |
| `host <server>` | **DNS lookups** and **authentication** (Kerberos/LDAP/OAuth) the client does first |
| `tcp` | **ICMP** errors from routers and switches (Unreachable, Fragmentation Needed, TTL exceeded) that explain the failure |

**Rule:** capture broadly, then **filter on display**. Capture filters are for when **volume forces it** (a busy server, a long ring buffer), once you already know what matters.

### 2. Start on the client
- Captures get **huge**. The client end is the **simplest** place to start.
- **Reduce noise:** have the user close email, other browser tabs, streaming audio and podcasts, and sync clients. Every unrelated flow is hay around your needle.
- The client capture **maps the dependencies**. Does it hit **DNS** first? An **auth server**? The **cloud** or the **local data center**? That tells you where to capture next.

### 3. Ring buffer for long-term capture
When you **don't know when** the problem occurs or **can't reproduce** it, use a ring buffer of smaller files rather than one giant file ([[WS02 - Your First Packet Capture]]).

### 4. Capture the problem, in time *and* place
The most common failure: a PCAP is sent for analysis and **nothing is wrong in it**, because the problem never happened during the capture, or happened on a path the capture point couldn't see.
- Get the user to note the **exact time** it happened (or have them press a key you can see, e.g. browse to a marker URL).
- Confirm the capture point is **on the path** of the failing traffic ([[WS03 - Where to Capture on the Network]]).

### 5. Invest in a TAP
Even a **100 Mbps / 1 Gbps copper TAP**, USB-powered and cheap online, belongs in an analyst's backpack. It avoids SPAN configuration and SPAN loss.

### 6. (Bonus) Capture from multiple locations simultaneously
The ideal troubleshooting capture: **client side + server side at the same time**, reproduce the problem, stop both.

| Client-side capture shows | Server-side capture shows |
|--------------------------|---------------------------|
| What the user experienced: total delay, retransmissions *from the client's view* | What the server actually **received** and **how long it took to respond** |
| Network RTT (SYN → SYN/ACK) | Server think time without network delay |

Comparing the two shows **where** packets were lost or delayed (sent at the client but never received at the server means the network path; received promptly but answered late means the server).

**Making two captures line up (beyond the lecture):**
- **Sync clocks** on both capture hosts with **NTP** ([[L16 - IP Services]]). Otherwise timestamps drift and the comparison is misleading.
- Match the same packet in both files by **TCP sequence number** or **IP ID** rather than by time.
- Combine the files: `mergecap -w both.pcapng client.pcapng server.pcapng`. Or shift one file's timestamps to correct an offset: `editcap -t -0.250 server.pcapng server_shifted.pcapng`.

### Handle captures as sensitive data (beyond the lecture)
Full captures can contain **credentials, session cookies, PII and health or payment data**. Store them encrypted, share them on a need-to-know basis, delete them when the case is closed, and consider `editcap -s 128` (truncate payloads) or sanitizing tools such as TraceWrangler before sending a capture to a vendor. Compliance rules (PCI DSS, GDPR, HIPAA, [[L18 - Basic Network Security]]) apply to PCAPs too.

---

## Best-Practice Checklist
```
☐ No capture filter (unless volume forces it; then filter on known-relevant hosts only)
☐ Capture point: client first, with background apps closed
☐ Long/intermittent → ring buffer (size × count = disk budget)
☐ Problem confirmed during the capture window, at this location (note the timestamp)
☐ TAP available for key links / the analyst's backpack
☐ Ideal: simultaneous client + server captures, NTP-synced
☐ Treat the PCAP as sensitive data
```

---

## Key Terms

| Term | Definition |
|------|-----------|
| Capture filter | BPF expression applied while capturing; non-matching packets are never saved |
| Display filter | Wireshark expression that hides or shows already-captured packets; reversible |
| Background noise | Unrelated traffic (mail, streaming, updates) that clutters a capture |
| Dependency mapping | Using a client capture to find every system the app touches (DNS, auth, back end) |
| Multi-point capture | Capturing at two or more locations simultaneously to locate loss or delay |
| mergecap / editcap | Wireshark CLI tools to merge capture files / edit them (time shift, truncate, convert) |
| Capture sanitization | Removing or truncating sensitive payloads before sharing a PCAP |

---

## Real-World Connections

**Network+ links:** These rules apply the troubleshooting methodology ([[L21 - Network Troubleshooting Methodology]]): *gather information* (client capture maps dependencies), *duplicate the problem* (ring buffer and confirmed timing), *question the obvious* (don't assume it's HTTP). Port assumptions ignore what [[L04 - Ports, Protocols & Services]] teaches: apps move to 443 and non-standard ports.

**k3s lab:** A slow Kibana dashboard is a **multi-point** problem: Kibana → Elasticsearch inside the cluster, browser → ingress outside it. Capture **both** at the same time:
```bash
# server side: on the node, ES traffic (no port filter at first, just the pod's interface)
sudo tcpdump -i vethXXXX -s 0 -w /var/tmp/es-side.pcap
# client side: on your Mac, the browser → ingress traffic
dumpcap -i en0 -w ~/caps/browser-side.pcapng
# afterwards
mergecap -w ~/caps/kibana-both.pcapng ~/caps/browser-side.pcapng es-side.pcap
```
Make sure the node and your Mac both run NTP (`timedatectl` on Ubuntu shows `System clock synchronized: yes`).

**Security / SOC:** "Don't capture-filter at the start" is even more important in an incident. Attacker traffic rarely uses the port you expect: C2 over DNS, or exfiltration over 443.

---

## Troubleshooting Scenario

**Scenario:** A junior analyst investigates a slow internal web app and captures on the user's laptop with the capture filter `tcp port 80` for an hour. The capture shows nothing unusual. The user insists the app "hangs for 10 seconds at login."

**Analysis:**
1. **Capture filter assumption:** the login uses **HTTPS** to an SSO provider, and the app also makes a **Kerberos/LDAP** call. None of it matched `port 80`.
2. **No timestamp:** nobody recorded when the hang happened.
3. **Single capture point:** even with the right traffic, a client-only view can't separate SSO server delay from network delay.

**Fix:** re-capture **without a capture filter**, apps closed, and the user noting the exact time of the hang. Display-filter afterwards (`dns || kerberos || tls.handshake || tcp.analysis.flags`). The capture shows a **10 s gap between the DNS query for the SSO host and its response**: the primary DNS server is unreachable and the client falls back to the secondary after a timeout. That would have been invisible under `tcp port 80`.

---

## Hands-On Ideas
1. **Prove rule 1:** capture with `-f "tcp port 80"` while browsing to an HTTPS site, then capture without a filter. Compare what's missing (DNS, QUIC/UDP 443, TLS).
2. **Dependency map:** close everything except one browser tab, capture while you log in to a site, then check *Statistics → Endpoints → IPv4* and *Statistics → Resolved Addresses*. That's the list of systems the "single" app talks to.
3. **Multi-point lab:** run `iperf3 -s` on your k3s node and the client on your Mac. Capture on both ends, check that NTP is synced, then `mergecap` and look at the same TCP stream from both sides.
4. **Sanitize a capture:** `editcap -s 96 big.pcapng sliced.pcapng`, and compare it with the Pre-Lab file's 96-byte snaplen ([[WS01 - Pre-Lesson - Your First Lab]]).

---

## Quiz

1. Why does the instructor advise against using a capture filter at the start?
   - A) Capture filters slow Wireshark down  B) They assume you know the problem, and discarded traffic (e.g., HTTPS, DNS, ICMP errors) can't be recovered  C) Capture filters aren't supported on Wi-Fi  D) Display filters don't work afterwards

2. Where should you start capturing when troubleshooting an application issue?
   - A) The core router  B) The firewall  C) The client, with unrelated apps closed  D) The cloud provider

3. What does starting on the client reveal besides the main app traffic?
   - A) Dependencies such as DNS, authentication servers, and cloud vs data center  B) Cable lengths  C) Switch CPU load  D) Nothing else

4. A capture comes back clean, but the user says the problem happened. What is the most likely cause?
   - A) Wireshark bug  B) The problem didn't occur during the capture window or on that capture point's path  C) The file was saved as pcapng  D) A TAP was used

5. What is the ideal capture setup for locating where delay or loss occurs?
   - A) One long capture on the server  B) Only SPAN on the core  C) Only NetFlow  D) Simultaneous client-side and server-side captures

6. What must be true when comparing client and server captures by timestamp?
   - A) Both use the same Wireshark version  B) Both capture hosts are NTP-synchronized (or offsets corrected)  C) Both files are pcap  D) Both used capture filters

7. Which tool combines two capture files into one?
   - A) capinfos  B) editcap  C) mergecap  D) dumpcap

8. Why treat PCAP files as sensitive?
   - A) They can contain credentials, cookies, and PII subject to compliance rules  B) They're large  C) They can't be compressed  D) They expire

**Answers:** 1-B, 2-C, 3-A, 4-B, 5-D, 6-B, 7-C, 8-A

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[WS02 - Your First Packet Capture]] — Ring buffers, Capture Options
- [[WS03 - Where to Capture on the Network]] — Capture points, TAPs
- [[WS01 - Pre-Lesson - Your First Lab]] — A client-side capture in action
- [[wireshark]] — Wireshark reference · [[wireshark-101]] — Course progress
- [[L21 - Network Troubleshooting Methodology]] · [[L16 - IP Services]] (NTP)
