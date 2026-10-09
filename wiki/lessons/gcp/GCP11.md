---
id: GCP11
title: Cloud NAT
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Private Connection Options
date: 2026-10-09
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, cloud-nat, public-nat, private-nat, inter-vpc-nat, network-connectivity-center, cloud-router, nat-ip-allocation, manual-auto, port-allocation, outbound-nat, private-google-access, pat]
---

# GCP11 — Cloud NAT

## Summary
**Cloud NAT** gives VMs **without external IPs** **outbound** internet access (updates, dependencies, external APIs). It never provides **inbound** access. It's a **distributed, software-defined managed service**, not a proxy box: each VM is allocated a **NAT IP + a slice of source ports**, and translation happens on the host. You configure it **on a Cloud Router** (control plane only). NAT IPs come in **manual** (you pick them, so you can give partners a fixed allowlist) or **auto** (scales automatically) mode. Two types: **Public NAT** (internet) and **Private NAT** (between private networks, e.g., **inter-VPC NAT** via **Network Connectivity Center**). Configuring NAT on a subnet range **forces Private Google Access on** for that range.

---

## Core Concepts

### Scenario (Cymbal non-prod environments)
Dev/test/staging VMs need **occasional outbound** internet access (updates, dependencies, external test tools) **without using up scarce public IPs**. Cloud NAT translates private IPs to a shared pool of public IPs and hides the internal network.

### Architecture: no middle proxy
| Traditional NAT (appliance/proxy VM) | Cloud NAT |
|-------------------------------------|-----------|
| Traffic hairpins through a NAT box: a **chokepoint** and single point of failure | **No NAT proxy in the path**. The Andromeda virtual network stack on each host does the translation |
| Scale by adding/upgrading appliances | Add NAT IPs to the **same gateway**; scales with the network |
| You patch and maintain it | Fully managed |

**Per-VM allocation:** each VM gets a **NAT IP + a port range** (default minimum **64 ports per VM**; adjustable, or use **dynamic port allocation**). Total connections are capped by ports: one NAT IP has ~64k ports, shared across VMs.

**Control plane vs data plane:** **Cloud Router** holds the NAT configuration (control plane). Google runs the NAT processing on the physical hosts running your VMs (data plane). **Cloud Router isn't in the packet path**, and no BGP is required for NAT.

### NAT IP allocation modes
| Mode | Behavior | Use when |
|------|----------|----------|
| **Manual** | You specify reserved static external IPs | The **destination allowlists your source IPs** (partner APIs, SaaS firewalls). Predictable egress IPs |
| **Auto** | Google allocates and **scales IPs automatically** with VM count | You don't care which IPs are used |

### Outbound only
```
Private VM 10.0.1.5 ──▶ Cloud NAT (35.x.x.x:port) ──▶ Internet update server     ✔ outbound + replies
Internet host ──────────────────────────────────────▶ 10.0.1.5                     ✘ no inbound NAT
```
External hosts **can't initiate** connections to VMs behind Cloud NAT. For inbound access, use a **load balancer** (or an external IP / IAP for admin).

### Benefits recap
- **Fewer external IPs** needed, and VMs stay internal-only.
- **Auto-scaling** NAT IPs.
- Works with **managed instance groups**, including autoscaling.
- **No single physical gateway** to fail.

### Cloud NAT + Private Google Access
- Cloud NAT **never NATs traffic to Google APIs'** external IPs. Google routes that internally.
- When a NAT gateway applies to a subnet range, **PGA is automatically in effect for that range and can't be disabled manually.**
- So VMs behind NAT reach Google APIs via **PGA** and everything else via **NAT**. (That's the module's lab: PGA + Cloud NAT together.)

### Public NAT vs Private NAT
| Type | Translates to | Use case |
|------|--------------|----------|
| **Public NAT** | **Public IPs** from a pool | Internal-only resources → **internet** |
| **Private NAT** | Private IPs (from a NAT subnet) | Communication **between private networks**, especially with **overlapping** address space |
| ↳ **Inter-VPC NAT** | | VPCs attached as spokes to a **Network Connectivity Center (NCC)** hub talk to each other despite overlapping ranges |
| ↳ (Hybrid NAT) | | VPC → on-prem/other cloud over VPN/Interconnect |

> **Transcript cleanup:** "each instance is **a located** NAT IP" = **allocated**. The closing "Gemini prompt" segment is a product demo (asking Gemini Cloud Assist how to configure PSA). It's not exam content, but it's a useful workflow.

---

## Key Terms

| Term | Definition |
|------|-----------|
| Cloud NAT | Managed, distributed outbound NAT for internal-only resources |
| NAT gateway | Cloud NAT configuration attached to a Cloud Router in a region |
| Manual / auto NAT IP allocation | You choose the IPs / Google allocates and scales them |
| Port allocation | Source ports reserved per VM (min 64 default); limits concurrent connections |
| Dynamic port allocation | Ports per VM scale between a min and max with demand |
| Outbound NAT | Translating internal-initiated connections; Cloud NAT's only direction |
| Public NAT | Private → public IP translation for internet access |
| Private NAT | Private → private translation between networks |
| Inter-VPC NAT | Private NAT between NCC spoke VPCs with overlapping ranges |
| Network Connectivity Center | Hub-and-spoke connectivity service for VPCs/hybrid |
| Andromeda | Google's SDN virtual network stack, where NAT actually executes |

---

## Real-World Connections

