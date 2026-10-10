---
id: WS01
aliases: [WS01]
title: Pre-Lesson - Your First Lab
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Pre-Lesson
date: 2026-10-10
status: Complete
quiz_attempted: No
weak_areas: []
lab_file: raw/assets/wireshark-101/Pre-Lab-SlowNetwork.pcapng
tags: [wireshark, packet-analysis, tcp-handshake, rtt, server-response-time, time-display-format, http, slow-application, client-network-server]
---

# WS01 — Pre-Lesson: Your First Lab ("Slow Network")

## Summary
This is the course kickoff: Wireshark is for **everyone** (NetOps, help desk, SecOps/SOC, threat hunters, developers), and the course is **lab-heavy, not slide-heavy**. The first lab answers the most common question in network troubleshooting: *"The network is slow. Is it the client, the network, or the server?"* Reading the timing of just six packets shows the network was fine (≈105 ms round trip) and the **application server took 20.6 seconds** to start responding. Upgrading WAN circuits or replacing copper with fiber would have changed nothing.

**Lab file:** `raw/assets/wireshark-101/Pre-Lab-SlowNetwork.pcapng` (123 packets, 156.5 s, captured 2014-03-26).

---

## Core Concepts

### The core skill: client vs network vs server
Every slowness complaint splits into three possible buckets, and packet timing tells you which one:

| Bucket | What you measure | In this capture |
|--------|-----------------|-----------------|
| **Network** | Round-trip time (RTT): SYN → SYN/ACK | **105.6 ms**: normal for a WAN |
| **Server / application** | Request arrival → first response data | **20,586 ms**: the culprit |
| **Client** | Time between receiving data and sending the next request | Milliseconds: not the problem |

### Identifying the client and server
- **Client** = whoever sends the first **SYN** (it initiates). Here: **172.16.0.13**.
- **Server** = the destination of that SYN, listening on a **well-known port**. Here: **10.0.0.100:80** (HTTP).
- The client's source port (**61330**) is an **ephemeral** port: a random high port chosen per connection ([[L04 - Ports, Protocols & Services]]).

### Packet-by-packet walk-through (verified with tshark)
| # | Time (s) | Δ prev (s) | Direction | What it is | What it tells you |
|---|---------|-----------|-----------|-----------|-------------------|
| 1 | 0.000 | — | C → S | **SYN** (MSS 1460, WS=256, SACK) | Client opens a connection to port 80 |
| 2 | 0.106 | **0.1056** | S → C | **SYN/ACK** | **Network RTT ≈ 105 ms** |
| 3 | 0.107 | 0.0011 | C → S | ACK | Handshake done. The 1 ms client reply means the capture was taken **next to the client** |
| 4 | 0.108 | 0.0011 | C → S | `GET /SlxClient/Opportunity.aspx` | Request sent immediately (no client delay) |
| 5 | 0.108 | 0.0005 | C → S | Continuation | GET too big for one packet (cookies, headers): spans 2 segments |
| 6 | 0.219 | 0.1103 | S → C | ACK (Ack=1522, no data) | Server **received** the full request one RTT later. The network delivered it promptly |
| 7 | **20.805** | **20.5860** | S → C | `HTTP/1.1 200 OK` | **20.6 s of server think time.** The application is slow |

> The instructor's phrasing: "**screen punching slow**." Once frame 6 shows the server got the request, every second until frame 7 belongs to the server.

### The rest of the capture (not covered in the video; found by analysis)
| Finding | Evidence | Lesson |
|---------|----------|--------|
| After the 20.6 s wait, **13 more GETs** follow (JS libraries, images), **one at a time on the same connection**, each ~110–130 ms apart (= 1 RTT each) | `http.request` frames 41–111, 21.3 s → 24.1 s | Serialized HTTP/1.1 requests each pay a full RTT; the page finishes at ≈24 s |
| **7 of those return `304 Not Modified`** | `http.response.code == 304` | The client already had the files cached, but revalidating each one still costs an RTT. Better `Cache-Control` headers would remove those round trips |
| **TCP Keep-Alives** from the client every ~45 s (frames 119, 121) | Expert info: *TCP keep-alive segment* | Idle connection kept open |
| Server sends **RST, ACK** at 156.5 s | Frame 123 | Server closed the idle connection (idle timeout) |
| Every frame shows **"Packet size limited during capture"** | `capinfos`: snaplen **96 bytes** | The capture was **sliced** to save space, so Wireshark sees headers only and can't reassemble HTTP bodies or compute `http.time`. Timing analysis still works, because timestamps are kept |

