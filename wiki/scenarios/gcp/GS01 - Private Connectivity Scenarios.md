---
id: GS01
aliases: [GS01]
title: Private Connectivity Scenarios — Explained Step by Step
course: Google Cloud Networking Specialization
cert_track: gcp-networking
covers: GCP07–GCP13 (Private Google Access, Private Service Connect, Private Services Access, Cloud NAT)
date: 2026-10-15
type: scenario-walkthrough
tags: [gcp, scenarios, private-google-access, private-service-connect, private-services-access, cloud-nat, troubleshooting, architecture]
---

# GS01 — Private Connectivity Scenarios, Explained

Each scenario follows the same pattern:
**Context → Architecture → How to think (workflow) → Steps (how) → Why it works → Verify → Takeaway.**
Diagrams are Mermaid (they render in Obsidian and GitHub).

**The one decision you keep making in this module:**
```mermaid
flowchart TD
    Q["Where must the traffic go?"] --> A{"Google API?<br/>storage, BigQuery..."}
    A -->|"from a VM, no external IP"| PGA["Private Google Access<br/>per subnet"]
    A -->|"from on-prem"| PGAOP["PGA for on-prem<br/>VPN + DNS + Cloud Router advert"]
    Q --> B{"Managed service<br/>Cloud SQL, Memorystore?"}
    B --> PSA["Private Services Access<br/>VPC peering + allocated range"]
    Q --> C{"Partner / own service<br/>maybe overlapping IPs?"}
    C --> PSC["Private Service Connect<br/>endpoint to service attachment"]
    Q --> D{"Public internet<br/>apt, GitHub, SaaS?"}
    D --> NAT["Cloud NAT<br/>outbound only"]
```

---

## Scenario 1 — Cymbal: sensitive VMs need BigQuery and Cloud Storage, no public IPs
*Source: [[GCP08 - Private Google Access]] (course use case)*

### Context
A recommendation engine processes **sensitive customer data** and needs **BigQuery** and **Cloud Storage**. Security wants **no external IPs** (smaller attack surface) and **no self-managed NAT boxes** (overhead, bottleneck).

### Architecture (target)
```mermaid
flowchart LR
    subgraph VPC["VPC cymbal-vpc"]
        subgraph SN["subnet reco-subnet 10.10.0.0/24  PGA = ON"]
            VM1["reco-vm-1<br/>internal IP only"]
            VM2["reco-vm-2<br/>internal IP only"]
        end
        R["default route 0.0.0.0/0<br/>next hop default-internet-gateway"]
    end
    VM1 --> R
    VM2 --> R
    R -->|"stays on Google network"| G["Google APIs<br/>BigQuery, Cloud Storage"]
    INET["Internet"] -.-x|"no external IP: unreachable"| VM1
```

### How to think
1. Destination = **Google APIs** → not NAT, not PSC (unless you need a custom internal IP).
2. Source = **VMs without external IPs** → **Private Google Access** on their subnet.
3. Check prerequisites: **default route present**, **egress firewall allows** it, the **APIs are enabled**.

### Steps (how)
```bash
# 1. Create the VMs without external IPs
gcloud compute instances create reco-vm-1 --zone=us-central1-a \
  --subnet=reco-subnet --no-address
# 2. Turn on PGA for the subnet (the ONLY switch: it is per subnet)
gcloud compute networks subnets update reco-subnet --region=us-central1 \
  --enable-private-ip-google-access
# 3. Confirm the default route still exists
gcloud compute routes list --filter="network:cymbal-vpc AND destRange=0.0.0.0/0"
# 4. Enable the APIs the workload uses
gcloud services enable bigquery.googleapis.com storage.googleapis.com
```

### Why it works
- Without PGA, a VM with only an internal IP can **only send traffic inside the VPC**.
- With PGA, traffic to Google API IPs uses the **default route → `default-internet-gateway`**, but Google keeps it **on its own network**: no public IP and no NAT involved.
- PGA **has no effect on VMs that already have external IPs**. They'd use their own IP, so removing external IPs is what makes the design secure.

### Verify
```bash
gcloud compute ssh reco-vm-1 --zone=us-central1-a --tunnel-through-iap
bq ls                                   # works
gcloud storage ls gs://<bucket>         # works
curl -m 5 https://example.com           # times out (no internet: by design)
```

### Takeaway
**Google APIs + no external IP = PGA, per subnet.** Nothing else to deploy.

---

