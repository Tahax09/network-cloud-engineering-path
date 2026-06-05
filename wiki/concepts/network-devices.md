# Network Devices & Appliances

## Device Reference

### Router (L3)
- Forwards packets based on **destination IP address**
- Maintains a routing table (connected, learned, default)
- Default route: `0.0.0.0/0` — catch-all for internet traffic
- Each interface = a gateway for a different subnet
- **AWS:** Internet Gateway + Route Table

### Layer 2 Switch (L2)
- Forwards frames based on **destination MAC address**
- Builds CAM table (MAC → port) by learning source MACs
- Unknown destination → flood all ports except incoming
- **vs Hub:** Hub broadcasts everything; switch learns and targets

### Layer 3 Switch (L2 + L3)
- Combines switching and routing in one device
- SVIs (Switched Virtual Interfaces) = L3 interfaces per VLAN
- Routes between VLANs at wire speed without external router

### Firewall
| Generation | Layer | Behavior |
|-----------|-------|----------|
| Packet Filter | L3/L4 | Static IP+port rules; stateless |
| Stateful | L4 | Tracks TCP state; auto-allows return traffic |
| NGFW | L7 | DPI, application-aware, integrated IPS |

### IDS (Intrusion Detection System)
- Out-of-band: receives a copy of traffic (tap/SPAN port)
- Passive — detects and alerts, does NOT block
- No latency impact
- Analogy: Security camera

### IPS (Intrusion Prevention System)
- Inline: all traffic passes through it
- Active — detects AND blocks in real time
- Adds latency; false positives = blocked legitimate traffic
- Analogy: Security guard at the door

### Load Balancer
- Distributes traffic across backend servers
- Health checks — removes failed servers automatically
- SSL offloading — handles TLS so backends serve plaintext
- Sticky sessions — same client → same backend

**Algorithms:** Round Robin, Weighted, Least Connections, IP Hash

**AWS:** ALB (L7), NLB (L4), GLB (L3)

### Proxy Server
| | Forward Proxy | Reverse Proxy |
|-|--------------|---------------|
| Serves | Clients (outbound) | Servers (inbound) |
| Hides | Client IPs | Server IPs |
| Use | Content filtering, caching | Load balancing, SSL termination |
| k3s equivalent | — | NGINX Ingress Controller |

### CDN
- Geographically distributed edge caches
- Serves content from nearest node to user
- Reduces load on origin; provides redundancy
- **AWS:** CloudFront

### NAT (Network Address Translation)
- Rewrites private source IPs to a public IP for internet access
- Private ranges (RFC 1918): 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16
- **AWS NAT Gateway:** private subnets → internet (outbound only)

### NAS vs SAN
| | NAS | SAN |
|-|-----|-----|
| Access | File-level (NFS, SMB) | Block-level |
| Network | IP | Dedicated (Fiber Channel) |
| Complexity | Simple | Enterprise |

## QoS Key Concepts
- **Delay:** End-to-end transit time
- **Jitter:** Variation in arrival timing — kills VoIP/video
- **Loss:** Dropped packets — TCP retransmits; UDP is gone
- VoIP gets **Expedited Forwarding (EF)** — highest priority

## TTL (Time To Live)
- IP header field, decremented at every L3 hop
- Reaches 0 → packet dropped, ICMP Time Exceeded returned
- Defaults: Linux=64, Windows=128, Cisco=255
- `traceroute` exploits this to map hops

## Linked Lessons
- [[L02]] — full lesson on appliances
- [[L01]] — OSI layers these devices operate at
- [[L03]] — virtualized equivalents in cloud
