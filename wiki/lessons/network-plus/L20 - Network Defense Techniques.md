---
id: L20
aliases: [L20]
title: Network Defense Techniques
course: CompTIA Network+ N10-009
cert_track: network-plus
objective: "4.3 — Network Security: Given a Scenario, Apply Network Security Features, Defense Techniques, and Solutions"
date: 2026-10-08
status: Complete
quiz_attempted: No
weak_areas: []
tags: [device-hardening, default-passwords, nac, port-security, sticky-mac, 802.1x, eapol, mab, mac-filtering, key-management, key-rotation, key-escrow, acl, wildcard-mask, implicit-deny, url-filtering, content-filtering, dlp, zones, trusted-untrusted, drop-vs-reject, screened-subnet, dmz]
---

# L20 — Network Defense Techniques

## Summary
L19 covered the attacks. This lesson covers the controls that stop them. Five groups: **device hardening** (shut unused ports and services, kill default credentials), **network access control** (port security, 802.1X, MAC filtering), **key management** (protect, rotate, and escrow keys), **security rules** (ACLs, URL filtering, content filtering), and **zones** (trusted vs untrusted, screened subnet). Objective 4.3 is a "given a scenario, apply" objective, so expect questions that describe a problem and ask which control fixes it, plus ACL-reading questions.

---

## Core Concepts

### Device Hardening

**Goal:** Shrink the **attack surface** (fewer enabled systems, ports, services, and features) so known weaknesses can't be used. The specific steps vary by OS and vendor, but the pattern is the same everywhere.

| Step | Why | Example |
|------|-----|---------|
| **Disable unused physical ports** | Someone with physical access can't just plug in | `interface range Gi1/0/20-48` → `shutdown`, then move them to an unused "parking" VLAN |
| **Disable unused services** | Every listening service is a potential exploit target | Turn off CDP/LLDP on edge ports, HTTP management, mDNS/SSDP auto-discovery, Telnet |
| **Host firewall: block inbound** | Hosts shouldn't accept requests on ports they don't serve | `ufw default deny incoming` |
| **Change/remove default credentials** | Defaults are in **published manuals**, so they're effectively public | Disable `admin`/`guest` accounts; if they can't be removed, change the password |
| **Apply vendor lockdown/hardening baselines** | Most systems are **insecure by default** for ease of setup | CIS Benchmarks, DISA STIGs, vendor "security wizard" profiles |

**Key exam idea:** Shutting down an unused switch port is a **hardening** step. It's the simplest defense against a rogue device plugged into a wall jack ([[L19 - Network Attacks]]).

---

### Network Access Control (NAC)

**NAC** limits *which devices* can get on the network, wired or wireless. The typical flow:

```
Device connects ──▶ Restricted state: only authentication + health-check (posture) traffic allowed
                          │
               Authenticated + passes posture? (patched OS, AV running, disk encrypted)
                   │ yes                                    │ no
                   ▼                                        ▼
        Full access / assigned VLAN             Quarantine / remediation VLAN
```

NAC may need a **persistent agent** installed on the device, or a **dissolvable (just-in-time) agent** downloaded at connection time. Some products run **agentless**.

#### Port Security (switch feature)
- **Limits how many MAC addresses** can be learned on a port. **Default maximum = 1.**
- Prevents the same MAC from being learned on **more than one port**, which blocks MAC spoofing.
- Supports **static** (manually entered) MACs and **sticky** MACs: dynamically learned, then written into the running config as if they had been typed in.
- Defends against **MAC flooding** and **spoofing** ([[L19 - Network Attacks]]).

```
interface Gi1/0/5
 switchport mode access
 switchport port-security
 switchport port-security maximum 3
 switchport port-security mac-address 0011.2233.4455      ! static entry
 switchport port-security mac-address sticky             ! learn the rest, then pin
 switchport port-security violation shutdown             ! default: err-disable the port
```

**Violation modes:**
| Mode | Drops offending traffic | Logs/SNMP trap | Increments counter | Port state |
|------|:-:|:-:|:-:|---|
| **Protect** | ✔ | ✘ | ✘ | Up |
| **Restrict** | ✔ | ✔ | ✔ | Up |
| **Shutdown** (default) | ✔ | ✔ | ✔ | **err-disabled** |

#### 802.1X (Port-Based Network Access Control)
802.1X was designed for wired networks and also works on wireless (WPA2/WPA3-Enterprise, [[L11 - Wireless Devices and Technologies]]). Most NAC products are built on it.

