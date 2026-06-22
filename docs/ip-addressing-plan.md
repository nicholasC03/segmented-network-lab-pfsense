# IP Addressing Plan

## Segments

| Segment | VLAN | Subnet | pfSense gateway | DHCP range | VirtualBox Internal Network |
|---|---|---|---|---|---|
| LAN | 10 | 192.168.10.0/24 | 192.168.10.1 | 192.168.10.100 – 192.168.10.200 | `LAN-VLAN10` |
| SERVERS | 20 | 192.168.20.0/24 | 192.168.20.1 | 192.168.20.100 – 192.168.20.200 | `SERVER-VLAN20` |
| GUEST | 30 | 192.168.30.0/24 | 192.168.30.1 | 192.168.30.100 – 192.168.30.200 | `GUEST-VLAN30` |
| WAN | — | DHCP (VirtualBox NAT, 10.0.2.0/24) | — | VirtualBox NAT |

> SERVERS and GUEST gateways/DHCP are the planned values; they are added as
> OPT interfaces in the pfSense web GUI during Phase 2.

## pfSense interface mapping

VirtualBox presents adapters to pfSense as `em0..em3` in the order they appear
in the VM's Network settings. Recording the mapping prevents mis-wiring during
interface assignment.

| VirtualBox adapter | pfSense interface | Role | Mode / network |
|---|---|---|---|
| Adapter 1 | em0 | WAN | NAT |
| Adapter 2 | em1 | LAN | Internal Network `LAN-VLAN10` |
| Adapter 3 | em2 | SERVERS (OPT1) | Internal Network `SERVER-VLAN20` |
| Adapter 4 | em3 | GUEST (OPT2) | Internal Network `GUEST-VLAN30` |

## Addressing rationale

- `.1` of each subnet is reserved for the pfSense gateway.
- DHCP pools start at `.100`, leaving `.2`–`.99` free for any static assignments
  (e.g. the SERVERS web host is a good candidate for a static IP).
- A `/24` mask (255.255.255.0) gives 254 usable hosts per segment — far more than
  this lab needs, but it is the conventional default and keeps the math simple.
- The LAN deliberately avoids the common `192.168.1.0/24` to prevent collisions
  with typical home-router ranges.