## Scenario 2 — "Cloud Storage works, but `apt update` hangs"
*Source: [[GCP07 - Private Connection Options - Overview]]*

### Context
An internal-only VM can `curl https://storage.googleapis.com` but `apt update` times out.

### Architecture (what's actually happening)
```mermaid
flowchart LR
    VM["vm-internal<br/>no external IP"] -->|"PGA ON"| G["storage.googleapis.com ✔"]
    VM -->|"no path"| D["deb.debian.org ✘"]
    VM -.->|"needed"| NAT["Cloud NAT"]
    NAT -.-> D
```

### How to think
```mermaid
flowchart TD
    S["Some destinations work, others don't"] --> Q1{"Do the working ones<br/>belong to Google APIs?"}
    Q1 -->|yes| Q2{"Are the failing ones<br/>normal internet hosts?"}
    Q2 -->|yes| A["PGA is fine. Internet needs Cloud NAT"]
    Q1 -->|no| B["Check routes and firewall instead"]
```

### Steps
```bash
gcloud compute routers create nat-router --network=<vpc> --region=<region>
gcloud compute routers nats create nat-gw --router=nat-router --region=<region> \
  --auto-allocate-nat-external-ips --nat-all-subnet-ip-ranges
# wait 1-3 minutes, then on the VM:
sudo apt-get update
```
Alternative with **no internet at all**: point apt at an **Artifact Registry remote repository** for Debian, which is a Google API and so reachable through PGA.

### Why
**PGA only covers Google APIs and services.** Debian mirrors are ordinary internet hosts, so they need **outbound NAT**.

### Takeaway
**PGA ≠ internet.** Split dependencies into "Google APIs" (PGA) and "everything else" (NAT or a mirror).

---

## Scenario 3 — On-prem Tokyo → Cloud Storage over VPN: DNS resolves, traffic times out
*Source: [[GCP08 - Private Google Access]]*

### Context
On-prem servers must reach Cloud Storage **privately** over Cloud VPN. `nslookup storage.googleapis.com` returns `199.36.153.5` (restricted VIP), but `curl` times out. The tunnel is up.

### Architecture (correct design)
```mermaid
flowchart LR
    subgraph ONP["On-prem Tokyo"]
        H["server"]
        DNS["on-prem DNS<br/>*.googleapis.com CNAME<br/>restricted.googleapis.com<br/>= 199.36.153.4/30"]
        RTR["on-prem router<br/>BGP"]
    end
    subgraph GCP["VPC"]
        CR["Cloud Router<br/>custom advert 199.36.153.4/30"]
        VPN["HA VPN gateway"]
        RT["static route 199.36.153.4/30<br/>to default-internet-gateway"]
    end
    H --> DNS
    H --> RTR
    RTR <-->|"IPsec + BGP"| VPN
    CR -.->|"advertises VIP range"| RTR
    VPN --> RT --> API["Google APIs"]
```

### How to think: three things must line up
| Layer | Question | Status here |
|-------|----------|-------------|
| **DNS** | Does the name resolve to the VIP? | ✔ `199.36.153.5` |
| **On-prem route** | Does on-prem send `199.36.153.4/30` **into the tunnel**? | ✘ probably not |
| **VPC route** | Does the VPC route the VIP to `default-internet-gateway`? | check |

```mermaid
flowchart TD
    A["DNS OK, curl times out"] --> B{"On-prem route for<br/>199.36.153.4/30 via tunnel?"}
    B -->|no| F1["Add Cloud Router custom<br/>advertisement of the range"]
    B -->|yes| C{"VPC route for range<br/>to default-internet-gateway?"}
    C -->|no| F2["Create static route<br/>needed if default route deleted"]
    C -->|yes| D{"Egress firewall allows it?"}
    D -->|no| F3["Allow egress to 199.36.153.4/30 tcp:443"]
```

### Steps
```bash
# 1. Check what on-prem learned (on the on-prem router)
show ip route 199.36.153.4        # no BGP route? It follows the default route to the internet
# 2. Advertise the VIP range from Cloud Router (keep subnets too!)
gcloud compute routers update tokyo-router --region=asia-northeast1 \
  --advertisement-mode=CUSTOM --set-advertisement-groups=ALL_SUBNETS \
  --set-advertisement-ranges=199.36.153.4/30
# 3. Make sure the VPC can deliver it
gcloud compute routes create restricted-apis --network=<vpc> \
  --destination-range=199.36.153.4/30 --next-hop-gateway=default-internet-gateway
```