**Three roles (frequently tested):**
| Role | What it is |
|------|-----------|
| **Supplicant** | The client device/software requesting access |
| **Authenticator** | The switch or wireless AP that controls the port |
| **Authentication server** | Usually **RADIUS** ([[L18 - Basic Network Security]]), backed by AD/LDAP or a certificate authority |

```
Supplicant ──EAPOL (EAP over LAN)──▶ Authenticator (switch/AP) ──RADIUS (UDP 1812)──▶ Auth server
     ◀──────────── port stays UNAUTHORIZED: only EAPOL frames allowed until Access-Accept ────────────
```
- Before authentication, the port passes **only EAPOL** traffic.
- **EAP** is a framework that supports many methods: **EAP-TLS** (certificates on both sides, the strongest), PEAP (password inside a TLS tunnel), and others. 802.1X is **built around certificate-based authentication**, but manual (username/password) authentication is supported.
- On Access-Accept, the port becomes **authorized**. RADIUS can also assign a VLAN dynamically.
- **Non-802.1X devices** (printers, IoT, cameras) go on a **bypass list** by MAC address, called **MAC Authentication Bypass (MAB)**. The weakness: MACs can be **spoofed**, so put MAB devices in a restricted VLAN.

#### MAC Address Filtering
An allow-list of approved MACs; everything else is refused. **Weak**, because MACs travel **in cleartext** in every frame: sniff an allowed MAC, clone it (`ip link set dev eth0 address …`), and you're in. It's acceptable as one layer, never as the only control.

**NAC strength ranking:** 802.1X with EAP-TLS > 802.1X with PEAP > port security (sticky/static) > MAB > MAC filtering.

---

### Key Management

Keys are only as secure as their storage and lifecycle (building on the PKI material in [[L18 - Basic Network Security]]).

| Key type | Main concern | Practices |
|----------|-------------|-----------|
| **Symmetric** | **Anyone holding the key can decrypt and encrypt** | Restrict access to where it's stored, **monitor usage**, **rotate regularly** (automated rotation) |
| **Asymmetric** | **Protect the private key**. Only the public key goes to the CA in the certificate request | If the private key or its host is compromised: traffic can be decrypted, **identity can be impersonated** → **revoke** the certificate (CRL/OCSP) and reissue |

**Key escrow / archive:** A trusted third party (or internal vault) **stores copies of keys so they can be recovered**. Without escrow, a lost key means **permanently unreadable data** (think a lost BitLocker key). The trade-off: the escrow itself becomes a high-value target.

**Where keys live in practice:** **HSMs** (hardware security modules, keys never leave the hardware), cloud **KMS**, HashiCorp **Vault**, **TPM** chips on endpoints.

---

### Security Rules

#### ACL: one term, two meanings
| Context | Meaning |
|---------|---------|
| **File system** (Windows NTFS, Linux `setfacl`) | Authorization list: who can read, write, or modify an object |
| **Network** (router/firewall) | Ordered list of **permit/deny** statements that filter packets |

#### How Network ACLs Are Processed (heavily tested)
1. Evaluated **top-down, in order**.
2. **First match wins**: the action is taken and processing **stops**.
3. No match → next rule.
4. **Implicit deny any** at the end, even if you don't see it written. **An ACL with only deny statements blocks everything.**
5. Cisco keeps a **hit counter** per rule (`show access-lists`), which is useful for troubleshooting.

The lecture's example, written correctly:
```
access-list 1 permit host 10.1.1.1               ! allow this one host...
access-list 1 deny   10.1.1.0 0.0.0.255          ! ...but deny the rest of its /24
access-list 1 permit 10.0.0.0 0.255.255.255      ! allow the rest of 10.0.0.0/8
! (implicit) deny any                             ! everything else is dropped
```
**Order matters:** Swap lines 1 and 2 and `10.1.1.1` is denied, because the `/24` deny matches first and the host permit is never reached. Rule of thumb: **most specific rules at the top.**

> **Transcript correction:** The lecture reads the last line as "permit 10.0.0.0 through 10.2.5.5…". The value is the **wildcard mask** `0.255.255.255` (the inverse of a subnet mask: 0 = must match, 1 = ignore). So the rule permits all of **10.0.0.0/8**, not a range ending in "10.255.255.255". Wildcard = 255.255.255.255 − subnet mask (/24 → `0.0.0.255`).

