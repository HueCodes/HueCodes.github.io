---
layout: post
title: "gretun: GRE Tunnel Management CLI"
---

CLI for creating and managing GRE tunnels on Linux. Four commands covering the full tunnel lifecycle: create, list, probe, delete.

![GRE Tunnel Topology](/assets/images/projects/gretun-tunnel.png)

## Why GRE

GRE tunnels provide kernel-native encapsulation for routing private IP traffic through public networks. No IPsec key exchange, no userspace VPN daemons. The kernel handles all encapsulation and decapsulation, which keeps overhead low. The tradeoff is no built-in encryption, so GRE works best when both endpoints are under your control (VPCs, dedicated circuits, lab environments).

The encapsulation is straightforward: an outer IP header uses public IPs to route across the internet, a GRE header marks the payload as encapsulated (IP protocol 47), and the inner packet travels unchanged with its private IPs. At the remote end, the kernel strips the headers and delivers the inner packet to the tunnel interface.

## Direct kernel configuration via netlink

Linux network configuration operates through netlink sockets, not by parsing command output or wrapping `ip tunnel` commands. gretun uses the vishvananda/netlink library for direct kernel communication.

Creating a tunnel involves four netlink operations in sequence: create a GRE link device with local and remote addresses, assign the tunnel IP to the interface, bring the interface up, and optionally add routes for remote subnets. Partial failures are the main concern. If step 3 fails, steps 1 and 2 leave an interface in a broken state. The CLI tracks which operations succeed and rolls them back if subsequent steps fail.

Permissions caused immediate problems. Netlink operations return opaque errors when CAP_NET_ADMIN is missing. gretun checks for the capability upfront before any netlink calls, so users get an actionable error message instead of a cryptic syscall failure.

## MTU calculation

GRE adds 24 bytes of overhead: 20 bytes for the outer IP header and 4 bytes for the GRE header. If the physical interface has a 1500-byte MTU, the tunnel can only carry 1476-byte inner packets without fragmentation. Larger packets get fragmented or dropped depending on the DF bit.

This is a common source of silent failures in production. Applications timeout on large transfers and basic ping tests pass fine because they use small packets. gretun calculates tunnel MTU automatically by querying the physical interface and subtracting overhead.

## Testing with network namespaces

Testing tunnel functionality without multiple physical machines uses Linux network namespaces. Two namespaces connected by veth pairs simulate separate machines with isolated network stacks. Creating GRE tunnels between namespaces exercises the full code path: encapsulation, routing, and decapsulation. 275+ validation test cases cover tunnel names, CIDRs, IPs, and TTLs.

## Limitations

IPv4 only for both outer and inner addresses. Health checks are ICMP-only; TCP handshake or HTTP probes would catch more failure modes like firewall misconfigurations that allow ICMP but block application traffic.

---

[View on GitHub](https://github.com/HueCodes/gretun)
