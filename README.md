# Segmented Network Lab — pfSense Firewall

A home-lab build of a small "office network" with three isolated segments behind
a pfSense firewall, demonstrating VLAN-style segmentation, least-privilege
firewall policy, NAT port forwarding, and traffic verification with packet
capture and logs.

> **Status:** Phase 1 complete (foundation + segmentation built). Phases 3–5
> (firewall rules, verification, final docs) in progress. See the checklist at
> the bottom.

---

## Problem Statement

Flat networks let any compromised device reach everything else. A single phished
laptop on a flat network can pivot to file servers, databases, and management
interfaces. This project simulates a small office network with segmentation and a
default-deny firewall policy to demonstrate least-privilege network design and
prove, with evidence, that cross-segment traffic is actually blocked.

## Tools Used

- Oracle VirtualBox (hypervisor, Linux Mint host)
- pfSense CE 2.8.1-RELEASE (firewall / router)
- Lubuntu 24.04 LTS (LAN client)
- Windows 11 Evaluation (LAN client — planned)
- Ubuntu Server 24.04 LTS (SERVERS segment web/SSH host — planned)
- Kali / Ubuntu (GUEST segment device — planned)
- Wireshark + pfSense built-in packet capture (verification — planned)
- draw.io (topology diagram)

## Architecture

Three isolated segments, each its own broadcast domain, all routed and filtered
by a single pfSense instance. The only path between segments — or out to the
internet — is through the firewall.

![Network topology](docs/topology-diagram.png)

| Segment | VLAN | Subnet | Gateway (pfSense) | VirtualBox network |
|---|---|---|---|---|
| LAN | 10 | 192.168.10.0/24 | 192.168.10.1 | Internal Network `LAN-VLAN10` |
| SERVERS | 20 | 192.168.20.0/24 | 192.168.20.1 | Internal Network `SERVER-VLAN20` |
| GUEST | 30 | 192.168.30.0/24 | 192.168.30.1 | Internal Network `GUEST-VLAN30` |
| WAN | — | DHCP (10.0.2.0/24) | — | VirtualBox NAT |

Full addressing detail: [docs/ip-addressing-plan.md](docs/ip-addressing-plan.md).

### Design note: separate adapters vs. 802.1Q trunk

This build uses **four separate VirtualBox adapters** on pfSense (WAN + one
Internal Network per segment) instead of a single trunk with 802.1Q VLAN tags.
The result is identical segmentation and firewall practice with less initial
complexity. True VLAN trunking is listed as a planned improvement — keeping the
trade-off explicit is intentional.

## What I Built

- Installed and configured pfSense CE from a blank VM (interface assignment,
  static LAN gateway, DHCP scopes).
- Three isolated network segments (LAN, SERVERS, GUEST), each on its own subnet
  with its own DHCP scope.
- *(Planned)* Default-deny policy between segments with explicit allow rules.
- *(Planned)* NAT port forwarding for a public-facing test web server.
- *(Planned)* Logging and packet-capture verification of blocked cross-segment
  traffic.

## Firewall Rule Table

Planned policy (see [docs/firewall-rule-table.md](docs/firewall-rule-table.md)
for the live version as rules are added):

| Source | Destination | Port | Action | Reasoning |
|---|---|---|---|---|
| LAN | SERVERS | 80, 443, 22 | Allow | Staff need web/SSH access to internal apps |
| GUEST | SERVERS | any | Deny | Guests should never reach internal servers |
| GUEST | LAN | any | Deny | Guests isolated from the corporate segment |
| SERVERS | WAN | any | Deny | Prevent data exfiltration / lateral pivot |
| GUEST | WAN | any | Allow | Guests get internet only |

## Verification

*(Planned — evidence will be captured into `/screenshots`)*

- Blocked cross-segment attempt shown in the pfSense firewall log.
- Wireshark / pfSense packet capture contrasting allowed vs. blocked traffic.
- NAT port-forward reaching the internal web server.

## What I'd Improve Next

- Add Suricata IDS/IPS on pfSense (see Project 3 in the portfolio roadmap).
- Add a site-to-site VPN segment.
- Migrate from separate Internal Network adapters to true 802.1Q VLAN trunking.

## Progress Checklist

- [x] Phase 1 — Foundation: pfSense installed, WAN/LAN assigned, LAN set to
      192.168.10.1/24, DHCP enabled, web GUI reached, setup wizard completed
- [ ] Phase 2 — Segments: add SERVERS + GUEST interfaces, DHCP scopes, install
      segment VMs, confirm leases
- [ ] Phase 3 — Firewall rules: default-deny + explicit allows, NAT port forward,
      logging on deny rules
- [ ] Phase 4 — Prove it: serve test page, confirm allowed/blocked traffic,
      capture logs + packet capture
- [ ] Phase 5 — Document: topology diagram, rule table, lessons learned