**Standard vs extended ACL (Cisco):**
| | Standard (1–99, 1300–1999) | Extended (100–199, 2000–2699) |
|-|---|---|
| Matches on | **Source IP only** | Source + destination IP, protocol, port |
| Placement | **Close to the destination** (so it doesn't block too much) | **Close to the source** (drop unwanted traffic early) |

#### URL Filtering
URL filtering inspects the requested URL and allows or blocks based on its parts:
```
https://www.infosecinstitute.com/resources/network-plus/
└─┬─┘   └┬┘ └──────┬───────┘└┬┘└──────────┬────────────┘
protocol host  registered    TLD    resource path
               domain
```
It can block by **domain/category** (malware, gambling, newly registered domains), **resource path**, or **keywords**. With HTTPS, the domain is visible via SNI and DNS, but the **path is encrypted** unless there's TLS inspection.

#### Content Filtering
Content filtering inspects the **actual payload**, not just the URL. It usually needs a **proxy**, often with **TLS inspection** (decrypt → inspect → re-encrypt, using an internal CA trusted by endpoints). Uses:
- **DLP (data loss prevention):** block outbound credit card numbers, SSNs, or documents labeled "Confidential"
- Block malicious file types and downloads
- Enforce acceptable-use policy

**URL filter vs content filter:** URL filtering decides by *where* you're going. Content filtering decides by *what* is inside the traffic.

---

### Zones

Modern firewalls assign interfaces and networks to **zones** and apply policy **between** zones. Enterprise traffic is grouped by source (on-premises, cloud services, hosted services, remote offices, Wi-Fi), and each group gets a **trust level**. Outside of personal browsing and email, enterprise traffic is predictable, so rules can be tight.

#### Trusted vs Untrusted
| Zone | Allowed |
|------|---------|
| **Trusted** (inside) | Outbound traffic, **return traffic for allowed sessions** (stateful), and inbound requests only to **authorized services** |
| **Untrusted** (internet) | Everything is **dropped or rejected** unless a rule explicitly allows it |

#### Drop vs Block/Reject
| Action | Response to sender | Effect |
|--------|-------------------|--------|
| **Drop** (deny silently) | **None**. The packet disappears | Sender learns nothing; port scans show **filtered**; scans slow down while waiting for timeouts |
| **Block / Reject** | **ICMP unreachable** (or TCP RST) | Sender knows it was refused; faster failure for legitimate misconfigured clients, but it **leaks information** |

**Best practice:** **drop** on the untrusted/internet edge, and **reject** internally (it makes troubleshooting easier).

Blocking can also key on source IP, unauthorized services, unauthorized devices, or content-filter verdicts.

#### Screened Subnet (formerly DMZ)
```
Internet ──▶ [ Outer firewall ] ──▶ SCREENED SUBNET (web, mail, reverse proxy, VPN)
                                          │ only specific, allowed flows
                                          ▼
                               [ Inner firewall ] ──▶ TRUSTED INTERNAL (DBs, AD, file servers)
```
- **Public-facing services** sit in the screened subnet, reachable from the internet.
- **Internal systems are not reachable from the internet**, but screened-subnet hosts may reach *specific* internal services (e.g., web → DB on 5432 only).
- If a public server is compromised, the attacker is still **behind another firewall**. This is defense in depth with multiple tiers of trust.
- It can be built with two firewalls (shown above) or one **three-legged firewall** (inside / outside / screened-subnet interfaces).

---

## Key Terms

| Term | Definition |
|------|-----------|
| Device hardening | Reducing attack surface by removing unused ports, services, and defaults |
| Attack surface | Total exposed systems, ports, services, and features |
| Default credentials | Factory accounts/passwords published in vendor docs |
| CIS Benchmark / STIG | Published hardening baselines |
| NAC | Controls which devices may join the network; often with posture checks |
| Posture assessment | Health check (patches, AV, encryption) before granting access |
| Persistent vs dissolvable agent | Installed NAC agent vs one downloaded temporarily at connect time |
| Port security | Switch feature limiting/pinning MACs per port (default max 1) |
| Sticky MAC | Dynamically learned MAC saved into the config |
| Violation modes | Protect / restrict / shutdown (default) |
| 802.1X | Port-based NAC; supplicant → authenticator → authentication server |
| Supplicant / authenticator / authentication server | Client / switch or AP / RADIUS server |
| EAPOL | EAP over LAN: the only traffic allowed on an unauthorized 802.1X port |
| EAP-TLS | Certificate-based EAP method (mutual certificates) |
| MAB | MAC Authentication Bypass for non-802.1X devices; spoofable |
| MAC filtering | MAC allow-list; easily bypassed by cloning |
| Key rotation | Regularly replacing keys to limit exposure |
| Key escrow | Stored copy of keys held for recovery |
| HSM / KMS | Hardware / managed service for secure key storage |
| ACL (network) | Ordered permit/deny rules; first match wins; implicit deny |
| Implicit deny | Invisible final "deny any" in every ACL |
| Wildcard mask | Inverse mask in Cisco ACLs (0 = match, 1 = ignore) |
| Standard vs extended ACL | Source-only vs source/destination/protocol/port |
| URL filtering | Allow/block by domain, path, keyword, or category |
| Content filtering | Inspect payload (via proxy/TLS inspection) |
| DLP | Data loss prevention: blocking sensitive data from leaving |
| TLS inspection | Proxy decrypts and re-encrypts traffic to inspect it |
| Zone | Group of interfaces/networks sharing a trust level |
| Trusted / untrusted zone | Internal / external networks |
| Drop vs reject | Silent discard vs discard with ICMP/RST notice |
| Screened subnet (DMZ) | Isolated tier for public-facing services between the internet and the internal network |

---

## Real-World Connections

**k3s lab:**
- **Hardening:** k3s ships with Traefik, ServiceLB, and local-path. Disable what you don't use (`--disable traefik`, `--disable servicelb`); that's hardening by removing unused services. Run `kube-bench` (the CIS Kubernetes Benchmark) against your node for a scored hardening report.
- **Default credentials:** Elasticsearch and Kibana used to ship with no authentication by default. Thousands of open clusters were wiped by ransom bots. Your ECK-managed Elasticsearch generates the `elastic` user password in a Secret. Rotate it, and never expose 9200 publicly.
- **ACLs and zones = NetworkPolicies + namespaces.** NetworkPolicies have the opposite default from Cisco ACLs: with **no** policy, everything is allowed. Once **any** policy selects a pod, it becomes implicit-deny for that direction. Same first-principles idea, opposite starting point.
- **Screened subnet:** Traefik ingress is your "screened subnet" tier. It's the only thing exposed, and it forwards only specific routes to internal Services.
- **Key management:** Kubernetes Secrets are only base64-encoded. Enable k3s `--secrets-encryption` (AES-CBC/GCM at rest in etcd/SQLite), and rotate the key with `k3s secrets-encrypt rotate-keys`.

**AWS/GCP:**
| Lesson concept | AWS | GCP |
|---|---|---|
| Stateless ordered ACL with implicit deny | **Network ACL** (numbered rules, lowest first, final `*` deny) | VPC firewall rules (priority-ordered) |
| Stateful zone policy | **Security Groups** (allow-only, return traffic automatic) | Firewall rules / tags |
| Screened subnet | **Public subnet** (ALB, bastion, NAT GW) + **private subnet** (app/DB) | Same pattern; IAP replaces bastion |
| URL filtering | **AWS Network Firewall** domain lists, Route 53 DNS Firewall | Cloud NGFW / Secure Web Proxy |
| Key management | **KMS** (auto-rotation yearly), **CloudHSM** | Cloud KMS / Cloud HSM |
| Hardening baseline | **Inspector**, Security Hub CIS checks | Security Command Center |

**Enterprise:** Cisco ISE / Aruba ClearPass do 802.1X + posture + MAB profiling, assigning VLANs dynamically by RADIUS attributes. Zscaler/Netskope deliver URL + content filtering + DLP as a cloud proxy (the SASE concept from [[L08 - Modern Networks]]).

---

## Troubleshooting Scenario

**Scenario:** After a network engineer adds an ACL to block one rogue host, `10.20.5.99`, users in `10.20.5.0/24` lose all access to the server subnet. The ACL applied inbound on the user-VLAN interface:
```
access-list 110 deny ip host 10.20.5.99 any
```

**Analysis:** The ACL has only a single deny. The **implicit deny any** at the end now blocks **every other host**. Clue: `show access-lists 110` shows only a few hits on the explicit deny while all traffic dies. The implicit deny has no counter, so the drops don't appear there.

**Fix:** Add an explicit permit after the deny:
```
access-list 110 deny   ip host 10.20.5.99 any
access-list 110 permit ip any any
```
**Better:** Instead of a reactive ACL, enable **802.1X / port security** so the rogue device can't get an IP on the VLAN in the first place, or **shut down its switch port** after finding it with `show mac address-table address <MAC>`.

**Second trap:** If the engineer had written the permit **first**, the deny would never be reached (first match wins).

---

## Hands-On Ideas

1. **Hardening audit:** On your Ubuntu ARM node: `sudo ss -tulpn` lists every listening service, so justify each one or disable it (`systemctl disable --now avahi-daemon`). Then run `sudo lynis audit system` for a hardening score.
2. **kube-bench:** `kubectl apply -f https://raw.githubusercontent.com/aquasecurity/kube-bench/main/job.yaml`, then read the logs. Each FAIL is a hardening step.
3. **ACL order lab (Packet Tracer/GNS3):** Build the lecture's standard ACL, then swap lines 1 and 2 and test `ping` from 10.1.1.1. Watch `show access-lists` hit counts.
4. **Drop vs reject with iptables + nmap:**
   ```bash
   sudo iptables -A INPUT -p tcp --dport 8080 -j DROP      # nmap shows "filtered"
   sudo iptables -A INPUT -p tcp --dport 8081 -j REJECT    # nmap shows "closed"
   nmap -p 8080,8081 <host>
   ```
5. **NetworkPolicy implicit deny:** Apply a default-deny ingress policy to the `elastic` namespace, confirm Kibana breaks, then add a policy allowing only Kibana → ES:9200.
6. **802.1X at home:** If your AP supports WPA2/WPA3-Enterprise, run FreeRADIUS in a pod and authenticate a laptop with EAP-TLS using certificates from your own `step-ca` (a full supplicant / authenticator / server chain).

---

## Quiz

1. A technician finds an unknown device plugged into a conference-room wall jack. What hardening step would have prevented it?
   - A) Administratively shutting down unused switch ports  B) Changing default passwords  C) URL filtering  D) Key escrow

