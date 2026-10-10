# Wireshark & Packet Analysis

**Definition:** Wireshark is an open-source **protocol analyzer**: it captures packets (or opens saved `.pcap`/`.pcapng` files) and decodes every protocol layer. **tshark** is its command-line version; **tcpdump** is the lightweight capture-only tool for servers.

**Role:** It's the ground truth for troubleshooting ("is it the client, the network, or the server?") and for security investigation (what did the attacker send, and where did data go?). Packet captures settle disputes that logs and dashboards can't.

## Core Workflow
1. **Capture** at the right place: client side for user experience, server side for server behavior, or both. Use a capture filter (BPF) only if volume forces it.
2. **Orient:** total packets/duration (status bar, `capinfos`), conversations (Statistics → Conversations), protocol mix (Statistics → Protocol Hierarchy).
3. **Time it:** handshake RTT, request → response gaps, retransmissions.
4. **Filter:** narrow down with display filters.
5. **Prove it:** cite frame numbers and deltas in the ticket.

## Time Display Formats (View → Time Display Format)
| Format | Use |
|--------|-----|
| Seconds since first captured packet | Default "stopwatch" view |
| Seconds since previous displayed packet | **Spot gaps.** Sort by it to find delays |
| Absolute time of day / UTC | Correlate with logs and SIEM |

## Reading Timing
| Measurement | Where | Means |
|-------------|-------|-------|
| Network RTT | SYN → SYN/ACK (client-side capture) | Path latency |
| Server response time | Request (or server ACK of it) → first response data; `http.time` | Application/back-end time |
| Client delay | Response received → next request | Client processing / user think time |

## Useful Display Filters (growing list)
| Filter | Shows |
|--------|-------|
| `tcp.flags.syn==1 && tcp.flags.ack==0` | Connection attempts (SYNs) |
| `frame.time_delta_displayed > 1` | Gaps over 1 s between displayed packets |
| `http.request` / `http.response.code == 304` | HTTP requests / Not Modified responses |
| `tcp.analysis.flags` | Anything TCP expert analysis flagged (retransmissions, dup ACKs, zero window…) |
| `tcp.flags.reset==1` | Resets |

## tshark / capinfos Cheatsheet
```bash
export PATH="/Applications/Wireshark.app/Contents/MacOS:$PATH"   # macOS
capinfos -c -u -l file.pcapng                         # packet count, duration, snaplen
tshark -r file.pcapng -q -z conv,tcp                  # TCP conversations
tshark -r file.pcapng -q -z io,phs                    # protocol hierarchy
tshark -r file.pcapng -Y 'frame.time_delta > 1' -T fields -e frame.number -e frame.time_delta -e _ws.col.Info
tshark -r file.pcapng -Y 'tcp.analysis.flags' -T fields -e frame.number -e _ws.expert.message
sudo tcpdump -i eth0 -s 0 -w out.pcap 'tcp port 443'   # capture full packets (-s 0)
```

## Capture Gotchas
- **Snaplen / slicing** ("Packet size limited during capture"): headers only. Timing analysis still works, but payloads, reassembly and `http.time` are lost.
- **Capture point matters:** client-side and server-side captures show different gaps.
- **TLS:** payload is encrypted unless you load keys (`SSLKEYLOGFILE`), see [[L25 - Troubleshooting Tools]].

## Cloud / Kubernetes Equivalents
| Need | Tool |
|------|------|
| Packet capture in Kubernetes | **Kubeshark** (cluster-wide, protocol-aware), `kubectl debug` + tcpdump, ksniff |
| AWS | VPC Traffic Mirroring (packets), VPC Flow Logs (metadata only) |
| GCP | Packet Mirroring (packets), VPC Flow Logs |

## Linked Lessons
- [[WS00 - Introduction]] — Why packet analysis, configuration profiles
- [[WS01 - Pre-Lesson - Your First Lab]] — Client vs network vs server timing
- [[L14 - Network Monitoring]] · [[L25 - Troubleshooting Tools]] · [[L24 - Troubleshooting Network Performance Issues]]
- Course page: [[wireshark-101]]
