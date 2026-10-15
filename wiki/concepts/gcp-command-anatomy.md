---
title: GCP Command Anatomy
type: concept
tags: [gcloud, cli, reference, command-anatomy, vpc, cloud-nat, cloud-router, firewall, dns, ncc, psc, cloud-armor, load-balancing, troubleshooting]
updated: 2026-10-15
---

# GCP Command Anatomy

Every command used in the GCP lessons and labs, broken into parts: **what each piece means, what the command does, and when you'd run it**. Grouped by task. Linked lessons show where each command appears in the course.

> Commands use placeholders in `UPPER_CASE` or `<angle brackets>`. New commands from future lessons get added here.

---

## 0. How every gcloud command is built

```
gcloud  compute  networks subnets  create  privatenet-us  --network=privatenet --region=us-west1 --range=10.130.0.0/20
└─┬──┘  └──┬──┘  └──────┬───────┘  └─┬──┘  └─────┬─────┘  └──────────────────────────┬──────────────────────────────┘
 CLI    service    resource group   verb     resource name                           flags
        (API)      (what kind)      (action) (what you call it)                      (how / where)
```

| Part | Examples | Meaning |
|------|----------|---------|
| **Service** | `compute`, `dns`, `network-connectivity`, `network-management`, `services`, `logging`, `storage` | Which Google API you're calling |
| **Resource group** | `networks`, `networks subnets`, `routers nats`, `firewall-rules`, `security-policies rules` | What kind of thing. Nested groups read like a path: a NAT config lives *inside* a router |
| **Verb** | `create`, `describe`, `list`, `update`, `delete`, `get-health`, `add-backend` | The action |
| **Name** | `nat-router`, `web-backend` | Your chosen ID (lowercase, digits, hyphens) |
| **Flags** | `--region=…`, `--network=…` | Settings and the resource's location |

### Location flags: the scope of the resource
| Flag | Use for | Examples |
|------|---------|----------|
| `--global` | Global resources | VPC networks, routes, firewall rules, global backend services, global forwarding rules, URL maps, Cloud Armor policies |
| `--region=R` | Regional resources | Subnets, Cloud Routers, Cloud NAT, regional addresses, service attachments, VPN gateways |
| `--zone=Z` | Zonal resources | VMs, zonal instance groups, disks |

Wrong scope is the #1 cause of `ERROR: (gcloud...) Could not fetch resource ... was not found`. For example, describing a global backend service without `--global`.

### Global flags (work on any command)
| Flag | What it does | When |
|------|-------------|------|
| `--project=ID` | Run against another project | Multi-project work, Shared VPC host vs service project |
| `--format="value(field)"` | Print just one field | Capture into a shell variable: `IP=$(gcloud ... --format="value(address)")` |
| `--format="table(a,b)"` / `json` / `yaml` | Choose the output shape | Readable tables, or JSON for `jq` |
| `--filter="network:my-vpc"` | Filter on the server side | Narrow `list` output |
| `--quiet` / `-q` | Skip confirmation prompts | Scripts, deletes |
| `--verbosity=debug` / `--log-http` | Show API calls | Debugging the CLI itself |

```bash
gcloud config set project PROJECT_ID          # default project for later commands
gcloud config set compute/region us-central1  # default region, so you can drop --region
gcloud config list                            # what's currently set
gcloud services enable compute.googleapis.com dns.googleapis.com   # turn on APIs (first step in any fresh project)
```

---

## 1. VPC networks and subnets
→ [[GCP15 - Cloud Network Architecture Overview]], [[GCP08 - Private Google Access]], [[GCP02 - IPv6 in Google Cloud]]

```bash
gcloud compute networks create privatenet --subnet-mode=custom
```
| Piece | Meaning |
|-------|---------|
| `networks create privatenet` | New **global** VPC called `privatenet` |
| `--subnet-mode=custom` | You define subnets yourself. `auto` creates one /20 per region from 10.128.0.0/9, which can overlap with on-prem. Production uses **custom** |

**When:** first step of any lab or design. Custom mode is also required for IPv6 subnets.