**Bottom line:** of ~24 s total page load, **~20.6 s is server processing**, ~3.5 s is RTT × serialized requests, and ~0 s is the client.

### Setting up the Time column
The instructor's setup, so your numbers match:
**View → Time Display Format → Seconds Since First Captured Packet** (the default; a stopwatch started at frame 1).

To find gaps fast, also use **Seconds Since Previous Displayed Packet**, or add a delta column (Preferences → Columns, type *Delta time displayed*). Sort by it and the 20.6 s gap jumps straight to the top.

---

## Key Terms

| Term | Definition |
|------|-----------|
| PCAP / pcapng | Packet capture file format (pcapng = next-gen, supports comments and multiple interfaces) |
| Client | Host that initiates the connection (sends the SYN) |
| Server | Host listening on a well-known port that receives the SYN |
| Ephemeral port | Temporary high source port chosen by the client per connection |
| Three-way handshake | SYN → SYN/ACK → ACK; the SYN → SYN/ACK gap ≈ network RTT (measured client-side) |
| Round-trip time (RTT) | Time for a packet to reach the peer and the reply to return |
| Server response time / think time | Time between the server ACKing a request and sending response data |
| Continuation | Wireshark label for a TCP segment carrying more of a message it can't parse on its own |
| HTTP 304 Not Modified | Server says the client's cached copy is still valid; costs a round trip, but no body |
| TCP keep-alive | Probe on an idle connection to check that the peer is still there |
| Snaplen / slicing | Capturing only the first N bytes of each packet ("Packet size limited during capture") |
| Time Display Format | How Wireshark shows the Time column: since first packet, since previous packet, absolute, etc. |

---

## Real-World Connections

**Network+ links:** This lab is [[L21 - Network Troubleshooting Methodology]] in practice: *identify the problem* (users say "slow"), *establish a theory* (client, network or server?), then *test it* with packet timing. It's the protocol analyzer from [[L25 - Troubleshooting Tools]]. It's also the point of [[L24 - Troubleshooting Network Performance Issues]]: **bandwidth upgrades don't fix latency or server delay**. The "100 gig everywhere" proposal would have spent money on the wrong layer.

**k3s lab:** Kubeshark shows the same split per request: the response-time column is server time, and comparing it with the TCP handshake time shows the network part. Reproduce this lab in-cluster:
```bash
# capture on a node, then measure handshake RTT vs. response time
sudo tcpdump -i any -w /tmp/es.pcap 'tcp port 9200' -c 500
tshark -r /tmp/es.pcap -Y 'tcp.flags.syn==1' -T fields -e frame.time_delta_displayed
tshark -r /tmp/es.pcap -Y 'http.response' -T fields -e http.time -e http.request.uri
```
A slow Kibana dashboard is usually **Elasticsearch query time** (server bucket), not the network.

**Cloud:** An AWS ALB access log or GCP load balancer log splits time into `request_processing_time` / `target_processing_time` / `response_processing_time`. That's the same client / server / network breakdown, done by the load balancer.

**Enterprise / APM:** This is the classic "mean time to innocence" for network teams: one packet capture proves the network isn't at fault and redirects the ticket to the application owners.

---

## Troubleshooting Scenario

**Scenario:** Users complain that the CRM "takes forever to open." Management proposes upgrading the branch WAN from 100 Mbps to 1 Gbps. You capture at a user's PC and see SYN → SYN/ACK = 105 ms, the GET is ACKed by the server 110 ms later, and the `200 OK` arrives 20.6 s after that.

**Analysis ([[L21 - Network Troubleshooting Methodology]]):**
1. **Identify:** slow initial page load; the capture is at the client.
2. **Theory:** client, network, or server?
3. **Test:** RTT is 105 ms and consistent (network fine). The client sends the GET within 1 ms of the handshake (client fine). The server ACKs the GET promptly but waits **20.6 s** before responding (server).
4. **Plan:** escalate to the application or database team with the PCAP and the exact frame numbers (6 → 7). Recommend checking backend query time and application logs for `/SlxClient/Opportunity.aspx`. **Don't** buy WAN bandwidth: it would cut maybe milliseconds off a 24 s load.
5. **Verify:** after the app fix, recapture. The frame 6 → 7 gap should be under 1 s.
6. **Preventive:** add synthetic monitoring for that URL's server response time.
7. **Document** in the ticket: "Network RTT 105 ms; server think time 20.6 s; see frames 6–7."

