---
id: GCP20
aliases: [GCP20]
title: Other Topologies - Mesh, Mirrored, Gated
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: "Course 3: Network Architecture — Module 2: Network Topologies"
date: 2026-10-15
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, mesh, full-mesh, partial-mesh, mirrored, gated-egress, gated-ingress, disaster-recovery, failover, hybrid, multicloud, gke-enterprise]
---

# GCP20 — Other Topologies: Mesh, Mirrored, Gated

## Summary
Beyond hub-and-spoke ([[GCP18 - Hub-and-Spoke Topology]]) there are three more patterns:
- **Mesh:** nodes have multiple interconnected links. **Full mesh** = everyone connects to everyone (e.g., microservices in **GKE Enterprise**). **Partial mesh** = only strategic links, e.g. **cross-region failover**. Benefits: **high availability, scalability, performance (load spreading), no single central point**.
- **Mirrored:** a **replica** of your network in another environment or region, for **DR**, **dev/test isolation**, and **nearby-region performance**.
- **Gated:** traffic forced through **controlled entry/exit points**: **gated egress** (outbound from cloud), **gated ingress** (inbound to cloud), **gated ingress and egress** (both), typically for **hybrid and multicloud** security and compliance.

---

## Core Concepts

### Mesh topology
| Type | Definition | Example use |
|------|-----------|-------------|
| **Full mesh** | **Every node ↔ every other node** | Tightly interconnected microservices (GKE Enterprise / service mesh across clusters) |
| **Partial mesh** | **Selected, strategic** links only | **Failover** between VPCs in different regions/zones: route to the active region normally, **fail over automatically** to a nearby one |

```
FULL MESH (4 VPCs = 6 links)        PARTIAL MESH (failover pair + shared)
  A ──── B                            us-east ═══ us-central   (active/standby link)
  │ ╲  ╱ │                               │
  │  ╳   │                            shared-svc
  │ ╱  ╲ │
  C ──── D
```
**Benefits (per the lesson):**
| Benefit | Why |
|---------|-----|
| **High availability / resilience** | Multiple paths: if one link fails, traffic reroutes. Suits mission-critical, high-uptime apps |
| **Scalability** | Grows as you add nodes or regions |
| **Performance** | Multiple paths allow load balancing and less congestion |
| **Security** | No single central point to compromise |

**Trade-offs (beyond the lecture):**
- Link count grows as **n(n−1)/2**: 10 VPCs = 45 peerings, which hits **peering quotas** and creates operational overhead. That's why an **NCC mesh preset** (one hub, n spokes, managed mesh routing) usually beats DIY peering ([[GCP19 - Lab - Implement a Hub-and-Spoke Network Using Network Connectivity Center]]).
- The security claim cuts both ways. With no single chokepoint, there's also **no single inspection point**: lateral movement paths multiply, so mesh needs **per-node policy** (firewall policies, service-account rules, mTLS in a service mesh). That's the zero-trust model ([[L08 - Modern Networks]]).

### Mirrored topology
A **replica of the network infrastructure** in another environment or region.
| Use case | How it helps |
|----------|-------------|
| **Disaster recovery** | Identical **standby** environment ready to take over ([[L15 - Disaster Recovery]]: warm/hot site) |
| **Testing & development** | Isolated but identical copy of prod, so experiments **don't affect production** |
| **Performance** | Identical environments in multiple regions, so workloads use the **nearby** one |

**Design note (beyond the lecture):** if mirrored environments **will ever be connected** (DR replication, failover routing, shared CI/CD), give them **unique IP ranges** with the same *layout* (the Cymbal Bank pattern in [[GCP15 - Cloud Network Architecture Overview]]). Fully isolated test copies *can* reuse ranges, but that blocks future interconnection. In Google's hybrid-patterns guidance, a mirrored dev/test environment typically talks to prod only through **CI/CD and management** paths.

### Gated topologies
Fine-grained control of data movement between your cloud and **external resources** (on-prem, other clouds), for **security and compliance**.
| Pattern | Controls | Typical implementation |
|---------|----------|----------------------|
| **Gated egress** | **Outbound** from cloud: cloud workloads may reach only **specific** external services/APIs | Cloud NAT + egress firewall policies, secure web proxy, NVAs, Private Service Connect to approved endpoints |
| **Gated ingress** | **Inbound** to cloud: external clients reach only **selected** cloud services *(the pattern shown in the lecture)* | Load balancers / API gateway (Apigee, API Gateway), Cloud Armor, PSC service attachments, NVAs at the edge |
| **Gated ingress and egress** | **Both directions** between hybrid/multicloud environments | Combination: exposed APIs each way via gateways, strict firewall policy in between |

The point of gating: **expose APIs, not networks**. Even with VPN/Interconnect in place, only defined services cross the boundary. This is the cloud version of the **screened subnet** ([[L20 - Network Defense Techniques]]). Google's docs ("Gated patterns" in the hybrid and multicloud architecture guide) also describe related patterns such as **meshed** and **handover**.

---

## Topology Comparison (all of Module 2)
| Topology | Shape | Strength | Weakness | Pick when |
|----------|-------|----------|----------|-----------|
| **Hub-and-spoke** | Central transit hub | Central control, simple admin, central security | Hub capacity/path; transitivity must be designed | Many VPCs/sites, shared services, central policy |
| **Full mesh** | All ↔ all | Direct paths, resilience | n(n−1)/2 links, quotas | Small, stable sets of tightly coupled networks |
| **Partial mesh** | Strategic links | Resilience where needed, fewer links | More complex routing/failover logic | Cross-region failover pairs |
| **Mirrored** | Replica env/region | DR, safe testing, regional performance | Duplicate cost | DR, dev/test parity, multi-region apps |
| **Gated** | Controlled entry/exit | Security, compliance, API-level exposure | Gateways add latency and management | Hybrid/multicloud with strict data-flow rules |