### Why
- The VIP range isn't a VPC subnet, so Cloud Router's **default advertisement mode doesn't announce it**. On-prem then sends it to its own internet path, where `199.36.153.4/30` isn't reachable.
- `ALL_SUBNETS` must stay in the custom list, or you'd **withdraw the subnet routes** and break everything else.
- `restricted` = **VPC Service Controls–supported APIs only**. If another API fails, switch to `private.googleapis.com` (`199.36.153.8/30`).

### Verify
On-prem: `show ip bgp 199.36.153.4/30` shows the route learned from Cloud Router. `curl -I https://storage.googleapis.com` returns HTTP 200/4xx (a response, not a timeout).

### Takeaway
**PGA for on-prem = DNS + advertise the range + VPC route.** Debug them in that order.

---

## Scenario 4 — A bank exposes a transaction API to partners (overlapping IPs)
*Source: [[GCP09 - Private Service Connect]] (course use case)*

### Context
A financial institution must offer a **sensitive API** to partners in other clouds and on-prem. It must not be exposed to the internet, IP allow-lists and VPNs don't scale, and **partner IP ranges may overlap** with the bank's.

### Architecture
```mermaid
flowchart LR
    subgraph P1["Partner A VPC 10.0.0.0/16"]
        A1["app"] --> EA["PSC endpoint<br/>10.0.5.10"]
    end
    subgraph P2["Partner B VPC 10.0.0.0/16 (overlaps!)"]
        B1["app"] --> EB["PSC endpoint<br/>10.0.9.20"]
    end
    subgraph BANK["Bank producer VPC"]
        SA["service attachment<br/>accept list: A, B"]
        NATS["PSC NAT subnet<br/>192.168.100.0/24"]
        ILB["internal LB"] --> API["transaction API VMs"]
        SA --> ILB
    end
    EA -->|"Google backbone"| SA
    EB -->|"Google backbone"| SA
```

### How to think
- *Overlapping IPs* rules out peering and PSA. *Many consumers* calls for a multi-tenant producer. *Consumers only call in* means one-way access. All three point to **PSC**.

### Steps
**Producer (bank):**
```bash
gcloud compute networks subnets create psc-nat --network=bank-vpc --region=us-central1 \
  --range=192.168.100.0/24 --purpose=PRIVATE_SERVICE_CONNECT
# internal passthrough/proxy LB in front of the API (forwarding rule: api-fr)
gcloud compute service-attachments create txn-api --region=us-central1 \
  --producer-forwarding-rule=api-fr --nat-subnets=psc-nat \
  --connection-preference=ACCEPT_MANUAL \
  --consumer-accept-list=partner-a-project=10,partner-b-project=10
# firewall: allow from the PSC NAT range ONLY
gcloud compute firewall-rules create allow-psc-nat --network=bank-vpc \
  --allow=tcp:443 --source-ranges=192.168.100.0/24
```
**Consumer (each partner):**
```bash
gcloud compute addresses create txn-ep-ip --region=us-central1 --subnet=app-subnet
gcloud compute forwarding-rules create txn-ep --region=us-central1 --network=partner-vpc \
  --address=txn-ep-ip \
  --target-service-attachment=projects/BANK/regions/us-central1/serviceAttachments/txn-api
```

### Why
- PSC exchanges **no routes**; the consumer only sees **one IP in its own subnet**, so overlap is irrelevant.
- The producer SNATs consumers into the **PSC NAT subnet**, so **one firewall rule** covers every partner.
- Connections are **consumer-initiated only**, so the bank can't reach into partner networks and vice versa.

### Verify
Producer: `gcloud compute service-attachments describe txn-api --region=us-central1` shows connections `ACCEPTED`. Consumer: `curl https://10.0.5.10/health`.

### Takeaway
**Publish a service, not a network.** PSC is the answer whenever you see "overlapping IPs" with "specific consumers."

---

## Scenario 5 — PSC endpoint unreachable from a peered VPC
*Source: [[GCP09 - Private Service Connect]]*

### Context
VPC-A has a PSC endpoint `10.10.5.50`. VPC-B is **peered** to VPC-A. VPC-A works; VPC-B times out.

### Architecture
```mermaid
flowchart LR
    subgraph A["VPC-A"]
        AW["workload ✔"] --> EP["PSC endpoint 10.10.5.50"]
    end
    subgraph B["VPC-B (peered to A)"]
        BW["workload ✘"]
        EP2["NEW PSC endpoint 10.20.5.50"]
    end
    BW -.-x|"not reachable over peering"| EP
    BW --> EP2
    EP --> SVC["producer service attachment"]
    EP2 --> SVC
```

