# Lessons Learned

Running notes on concepts and gotchas encountered during the build. These double
as interview talking points.

## Phase 1 — Foundation

**VirtualBox network modes are the foundation, and the #1 source of beginner
errors.**
- *NAT* — VM gets internet via a private per-VM NAT; VMs can't easily reach each
  other; host can't reach the VM. Used for pfSense's **WAN**.
- *Internal Network* — VMs sharing the same network name talk only to each other,
  fully sealed off from the host and the internet. Used for **all lab segments**.
  This isolation is the whole point of the lab.
- *Bridged* — VM joins the real physical LAN and gets an IP from the home router.
  Avoided here so the lab never touches the real network.
- An Internal Network isn't "created" in a dialog — it exists the moment its name
  is typed into a VM adapter. Any VM using the same name is on that segment.

**Adapter order = interface order.** VirtualBox adapters map to pfSense as
`em0..em3` in listed order, so Adapter 1 = em0 = WAN, etc. Getting the order
right up front avoids assigning WAN to the wrong segment.

**A LAN/gateway interface must be static, never DHCP.** The LAN interface is the
gateway and DHCP server for its segment, so it needs a fixed, known address
(192.168.10.1). Setting a gateway interface to pull its own address from DHCP
breaks NAT. (WAN, which points upstream, correctly uses DHCP.)

**"No carrier" on an Internal Network adapter is normal** when nothing else is on
that segment yet. The link comes up once a second VM joins the network.

**The host can't reach the LAN GUI** (`192.168.10.1`) because the LAN is an
isolated Internal Network — by design. Access requires a VM *on* that segment.
This is the isolation working, not a bug.

**Self-signed certificate warning** on first GUI access is expected — pfSense
ships a self-signed TLS cert. Safe to proceed on an isolated lab LAN.

**Installer note (pfSense CE 2.8.x).** The download now routes through the Netgate
Installer / store rather than a plain ISO link, and the installer leans toward
the paid Plus edition. In a VM (not Plus-eligible) it lands on CE; if it stalls,
restarting the installer or enabling CE repos resolves it.

**"Block private networks" on WAN, behind NAT.** Because the WAN sits behind
VirtualBox's NAT (a private 10.0.2.x range), the wizard's "Block RFC1918 Private
Networks" option is unchecked — otherwise pfSense filters its own upstream and it
would also block the Phase 3 NAT-port-forward test.

## Security concept reinforced

Segmentation + default-deny = least privilege at the network layer. Nothing
crosses a segment boundary unless an explicit rule allows it; the absence of a
rule is itself a deny. The evidence that matters is the *blocked* attempt in the
logs, not just the working allow.

<!-- Add Phase 2+ notes below as you go. -->
