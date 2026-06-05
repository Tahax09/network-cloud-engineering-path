---
lab: k3s Observability Stack
status: Ongoing
platform: ARM64 Ubuntu 22.04
k3s_version: current
tags: [k3s, kubernetes, kubeshark, elasticsearch, kibana, observability]
---

# Lab: k3s Observability Stack

## Goal
Run a production-like observability stack on a local k3s cluster. Use it as a live networking lab — every OSI layer is active and inspectable.

## Stack
- **k3s** — lightweight Kubernetes distribution
- **Kubeshark** — real-time network traffic analyzer (operates in promiscuous mode)
- **Elasticsearch** — log/metrics storage (IaaS-equivalent storage backend)
- **Kibana** — visualization layer

## OSI Layer Mapping in This Cluster

| OSI Layer | Component | What It Does |
|-----------|-----------|-------------|
| L1–L2 | Network interface (eth0/veth) | Physical/virtual NIC; frames |
| L2 | CNI (Flannel/Calico) | MAC-based forwarding within a node |
| L3 | CNI routing + `ip route` | Pod-to-pod routing across nodes |
| L3 | kube-proxy (iptables NAT) | Service IP → Pod IP translation |
| L4 | kube-proxy port rules | Port-based service routing |
| L4–L7 | Kubeshark | Captures and reassembles TCP sessions, HTTP, DNS |
| L7 | NGINX Ingress Controller | Reverse proxy — routes by hostname/path |
| L7 | Elasticsearch API (port 9200) | HTTP-based data storage |
| L7 | Kibana (port 5601) | HTTP-based visualization |

## Network Device Equivalents

| k3s Component | Network Device Equivalent | Lesson |
|--------------|--------------------------|--------|
| iptables rules | Stateful firewall | [[L02]] |
| kube-proxy | Layer 4 load balancer | [[L02]] |
| NGINX Ingress | Reverse proxy | [[L02]] |
| CNI plugin | L3 router (pod network) | [[L01]], [[L02]] |
| Kubeshark | IDS / packet analyzer (promiscuous mode) | [[L01]] |
| kube-proxy NAT | NAT Gateway | [[L03]] |
| Pod CIDR | VPC CIDR / private subnet | [[L03]] |

## Key Commands

```bash
# View cluster networking
kubectl get services -A           # all services and their IPs
kubectl get pods -A -o wide       # pod IPs and which node they're on
kubectl get ingress -A            # Ingress rules (reverse proxy config)

# Layer 3 — routing
ip route show                     # host routing table including pod CIDR
traceroute <pod-ip>               # trace path to a pod

# Layer 2 — ARP
arp -n                            # MAC-to-IP on local segment
ip neigh                          # same, more detail

# Layer 4 — iptables (firewall + NAT + LB rules)
sudo iptables -L -n -v            # firewall rules
sudo iptables -t nat -L -n -v     # NAT rules (service IP translation)

# Kubeshark — promiscuous capture
# Access Kubeshark UI then filter by namespace, pod, or protocol
# Maps to OSI: you see L2 MACs, L3 IPs, L4 ports, L7 HTTP/DNS payloads

# Inspect CNI
kubectl get pods -n kube-system   # look for flannel/calico/weave
ip addr show                      # virtual NICs created by CNI

# curl test end-to-end (tests L4 port open, L7 response)
curl -v http://<service-ip>:<port>
curl -v https://<ingress-hostname>
```

## Concepts Demonstrated

### Promiscuous Mode (L01)
Kubeshark sets the pod network NIC to promiscuous mode, accepting all frames regardless of destination MAC. This is exactly how Wireshark works on a physical NIC.

### MAC Address Hop-by-Hop (L01)
When pod A on node 1 sends to pod B on node 2:
- L2 frame: pod A MAC → node 1 NIC MAC (not pod B's MAC)
- Packet leaves node 1, arrives at node 2
- New frame: node 2 NIC MAC → pod B's veth MAC
Layer 2 is always local to each segment.

### Load Balancing via kube-proxy (L02)
`kubectl get service` shows a ClusterIP. That IP doesn't exist on any physical interface — kube-proxy uses iptables DNAT rules to redirect it to an actual pod IP. This is Layer 4 load balancing in software.

### Reverse Proxy via Ingress (L02)
NGINX Ingress Controller receives all external HTTP/HTTPS traffic, reads the `Host` header (L7), and routes to the correct backend service. This is a reverse proxy — it hides backend pod IPs from external clients.

### Private Cloud / VPC Equivalent (L03)
Your pod network (e.g., 10.42.0.0/16) is a private RFC 1918 range. Pods can't be directly reached from the internet — identical to a private VPC subnet. kube-proxy NAT = AWS NAT Gateway behavior.

## Observations Log
*(append observations here as you work in the cluster)*

| Date | Observation | Related Concept |
|------|-------------|-----------------|
| — | — | — |

## Next Lab Exercises
- [ ] Capture a pod-to-pod HTTP request in Kubeshark and identify all 7 OSI layers in the packet
- [ ] Use `iptables -t nat -L` to trace how a Service ClusterIP is NAT'd to a pod IP
- [ ] Deploy a second service behind Ingress and verify reverse proxy routing by hostname
- [ ] Use `traceroute` from one pod to another on a different node — count L3 hops
- [ ] Run `curl -v` against Elasticsearch and identify TLS (L6), TCP (L4), HTTP (L7) layers