### How to think
Firewall and routes are fine, so think "**what does PSC not support?**" → **endpoints aren't reachable from peered VPCs** (a documented caveat).

### Steps
```bash
gcloud compute addresses create b-ep-ip --region=<r> --subnet=<vpc-b-subnet>
gcloud compute forwarding-rules create b-ep --region=<r> --network=vpc-b \
  --address=b-ep-ip --target-service-attachment=<same attachment URI>
# Optional: Cloud DNS private zones so "partner-api.internal" resolves per VPC
```

### Why
The endpoint is a forwarding rule tied to its VPC; peering doesn't export it. Each consuming VPC needs **its own endpoint**. The producer may need to **accept** VPC-B's project.

### Takeaway
**One endpoint per consuming VPC.** Use DNS so apps don't care which IP they hit.

---

## Scenario 6 — On-prem can't reach Cloud SQL's private IP (PSA)
*Source: [[GCP10 - Private Services Access]]*

### Context
On-prem (HA VPN + Cloud Router) must query Cloud SQL at `10.240.0.2`. VMs in the VPC work; on-prem times out.

### Architecture
```mermaid
flowchart LR
    ONP["On-prem 172.16.0.0/16"] <-->|"HA VPN + BGP"| CR["Cloud Router"]
    CR --- VPC["Your VPC<br/>subnets 10.10.0.0/16<br/>allocated range 10.240.0.0/16"]
    VPC <-->|"VPC peering<br/>servicenetworking-googleapis-com"| PROD["Google producer VPC<br/>Cloud SQL 10.240.0.2"]
```

### How to think: two directions, two fixes
```mermaid
flowchart TD
    A["On-prem to 10.240.0.2 fails"] --> B{"Forward path:<br/>does on-prem know 10.240.0.0/16?"}
    B -->|no| F1["Cloud Router custom advertisement<br/>of the allocated range"]
    A --> C{"Return path:<br/>does producer VPC know 172.16.0.0/16?"}
    C -->|no| F2["Export custom routes<br/>on the PSA peering"]
```

### Steps
```bash
# Forward: tell on-prem about the PSA range
gcloud compute routers update onprem-router --region=<r> --advertisement-mode=CUSTOM \
  --set-advertisement-groups=ALL_SUBNETS --set-advertisement-ranges=10.240.0.0/16
# Return: let the producer VPC learn on-prem routes
gcloud compute networks peerings update servicenetworking-googleapis-com \
  --network=<vpc> --export-custom-routes
# Check what is exported
gcloud compute networks peerings list-routes servicenetworking-googleapis-com \
  --network=<vpc> --region=<r> --direction=OUTGOING
```

### Why
- The allocated range isn't a local subnet, so it's **not advertised by default**.
- Peering is **non-transitive**: the producer VPC only gets your **subnet** routes, not **dynamic** (on-prem) routes, unless you **export custom routes**.

### Verify
From on-prem: `psql -h 10.240.0.2 -U postgres`.

### Takeaway
**Hybrid + PSA = advertise the allocated range + export custom routes.** Always check both directions.

---

## Scenario 7 — CI builds fail intermittently behind Cloud NAT (port exhaustion)
*Source: [[GCP11 - Cloud NAT]]*

### Context
50 internal-only CI runners, Cloud NAT in auto mode with 1 NAT IP and the **default 64 ports/VM**. Peak builds fail with timeouts during `npm install` / `docker pull`; small builds pass.

### Architecture
```mermaid
flowchart LR
    subgraph RUN["50 CI runner VMs"]
        R1["runner: 64 ports"]
        R2["runner: 64 ports"]
        R3["... x50"]
    end
    RUN --> NAT["Cloud NAT<br/>1 NAT IP = ~64k ports"]
    NAT --> NPM["registry.npmjs.org"]
    NAT --> DH["Docker Hub"]
    RUN -->|"PGA, not NAT"| AR["Artifact Registry ✔"]
```

### How to think
*Intermittent* + *load-dependent* + *only internet destinations fail* points to a **finite shared resource**, here **NAT ports** (the cloud version of DHCP pool exhaustion).

