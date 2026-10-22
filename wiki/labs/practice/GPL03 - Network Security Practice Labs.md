---
id: GPL03
aliases: [GPL03]
title: Network Security Practice Labs
course: Google Cloud Networking Specialization
cert_track: gcp-networking
covers: GCP23–GCP38
date: 2026-10-22
type: practice-labs
tags: [gcp, practice, labs, cloud-armor, rate-limiting, iam, deny-policy, firewall-policies, secure-tags, cloud-ids, packet-mirroring, tcpdump, secure-web-proxy]
---

# GPL03 — Network Security Practice Labs

Scenarios: [[GS04 - Network Security Scenarios]]. Run everything in a **personal test project**, test only **your own resources**, and **clean up** after each lab (each one lists its delete commands).

```bash
export PROJECT=$(gcloud config get-value project) REGION=us-central1 ZONE=us-central1-a
```

---

## Lab S1 — Rate-limit a login path with Cloud Armor (preview → enforce)
**Goal:** build a small web app behind a global external ALB, add a per-IP throttle on `/login` in **preview**, read the logs, then enforce.

**Tasks**
1. MIG (2 × e2-small, nginx with `/login` and `/` pages), health check, backend service, URL map, HTTP proxy, forwarding rule.
2. Policy `shop-policy`: rule 100 throttles `/login` at 20 requests / 60 s per IP, deny-429, `--preview`.
3. Send 50 requests to `/login` from Cloud Shell (your own LB) and read the logs: `previewSecurityPolicy` should show what would have been denied.
4. Remove preview and repeat. Requests after the 20th return **429**, while `/` stays 200.

<details><summary>Solution hints</summary>

```bash
for i in $(seq 1 50); do curl -s -o /dev/null -w "%{http_code} " http://$LB_IP/login; done; echo
gcloud logging read 'resource.type="http_load_balancer" AND jsonPayload.previewSecurityPolicy.priority=100' --limit=5 --format=json
gcloud compute security-policies rules update 100 --security-policy=shop-policy --no-preview
```
</details>

**Success:** you can explain why preview exists and how `--enforce-on-key=IP` changes behind a corporate NAT (everyone shares one IP; consider `HTTP-HEADER` or `XFF-IP`).
**Cleanup:** forwarding rule → proxy → URL map → backend service → MIG → template → health check → policy.

---

## Lab S2 — IAM: prove inheritance and a deny policy
**Goal:** see that a child grant can't take away a parent grant, and that a deny policy can.

**Tasks** (needs a second test account, or use a service account as the principal)
1. Grant the test principal **Editor** on the project, then **Network Viewer** too. Confirm it can still create a firewall rule (union of grants).
2. Create a **project-level deny policy** denying `compute.googleapis.com/firewalls.create` for that principal.
3. Retry: creating the rule fails. Run `policy-intelligence troubleshoot-policy iam` and find the deny rule in the output.
4. Replace Editor with a **custom role** (firewall create/list only), remove the deny, and confirm least privilege.

**Success:** you can state the evaluation order (deny policies, then allow) from evidence, not memory.
**Cleanup:** remove the bindings, delete the deny policy and the custom role.

---

## Lab S3 — Firewall policy hierarchy with secure tags
**Goal:** replace tag-based VPC rules with a **global network firewall policy** using IAM-governed **secure tags**.

**Tasks**
1. VPC `fw-lab` with `web-vm` and `db-vm` (no external IPs) and an IAP SSH allow.
2. Tag key `tier` (purpose `GCE_FIREWALL`, scoped to `fw-lab`) with values `web` and `db`; bind them to the VMs.
3. Network policy `fw-pol`: allow tcp:5432 from `tier=web` to `tier=db` (priority 100); deny all ingress to `tier=db` (priority 200). Associate it with `fw-lab`.
4. From web: `nc -zv DB_IP 5432` connects. From a third untagged VM, it doesn't.
5. Switch `--network-firewall-policy-enforcement-order=BEFORE_CLASSIC_FIREWALL`, add a conflicting classic VPC allow, and observe which wins.