---

## Hands-On Ideas
1. Open the lab in Wireshark and set the **Seconds Since Previous Displayed Packet** format. Sort by Time descending and confirm the 20.586 s gap is at frame 7.
2. **Statistics → Conversations → TCP**: one conversation, 123 frames, 78 kB.
3. **Statistics → Flow Graph** (TCP flows): see the handshake, the GET, the long gap, then the burst of requests.
4. With tshark (add it to your PATH first: `export PATH="/Applications/Wireshark.app/Contents/MacOS:$PATH"`):
   ```bash
   cd raw/assets/wireshark-101
   capinfos -c -u -l Pre-Lab-SlowNetwork.pcapng                    # packets, duration, snaplen
   tshark -r Pre-Lab-SlowNetwork.pcapng -Y 'frame.time_delta > 1' \
     -T fields -e frame.number -e frame.time_delta -e _ws.col.Info  # every gap > 1 s
   tshark -r Pre-Lab-SlowNetwork.pcapng -Y http.request -T fields -e frame.time_relative -e http.request.uri
   ```
5. Import the instructor's profiles: **Edit → Configuration Profiles → Import from zip** → `Profiles/Chris-TCPPlain.zip` (and `Chris-Security.zip`). They contain his columns, coloring rules, and filter buttons.
6. Capture your own: load a website with DevTools open, then compare Wireshark's SYN → SYN/ACK RTT with the browser's "Waiting for server response" time.

---

## Quiz

1. In a capture, which host is the client?
   - A) The host with the higher IP address  B) The host that sends the first SYN  C) The host using port 80  D) The host sending the most bytes

2. Where can you measure network round-trip time in a client-side capture?
   - A) Between the GET and the 200 OK  B) Between the SYN and the SYN/ACK  C) Between keep-alives  D) Between the RST and the last ACK

3. The client sent its GET at 0.108 s, the server ACKed it at 0.219 s, and the `200 OK` arrived at 20.805 s. Where is the delay?
   - A) Client  B) Network  C) Server / application  D) DNS

4. Why would upgrading the WAN to 1 Gbps barely help this user?
   - A) WAN links can't exceed 100 Mbps  B) The delay is server processing time, not bandwidth  C) TCP window scaling is disabled  D) The capture was sliced

5. The ACK from the client (frame 3) arrives 1 ms after the SYN/ACK. What does that suggest about where the capture was taken?
   - A) Next to the server  B) Next to the client  C) In the middle of the WAN  D) It can't be determined

6. What does "Packet size limited during capture" mean?
   - A) The packets were fragmented  B) The capture used a snaplen and stored only the first N bytes of each packet  C) The MTU was too small  D) The packets were dropped

7. After the main page loads, the client requests 13 more files one after another, each about one RTT apart, and several return `304 Not Modified`. What's the takeaway?
   - A) The server is broken  B) Each serialized request costs a round trip; cache revalidations still cost RTTs  C) The 304s are errors  D) The network is congested

8. Which Wireshark Time Display Format makes large gaps easiest to spot?
   - A) UTC date and time  B) Seconds since previous displayed packet  C) Seconds since epoch  D) Absolute time of day

**Answers:** 1-B, 2-B, 3-C, 4-B, 5-B, 6-B, 7-B, 8-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[wireshark]] — Wireshark & packet analysis reference
- [[wireshark-101]] — Course progress page
- [[L04 - Ports, Protocols & Services]] — Well-known vs ephemeral ports, TCP handshake
- [[L14 - Network Monitoring]] — Packet capture, SPAN/TAP
- [[L21 - Network Troubleshooting Methodology]] — Identify → theory → test
- [[L24 - Troubleshooting Network Performance Issues]] — Latency vs bandwidth vs throughput
- [[L25 - Troubleshooting Tools]] — Protocol analyzers, tcpdump/tshark