**AWS comparison:**
| GCP Cloud NAT | AWS NAT Gateway |
|---------------|-----------------|
| Regional, configured on Cloud Router, **distributed** (no box in the path) | **Zonal** managed device in a **public subnet**; route private subnets to it; deploy one per AZ for HA |
| Manual/auto NAT IPs, scales by adding IPs | One Elastic IP per gateway (secondary IPs available), 55k connections per IP per destination |
| Private NAT (inter-VPC, hybrid) | NAT Gateway **private** connectivity type |
| PGA auto-on for NAT'd ranges | Gateway endpoint for S3 to avoid NAT data-processing charges |

**Network+ links:** Cloud NAT is **PAT / NAT overload** ([[L09]]): many internal IPs share a few public IPs, told apart by source port. **Port exhaustion** is the cloud version of DHCP pool exhaustion ([[L23]]): a finite resource runs out under load. Manual NAT IPs for partner allowlists is the **ACL/allowlist** pattern ([[L20]]).

**k3s:** If your cluster nodes were internal-only GCE VMs, Cloud NAT is what lets `k3s` pull images from Docker Hub/GHCR and lets Kubeshark/Elastic fetch updates. A big Elasticsearch reindex from an external source, or many pods pulling images at once, is the classic way to **exhaust NAT ports**.

---

## Troubleshooting Scenario

**Scenario:** A CI runner fleet (50 internal-only VMs behind Cloud NAT, auto mode, 1 NAT IP, default 64 ports/VM) intermittently fails `npm install` / `docker pull` with connection timeouts during peak builds. Small builds succeed.

**Analysis ([[L21]]):**
1. **Identify:** intermittent, load-dependent, only outbound internet. Google APIs (Artifact Registry) work fine because they go via PGA, not NAT.
2. **Theory:** **NAT port exhaustion.** Each VM has only 64 ports. A parallel `npm install` opens hundreds of connections.
3. **Test:** Cloud NAT logging / metrics show **`OUT_OF_RESOURCES`** drops (`nat/dropped_sent_packets_count` with reason OUT_OF_RESOURCES).
4. **Plan/implement:** enable **dynamic port allocation** (e.g., min 64, max 4096), raise min ports per VM, and/or add NAT IPs. If a partner allowlists you, switch to **manual** IPs first so the egress IPs stay known.
```
gcloud compute routers nats update ci-nat --router=ci-router --region=REGION \
  --enable-dynamic-port-allocation --min-ports-per-vm=64 --max-ports-per-vm=4096
```
5. **Verify:** drops go to 0 during a peak build. **Preventive:** alert on the NAT drop metric. **Document.**

---

## Hands-On Ideas
1. **Module lab replica:** a custom VPC + subnet with PGA, an internal-only VM. Show that `gcloud storage ls` works (PGA) and `curl example.com` fails. Then add Cloud Router + Cloud NAT:
   ```bash
   gcloud compute routers create nat-router --network=VPC --region=REGION
   gcloud compute routers nats create nat-gw --router=nat-router --region=REGION \
     --auto-allocate-nat-external-ips --nat-all-subnet-ip-ranges --enable-logging
   ```
   `curl example.com` now works. Confirm `curl ifconfig.me` shows the NAT IP.
2. Prove no inbound: from outside, try to reach the VM via the NAT IP. It fails.
3. Switch to **manual** allocation with a reserved static IP and confirm `ifconfig.me` shows that exact IP.
4. Look at NAT logs in Cloud Logging (`resource.type="nat_gateway"`).

---

## Quiz

1. Which direction of traffic does Cloud NAT support?
   - A) Inbound only  B) Outbound (and replies) only  C) Both  D) Neither; it's for DNS

2. Where is a Cloud NAT gateway configured?
   - A) On a VM acting as a proxy  B) On a Cloud Router  C) On an internal load balancer  D) On the default internet gateway

3. A partner requires a fixed list of source IPs to allowlist. Which NAT IP mode should you use?
   - A) Auto  B) Manual  C) Dynamic  D) Private NAT

4. Why is Cloud NAT not a chokepoint like a NAT appliance?
   - A) It's distributed and software-defined; no NAT proxy in the path  B) It uses a bigger VM  C) It uses BGP multipath  D) It caches content

5. A Cloud NAT gateway covers a subnet range. Can you disable Private Google Access for that range?
   - A) Yes, anytime  B) No; PGA is in effect for NAT'd ranges and can't be disabled manually  C) Only with manual NAT IPs  D) Only on legacy networks

6. Does Cloud NAT translate traffic to Google APIs' external IPs?
   - A) Yes, always  B) No; Google routes it internally (PGA)  C) Only for Cloud Storage  D) Only in auto mode

7. Two VPCs attached to a Network Connectivity Center hub have overlapping ranges and must communicate. Which Cloud NAT type?
   - A) Public NAT  B) Private NAT (inter-VPC NAT)  C) Manual NAT  D) Static NAT

8. VMs behind Cloud NAT see intermittent outbound timeouts under heavy parallel connections. What's the likely cause?
   - A) DNS failure  B) NAT port exhaustion (too few ports per VM)  C) PGA disabled  D) Firewall ingress rules

9. Which Network+ concept does Cloud NAT implement?
   - A) Static 1:1 NAT  B) PAT / NAT overload  C) NAT64  D) Proxy ARP

**Answers:** 1-B, 2-B, 3-B, 4-A, 5-B, 6-B, 7-B, 8-B, 9-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[GCP07]] — Overview · [[GCP08]] — PGA (works alongside NAT) · [[GCP09]] — PSC · [[GCP10]] — PSA
- [[GCP12]] — Module debrief
- [[L09]] — NAT / PAT
- [[concepts/gcp-private-access]]
- [[certs/gcp-networking]]