```mermaid
flowchart TD
    A["Timeouts only at peak"] --> B["Check NAT logs / metrics"]
    B --> C{"OUT_OF_RESOURCES drops?"}
    C -->|yes| D["Raise ports per VM:<br/>dynamic port allocation"]
    D --> E{"Total ports enough?"}
    E -->|no| F["Add NAT IPs<br/>manual if partners allowlist"]
    C -->|no| G["Look elsewhere: DNS, registry rate limits"]
```

### Steps
```bash
# 1. Evidence
gcloud logging read 'resource.type="nat_gateway" AND jsonPayload.allocation_status="DROPPED"' --limit=10
# 2. Scale ports per VM with demand
gcloud compute routers nats update ci-nat --router=ci-router --region=<r> \
  --enable-dynamic-port-allocation --min-ports-per-vm=64 --max-ports-per-vm=4096
# 3. More capacity if needed (each IP adds ~64k ports)
gcloud compute routers nats update ci-nat --router=ci-router --region=<r> \
  --nat-external-ip-pool=nat-ip-1,nat-ip-2
```

### Why
Each VM gets a fixed **slice of source ports** on a NAT IP. A parallel `npm install` opens hundreds of connections, so 64 ports run out and new connections are dropped. Dynamic allocation grows the slice only when it's needed.

### Verify
Drop counter at 0 during a peak build: `nat/dropped_sent_packets_count` (reason OUT_OF_RESOURCES).

### Takeaway
**Intermittent egress failures under load → check NAT ports first.** Also route registries through PGA (Artifact Registry) to take load off NAT.

---

## Scenario 8 — Lab rebuilt in Terraform: NAT "Running" but no internet
*Source: [[GCP12 - Lab - Implement Private Google Access and Cloud NAT]]*

### Context
`gcloud storage cp` works (PGA OK) but `apt-get update` hangs 10 minutes after NAT shows Running.

### How to think
```mermaid
flowchart TD
    A["NAT Running, no internet"] --> B{"Same region as VM?"}
    B -->|no| F1["Recreate NAT in the VM's region<br/>Cloud NAT is regional"]
    B -->|yes| C{"Subnet mapped?<br/>sourceSubnetworkIpRangesToNat"}
    C -->|"LIST, subnet missing"| F2["Add subnet or use<br/>ALL_SUBNETWORKS_ALL_IP_RANGES"]
    C -->|yes| D{"Egress firewall deny?"}
    D -->|yes| F3["Allow egress 0.0.0.0/0 for needed ports"]
```

### Steps
```bash
gcloud compute routers nats describe nat-config --router=nat-router --region=us-west1 \
  --format="yaml(sourceSubnetworkIpRangesToNat,subnetworks)"
gcloud compute routers nats update nat-config --router=nat-router --region=us-west1 \
  --nat-all-subnet-ip-ranges
```
Terraform fix: `source_subnetwork_ip_ranges_to_nat = "ALL_SUBNETWORKS_ALL_IP_RANGES"` (or list the subnet in a `subnetwork {}` block).

### Why
A NAT gateway only translates for **the subnet ranges mapped to it**, and only **in its region**.

### Takeaway
**"Running" ≠ "serving my subnet."** Check region and mapping.

---

## Summary Table

| # | Symptom / need | Answer | Key command |
|---|----------------|--------|-------------|
| 1 | Google APIs, no external IP | PGA on subnet | `subnets update --enable-private-ip-google-access` |
| 2 | Google APIs OK, internet not | Cloud NAT (or AR mirror) | `routers nats create` |
| 3 | On-prem: DNS OK, traffic times out | Advertise VIP + VPC route | `routers update --set-advertisement-ranges` |
| 4 | Partners, overlapping IPs | PSC service attachment + endpoints | `service-attachments create` |
| 5 | PSC from a peered VPC fails | Endpoint per VPC | `forwarding-rules create --target-service-attachment` |
| 6 | On-prem → Cloud SQL private IP | Advertise range + export custom routes | `peerings update --export-custom-routes` |
| 7 | Intermittent egress at peak | NAT port allocation / more IPs | `nats update --enable-dynamic-port-allocation` |
| 8 | NAT Running, no internet | Region + subnet mapping | `nats describe` |

**Practice:** [[GPL01 - Private Connectivity Practice Labs]]

## Linked Pages
[[GCP07 - Private Connection Options - Overview]] · [[GCP08 - Private Google Access]] · [[GCP09 - Private Service Connect]] · [[GCP10 - Private Services Access]] · [[GCP11 - Cloud NAT]] · [[GCP12 - Lab - Implement Private Google Access and Cloud NAT]] · [[gcp-private-access]]