---

## Key Terms
| Term | Definition |
|------|-----------|
| Full mesh | Every node directly connected to every other |
| Partial mesh | Only selected nodes interconnected |
| Automatic failover | Traffic shifts to a standby region/zone when the primary fails |
| Mirrored topology | Replica of network infrastructure in another environment or region |
| Gated egress | Restricting outbound traffic from cloud to approved external destinations |
| Gated ingress | Restricting inbound traffic into cloud to selected services |
| Gated ingress and egress | Both directions controlled between environments |
| GKE Enterprise | Google's multi-cluster Kubernetes platform (formerly Anthos) |
| Lateral movement | Attacker moving from one compromised node to others |

---

## Real-World Connections
- **Network+:** full/partial mesh WAN and the n(n−1)/2 formula ([[L06 - Network Topologies, Architectures & Traffic Flows]]); hot/warm DR sites = mirrored ([[L15 - Disaster Recovery]]); screened subnet and proxies = gated ([[L20 - Network Defense Techniques]]).
- **AWS:** full mesh = VPC peering mesh; partial-mesh failover = Route 53 failover + inter-region TGW peering; mirrored = multi-region / pilot-light DR; gated ingress = ALB/API Gateway + WAF; gated egress = NAT GW + Network Firewall domain allow-lists.
- **k3s / Kubernetes:** a **service mesh** (Istio/Linkerd) is a full mesh of mTLS connections between pods, which is why GKE Enterprise uses mesh. Your **Ingress controller is gated ingress**. A **default-deny egress NetworkPolicy** with allowed destinations is **gated egress**. A k3s staging cluster built from the same manifests as prod is a mirrored environment.
- **SOC / AZ-500 path:** gated egress is a top **data-exfiltration** control. Azure's equivalent is Azure Firewall + forced tunneling through a hub VNet.

---

## Troubleshooting Scenario
**Scenario:** A bank mirrored its prod VPC (`10.10.0.0/16`) into a DR region with the **same ranges** "for simplicity." When they try to set up a partial-mesh link for database replication and failover, the connection can't be established, and failover routing is impossible.
**Analysis:** identical ranges in two networks that must be connected make the routes conflict (NCC and peering both reject overlapping subnet routes, [[GCP18 - Hub-and-Spoke Topology]]).
**Fix:** re-IP the DR mirror to `10.20.0.0/16` with the **same layout**, connect it via an NCC spoke or a peering link, and configure health-checked failover (global LB or DNS failover policy, [[GCP04 - Cloud DNS]]). Rule: **mirror the layout, not the addresses**, whenever environments must ever talk.

---

## Hands-On Ideas
1. Extend the GCP19 lab: add `spoke3-vpc` and count how many peerings a full mesh would need vs one more NCC spoke.
2. Build a **partial mesh failover**: two regional backends behind a global external Application LB; stop one region's VMs and watch traffic shift (health checks).
3. **Gated egress** in a test VPC: Cloud NAT + egress firewall rules that deny `0.0.0.0/0` except approved FQDN/IP ranges (or Secure Web Proxy), then test from a VM.
4. **k3s:** write a default-deny egress NetworkPolicy for a namespace and allow only `kube-dns` plus Elasticsearch. That's gated egress at pod level.

---

## Quiz
1. In a full mesh, how are nodes connected?
   - A) Through one central hub  B) In a ring  C) Every node directly to every other node  D) Only to the internet
2. Which topology suits failover between VPCs in different regions, using only strategic links?
   - A) Full mesh  B) Partial mesh  C) Gated egress  D) Star
3. Which is NOT a listed benefit of mesh topology?
   - A) High availability  B) Scalability  C) A single central point of control  D) Improved performance
4. What is a mirrored topology in Google Cloud?
   - A) A replica of your network infrastructure in another environment or region  B) A load balancer  C) Port mirroring on a switch  D) A CDN cache
5. Which is a use case for a mirrored topology?
   - A) Disaster recovery standby  B) DNS resolution  C) Cloud NAT  D) Packet capture
6. Which gated pattern controls traffic coming into the cloud?
   - A) Gated egress  B) Gated ingress  C) Partial mesh  D) Mirrored
7. Gating topologies are especially important in which scenarios?
   - A) Single-VM projects  B) Offline labs  C) DNS-only setups  D) Hybrid and multicloud environments
8. Mirrored environments must later be connected for replication. What's the key IP rule?
   - A) Reuse identical ranges  B) Mirror the layout but use unique IP ranges  C) Use only IPv6  D) Use public IPs

**Answers:** 1-C, 2-B, 3-C, 4-A, 5-A, 6-B, 7-D, 8-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[GCP18 - Hub-and-Spoke Topology]] · [[GCP19 - Lab - Implement a Hub-and-Spoke Network Using Network Connectivity Center]] · [[GCP17 - Module Introduction - Network Topologies]]
- [[gcp-network-architecture]] — Topology comparison reference
- [[L06 - Network Topologies, Architectures & Traffic Flows]] · [[L15 - Disaster Recovery]] · [[L20 - Network Defense Techniques]]
- [[certs/gcp-networking]]