2. In 802.1X, what is the switch's role?
   - A) Supplicant  B) Authentication server  C) Certificate authority  D) Authenticator

3. Before 802.1X authentication succeeds, what traffic can the port pass?
   - A) DHCP only  B) All traffic in the native VLAN  C) EAPOL only  D) RADIUS only

4. Printers that don't support 802.1X are allowed by MAC address. What is this called, and what is its weakness?
   - A) Sticky MAC; it fills the CAM table  B) MAB; MACs can be spoofed  C) EAP-TLS; certificates expire  D) DAI; it needs DHCP snooping

5. Port security is configured with `maximum 2` and default settings. A third MAC appears on the port. What happens?
   - A) The frame is dropped silently and the port stays up  B) The port goes err-disabled  C) The oldest MAC is replaced  D) Nothing; sticky learns it

6. An ACL reads: `deny 192.168.1.0 0.0.0.255` / `permit host 192.168.1.10`. Can 192.168.1.10 pass?
   - A) Yes, the permit is more specific  B) No, the first match (the deny) wins  C) Yes, permits override denies  D) Only if the ACL is extended

7. An ACL contains only `deny host 10.0.0.5`. What happens to traffic from 10.0.0.6?
   - A) Denied by implicit deny  B) Permitted  C) Logged only  D) Forwarded to the screened subnet

