# Firewall Rule Table

The core deliverable. pfSense evaluates rules **top to bottom, first match wins**,
and each interface has an **implicit deny-all** at the bottom — so an interface
with no pass rules blocks everything by default. That implicit deny is what makes
this "default-deny."

Update the Status column as each rule is created in `Firewall > Rules`.

| # | Interface (Source) | Destination | Port/Proto | Action | Log | Reasoning | Status |
|---|---|---|---|---|---|---|---|
| 1 | LAN | SERVERS net | 80, 443, 22 (TCP) | Pass | — | Staff need web/SSH access to internal apps | ☐ |
| 2 | LAN | WAN / internet | any | Pass | — | Staff need internet | ☐ |
| 3 | SERVERS | WAN / internet | any | Block | ✔ | Servers shouldn't "phone home"; limits exfil / pivot | ☐ |
| 4 | SERVERS | LAN, GUEST nets | any | Block | ✔ | Servers don't initiate into client segments | ☐ |
| 5 | GUEST | WAN / internet | any | Pass | — | Guests get internet only | ☐ |
| 6 | GUEST | LAN, SERVERS nets | any | Block | ✔ | Guests fully isolated from internal segments | ☐ |

## Notes

- **Remove the default `LAN net -> any` allow rule.** pfSense ships with a
  permissive "anti-lockout"-adjacent allow rule on LAN; leaving it in place
  defeats segmentation. Replace it with the explicit rules above. (Keep the
  WebGUI anti-lockout rule itself so you don't lock yourself out.)
- **Order matters.** Put specific allow rules above broad blocks. A broad block
  placed above a needed allow will shadow it.
- **Logging.** Enable logging on the Block rules (3, 4, 6) so blocked attempts
  appear in `Status > System Logs > Firewall` — that log is the evidence that
  the policy is doing something.
- **"Block" vs "Reject."** Block silently drops (better for WAN-facing / stealth);
  Reject sends back a refusal (snappier failures during internal testing). This
  lab uses Block.
