# Ports, Protocols & Services

## Port Ranges
| Range | Numbers | Description |
|-------|---------|-------------|
| Well-known | 0–1,023 | Standard services |
| Registered | 1,024–49,151 | Vendor apps |
| Ephemeral | 49,152–65,535 | Client outbound (OS-assigned) |

## Critical Ports
| Protocol | Port | Transport |
|----------|------|-----------|
| FTP data | 20 | TCP |
| FTP control | 21 | TCP |
| SSH/SFTP | 22 | TCP |
| Telnet | 23 | TCP |
| SMTP | 25 | TCP |
| DNS | 53 | TCP+UDP |
| DHCP server | 67 | UDP |
| DHCP client | 68 | UDP |
| HTTP | 80 | TCP |
| NTP | 123 | UDP |
| IMAP | 143 | TCP |
| SNMP queries | 161 | UDP |
| SNMP traps | 162 | UDP |
| LDAP | 389 | TCP |
| HTTPS | 443 | TCP |
| SMB | 445 | TCP |
| Syslog | 514 | UDP |
| SMTP secure | 587 | TCP |
| LDAPS | 636 | TCP |
| TACACS+ | 49 | TCP |
| RADIUS auth | 1812 (legacy 1645) | UDP |
| RADIUS accounting | 1813 (legacy 1646) | UDP |
| POP3 | 110 | TCP |
| IMAPS | 993 | TCP |
| POP3S | 995 | TCP |
| RDP | 3389 | TCP |
| MySQL | 3306 | TCP |
| SIP | 5060/5061 | TCP+UDP |

## IP Protocol Numbers
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
| 0 | Echo Reply |
| 3 | Destination Unreachable |
| 8 | Echo Request |
| 11 | Time Exceeded (traceroute) |

## IPsec Modes
| Mode | Encrypts | Original IP | Use |
|------|---------|-------------|-----|
| Transport | Payload only | Visible | Host-to-host |
| Tunnel | Entire packet | Hidden (new outer headers) | Site-to-site VPN |

## Traffic Types
| Type | Scope | Example |
|------|-------|---------|
| Unicast | 1→1 | TCP/UDP |
| Broadcast | 1→all (local) | ARP, DHCP |
| Multicast | 1→group | OSPF 224.0.0.5 |
| Anycast | 1→nearest | Cloudflare 1.1.1.1 |

## Exam Traps
- SFTP = SSH subsystem on port 22. NOT FTP over SSL.
- DNS = both TCP (zone transfers) AND UDP (queries)
- Tunnel mode hides original IPs; transport mode does not

## Linked Lessons
- [[L04]] — full lesson
- [[L02]] — firewalls filter by port
