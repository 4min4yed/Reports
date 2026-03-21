# Networking Fundamentals

## Core Terms

- Jitter: variation in packet delay over time.
- Uplink: connection from local network equipment to upstream networks.
- Port mirroring: copies traffic to a monitoring port.
- Livebox: fiber to Ethernet CPE device.

## DNS Basics

- A record: domain to IP mapping.
- MX record: mail routing endpoint for a domain.

Examples:
- `domain.com -> 104.21.55.1` (A record)
- `domain.com -> domain.protection.outlook.com` (MX)

## Routing and Reachability

- Traceroute relies on TTL expiration and ICMP replies.
- VPNs can hide intermediate hops, reducing traceroute visibility.

## WAN and ISP Notes

- Static public service often includes a small routed block.
- One address is commonly used on customer edge, another as ISP gateway.
- Delivery can be PPPoE or routed/non-PPPoE depending on provider design.
- CGNAT is common on mobile networks (shared public IP).

## Redundancy and High Availability

- FHRP family:
1. HSRP: Cisco protocol.
2. VRRP: standard protocol with shared virtual gateway IP.

- Active-active designs can increase throughput and resilience.

## Network Interfaces and Switching

- `br*`: bridge interfaces between virtual and physical interfaces.
- `fw*`: firewall-created interfaces for policy control.

Switching feature list:
- Port configuration
- Port mirroring
- Loop protection
- IGMP snooping
- Interface auto recovery
- Trunk configuration
- EEE configuration
- Spanning tree
- Flow control
- Storm control
- Jumbo frames

## SNMP

- SNMP manager queries agent on UDP/161.
- Device can send traps to manager on UDP/162.
- Managed data is exposed via MIB and OIDs.

Common tools:

```bash
snmpget
snmpgetnext
snmpwalk -v 1|2c|3 -c community <IP> <OID>
snmpwalk ... | grep -i "STRING"
```

- Telemetry can be exported as custom OIDs depending on platform.

## Additional Concepts

- Jump server: controlled gateway before sensitive zones such as DMZ.
- Anycast: one destination IP announced by multiple nodes.
- TETRA: secure radio communications network.
- ASN can be useful for filtering and attribution:

```bash
curl https://ipinfo.io
```
