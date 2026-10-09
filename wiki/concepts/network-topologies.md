# Network Topologies, Architectures & Traffic Flows

## Topology Types

| Topology | Description | Trade-off |
|----------|-------------|-----------|
| Point-to-point | Direct link, two endpoints | No shared BW; cost scales linearly |
| Hub-and-spoke | Spokes connect via hub; spoke-to-spoke = through hub | Centralized security; higher inter-spoke latency |
| Full mesh | Every node direct to every other; n(n-1)/2 links | Max redundancy; very expensive at scale |
| Partial mesh | Some direct links, some routed | Practical compromise |
| Wireless mesh | APs relay wirelessly; dynamic rerouting | No wired backhaul needed |

## Three-Tier Campus Architecture

| Tier | Also Called | Role | Rules |
|------|-------------|------|-------|
| Access | Tier 1 | Connects end devices; L2; PoE | Never skip for end devices |
| Distribution | Tier 2, Aggregation, Workgroup | Inter-VLAN routing, QoS, ACLs | Policy enforcement point |
| Core | Tier 3 | High-speed backbone; L3 | NO workstations; NO policy |

**Collapsed core (two-tier):** Distribution + Core merged. Common in mid-size networks.

## Spine-Leaf (Data Center)

**Rules:**
- Leaf → leaf: **prohibited**
- Spine → spine: **prohibited**
- Any two endpoints: exactly **2 hops** (leaf → spine → leaf)

Leaf = access layer (connects servers). Spine = backbone.
Fixed 2-hop path = predictable latency.

## Traffic Flows

| Flow | Direction | Example | Security point |
|------|-----------|---------|---------------|
| North-South | Crosses perimeter (in/out) | Internet → web server | Perimeter firewall, WAF, LB |
| East-West | Lateral within DC | Web → app → DB | Micro-segmentation, internal firewall |

**Most Kubernetes pod-to-pod traffic is east-west.**

## SDN API Directions (≠ traffic directions)
- **Southbound API:** Controller → device (OpenFlow)
- **Northbound API:** Application → controller

Do NOT confuse with north-south traffic direction.

## Linked Lessons
- [[L06 - Network Topologies, Architectures & Traffic Flows]] — full lesson
- [[L08 - Modern Networks]] — SDN and modern architectures
- [[labs/k3s-lab]] — east-west traffic in k3s