```bash
gcloud compute networks subnets create privatenet-us \
    --network=privatenet --region=us-west1 --range=10.130.0.0/20 \
    [--enable-private-ip-google-access] \
    [--stack-type=IPV4_IPV6 --ipv6-access-type=EXTERNAL]
```
| Piece | Meaning |
|-------|---------|
| `--network` | Parent VPC |
| `--region` | Subnets are **regional**: one subnet spans every zone in the region |
| `--range` | Primary IPv4 CIDR. You can expand it later (`expand-ip-range`), never shrink it |
| `--enable-private-ip-google-access` | **PGA on**: VMs without external IPs can reach Google APIs |
| `--stack-type=IPV4_IPV6` | Dual-stack. `IPV6_ONLY` is also possible now |
| `--ipv6-access-type` | `INTERNAL` (ULA, VPC-only) or `EXTERNAL` (GUA, reachable from the internet) |

```bash
gcloud compute networks subnets update subnet-a --region=us-central1 --enable-private-ip-google-access
```
**When:** turning PGA on for an existing subnet. PGA is set **per subnet**, not per VPC.

```bash
gcloud compute networks list
gcloud compute networks subnets list --filter="network:privatenet"
gcloud compute networks subnets describe privatenet-us --region=us-west1 --format="value(privateIpGoogleAccess)"
```
**When:** checking what exists, or confirming PGA is `True`.

---

## 2. Routes
→ [[GCP01 - Routes and Route Preferences]] · visualization: [VPC Route Selection](../visualizations/gcp/GCP01-03%20-%20VPC%20Route%20Selection.html)

```bash
gcloud compute routes create to-onprem \
    --network=vpc-prod --destination-range=10.20.0.0/16 \
    --next-hop-vpn-tunnel=tunnel-1 --next-hop-vpn-tunnel-region=us-central1 \
    --priority=1000 [--tags=web]
```
| Piece | Meaning |
|-------|---------|
| `--destination-range` | The prefix this route covers. **Longest prefix wins first** |
| `--next-hop-*` | Where to send traffic. One of: `-gateway=default-internet-gateway`, `-instance=VM` (+`-instance-zone`), `-address=IP`, `-vpn-tunnel=T`, `-ilb=FORWARDING_RULE` |
| `--priority` | Tie-breaker when prefixes are equal. **Lower number wins**. Default 1000 |
| `--tags` | Apply only to VMs with this network tag (otherwise the route applies to the whole VPC) |

**When:** sending traffic through an NVA, forced tunneling (`0.0.0.0/0` at priority < 1000 to a VPN), restricted/private PGA VIPs (`199.36.153.4/30` or `.8/30` → default-internet-gateway).

```bash
gcloud compute routes list --filter="network:vpc-prod" \
    --format="table(name,destRange,nextHopGateway,nextHopInstance,nextHopVpnTunnel,priority)"
gcloud compute routes delete default-route-xxxx       # e.g. remove internet egress entirely
```
**When:** "why does traffic go *there*?" Compare against the routing order (policy-based → subnet → most specific → priority → preference category). Dynamic (BGP) routes **don't appear** in `routes list`. See `routers get-status` below.

---

## 3. Firewall rules and firewall policies
→ [[GCP12 - Lab - Implement Private Google Access and Cloud NAT]], [[GCP20 - Other Topologies - Mesh, Mirrored, Gated]]

```bash
gcloud compute firewall-rules create privatenet-allow-ssh \
    --network=privatenet --direction=INGRESS --action=ALLOW --rules=tcp:22 \
    --source-ranges=35.235.240.0/20 [--target-tags=ssh] [--priority=1000] [--enable-logging]
```
| Piece | Meaning |
|-------|---------|
| `--direction` | `INGRESS` (default) or `EGRESS` |
| `--action` + `--rules` | `ALLOW`/`DENY` + `tcp:22`, `tcp:80,443`, `icmp`, `all`. Shorthand: `--allow=tcp:22` |
| `--source-ranges` | Who may connect (ingress). `35.235.240.0/20` = **IAP TCP forwarding** range |
| `--target-tags` / `--target-service-accounts` | Which VMs the rule protects. Service accounts are stronger: tags can be edited by anyone who can edit the VM |
| `--priority` | 0–65535, lower is evaluated first. Implied rules: deny all ingress / allow all egress at 65535 |
| `--enable-logging` | Firewall Rules Logging (needed for Firewall Insights) |

**When:** every lab. Common ones:
- SSH via IAP: `35.235.240.0/20` → tcp:22
- **Health checks**: `130.211.0.0/22,35.191.0.0/16` → backend port. Without it, the LB returns 502
- Internal east-west traffic: source = your subnet ranges

