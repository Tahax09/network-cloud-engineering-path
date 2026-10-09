# Modern Networks — SDN, VXLAN, Zero Trust, SASE, IaC, IPv6

## SDN — Three Planes
| Plane | Role | Examples |
|-------|------|---------|
| Management | Configure devices | SSH, SNMP, REST API |
| Control | Forwarding decisions | OSPF, BGP, STP |
| Data (Forwarding) | End-user traffic | Packets source→dest |

**SDN centralizes the control plane.** Southbound API (OpenFlow) pushes rules to devices. Northbound API = applications → controller.

**SD-WAN** = SDN applied to WAN.

## VXLAN
L2 overlay over L3 IP. Encapsulates Ethernet frames in UDP.
- **VNI:** 24-bit → ~16 million segments (vs 4,094 for 802.1Q)
- **Underlay:** Physical IP network
- **Overlay:** Logical L2 network on top
- In k3s: Flannel uses VXLAN — pods communicate cross-node via VXLAN tunnels

## Zero Trust
**Premise:** Network is already breached. Verify every request.

| Principle | Implementation |
|-----------|---------------|
| Policy-based auth | User + device health + time + location + app |
| Least privilege | JIT/JEA; tokens expire |
| Micro-segmentation | Internal security zones; limits blast radius |

## SASE
Converges SD-WAN + cloud security at cloud PoPs (not HQ).

| Component | Role |
|-----------|------|
| SD-WAN | Site connectivity |
| SWG | Web filtering |
| CASB | Cloud app control |
| ZTNA | Replaces VPN |
| FWaaS | Cloud NGFW |

Problem solved: eliminates traffic hairpinning through HQ for cloud apps.

## IaC Tools

| Tool | Config File | Agent | Language |
|------|------------|-------|---------|
| **Ansible** | **Playbooks** | **Agentless** (SSH) | YAML |
| **Chef** | **Cookbooks** | **Agent-based** | Ruby |
| **Puppet** | **Manifests** | **Agent-based** | Puppet DSL |
| Salt | States | Both | Python |

## IPv6 Address Types
| Type | Prefix | IPv4 Equivalent |
|------|--------|----------------|
| Global Unicast | 2000::/3 | Public IP |
| Unique Local | FC00::/7 | RFC 1918 private |
| Link Local | FE80::/10 | APIPA |
| Multicast | FF00::/8 | Class D |
| Loopback | ::1 | 127.0.0.1 |

**Compression:** Drop leading zeros; replace consecutive zero groups with `::` (once per address).

## IPv4→IPv6 Transition
| Method | How |
|--------|-----|
| Dual Stack | Both protocols on same device (preferred) |
| Tunneling | Encapsulate one inside the other |
| NAT64 | Translate IPv6 client → IPv4 server |

## k3s Connections
- Flannel CNI = VXLAN overlay (`ip -d link show flannel.1`)
- kube-proxy + iptables = SDN data plane
- Ansible = standard tool for node provisioning

## Linked Lessons
- [[L08 - Modern Networks]] — full lesson
- [[L06 - Network Topologies, Architectures & Traffic Flows]] — topology and SDN context
- [[labs/k3s-lab]] — VXLAN, iptables as SDN
