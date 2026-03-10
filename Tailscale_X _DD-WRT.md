#Technical Deployment Report
##Patching a Netgear router with *DD-WRT* AND Deploying Tailscale on it.

###1. Executive Overview

This document describes the complete deployment and troubleshooting process for converting a consumer router running DD-WRT into a Tailscale gateway router.

The router transparently forwards traffic from LAN clients through a remote Tailscale exit node, allowing devices connected to the router to use the exit node’s public IP.

The deployment required solving several non-trivial networking problems including:

Kernel compatibility with userspace networking

NAT interaction with overlay networks

Linux bridge behavior in router firmware

Packet forwarding through a virtual interface

Persistence on embedded systems

Startup automation via NVRAM hooks

This document explains not only the steps taken but why each step works and the networking concepts involved.

###2. System Architecture
####2.1 Physical Architecture

Router:

Netgear R6700v3
Broadcom BCM4708 / BCM4709
RAM: ~256MB
Flash: ~128MB

Firmware:

DD-WRT v3.0 (2026 build)
Linux kernel 4.4.302
BusyBox environment

Network topology:

```
LAN clients
   │
   │ WiFi / Ethernet
   │
Router (DD-WRT)
   │
   │ Tailscale tunnel
   │
Exit Node (Windows machine)
   │
Internet
```

###3. Network Topology
Router Interfaces
br0       LAN bridge
vlan2     WAN interface
tailscale0 Tailscale virtual interface
LAN
192.168.2.0/24
Router: 192.168.2.1
WAN
192.168.0.63
Gateway: 192.168.0.254
Tailscale network
100.x.x.x

Tailscale uses Carrier-Grade NAT space (100.64.0.0/10) internally.

4. Goal of the Deployment

The goal is to allow any device connected to the router to:

Browse the internet through a remote Tailscale exit node.

This means:

LAN device → router → tailscale tunnel → exit node → internet

The LAN device itself does not run Tailscale.

5. Key Networking Concepts Used

The deployment touches several advanced networking topics.

5.1 Overlay Networks

Tailscale is an overlay network.

An overlay network runs on top of an existing network.

Example:

Internet (underlay)
        │
        ▼
Tailscale network (overlay)

Overlay networks create virtual IP networks.

Example:

Device A: 100.70.224.31
Device B: 100.64.186.77

These IPs exist only inside Tailscale.

5.2 Exit Nodes

An exit node acts like a VPN gateway.

Traffic path:

client → tailscale tunnel → exit node → internet

Public IP used:

exit node IP
5.3 Userspace Networking

Normally WireGuard uses kernel networking.

However DD-WRT's kernel may lack required modules.

Tailscale supports:

userspace networking

Meaning:

WireGuard engine runs inside userspace
instead of kernel

Interface created:

tailscale0
5.4 Linux Bridge

Routers often use bridges.

Bridge example:

br0
 ├─ eth1 (WiFi)
 ├─ eth2 (WiFi)
 └─ vlan1 (LAN ports)

A bridge works like a virtual switch.

All devices connected appear in the same LAN.

5.5 NAT in This Deployment

Without NAT the exit node would receive packets from:

192.168.2.x

Those addresses are private.

Therefore the router performs:

MASQUERADE → source becomes router’s tailscale IP
5.6 Packet Flow (Important Concept)

Actual packet flow:

Client: 192.168.2.10
Destination: google.com

1. client → router
2. router NAT
3. router → tailscale0
4. encrypted via WireGuard
5. exit node decrypts
6. exit node sends to internet
6. Linux Routing Concepts

Routing table example:

default via 192.168.0.254 dev vlan2
192.168.2.0/24 dev br0

Meaning:

local LAN traffic → br0
internet traffic → WAN gateway

Tailscale modifies routing using policy routing.

7. Filesystem Constraints on Routers

DD-WRT uses several filesystems:

/tmp   RAM filesystem (temporary)
/jffs  flash storage (persistent)
/nvram configuration storage

Important rule:

/tmp resets after reboot

This caused early failures.

8. Persistence Strategy

Persistent components stored in:

/jffs/tailscale/

Files:

tailscale
tailscaled
tailscale.state
9. Startup Automation

DD-WRT allows startup hooks stored in NVRAM.

Examples:

nvram set rc_startup="script"
nvram set rc_firewall="script"

These run during boot.

10. Perfect Deployment Guide (No Problems Scenario)

This section describes the ideal deployment path.

Step 1 — Enable JFFS

In DD-WRT:

Administration → Management → JFFS2
Enable
Step 2 — Create directories
mkdir -p /jffs/tailscale
Step 3 — Download Tailscale