```bash
gcloud compute firewall-rules list --format="table(name,network,direction,sourceRanges.list(),allowed[].map().firewall_rule().list(),targetTags.list())"
```
**When:** audits. Flag any ingress from `0.0.0.0/0`.

```bash
# Network firewall policy (newer model, attachable to many VPCs, supports FQDN objects)
gcloud compute network-firewall-policies create gate --global
gcloud compute network-firewall-policies rules create 100 --firewall-policy=gate --global-firewall-policy \
    --direction=EGRESS --action=allow --dest-fqdns=api.github.com --layer4-configs=tcp:443
gcloud compute network-firewall-policies rules create 65000 --firewall-policy=gate --global-firewall-policy \
    --direction=EGRESS --action=deny --dest-ip-ranges=0.0.0.0/0 --layer4-configs=all
gcloud compute network-firewall-policies associations create --firewall-policy=gate \
    --network=app-vpc --global-firewall-policy
```
| Piece | Meaning |
|-------|---------|
| `rules create 100` | The number **is** the priority |
| `--dest-fqdns` | Match by domain name. Classic VPC firewall rules can't do this |
| `--layer4-configs` | Same as `--rules` in classic firewall rules |
| `associations create` | Attaches the policy to a VPC. A policy does nothing until it's associated |

**When:** gated egress (allowlist of domains, then deny everything else), or central policy across many VPCs.

---

## 4. VMs and access
→ [[GCP12 - Lab - Implement Private Google Access and Cloud NAT]], [[GCP19 - Lab - Implement a Hub-and-Spoke Network Using Network Connectivity Center]]

```bash
gcloud compute instances create vm-internal \
    --zone=us-west1-b --machine-type=e2-standard-2 \
    --image-family=debian-12 --image-project=debian-cloud \
    --subnet=privatenet-us --no-address \
    [--tags=ssh,web] [--can-ip-forward] [--metadata=startup-script='#!/bin/bash ...']
```
| Piece | Meaning |
|-------|---------|
| `--image-family` + `--image-project` | Latest image in that family (e.g., Debian 12 from `debian-cloud`) |
| `--subnet` | Which subnet the NIC goes in (this also sets the VPC) |
| `--no-address` | **No external IP**. The VM needs PGA (for Google APIs) / Cloud NAT (for the internet) / IAP (for SSH) |
| `--tags` | Network tags, which firewall rules and routes target |
| `--can-ip-forward` | Lets the VM forward packets not addressed to itself. Required for NVAs, routers, NAT VMs |
| `--metadata=startup-script` | Script run at boot (install a web server, etc.) |

```bash
gcloud compute ssh vm-internal --zone=us-west1-b --tunnel-through-iap
```
| Piece | Meaning |
|-------|---------|
| `--tunnel-through-iap` | SSH through Identity-Aware Proxy. Works with **no external IP**. Needs a firewall rule for `35.235.240.0/20` |

**When:** reaching private VMs without a bastion host or a VPN.

```bash
gcloud compute instances describe access-test --zone=Z \
    --format="value(networkInterfaces[0].networkIP, networkInterfaces[0].accessConfigs[0].natIP)"
```
**When:** getting a VM's internal and external IP (e.g., the access-test IP for a Cloud Armor rule).

---

## 5. Cloud Router and Cloud NAT
→ [[GCP11 - Cloud NAT]], [[GCP12 - Lab - Implement Private Google Access and Cloud NAT]]

```bash
gcloud compute routers create nat-router --network=privatenet --region=us-west1 [--asn=65001]
```
| Piece | Meaning |
|-------|---------|
| `routers create` | **Cloud Router**: Google's managed control plane. Speaks BGP for VPN/Interconnect/NCC, and hosts NAT configs |
| `--asn` | BGP autonomous system number (private range 64512–65534 or 4200000000–4294967294). Needed for BGP, optional for NAT-only routers |

```bash
gcloud compute routers nats create nat-config --router=nat-router --region=us-west1 \
    --nat-all-subnet-ip-ranges --auto-allocate-nat-external-ips \
    [--min-ports-per-vm=64] [--enable-logging --log-filter=ERRORS_ONLY]
```
| Piece | Meaning |
|-------|---------|
| `routers nats create` | A NAT config **inside** a router. No NAT VM: it's programmed into the network fabric |
| `--nat-all-subnet-ip-ranges` | NAT every subnet in the region (alternative: `--nat-custom-subnet-ip-ranges=sub-a,sub-b`) |
| `--auto-allocate-nat-external-ips` | Google picks and scales the NAT IPs. Use `--nat-external-ip-pool=ip1,ip2` instead for **manual**, fixed IPs that partners can allowlist |
| `--min-ports-per-vm` | Source ports reserved per VM (default 64). Each IP has 64,512 usable ports |
| `--enable-logging --log-filter` | `ERRORS_ONLY` (port exhaustion drops), `TRANSLATIONS_ONLY`, or `ALL` |

