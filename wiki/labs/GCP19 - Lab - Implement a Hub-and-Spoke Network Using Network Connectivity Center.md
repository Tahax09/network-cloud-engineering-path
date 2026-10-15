---
id: GCP19
aliases: [GCP19]
title: "Lab: Implement a Hub-and-Spoke Network Using Network Connectivity Center"
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: "Course 3: Network Architecture — Module 2: Network Topologies"
date: 2026-10-15
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, lab, network-connectivity-center, ncc, hub-and-spoke, vpc-spoke, mesh-preset, network-topology, network-intelligence-center, ping, qwiklabs]
---

# GCP19 — Lab: Implement a Hub-and-Spoke Network Using NCC

## Lab Goal
Prove that **two separate VPCs can't talk** by default, then connect them as **VPC spokes on a Network Connectivity Center hub** and show they can. Finally, view the result in **Network Topology** (Network Intelligence Center). This is the hands-on lab for [[GCP18 - Hub-and-Spoke Topology]].

> Qwiklabs temporary credentials from the lab page are intentionally **not** stored here.

---

## Topology
```
BEFORE                                     AFTER
spoke1-vpc            spoke2-vpc           spoke1-vpc ──┐         ┌── spoke2-vpc
 └ spoke1-subnet       └ spoke2-subnet      spoke1-vm    │  NCC    │   spoke2-vm
    spoke1-vm  ✘ ping ✘  spoke2-vm                       └─ my-hub ┘
 (no route between VPCs)                     (mesh preset: subnet routes exchanged)  ✔ ping
```
| Resource | Notes |
|----------|-------|
| `spoke1-vpc` / `spoke1-subnet`, `spoke2-vpc` / `spoke2-subnet` | **Pre-created**, non-overlapping ranges |
| Firewall rules `app-allow-icmp`, `app-allow-ssh-rdp` | **Pre-created**, so ICMP is allowed and any ping failure is a **routing** problem, not a firewall one |
| `spoke1-vm`, `spoke2-vm` | Created by you, one per VPC (default machine settings) |
| `my-hub` | NCC hub, **Mesh topology** preset |
| `spoke1`, `spoke2` | NCC **VPC spokes** for each VPC |

> **Lab-intro discrepancy:** the intro video mentions "**three** VPC networks, a central hub and two spokes." The actual lab has **two VPCs**. The **hub is an NCC resource**, not a third VPC. That's one of NCC's advantages over a peering-based hub: no hub VPC needed.

---

## Steps & Commands (console → gcloud equivalents)

### Tasks 1–2: explore, then create VMs
```bash
gcloud compute networks list                   # default, spoke1-vpc, spoke2-vpc
gcloud compute firewall-rules list --format="table(name,network,sourceRanges.list(),allowed[].map().firewall_rule().list())"
gcloud compute instances create spoke1-vm --zone=<zone> --subnet=spoke1-subnet
gcloud compute instances create spoke2-vm --zone=<zone> --subnet=spoke2-subnet
```

### Task 3: test (expect failure)
```bash
# from spoke2-vm (SSH):
ping <spoke1-vm internal IP>      # 100% packet loss: no route between separate VPCs
# from spoke1-vm:
ping <spoke2-vm internal IP>      # 100% packet loss
```

### Task 4: NCC hub and VPC spokes
Console: **Network Connectivity → Create hub → Mesh topology → name `my-hub` → Add a spoke (VPC network) `spoke1` → spoke1-vpc → Add `spoke2` → spoke2-vpc → Create.**
```bash
gcloud network-connectivity hubs create my-hub --preset-topology=mesh
gcloud network-connectivity spokes linked-vpc-network create spoke1 \
  --hub=my-hub --vpc-network=projects/<PROJECT>/global/networks/spoke1-vpc --global
gcloud network-connectivity spokes linked-vpc-network create spoke2 \
  --hub=my-hub --vpc-network=projects/<PROJECT>/global/networks/spoke2-vpc --global
gcloud network-connectivity hubs list-spokes my-hub
gcloud compute routes list --filter="network~spoke1-vpc"   # spoke2-subnet route now present
```

### Task 5: retest (expect success)
```bash
# from spoke1-vm:
ping -c 3 <spoke2-vm internal IP>   # replies
# from spoke2-vm:
ping -c 3 <spoke1-vm internal IP>   # replies
```

### Task 6: Network Topology
**Network Intelligence → Network Topology**: close View options, slide the **timeline** to the current time, **Zoom out**, and **Expand** the region. The VMs and the traffic between them appear, with **one metric per entity/connection** (e.g., throughput) for VMs, regions, and connections, including inter-region and Shared VPC traffic.

