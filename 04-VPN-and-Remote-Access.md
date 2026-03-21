# VPN and Remote Access

## VPN Fundamentals

- VPN adds encryption and privacy for metadata and routing visibility, not just application payload.
- Best practice: assign VPN users from dedicated ranges (example: `10.x.x.x`) to avoid overlap and improve observability.
- DNS should also go through VPN to avoid DNS leaks.

## Protocol Notes

- WireGuard is typically faster than OpenVPN.
- WireGuard can be simpler and modern in many deployments.
- OpenVPN remains flexible and widely supported.
- IPsec operates at network layer; SSL/TLS secures application-layer sessions.

## Overlay VPN

- Overlay VPNs run on top of the public internet.
- Example platforms: Tailscale, NetBird.

Tailscale command sample:

```bash
tailscale up --advertise-exit-node --accept-dns --accept-routes
```

Tailscale notes:
- Mesh-style private network over WireGuard.
- Supports exit nodes and subnet route advertisement.
- Can run on embedded systems; key-expiry settings may need adjustment.

## Router VPN Concepts

- Router usage depends on router VPN capabilities.
- OpenVPN on routers is common for remote access.
- Understand TUN vs TAP mode trade-offs for routing versus bridging behavior.

## Blocking and Bypass Notes

Common VPN blocking methods:
- IP/DNS blacklisting
- DPI protocol detection
- Geo/datacenter heuristics

Possible bypass approaches:
- IP rotation
- Obfuscation
- Tor

## Remote Access Authentication

- RADIUS centralizes AAA for remote access.
- Captive portal and 802.1X solve different layers:
1. WPA2/WPA3-Enterprise (802.1X) controls layer-2 access.
2. Captive portal generally applies after layer-2 connectivity.