**When:** private VMs need outbound internet (package updates, external APIs) and must **not** accept inbound connections.

```bash
gcloud compute routers get-status nat-router --region=us-west1
```
**When:** shows **BGP-learned routes** (`bestRoutes`), BGP peer state, and NAT IPs in use. This is where dynamic routes show up.

```bash
gcloud logging read 'resource.type="nat_gateway"' --limit=20 --format=json
```
**When:** checking NAT translations or dropped connections (`allocation_status: DROPPED` = port exhaustion).

---

## 6. Private access: PSC and PSA
→ [[GCP09 - Private Service Connect]], [[GCP10 - Private Services Access]], [[gcp-private-access]]

```bash
# PSC endpoint for Google APIs (consumer)
gcloud compute addresses create psc-apis --global --purpose=PRIVATE_SERVICE_CONNECT \
    --addresses=10.255.255.254 --network=VPC
gcloud compute forwarding-rules create pscapis --global --network=VPC \
    --address=psc-apis --target-google-apis-bundle=all-apis      # or vpc-sc
```
| Piece | Meaning |
|-------|---------|
| `--purpose=PRIVATE_SERVICE_CONNECT` | Reserve an internal IP for a PSC endpoint. It must be outside every subnet |
| `--target-google-apis-bundle` | `all-apis` (like private.googleapis.com) or `vpc-sc` (like restricted.googleapis.com) |

**When:** reaching Google APIs at **your own chosen internal IP**, including from on-prem across VPN/Interconnect.

```bash
# PSC for a published service (producer side)
gcloud compute networks subnets create psc-nat --network=producer-vpc --region=R \
    --range=192.168.0.0/24 --purpose=PRIVATE_SERVICE_CONNECT
gcloud compute service-attachments create nginx-sa --region=R \
    --producer-forwarding-rule=nginx-fr --connection-preference=ACCEPT_AUTOMATIC --nat-subnets=psc-nat
# consumer side
gcloud compute addresses create ep-ip --region=R --subnet=consumer-sn --addresses=10.0.0.100
gcloud compute forwarding-rules create nginx-ep --region=R --network=consumer-vpc \
    --address=ep-ip --target-service-attachment=projects/PROD_PROJECT/regions/R/serviceAttachments/nginx-sa
```
| Piece | Meaning |
|-------|---------|
| `--purpose=PRIVATE_SERVICE_CONNECT` (subnet) | **NAT subnet**: consumer traffic is SNATed into this range, which is why IP overlap doesn't matter |
| `service-attachments create` | Publishes the producer's internal LB as a service |
| `--connection-preference` | `ACCEPT_AUTOMATIC`, or `ACCEPT_MANUAL` with `--consumer-accept-list=PROJECT=LIMIT` |
| `--target-service-attachment` | Consumer endpoint pointing at the producer's attachment |

**When:** SaaS or shared-platform teams exposing **one service** privately to many VPCs, even ones with overlapping ranges.

```bash
# PSA (Cloud SQL, Memorystore, etc.)
gcloud compute addresses create google-managed --global --purpose=VPC_PEERING \
    --prefix-length=16 --network=VPC
gcloud services vpc-peerings connect --service=servicenetworking.googleapis.com \
    --ranges=google-managed --network=VPC
```
| Piece | Meaning |
|-------|---------|
| `--purpose=VPC_PEERING --prefix-length=16` | **Allocated range** that Google carves service subnets from |
| `vpc-peerings connect` | Creates the peering to Google's producer VPC |

**When:** before creating private-IP Cloud SQL/Memorystore. Allocate a big enough range up front, because each service and region takes subnets from it.

---

## 7. VPC peering and Network Connectivity Center
→ [[GCP18 - Hub-and-Spoke Topology]], [[GCP19 - Lab - Implement a Hub-and-Spoke Network Using Network Connectivity Center]]

