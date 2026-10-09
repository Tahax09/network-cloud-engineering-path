---
id: L13
aliases: [L13]
title: Organizational Processes and Procedures
course: CompTIA Network+
cert_track: network-plus
objective: "3.1 — Network Operations: Organizational Processes and Procedures"
date: 2026-06-16
status: Complete
quiz_attempted: No
weak_areas: []
tags: [documentation, change-management, configuration-management, lifecycle, sla, asset-inventory, network-diagrams]
---

# L13 — Organizational Processes and Procedures

## Summary

Domain 3 (Network Operations) opens with the operational backbone that keeps infrastructure manageable: documentation, lifecycle management, change management, and configuration management. These are the processes that prevent "mystery box" networks where nobody knows what's running, what version it's on, or who changed what last Tuesday. For the exam: know the document types, the difference between end of life vs end of support, and what change/config management each solve.

---

## Core Concepts

### Documentation Types

| Document | What It Shows |
|----------|--------------|
| Physical diagram | Physical cable connections, device locations, rack positions — doesn't imply logical adjacency |
| Logical diagram | Layer 2 (VLANs, MACs) or Layer 3 (IP subnets, routing) topology |
| Rack diagram | U-by-U device placement, power planning, cabling details |
| Cable map | Physical cable runs, patch panel port assignments, labeling |
| Asset inventory | Hardware (model, serial, location), software (version, patch level), licenses, support contracts |
| IP address management (IPAM) | Private ranges in use, public block assignments, firewall/NAT interconnections |
| Wireless heat map / site survey | AP placement overlay on floor plan with signal strength, SNR, expected throughput |
| SLA (Service Level Agreement) | Contractual performance expectations between client and vendor (or IT and business units) |

**Key distinction:** Physical diagram ≠ logical diagram. Two servers physically cabled to the same switch may be on different VLANs and logically isolated.

### Service Level Agreements (SLAs)

- Define performance expectations: uptime, bandwidth, response times, resolution times
- Tiered support levels (e.g., 2-hour / 4-hour / 8-hour resolution)
- Can be external (vendor contract) or internal (IT dept ↔ business unit)
- Equipment replacement SLAs: replacement hardware delivered within 24/48/72 hours of ticket submission

### Lifecycle Management

```
Planning → Installation/Config → Maintenance/Operations → Decommissioning
```

| Phase | Key Activities |
|-------|---------------|
| Planning | Vendor selection, sizing, budgeting |
| Installation | Initial configuration, baseline setup |
| Maintenance | Patch updates, firmware updates, OS updates, monitoring, vulnerability scanning |
| Decommissioning | Config backup, data wiping, secure disposal |

**End of Life vs End of Support — critical exam distinction:**

| Term | Meaning | Impact |
|------|---------|--------|
| End of Life (EoL) | Product no longer sold or actively developed | Decided internally; may still have support |
| End of Support (EoS) | Vendor stops issuing patches and technical support | **Security risk** — new CVEs are never patched; system becomes legacy |

> Windows 8.1 → EoS Jan 2023. Windows 10 → EoS Oct 2025. After EoS, any new vulnerability found is permanently unpatched.

**Legacy equipment** = hardware or software past EoS that can no longer be updated or maintained.

### Change Management

**Problem it solves:** Uncontrolled changes cause outages. "Someone changed something" is not a diagnosis — it's a horror story.

**Process:**
1. Change request submitted via ticket/service management system (ServiceNow, Jira, etc.)
2. Change reviewed, approved by appropriate stakeholders
3. Change implemented — documented in the ticket
4. Ticket closed with outcome documented (success, rollback, follow-up needed)

Troubleshooting tickets should also document **how the problem was solved** — this creates institutional knowledge for future incidents.

### Configuration Management

**Problem it solves:** After an outage, you need to restore the device to its exact running state — not guess at a config from memory.

**Key concepts:**
- **Baseline configuration:** Reviewed and approved initial config for a device/system type
- **Configuration items (CIs):** Individual tracked components (a switch config, a firewall ruleset, an OS image)
- **Change control for configs:** Any config change (planned or ad hoc during troubleshooting) must be documented and stored
- **Config backup:** Production configs stored so devices can be restored without manual reconstruction

Change management and configuration management work together: the change ticket documents *what* changed; the config backup captures *the result*.

---

## Key Terms

