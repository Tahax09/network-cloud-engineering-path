---
id: L04
aliases: [L04]
title: Ports, Protocols & Services
course: CompTIA Network+
cert_track: network-plus
objective: "1.4 — Ports, Protocols & Services"
date: 2025-05-28
status: Complete
quiz_attempted: No
weak_areas: []
tags: [ports, protocols, tcp, udp, ipsec, icmp, dns, dhcp, ftp, ssh, smtp, snmp, sip]
---

# L04 — Ports, Protocols & Services

## Summary
Port numbers, protocol numbers, and traffic types are heavily tested on Network+. Know the critical port table cold, understand the two IPsec modes, and never confuse SFTP with FTP-over-SSL.

## Port Ranges

| Range | Numbers | Description |
|-------|---------|-------------|
| Well-known | 0–1,023 | Standard services (historically root-bound) |
| Registered | 1,024–49,151 | Vendor-registered apps (MySQL 3306, RDP 3389) |
| Ephemeral | 49,152–65,535 | OS-assigned for client outbound connections |

Ports are 16-bit → max 65,535. Client-side source ports are always ephemeral.

## Critical Port Table

| Protocol | Port | Transport | Notes |
|----------|------|-----------|-------|
| FTP data | 20 | TCP | Active mode data channel |
| FTP control | 21 | TCP | Commands |
| SSH / SFTP | 22 | TCP | SFTP is SSH subsystem — NOT FTP over SSL |
| Telnet | 23 | TCP | Cleartext — avoid |
| SMTP | 25 | TCP | Server-to-server email |
| DNS | 53 | TCP + UDP | UDP for queries; TCP for zone transfers |
| DHCP server | 67 | UDP | Server listens |
| DHCP client | 68 | UDP | Client listens |
| HTTP | 80 | TCP | |
| NTP | 123 | UDP | |
| IMAP | 143 | TCP | |
| SNMP queries | 161 | UDP | |
| SNMP traps | 162 | UDP | |
| LDAP | 389 | TCP | |
| HTTPS | 443 | TCP | |
| SMB | 445 | TCP | Windows file sharing |
| Syslog | 514 | UDP | |
| SMTP secure | 587 | TCP | Client-to-server with auth |
| LDAPS | 636 | TCP | |
| POP3 | 110 | TCP | |
| IMAPS | 993 | TCP | |
| POP3S | 995 | TCP | |
| RDP | 3389 | TCP | |
| MySQL | 3306 | TCP | |
| SIP | 5060/5061 | TCP + UDP | VoIP signaling |

## IP Protocol Numbers (not ports — these go in the IP header)

| Protocol | Number |
|----------|--------|
| ICMP | 1 |
| TCP | 6 |
| UDP | 17 |
| GRE | 47 |
| ESP (IPsec) | 50 |
| AH (IPsec) | 51 |

## ICMP Types

| Type | Meaning |
|------|---------|
| 0 | Echo Reply (ping response) |
| 3 | Destination Unreachable |
| 8 | Echo Request (ping) |
| 11 | Time Exceeded (TTL=0 → traceroute) |

## IPsec

**AH (protocol 51):** Authentication + integrity. No encryption.
**ESP (protocol 50):** Authentication + integrity + encryption. Almost always used.

| Mode | What's Encrypted | Original IP Header | Use Case |
|------|-----------------|-------------------|----------|
| Transport | Payload only | Visible | Host-to-host |
| Tunnel | Entire original packet | Hidden (new outer headers added) | Site-to-site VPN |

**IKE Phase 1:** Bidirectional SA established.
**IKE Phase 2:** Unidirectional tunnels established.

## Traffic Types

| Type | Description | Example |
|------|-------------|---------|
| Unicast | 1 to 1 | Standard TCP/UDP |
| Broadcast | 1 to all (local segment) | ARP, DHCP Discovery |
| Multicast | 1 to group members | OSPF (224.0.0.5), video streaming |
| Anycast | 1 to nearest | DNS root servers, Cloudflare 1.1.1.1 |

## Exam Traps
- "Connectionless" = UDP (Layer 4). IP (Layer 3) is also connectionless but never framed that way on the exam.
- **SFTP ≠ FTP over SSL** — SFTP is a separate SSH subsystem on port 22.
- **Tunnel mode** hides original IP addresses. **Transport mode** = original IPs still visible.
- **DNS uses BOTH TCP and UDP** — UDP for queries, TCP for zone transfers and large responses.

## Real-World Connections
- Your k3s cluster: Kubeshark shows L4 ports for every flow — filter by port to identify service type
- DNS (port 53 UDP) is the first protocol in almost every connection — Kubeshark will show DNS queries before HTTP
- SSH (port 22) = how you manage every Linux server; also runs SFTP for file transfer
- SNMP (161/162) = how network devices are monitored in enterprise (your Elasticsearch stack uses HTTP on 9200, not SNMP, but concept is the same)

## Quiz Questions
1. What port does SFTP use? Why is "FTP over SSL" wrong?
2. ICMP Type 11 is seen in Wireshark. What caused it?
3. A firewall rule allows TCP 443. Does it need a separate rule for the return traffic if stateful?
4. You're setting up a site-to-site VPN. AH or ESP? Transport or tunnel mode?
5. A DNS zone transfer is failing through a firewall that only allows UDP 53. Fix?

## Related
- [[concepts/ports-and-protocols]] — reference table
- [[L02 - Networking Appliances & Applications]] — firewalls make decisions based on these port numbers
- [[L03 - Network Virtualization & Cloud Technologies]] — cloud security groups use the five-tuple including ports