```bash
gcloud compute networks peerings create a-to-b --network=vpc-a --peer-network=vpc-b \
    [--peer-project=OTHER] [--export-custom-routes --import-custom-routes]
```
| Piece | Meaning |
|-------|---------|
| `peerings create` | **One side** only. The peering is `INACTIVE` until the other VPC creates the matching peering back |
| `--export/--import-custom-routes` | Share static/dynamic routes (e.g., let a spoke learn the hub's VPN routes). Subnet routes are always exchanged |

**When:** simple VPC-to-VPC connections. **Not transitive**: A↔B and B↔C does **not** give A↔C.

```bash
gcloud network-connectivity hubs create my-hub --preset-topology=mesh     # or star
gcloud network-connectivity spokes linked-vpc-network create spoke1 \
    --hub=my-hub --vpc-network=projects/PROJECT/global/networks/spoke1-vpc --global
gcloud network-connectivity hubs list-spokes my-hub
```
| Piece | Meaning |
|-------|---------|
| `--preset-topology` | `mesh` = all spokes reach each other. `star` = edge spokes reach only center spokes |
| `spokes linked-vpc-network` | A **VPC spoke**. Other kinds: `linked-vpn-tunnels`, `linked-interconnect-attachments`, `linked-router-appliances` (hybrid spokes) |
| `--global` | VPC spokes are global resources |

**When:** many VPCs needing transitive connectivity without a peering mesh, or with hybrid links shared through one hub.

---

## 8. Cloud DNS
→ [[GCP04 - Cloud DNS]], [[gcp-dns]]

```bash
gcloud dns managed-zones create example --dns-name=example.com. \
    --visibility=private --networks=default --description="internal zone"
gcloud dns record-sets create web.example.com. --zone=example --type=A --ttl=300 --rrdatas=10.0.0.5
gcloud dns record-sets create geo.example.com. --zone=example --type=A --ttl=5 \
    --routing-policy-type=GEO --routing-policy-data="us-west1=$US_IP;europe-central2=$EU_IP"
gcloud dns record-sets list --zone=example
```
| Piece | Meaning |
|-------|---------|
| `--dns-name=example.com.` | Zone apex. The **trailing dot** = fully qualified |
| `--visibility=private --networks` | Answered only to VMs in the listed VPCs. `public` = internet-facing zone |
| `--ttl` | Cache time. Short TTLs (5s) let routing policies take effect fast |
| `--routing-policy-type` | `GEO` (closest region to the resolver), `WRR` (weighted), `FAILOVER` |
| `--rrdatas` | The record's value(s) |

**When:** private names for internal services, split-horizon DNS, geo-steering. Private zone for `googleapis.com` + `private.googleapis.com` A records = PGA through private VIPs.

---

## 9. Load balancing and Cloud Armor
→ [[GCP26 - Google Cloud DDoS Mitigations]], [[GCP28 - Lab - Configuring Traffic Blocklisting with Google Cloud Armor]] · visualization: [Cloud Armor at the Edge](../visualizations/gcp/GCP28%20-%20Cloud%20Armor%20at%20the%20Edge.html)

Global external Application LB, built in this order:
```bash
gcloud compute health-checks create http web-hc --port=80
gcloud compute backend-services create web-backend --protocol=HTTP --health-checks=web-hc --global
gcloud compute backend-services add-backend web-backend --instance-group=web-mig --instance-group-zone=Z --global
gcloud compute url-maps create web-map --default-service=web-backend
gcloud compute target-http-proxies create web-proxy --url-map=web-map
gcloud compute forwarding-rules create web-rule --global --target-http-proxy=web-proxy --ports=80
```
| Command | What it is | Why it exists |
|---------|-----------|---------------|
| `health-checks create` | Probe definition | Unhealthy backends get no traffic |
| `backend-services create` | Group of backends + balancing settings | **Cloud Armor, CDN, logging attach here** |
| `add-backend` | Put a MIG/NEG into the service | Actual capacity |
| `url-maps create` | Host/path → backend rules | `/api` → api-backend, everything else → web-backend |
| `target-http-proxies create` | The GFE proxy object | Terminates HTTP (use `target-https-proxies` + certificate for TLS) |
| `forwarding-rules create --global` | **The anycast IP + port** | What clients connect to |

```bash
gcloud compute backend-services get-health web-backend --global
gcloud compute forwarding-rules describe web-rule --global --format="value(IPAddress)"
```
**When:** the lab's first checks. Get-health tells LB problems (502) apart from Cloud Armor (403/404).

```bash
gcloud compute security-policies create blocklist-access-test
gcloud compute security-policies rules create 1000 --security-policy=blocklist-access-test \
    --src-ip-ranges="198.51.100.23/32" --action=deny-404 [--preview]
gcloud compute security-policies rules create 2000 --security-policy=web-shield \
    --expression="evaluatePreconfiguredWaf('sqli-v33-stable')" --action=deny-403
gcloud compute security-policies update web-shield --enable-layer7-ddos-defense
gcloud compute backend-services update web-backend --security-policy=blocklist-access-test --global
gcloud compute security-policies rules list blocklist-access-test
```
| Piece | Meaning |
|-------|---------|
| `rules create 1000` | Priority 1000. Lowest number is evaluated first, and the **first match wins**. The default rule is 2147483647 |
| `--src-ip-ranges` | Basic mode: match client IPs (up to 10 ranges per rule) |
| `--expression` | Advanced mode: CEL expression (`origin.region_code == 'XX'`, headers, preconfigured WAF) |
| `--action` | `allow`, `deny-403`, `deny-404`, `deny-502`, `rate-based-ban`, `throttle`, `redirect` |
| `--preview` | Log what the rule *would* do without enforcing it. Use before turning a rule on in production |
| `--enable-layer7-ddos-defense` | **Adaptive Protection** (ML attack detection, needs Enterprise for full features) |
| `backend-services update --security-policy` | **Attach** the policy. Until then it does nothing |

**When:** blocking abusive IPs, WAF rules against OWASP attacks, rate limiting `/login`, DDoS response.

---

## 10. Troubleshooting and visibility
→ [[GCP21 - Getting Topology Data]]

```bash
gcloud network-management connectivity-tests create web-db \
    --source-instance=projects/P/zones/Z/instances/web-vm \
    --destination-instance=projects/P/zones/Z/instances/db-vm \
    --protocol=TCP --destination-port=5432
gcloud network-management connectivity-tests describe web-db --format="value(reachabilityDetails.result)"
```
| Piece | Meaning |
|-------|---------|
| `connectivity-tests create` | **Simulates** a packet against your config (routes, firewalls, peering). Sends no real traffic |
| result | `REACHABLE`, `UNREACHABLE` (with the blocking rule named), `AMBIGUOUS` |

**When:** "Can A reach B on port X?" before or after changes. Answers in seconds without SSHing anywhere.

```bash
gcloud logging read 'resource.type="http_load_balancer" AND jsonPayload.enforcedSecurityPolicy.outcome="DENY"' --limit=10
gcloud logging read 'resource.type="gce_subnetwork" AND logName:"vpc_flows"' --limit=10    # VPC Flow Logs
```
**When:** after the fact: who was blocked, which flows happened.

---

## 11. Inside the VM: verification commands
| Command | What it tells you | Used in |
|---------|------------------|---------|
| `curl -m1 http://IP` | HTTP response; `-m1` = 1-second timeout so loops don't hang | GCP28 (LB / Cloud Armor) |
| `curl -s -o /dev/null -w "%{http_code}\n" URL` | Just the status code (200 / 403 / 404 / 502) | Scripted checks |
| `curl ifconfig.me` | Your **public** source IP as the internet sees it, e.g. the NAT IP | GCP12 (Cloud NAT) |
| `ping -c 3 IP` | ICMP reachability and RTT. Needs a firewall rule allowing ICMP | GCP19 (NCC) |
| `traceroute IP` / `mtr IP` | Hop-by-hop path | Service tiers, hybrid paths |
| `dig +short NAME` / `dig @169.254.169.254 NAME` | DNS answer, optionally from the VM's metadata resolver | GCP04, PGA private zone |
| `ip addr show` | Interfaces and IPs (internal only: the external IP is 1:1 NAT and never appears on the VM) | Any |
| `ip route show` | The VM's local routes. The default route points at the subnet gateway (`.1`) | NVA / multi-NIC labs |
| `gcloud storage ls gs://BUCKET` | Reaches a Google API. Works on a no-external-IP VM only if **PGA** (or PSC) is set up | GCP12 |
| `sudo tcpdump -ni ens4 port 80` | Live capture on the VM NIC (`ens4` on GCE Debian) | Pairs with the Wireshark course |

---

## 12. IAM: roles, bindings, deny policies
→ [[GCP30 - IAM Roles]] · visualization: [IAM Access Explorer](../visualizations/gcp/GCP29-30%20-%20IAM%20Access%20Explorer.html)

```bash
gcloud iam roles describe roles/compute.networkAdmin
gcloud iam roles describe roles/compute.networkAdmin --format="value(includedPermissions)" | tr ';' '\n' | grep firewalls
```
**When:** "what exactly does this role allow?" before you grant it. The grep shows Network Admin has only `firewalls.get/list`.

```bash
gcloud projects add-iam-policy-binding PROJECT_ID \
    --member="group:net-eng@cymbal.com" --role="roles/compute.networkViewer" \
    [--condition='expression=request.time < timestamp("2026-12-31T00:00:00Z"),title=temp']
gcloud resource-manager folders add-iam-policy-binding FOLDER_ID --member=... --role=...
gcloud organizations add-iam-policy-binding ORG_ID --member=... --role=...
gcloud projects remove-iam-policy-binding PROJECT_ID --member=... --role=...
```
| Piece | Meaning |
|-------|---------|
| `projects` / `resource-manager folders` / `organizations` | **Where** in the hierarchy the binding goes. Everything below inherits it |
| `add-iam-policy-binding` | Adds one role → member binding (read-modify-write of the policy, done for you) |
| `--member` | `user:`, `group:`, `serviceAccount:`, `domain:`. Prefer **groups** |
| `--role` | Predefined `roles/...` or custom `projects/P/roles/NAME` / `organizations/O/roles/NAME` |
| `--condition` | IAM Condition: time-bound or resource-scoped grant |

**When:** granting least-privilege access. Pick the **smallest scope** that covers the need.

```bash
gcloud projects get-iam-policy PROJECT_ID --flatten="bindings[].members" \
    --format="table(bindings.role,bindings.members)"
```
**When:** auditing who has what *directly* on the project. Inherited grants are on the folder/org policies, so check those too (or use Policy Analyzer).

```bash
gcloud iam roles create customNetFirewall --project=PROJECT_ID \
    --title="Firewall Editor + SSL Viewer" --stage=GA \
    --permissions=compute.firewalls.create,compute.firewalls.delete,compute.firewalls.get,compute.firewalls.list,compute.firewalls.update,compute.networks.get,compute.networks.list,compute.networks.updatePolicy,compute.sslCertificates.get,compute.sslCertificates.list
gcloud iam roles update customNetFirewall --project=PROJECT_ID --add-permissions=compute.firewalls.get
```
| Piece | Meaning |
|-------|---------|
| `--project` / `--organization` | Where the custom role is **defined** (org-level roles are reusable across projects) |
| `--permissions` | Exact list. Some permissions can't be used in custom roles |
| `--stage` | `ALPHA` / `BETA` / `GA` / `DEPRECATED`: a label for lifecycle |

**When:** no predefined role fits (too much or too little).

```bash
gcloud iam policies create deny-fw-edits --kind=denypolicies \
    --attachment-point="cloudresourcemanager.googleapis.com/projects/PROJECT_ID" \
    --policy-file=deny.json
# deny.json: {"rules":[{"denyRule":{"deniedPrincipals":["principalSet://goog/public:all"],
#   "exceptionPrincipals":["principalSet://goog/group/sec-team@cymbal.com"],
#   "deniedPermissions":["compute.googleapis.com/firewalls.create","compute.googleapis.com/firewalls.update"]}}]}
```
| Piece | Meaning |
|-------|---------|
| `--kind=denypolicies` | Deny policy (checked **before** allow policies) |
| `--attachment-point` | Org, folder or project (URL-encoded resource name) |
| `deniedPermissions` | Uses the `service.googleapis.com/resource.verb` format |
| `exceptionPrincipals` | Who is exempt (e.g., the security team) |

**When:** hard guardrails that inherited allow grants can't override ("nobody but security edits firewall rules").

```bash
gcloud policy-intelligence troubleshoot-policy iam RESOURCE_FULL_NAME \
    --principal-email=USER --permission=compute.subnetworks.delete
```
**When:** "why can (or can't) this person do X?" Shows which binding or deny rule decided it.

---

## Linked Pages
- [[gcp-product-map]] · [[gcp-private-access]] · [[gcp-network-architecture]] · [[gcp-routing]] · [[gcp-dns]]
- [[certs/gcp-networking]]
