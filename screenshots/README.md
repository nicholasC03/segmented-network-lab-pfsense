# Screenshots — evidence checklist

Drop captures here as you complete each phase. Hiring managers skim, so keep
filenames predictable and name them for what they prove.

| Filename | What it shows | Capture when |
|---|---|---|
| `pfsense-dashboard.png` | pfSense dashboard after setup wizard | End of Phase 1 |
| `interface-assignments.png` | Interfaces > Assignments (WAN/LAN/SERVERS/GUEST) | Phase 2 |
| `dhcp-leases.png` | Status > DHCP Leases showing client leases per segment | Phase 2 |
| `firewall-rules-lan.png` | Firewall > Rules > LAN with explicit allow rules | Phase 3 |
| `nat-portforward-config.png` | Firewall > NAT > Port Forward (WAN 8080 → server:80) | Phase 3 |
| `guest-blocked-firewall-log.png` | Firewall log entry for the blocked GUEST→SERVERS attempt | Phase 4 |
| `allowed-traffic-wireshark.png` | Packet capture: allowed vs. blocked traffic | Phase 4 |
| `before-no-rules-blocked.png` | The "before" — blocked cross-segment traffic with zero custom rules | Phase 3 (before adding rules) |

> Tip: take the **"before"** capture (cross-segment traffic failing with no rules
> yet) early. You need the contrast to prove a rule actually changed behavior.