Place binaries:

/jffs/tailscale/tailscale
/jffs/tailscale/tailscaled

Make executable:

chmod +x /jffs/tailscale/*
Step 4 — Start daemon
/jffs/tailscale/tailscaled --state=/jffs/tailscale/tailscale.state &
Step 5 — Connect to Tailscale
/jffs/tailscale/tailscale up --exit-node=100.64.186.77 --exit-node-allow-lan-access

Authenticate through browser.

Step 6 — Enable routing
echo 1 > /proc/sys/net/ipv4/ip_forward
Step 7 — Configure NAT
iptables -t nat -A POSTROUTING -o tailscale0 -j MASQUERADE
Step 8 — Allow forwarding
iptables -A FORWARD -i br0 -o tailscale0 -j ACCEPT
iptables -A FORWARD -i tailscale0 -o br0 -m state --state RELATED,ESTABLISHED -j ACCEPT
Step 9 — Save firewall script
/jffs/tailscale-fw.sh
Step 10 — Save startup script
/jffs/tailscale-start.sh
Step 11 — Register scripts in NVRAM
nvram set rc_startup="sh /jffs/tailscale-start.sh &"
nvram set rc_firewall="sh /jffs/tailscale-fw.sh"
nvram commit
Step 12 — Reboot

After reboot verify:

tailscale status
11. Verification

Check routing:

ip route

Check NAT:

iptables -t nat -L

Check tailscale peers:

tailscale status
12. Packet Path Verification

From LAN device:

curl ifconfig.me

Expected:

exit node public IP
13. Key Concepts Learned

During the deployment the following advanced networking topics were encountered:

Linux bridges

Overlay networks

WireGuard userspace mode

NAT behavior

Linux routing tables

Embedded filesystem persistence

Boot-time scripting in router firmware

14. Troubleshooting Methodology Used During Deployment

A critical skill for network engineers is structured troubleshooting.

During this project, issues were not solved randomly. Instead, the following methodology was applied.

Layered Troubleshooting Model

Problems were diagnosed using a layered model similar to the OSI approach:

Layer 1  Hardware
Layer 2  Link / Bridge
Layer 3  Routing
Layer 4  NAT / Firewall
Layer 5  Overlay Network
Layer 6  Application

When connectivity failed, each layer was validated sequentially.

Example debugging path:

LAN device → bridge → router forwarding → NAT → tailscale0 → exit node
15. Initial Problem: TUN Interface Crash
Observed Error

When starting Tailscale:

panic: runtime error: invalid memory address
wireguard-go tun_linux.go
Root Cause

The router kernel was:

Linux 4.4.302

Tailscale versions after 1.50 assume more modern kernel capabilities.

The crash occurred in:

wireguard-go TUN driver initialization
What Is a TUN Interface?

A TUN interface is a virtual network interface operating at Layer 3.

Example:

tailscale0

Packets sent to this interface are:

captured by userspace
processed by WireGuard
encrypted
sent to peers
Why the Crash Happened

DD-WRT kernels often:

lack newer TUN ioctl implementations

use older netlink APIs

omit certain kernel modules

The Tailscale engine expected features not present.

Diagnostic Commands

Check TUN device:

ls -l /dev/net/tun

Check module:

lsmod | grep tun
Final Resolution

The deployment switched to:

userspace networking

Instead of kernel WireGuard.

Meaning:

tailscaled handles networking internally
16. Second Problem: SOCKS Proxy Failure

Initially a workaround was attempted:

tailscale SOCKS5 proxy

LAN clients would forward traffic through:

SOCKS5 → router → tailscale
Error Observed

Logs showed:

socks5: client connection failed: incompatible SOCKS version
Root Cause

Android WiFi proxy settings only support:

HTTP proxy

Not:

SOCKS5

Thus the proxy protocol negotiation failed.

Why SOCKS Was Abandoned

Even if SOCKS worked, it would require:

per-device configuration

The objective was:

transparent routing

Therefore NAT-based routing was implemented instead.

17. Third Problem: WiFi Clients Had No Internet

This was the most complex issue.

Observed Behavior
Ethernet client → works
WiFi client → no connectivity
Key Diagnostic Commands

Bridge configuration:

brctl show

Output:

br0
 ├ eth1
 ├ eth2
 └ vlan1

Meaning:

WiFi and LAN ports share same bridge
Why This Was Confusing

Because both WiFi and Ethernet were in the same bridge:

traffic should behave identically

But packet counters showed:

iptables FORWARD rules = 0 packets

for WiFi interfaces.

Investigation

MAC table was checked:

brctl showmacs br0

This confirmed WiFi devices were visible.

Thus the issue was not Layer 2.

18. NAT Misconfiguration

The key NAT rule originally used:

MASQUERADE -o tailscale0

But earlier rules also existed:

SNAT → vlan2

Meaning:

LAN traffic was still being NATed to WAN

instead of Tailscale.

Understanding NAT Priority

Linux NAT chains run in order.

If an earlier rule matches, later rules are never executed.

Therefore:

traffic exited through WAN

instead of Tailscale.

Correct Rule
iptables -t nat -A POSTROUTING -o tailscale0 -j MASQUERADE
19. Linux Bridge Netfilter Interaction

A subtle issue encountered was:

bridge-nf-call-iptables
Why This Matters

Linux bridges normally operate at Layer 2.

Meaning packets do not pass through iptables.

To allow firewall rules on bridge traffic:

net.bridge.bridge-nf-call-iptables = 1

must be enabled.

Command Used
echo 1 > /proc/sys/net/bridge/bridge-nf-call-iptables

Without this setting:

WiFi traffic bypasses iptables

Which explains earlier failures.

20. Routing Table Mistake

At one point the default route was changed to:

default dev tailscale0

This broke connectivity.

Why This Failed

The router itself must still reach:

WAN gateway
Tailscale DERP servers
control plane

If the default route is the tunnel:

bootstrap networking fails
Correct Design

Router routing table remains:

default via WAN

Only LAN client traffic is NATed to Tailscale.

21. Understanding Tailscale Routing

Tailscale uses policy routing.

Example rule:

fwmark 0x80000 lookup table 52

Meaning:

packets marked by tailscale
use alternate routing table
Command to Inspect
ip rule

Example output:

5210: from all fwmark 0x80000 lookup main
22. DERP Relays

In logs we saw:

magicsock: derp-23 connected
What DERP Is

DERP is a relay server used when peers cannot connect directly.

Example:

router → DERP → exit node

This is less efficient but ensures connectivity.

23. DNS Resolver Errors

Logs contained:

resolver: forward: no upstream resolvers

This happens because:

DD-WRT DNS is handled by dnsmasq

Tailscale DNS manager does not modify router DNS.

This warning is harmless.

24. Reboot Loop Issue

During early attempts the router entered reboot loops.

Cause

Startup script launched:

tailscale up

before:

network initialization completed
Fix

Added delay:

sleep 45

allowing:

WAN interface initialization
bridge creation
firewall loading
25. Persistent Storage Problem

Initially binaries were stored in:

/tmp

But:

/tmp = RAM filesystem

Contents disappear after reboot.

Correct Location

Persistent storage:

/jffs

Used for:

binaries
state file
scripts
26. State File Importance

The file:

tailscale.state

contains:

machine keys
node identity
login session

If lost, router must:

re-authenticate
27. Understanding NVRAM

Routers use NVRAM to store configuration variables.

Example:

rc_startup
rc_firewall

Commands:

nvram set
nvram commit
28. Final Stable Architecture

Final working design:

LAN clients
    ↓
br0 bridge
    ↓
iptables FORWARD
    ↓
NAT (MASQUERADE)
    ↓
tailscale0
    ↓
WireGuard encryption
    ↓
Exit node
    ↓
Internet
29. Diagnostic Commands Cheat Sheet
Interface status
ip addr
Routing table
ip route
Bridge configuration
brctl show
Firewall rules
iptables -L
NAT rules
iptables -t nat -L
Tailscale peers
tailscale status
Packet counters
iptables -L -v
30. Lessons Learned

Key lessons from the deployment:

Embedded systems behave differently

Router firmware is not standard Linux.

Overlay networks require NAT understanding

Routing alone is insufficient.

Bridge networking introduces hidden complexity

WiFi traffic may bypass firewall rules.

Persistence must be planned

Filesystems differ in embedded environments.

Debugging requires visibility

Packet counters are extremely valuable.

31. Potential Improvements

Future enhancements could include:

Advertised routes

Allow Tailscale peers to reach LAN.

tailscale up --advertise-routes=192.168.2.0/24
High availability

Deploy multiple routers.

Traffic shaping

Apply QoS before tunnel.

Monitoring

Export router metrics.

32. Conclusion

This project successfully transformed a consumer router into a Tailscale exit-node gateway.

The deployment required understanding:

Linux networking

router firmware architecture

overlay network routing

NAT interactions

bridge firewall behavior

embedded persistence mechanisms

The final system provides:

transparent VPN routing
for all LAN clients
without requiring client software
End of Report