**Success:** you can explain why secure tags are safer than network tags (`tagUser` IAM vs anyone with instance edit rights).
**Cleanup:** disassociate and delete the policy, delete the tag bindings, values and key, the VMs and the VPC.

---

## Lab S4 — DIY IDS: Packet Mirroring to your own collector
**Goal:** see exactly what Cloud IDS receives, with no IDS cost. Mirror a web VM to a collector and capture with tcpdump. This ties straight into the Wireshark course.

**Tasks**
1. Subnets `web-sn` and `col-sn`. A collector MIG (1 VM) behind an **internal passthrough NLB** with `--is-mirroring-collector`.
2. Mirroring policy on `web-sn`, filter `--filter-protocols=tcp --filter-direction=ingress`.
3. On the collector: `sudo tcpdump -ni ens4 -w /tmp/mirror.pcap port 80`. From Cloud Shell, curl the web VM's page through its LB or an IAP tunnel.
4. Copy the pcap to your laptop (`gcloud compute scp`) and open it in **Wireshark**. Find the HTTP request, then compare the source IPs you see with what the app logs say.
5. Change the filter to `--filter-direction=both` and compare pcap size.

**Success:** you can describe the mirror path, the egress cost on the source VM, and the 30-filter rule.
**Cleanup:** mirroring policy → forwarding rule → backend service → MIG → VMs → subnets.

---

## Lab S5 — Egress allowlist with Secure Web Proxy + a bypass guard
**Goal:** CI VMs reach only registries, through the proxy, and can't bypass it.

**Tasks**
1. Proxy-only subnet (`REGIONAL_MANAGED_PROXY`), Cloud NAT for the proxy's egress.
2. URL list `dev-registries` (`pypi.org`, `files.pythonhosted.org`, `registry.npmjs.org`, `*.github.com`).
3. Gateway security policy rule: `source.matchServiceAccount('ci@…') && inUrlList(host(), '…/dev-registries')` → ALLOW. Deploy the gateway.
4. Egress firewall on the CI VM: deny tcp:443 and tcp:80 to `0.0.0.0/0` (priority 1000), allow tcp:443 to the gateway IP (priority 900).
5. Test `pip download requests` with `HTTPS_PROXY` set (works), `curl https://example.com` (403 from the proxy), and `curl --noproxy '*' https://pypi.org` (blocked by the firewall).

**Success:** every outbound attempt appears in the SWP logs, and the bypass fails.
**Cleanup:** gateway → policy rules → policy → URL list → proxy-only subnet → NAT → VMs.

---

## Lab S6 — VPC Service Controls dry run (if you have an organization)
**Goal:** watch an exfiltration attempt get flagged, without breaking anything.

**Tasks**
1. Access policy + perimeter around a test project with restricted service `storage.googleapis.com`, in **dry-run** mode.
2. From Cloud Shell (outside the perimeter), copy an object from a bucket in the project to a bucket in a second project.
3. Find the **dry-run violation** in Cloud Audit Logs (`protoPayload.metadata.dryRun=true`, `VPC_SERVICE_CONTROLS`).
4. Enforce the perimeter and repeat: the copy now fails.

**Cleanup:** delete the perimeter (and the access policy if it was created just for this).

---

## Linked Pages
- [[GS04 - Network Security Scenarios]] · [[GCP26 - Google Cloud DDoS Mitigations]] · [[GCP30 - IAM Roles]] · [[GCP31 - Firewall Rules]] · [[GCP33 - Cloud IDS]] · [[GCP35 - Secure Web Proxy]] · [[GCP37 - Packet Mirroring for Network Traffic Inspection]]
- [[gcp-command-anatomy]] · [[certs/gcp-networking]]