| Term | Definition |
|------|-----------|
| Physical diagram | Shows physical cable connections and device locations |
| Logical diagram | Shows L2/L3 topology — VLANs, IP subnets, routing |
| Rack diagram | U-slot-by-slot device placement plan |
| IPAM | IP Address Management — tracks all private/public IP assignments |
| SLA | Service Level Agreement — contractual uptime/response/resolution expectations |
| Wireless heat map | AP coverage overlay on floor plan; shows signal strength and gaps |
| End of Life | Product no longer sold or developed |
| End of Support | Vendor no longer issues patches — security risk begins here |
| Legacy | Equipment/software past EoS; cannot be safely updated |
| Baseline config | Approved standard configuration for a device type |
| Configuration item (CI) | Individual tracked unit within a configuration management system |
| Change management | Formal process for requesting, approving, implementing, and documenting changes |
| Configuration management | Process for maintaining current, backed-up, version-controlled device configs |

---

## Real-World Connections

**k3s lab:** Your Kubernetes cluster is a live configuration management exercise. `kubectl apply -f` is a config change — and if you're not tracking those manifests in Git, you're doing config management wrong. GitOps (ArgoCD, Flux) is the industry answer to this exact problem.

**Enterprise ops:** Change Advisory Boards (CAB) meet weekly to review and approve change requests before they're implemented in production windows (usually nights/weekends). Unauthorized changes are a root cause of major outages — and an audit finding.

**Lifecycle relevance:** Running a node on Ubuntu 20.04 LTS (EoS Apr 2025) in your k3s cluster is exactly the legacy equipment problem — CVEs pile up with no OS-level patches available. Ubuntu 22.04 LTS (EoS Apr 2027) is your current safe zone.

**Cloud analogy:** AWS Config, Azure Policy, and GCP Security Command Center all implement configuration management at cloud scale — continuously auditing resource configs against baselines and flagging drift.

---

## Troubleshooting Scenario

**Symptom:** A core switch fails on Friday evening. The on-call engineer restores it from a backup config — but the restored config is 3 months old. Six VLANs that were added since then are missing, and half the floor loses connectivity until Monday.

**Root cause:** Configuration changes were made directly on the device (ad hoc during a troubleshooting session) but never saved to the configuration management system. The backup is stale.

**Fix:**
1. Implement change management: every config change requires a ticket
2. Automate config backups (RANCID, Oxidized, Ansible) — pull device configs nightly to version control
3. After any change, immediately push the updated config to the config management system
4. Test restore procedures quarterly — "we have backups" means nothing until you've tested a restore

---

## Hands-On Ideas

1. Set up Oxidized or RANCID on a Linux VM — automatically pull and version-control your switch/router configs
2. Create an IP address spreadsheet (or use phpIPAM) for your homelab: document every subnet, VLAN, and gateway
3. Draw a logical diagram of your k3s cluster: node IPs, pod CIDRs, service CIDRs, ingress, external traffic flow
4. Check EoS dates for every OS in your lab (`cat /etc/os-release`) — flag anything past or near EoS
5. Simulate change management: write a one-line "change request" before modifying any config in your lab for one week; see what you learn about your own change rate

---

## Quiz

1. A switch in the IDF needs its firmware updated. The engineer makes the change but does not document it. Three months later the switch is replaced and restored from backup — the firmware is now outdated. Which process failure caused this?
   - A) Lack of physical documentation
   - B) Configuration management — config changes were not recorded
   - C) Poor SLA negotiation
   - D) Wireless heat map not updated

2. What is the critical difference between End of Life and End of Support?
   - A) End of Life means the product is no longer sold; End of Support means no more patches — new CVEs go unpatched
   - B) They mean the same thing
   - C) End of Support only applies to hardware, not software
   - D) End of Life removes vendor support; End of Support removes software licenses

3. A network diagram shows two servers connected to the same switch. Can you conclude they are on the same network?
   - A) Yes — if they share a switch, they share a broadcast domain
   - B) No — they may be on different VLANs and logically isolated
   - C) Yes — physical connection always means logical adjacency
   - D) Only if the switch is unmanaged

4. Which document would you reference to understand your vendor's guaranteed response time when a core router fails?
   - A) Physical network diagram
   - B) Asset inventory
   - C) Service Level Agreement (SLA)
   - D) Wireless heat map

5. A Windows 10 machine in the network is identified as running past its End of Support date. What is the primary concern?
   - A) The machine will stop working immediately
   - B) New vulnerabilities discovered after that date will never receive patches
   - C) The machine's IP address will be reclaimed by IPAM
   - D) The SLA with Microsoft is automatically cancelled

6. Which tool category provides automated, ongoing backup of network device configurations into version control?
   - A) Wireless site survey software
   - B) IPAM tools
   - C) Configuration management tools (e.g., Oxidized, Ansible, RANCID)
   - D) Heat map generators

**Answers:** 1-B, 2-A, 3-B, 4-C, 5-B, 6-C

---

## Weak Areas to Revisit

*(none flagged yet)*
