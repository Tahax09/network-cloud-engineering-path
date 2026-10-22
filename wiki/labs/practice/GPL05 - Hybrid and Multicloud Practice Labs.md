---
id: GPL05
aliases: [GPL05]
title: Hybrid and Multicloud Practice Labs
course: Google Cloud Networking Specialization
cert_track: gcp-networking
covers: GCP49–GCP60
date: 2026-10-22
type: practice-labs
tags: [gcp, practice, labs, ha-vpn, bgp, med, active-passive, ncc, hybrid-spokes, home-lab, frr, strongswan, k3s, troubleshooting]
---

# GPL05 — Hybrid and Multicloud Practice Labs

Scenarios: [[GS06 - Hybrid and Multicloud Scenarios]]. Interconnect can't be bought for a lab, so these labs practice **everything around it** (BGP, routing modes, NCC, failover) with HA VPN, which uses the same Cloud Router commands.

---

## Lab H1 — Active/passive HA VPN with MED
**Goal:** extend [[GCP58 - Lab - Configuring Google Cloud HA VPN]] so one tunnel carries all traffic and the other is standby.

**Tasks**
1. Rebuild the GCP58 lab (two VPCs, 4 tunnels, BGP).
2. On **both** routers, set `--advertised-route-priority=100` on the tunnel0 peer and `200` on the tunnel1 peer.
3. `routers get-status` on each side: `bestRoutes` should show only the tunnel0 next hop for the remote prefixes.
4. Delete tunnel0 on one side. Best routes move to tunnel1, and a continuous `ping` loses at most a few packets.
5. Bonus: set both to 100 and confirm both tunnels appear (ECMP).

**Success:** you can explain why you set priorities on **both** sides (each controls the other's inbound choice).

---

## Lab H2 — Regional vs global routing, measured
**Goal:** see exactly which prefixes are advertised in each mode.

**Tasks**
1. Add a third subnet to `vpc-demo` in a third region.
2. In REGIONAL mode, `gcloud compute routers get-status vpc-demo-router1` → `advertisedRoutes`. Record them.
3. Switch to GLOBAL and record them again, including the **MED** values (base 100 + region-to-region cost for remote regions).
4. From on-prem, `traceroute` to each region's VM and compare RTT with the MEDs.

**Success:** a table of prefix × mode × advertised MED, in your own words.

---

## Lab H3 — NCC hub with hybrid and VPC spokes
**Goal:** connect three networks without a peering mesh, then enable site-to-site.

**Tasks**
1. VPCs `spoke-a` and `spoke-b` (VPC spokes, mesh preset).
2. HA VPN from `spoke-a` to `on-prem` (as in GCP58). Attach the tunnels as a **hybrid spoke** (`linked-vpn-tunnels`).
3. From on-prem, ping a VM in `spoke-b`. It should work through the hub because hybrid routes reach other VPC spokes.
4. Build a second "site" (`branch` VPC + its own HA VPN to `spoke-a`) as another hybrid spoke. Try site ↔ site with and without `--site-to-site-data-transfer` (only supported regions allow it).

**Success:** you can draw which routes the hub exchanges for each spoke type.

---

## Lab H4 — Your k3s lab over HA VPN (home lab, optional)
**Goal:** private connectivity from your **ARM64 Ubuntu k3s host** to a GCP VPC, using real IPsec and BGP.

**Tasks**
1. GCP: HA VPN gateway + Cloud Router (ASN 65010). The external VPN gateway resource gets your home public IP (`SINGLE_IP_INTERNALLY_REDUNDANT`, or two IPs if you have dual WAN).
2. Home: **strongSwan** (IKEv2, two tunnels to the two HA VPN interface IPs) + **FRR** (`bgpd`, ASN 65020, neighbors 169.254.x.x, advertise your k3s pod/service CIDRs or a home /24).
3. Port-forward UDP 500/4500 (and ESP) on your router to the host if it's behind NAT. NAT traversal makes it UDP 4500.
4. Check `vtysh -c 'show ip bgp summary'` (established) and `gcloud compute routers get-status` (home routes learned).
5. From a GCP VM, `curl` a k3s service's ClusterIP or NodePort over the tunnel. Watch it in **Kubeshark**.

**Security:** use a long random pre-shared key, keep it out of git, restrict the GCP firewall to your home ranges, and tear it down when you're done (VPN tunnels bill hourly).

**Success:** a working hybrid link with your own BGP config. This is the closest home equivalent of Partner/Dedicated Interconnect routing.

---

## Lab H5 — Break-fix drill
**Goal:** practice the bottom-up checklist from GS06 Scenario 6. A partner (or a script) breaks one thing; you find it with commands only.

| Break | Symptom to find | Command that reveals it |
|-------|----------------|------------------------|
| Wrong shared secret on one tunnel | `AUTHORIZATION_ERROR` | `vpn-tunnels describe … --format="value(status,detailedStatus)"` |
| Peer ASN typo | BGP down, tunnel up | `routers get-status → bgpPeerStatus[].state` |
| Link-local IPs swapped | BGP idle | `routers describe → interfaces / bgpPeers` |
| Routing mode back to REGIONAL | Region-2 VM unreachable | `networks describe → routingConfig` |
| Remote-range firewall rule deleted | BGP fine, ping fails | `firewall-rules list`, Connectivity Test |

**Success:** each fault found in under 5 minutes, using only gcloud output.

---

## Linked Pages
- [[GS06 - Hybrid and Multicloud Scenarios]] · [[GCP58 - Lab - Configuring Google Cloud HA VPN]] · [[GCP59 - Influence Best Path Selection]] · [[GCP60 - Network Connectivity Center]] · [[k3s-lab]] · [[certs/gcp-networking]]