8. A company wants to stop employees from emailing spreadsheets that contain customer credit card numbers. Which control fits?
   - A) URL filtering  B) Content filtering / DLP  C) Port security  D) MAC filtering

9. Which firewall action gives an attacker running a port scan the least information?
   - A) Reject with ICMP unreachable  B) Reject with TCP RST  C) Drop silently  D) Permit and log

10. A company hosts its public web server so that a compromise doesn't give direct access to internal databases. Where should the web server go?
    - A) Trusted zone  B) Guest VLAN  C) Out-of-band management network  D) Screened subnet

11. Why is MAC filtering considered weak?
    - A) MACs are encrypted  B) MACs travel in cleartext and can be cloned  C) It requires RADIUS  D) It only works on wireless

12. A server's private key is stolen. What is the most important immediate action?
    - A) Rotate the symmetric session keys only  B) Revoke the certificate and reissue a new key pair  C) Enable key escrow  D) Change the URL filter

13. A user loses the BitLocker key for an encrypted laptop. Which practice would have allowed recovery?
    - A) Key rotation  B) Sticky MAC  C) Key escrow  D) Certificate pinning

**Answers:** 1-A, 2-D, 3-C, 4-B, 5-B, 6-B, 7-A, 8-B, 9-C, 10-D, 11-B, 12-B, 13-C

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[L02 - Networking Appliances & Applications]] — Firewalls, proxies, NGFW appliances
- [[L08 - Modern Networks]] — Zero Trust, SASE (cloud-delivered filtering)
- [[L10 - Switching Technologies]] — VLANs, switch port configuration
- [[L11 - Wireless Devices and Technologies]] — WPA2/WPA3-Enterprise uses 802.1X
- [[L17 - Network Access and Management]] — Jump box, out-of-band management (hardening management access)
- [[L18 - Basic Network Security]] — PKI, RADIUS, segmentation, least privilege
- [[L19 - Network Attacks]] — The attacks these controls stop (MAC flooding, rogue devices, VLAN hopping)
- [[certs/network-plus]] — Domain 4.3 objective
