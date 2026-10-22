---
id: GPL04
aliases: [GPL04]
title: Load Balancing Practice Labs
course: Google Cloud Networking Specialization
cert_track: gcp-networking
covers: GCP39–GCP48
date: 2026-10-22
type: practice-labs
tags: [gcp, practice, labs, url-map, canary, header-routing, hybrid-neg, ha-vpn, cloud-cdn, cache-modes, edge-security-policy, next-hop-ilb, nat-gateway, autoscaling]
---

# GPL04 — Load Balancing Practice Labs

Scenarios: [[GS05 - Load Balancing Scenarios]]. Personal test project only; clean up after each lab.

---

## Lab L1 — Canary + header routing on one URL map
**Goal:** v1 and v2 behind one global external ALB; 90/10 split; `X-Canary: 1` always goes to v2.

**Tasks**
1. Two MIGs, each serving `/version` that returns `v1` or `v2`. Two backend services.
2. Export the URL map, then add two route rules: header match at priority 1 (→ v2), and a weighted split at priority 10.
3. `validate`, then `import`.
4. Measure: `for i in $(seq 1 200); do curl -s http://$IP/version; echo; done | sort | uniq -c`.
5. Roll back to 100/0 and time how long until no more v2 responses appear.

<details><summary>Solution hints</summary>

```bash
gcloud compute url-maps export web-map --global --destination=map.yaml
# edit map.yaml: pathMatchers[0].routeRules = header rule (priority 1) + weighted rule (priority 10)
gcloud compute url-maps validate --source=map.yaml --global && gcloud compute url-maps import web-map --global --source=map.yaml -q
curl -s -H 'X-Canary: 1' http://$IP/version
```
</details>

**Success:** you get about a 180/20 split and 100% v2 with the header.

---

## Lab L2 — Hybrid NEG against a simulated on-prem
**Goal:** a Google LB fronting servers "outside Google" (simulated by a second VPC reached through HA VPN). Combines Courses 5 and 6.

**Tasks**
1. Build the HA VPN from [[GCP58 - Lab - Configuring Google Cloud HA VPN]] (`vpc-demo` ↔ `on-prem`), with `vpc-demo` in **GLOBAL** routing mode.
2. On `on-prem-instance1`, run nginx on :80.
3. In `vpc-demo`: hybrid NEG `NON_GCP_PRIVATE_IP_PORT` in the zone closest to on-prem, endpoint `192.168.1.x:80`.
4. Regional internal ALB (proxy-only subnet in the same region as the VPN) with that NEG as the backend.
5. Health checks: advertise the **proxy-only subnet** from `vpc-demo-router1` to on-prem (custom advertisement) and allow it in on-prem's firewall.
6. `curl` the ILB IP from `vpc-demo-instance1`.

**Success:** the backend shows HEALTHY and you get nginx's page. Then deliberately remove the custom advertisement and watch it go UNHEALTHY. That's the most common real-world hybrid NEG failure.
**Note:** production hybrid NEGs should hold only truly external endpoints. A second VPC is a learning stand-in (Google's codelabs use the same trick).

---

## Lab L3 — Cloud CDN cache modes, headers and invalidation
**Goal:** prove what each cache mode stores.

**Tasks**
1. Bucket with `logo.png` (`Cache-Control: public, max-age=3600`), `private.json` (`Cache-Control: private`) and `nocache.txt` (no header).
2. Backend bucket with CDN on, behind an external ALB.
3. For each mode (`USE_ORIGIN_HEADERS`, `CACHE_ALL_STATIC`, `FORCE_CACHE_ALL`), request each object twice and record `Age` plus the log's `cacheHit`.
4. Fill in the result matrix. Then `invalidate-cdn-cache --path="/*"` and see the next request miss.
5. Repeat the GSP878 trick: delete `logo.png` from the bucket and show it's still served.

| Object | USE_ORIGIN_HEADERS | CACHE_ALL_STATIC | FORCE_CACHE_ALL |
|--------|-------------------|------------------|-----------------|
| logo.png (public, max-age) | ? | ? | ? |
| private.json | ? | ? | ? |
| nocache.txt | ? | ? | ? |

<details><summary>Expected</summary>

logo: hit / hit / hit. private.json: miss / miss / **hit** (dangerous). nocache.txt: miss / depends on content type (CACHE_ALL_STATIC treats images, CSS, JavaScript, fonts and media as static; check whether your `text/plain` object hits and note it) / hit.
</details>

---

## Lab L4 — Load-balanced NAT gateways as a next-hop ILB
**Goal:** egress for private VMs through two Linux NAT gateways behind an internal passthrough NLB, with failover.

**Tasks**
1. Two gateway VMs with external IPs and `--can-ip-forward`, running `sysctl -w net.ipv4.ip_forward=1` and `iptables -t nat -A POSTROUTING -o ens4 -j MASQUERADE`.
2. Unmanaged instance groups → an internal passthrough NLB (TCP health check on port 22, or a small HTTP health endpoint).
3. Route `0.0.0.0/0` priority 800 → `--next-hop-ilb` (tagged `--tags=private` so only the test VM uses it). Delete nothing else.
4. Test VM (tag `private`, no external IP): `curl -s ifconfig.me` shows one gateway's IP. Run it repeatedly and watch it change per flow.
5. Stop gateway A, and new flows use B. Stop both, and the route still exists but traffic black-holes. Add a failover policy with a backup.

**Success:** you can explain why custom NAT VMs exist at all: filtering, logging, appliances that Cloud NAT can't do.

---

## Lab L5 — Autoscale on LB utilization
**Goal:** watch a MIG scale on the load balancer's signal, not on CPU.

**Tasks**
1. MIG min 1, max 6, `--target-load-balancing-utilization=0.6`; backend `--balancing-mode=RATE --max-rate-per-instance=20`.
2. Generate moderate load against **your own** LB from Cloud Shell (`hey -z 3m -q 10 -c 10 http://$IP/` or a curl loop).
3. Watch `gcloud compute instance-groups managed list-instances` grow, then shrink after the cool-down.

**Success:** you can tie the size to the math: requests per second ÷ (20 × 0.6) ≈ instances.

---

## Linked Pages
- [[GS05 - Load Balancing Scenarios]] · [[GCP42 - Traffic Management]] · [[GCP41 - Hybrid Load Balancing]] · [[GCP44 - Internal Network Load Balancers as Next Hops]] · [[GCP45 - Cloud CDN]] · [[GCP48 - Load Balancer Optimization Strategies]] · [[certs/gcp-networking]]