---

## Observations
| Test | Before NCC | After NCC |
|------|:----------:|:---------:|
| spoke1-vm → spoke2-vm (ICMP) | ✘ 100% loss | ✔ |
| spoke2-vm → spoke1-vm (ICMP) | ✘ 100% loss | ✔ |
| Firewall changed? | — | **No.** The fix was purely **routing** (subnet routes exchanged via the hub) |

**Why it failed before:** each VPC is an isolated routing domain. `spoke1-vpc` had no route to `spoke2-subnet`, and the default route (`0.0.0.0/0 → internet gateway`) can't carry traffic to another VPC's **private** IP.

**Why it works after:** the hub exchanges **subnet routes** between VPC spokes. Each VPC gets a route for the other's subnet with the NCC hub as next hop, so the network becomes a managed **full mesh between spokes**.

---

## Concepts Demonstrated
| Concept | Shown by | Lesson |
|---------|---------|--------|
| VPC isolation by default | 100% loss before NCC | [[GCP01 - Routes and Route Preferences]] |
| Firewall vs routing | ICMP allowed, but no route meant no connectivity; **both** are required | [[GCP01 - Routes and Route Preferences]] |
| NCC VPC spokes exchange subnet routes | Ping works after spokes join | [[GCP18 - Hub-and-Spoke Topology]] |
| No pairwise peering management | Two spokes, one hub; adding a third VPC = one more spoke, not two more peerings | [[GCP18 - Hub-and-Spoke Topology]] |
| Non-overlapping ranges required | Pre-built subnets use distinct CIDRs | [[GCP18 - Hub-and-Spoke Topology]] |
| Observability | Network Topology metrics per VM/region/connection | Network Intelligence Center |

---

## Real-World Connections
- **Network+:** the before/after is OSI troubleshooting in practice ([[L21 - Network Troubleshooting Methodology]]): Layer 3 reachability (route) separate from policy (firewall). Network Topology plays the same role as NetFlow/topology maps ([[L14 - Network Monitoring]]).
- **AWS:** same lab with **Transit Gateway**: two VPC attachments plus TGW route propagation, then ping.
- **k3s:** if you ran two clusters in two VPCs (e.g., `prod` and `observability`), NCC is how the app cluster ships logs to the observability cluster's Elasticsearch by private IP, provided both VPCs' node, pod and service ranges are unique.

---

## Troubleshooting Scenario
**Scenario:** After adding a third VPC, `spoke3-vpc`, as a spoke, VMs in spoke3 still can't ping spoke1, though spoke1 ↔ spoke2 works.
**Check, in order:**
1. **Spoke state:** `gcloud network-connectivity hubs list-spokes my-hub`. Is spoke3 `ACTIVE`, or still pending acceptance (cross-project spokes may need acceptance)?
2. **IP overlap:** does `spoke3-subnet` overlap spoke1's range? Overlapping subnet routes aren't exchanged.
3. **Firewall:** does spoke3-vpc have an ICMP allow rule like the pre-built `app-allow-icmp`? (Firewalls are per VPC; nothing is inherited from the hub.)
4. **Routes:** `gcloud compute routes list --filter="network~spoke3-vpc"` should show routes to the other spokes' subnets.
5. **Connectivity Tests** for spoke3-vm → spoke1-vm, to name the blocking hop.

---

## Hands-On Ideas
1. Repeat the lab with gcloud only (commands above), then add a third VPC and a spoke.
2. Switch the hub preset to **Star** (center = shared services, edge = app VPCs) in a new hub and confirm edge spokes can't reach each other.
3. Compare with peering: delete the hub, peer spoke1↔spoke2 directly, ping, then add spoke3 peered only to spoke2 and show spoke1 ↛ spoke3 (non-transitive).
4. Capture on spoke2-vm during the ping (`sudo tcpdump -i ens4 icmp -w ncc.pcap`) and open it in Wireshark. Source IPs are the original private IPs (no NAT). Add a TTL column and note whether the value arrives as the sender's default (64 on Debian) or decremented, which shows whether the VPC fabric acts as a visible router hop ([[WS06 - Configuring Columns]]).

---

## Linked Pages
- [[GCP18 - Hub-and-Spoke Topology]] · [[GCP17 - Module Introduction - Network Topologies]]
- [[GCP12 - Lab - Implement Private Google Access and Cloud NAT]] — Previous lab
- [[certs/gcp-networking]]
