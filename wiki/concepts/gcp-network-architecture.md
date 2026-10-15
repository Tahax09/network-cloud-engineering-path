# GCP Network Architecture & Topologies

**Definition:** The design of a Google Cloud virtual network (VPCs, subnets, routing, firewall policy, hybrid connectivity, load balancing) and the **topology** connecting multiple networks. Google runs the physical layer; you design and configure the virtual one.

## Design Considerations & Process
| Considerations | Process |
|----------------|---------|
| **Scalability · Security (+ compliance) · Performance · Cost efficiency** | Stakeholders → requirements (incl. OpEx) → **HLD** → **LLD** (IP plan, configs) → **BoM** & cost (CapEx/OpEx) |

Define **quantifiable metrics** per consideration before choosing services ([[GCP15 - Cloud Network Architecture Overview]], [[GCP16 - Getting Started with Network Architecture]]).

## Toolkit
VPC + regional subnets · firewall rules/policies (prefer service-account targets) · Cloud VPN / HA VPN · Cloud Interconnect (+ HA VPN over Interconnect) · Cloud Router (BGP for VPN/Interconnect/router appliances; NAT control plane) · load balancers · Cloud CDN · **Network Service Tiers** (Premium = Google backbone + global LB; Standard = internet, regional, cheaper).

## Topologies
| Topology | Shape | Pros | Cons | GCP implementation |
|----------|-------|------|------|--------------------|
| **Hub-and-spoke** | Central hub as transit for spokes | Central control, simple admin, add spokes easily, central security | Hub path/capacity; must design transitivity | **NCC** (VPC + hybrid spokes), HA VPN + BGP, or peering + NVA |
| **Mesh** (full / partial) | Full: every network ↔ every other · Partial: strategic links only (e.g., cross-region failover) | HA, resilience, load spreading, no central point | n(n−1)/2 links; quotas; no central inspection point | VPC peering mesh, or NCC **mesh preset**; partial mesh for failover pairs |
| **Mirrored** | Replica of network infra in another env/region | DR standby, isolated dev/test, nearby-region performance | Duplicate cost | Same layout per region/env; **unique IP ranges** if they will ever connect |
| **Gated** (egress / ingress / both) | Controlled exit, entry, or both between cloud and external (hybrid/multicloud) | Security, compliance, expose APIs not networks | Gateways add latency/management | Cloud NAT + egress policy/SWP (egress); LB/API gateway/Cloud Armor/PSC (ingress); NVAs |

## Hub-and-Spoke Implementation Comparison
| Method | Transitive spoke↔spoke? | Notes |
|--------|------------------------|-------|
| VPC Network Peering | **No** (needs NVA or direct peerings) | Low latency, free, quota-limited |
| Cloud VPN (HA VPN + Cloud Router) | Yes (BGP re-advertisement) | Encrypted, cross-region/on-prem; more latency and config |
| **Network Connectivity Center** | **Yes**: subnet routes exchanged among VPC spokes | Centralized and automated; extra cost; Mesh or Star presets |

## Network Connectivity Center Facts
- **VPC spokes** (same/other project or org) and **hybrid spokes** (HA VPN tunnels, Interconnect VLAN attachments, **router appliance** VMs with partner or custom images).
- No overlapping IP ranges · IPv6 unsupported on hybrid spokes · privately used public IPs supported.
- NCC also hosts **inter-VPC (private) NAT** ([[GCP11 - Cloud NAT]]).

## CLI
```bash
gcloud network-connectivity hubs create my-hub --preset-topology=mesh   # or star
gcloud network-connectivity spokes linked-vpc-network create spoke1 \
  --hub=my-hub --vpc-network=projects/PROJECT/global/networks/spoke1-vpc --global
gcloud network-connectivity hubs list-spokes my-hub
gcloud compute routes list --filter="network~spoke1-vpc"
```

## AWS / Azure Equivalents
| GCP | AWS | Azure |
|-----|-----|-------|
| NCC hub | Transit Gateway | Virtual WAN hub |
| VPC spoke | TGW VPC attachment | VNet connection |
| Hybrid spoke (VPN/Interconnect) | VPN / Direct Connect attachment | VPN / ExpressRoute gateway |
| Router appliance | TGW Connect (SD-WAN) | NVA in hub |
| Premium Tier | Global Accelerator | Microsoft global network routing preference |

## Linked Lessons
- [[GCP14 - Network Architecture - Course Introduction]] · [[GCP15 - Cloud Network Architecture Overview]] · [[GCP16 - Getting Started with Network Architecture]]
- [[GCP17 - Module Introduction - Network Topologies]] · [[GCP18 - Hub-and-Spoke Topology]] · [[GCP19 - Lab - Implement a Hub-and-Spoke Network Using Network Connectivity Center]] · [[GCP20 - Other Topologies - Mesh, Mirrored, Gated]]
- [[L06 - Network Topologies, Architectures & Traffic Flows]] · [[L09 - Routing Technologies]]
