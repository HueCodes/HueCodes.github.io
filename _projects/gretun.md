---
layout: post
title: "gretun: NAT-traversing P2P GRE overlay for Linux"
---

Two hosts behind consumer NAT run `gretun up --coordinator <url>` and get a direct, kernel-fastpath GRE tunnel between them. No port forwarding, no static IPs, no manual config. The control plane uses Ed25519 and Curve25519 disco envelopes in the Tailscale wire format. The data plane is pure kernel GRE-over-FOU.

## Why FOU

Bare GRE is IP protocol 47 with no UDP header, so consumer NATs cannot map it. Linux FOU (Foo-over-UDP, kernel 3.18+) wraps the GRE packet in a plain UDP header so the outer transport is UDP. Any NAT that forwards UDP works.

The daemon sets this up via two netlink calls: `FouAdd` to install the UDP decap on the receive port, then `LinkAdd(Gretun{EncapType:FOU, EncapDport})` to create the GRE link bound to that encapsulation. Equivalent iproute2:

```
ip fou add port 7777 ipproto 47
ip link add tun0 type gretap \
  local 192.0.2.1 remote 192.0.2.2 \
  encap fou encap-dport 7777 encap-csum
```

Default tunnel MTU is 1468 to accommodate the 32-byte outer header (IP 20 + UDP 8 + GRE 4).

## STUN, coordinator, disco envelopes

Each node discovers its public `ip:port` via STUN over a shared userspace UDP socket. The coordinator is a small HTTP service that registers peers and relays sealed disco envelopes between them. It holds no node private keys; a coordinator compromise leaks only the public peer graph.

Disco envelope format is a 6-byte magic header plus the sender's Curve25519 public key plus a NaCl-box sealed body. Wire-compatible with Tailscale's disco format.

## Hole punching

Each side sends disco `ping` messages to the other's published endpoints and the first `pong` wins. Symmetric NAT is detected; the 256-socket mitigation probe is opt-in behind `--aggressive-punch` (~98% success at 1024 probes, per Tailscale's measurements).

Once the path is validated, the daemon configures FOU and GRE via netlink and steps out of the data path. Every packet after that is kernel fastpath.

## Architecture

```
                ┌──────────────────────┐
                │  gretun-coord (HTTP) │
                │   registry + relay   │
                └────▲────────▲────────┘
            register │        │ register
                     │        │
 ┌───────────────────┴─┐   ┌──┴─────────────────────┐
 │ node A (behind NAT) │   │ node B (behind NAT)    │
 │ ed25519 + disco key │   │ ed25519 + disco key    │
 │ disco socket (UDP)  │   │ disco socket (UDP)     │
 │ kernel FOU RX port  │   │ kernel FOU RX port     │
 └─────────┬───────────┘   └────────────┬───────────┘
           │  GRE-over-UDP (FOU) direct │
           └────────────────────────────┘
```

## Packages

| Package | Purpose |
|---------|---------|
| `cmd/gretun` | CLI entry point (`up`, `stun`, `create`, `list`, `status`) |
| `cmd/gretun-coord` | Coordinator HTTP server (registry and signaling relay) |
| `internal/daemon` | `gretun up` peer state machine; kernel FOU+GRE setup |
| `internal/disco` | STUN client, disco envelope format, NaCl-box sealing |
| `internal/coord` | Peer registry, signaling relay, pool allocation |
| `internal/tunnel` | GRE link create/delete via netlink; encap config |
| `internal/health` | ICMP probe of tunnels |
| `internal/capabilities` | `CAP_NET_ADMIN` check |

## Also available: plain GRE

`gretun create` still works for point-to-point GRE between known endpoints, bare or with FOU encap:

```bash
sudo gretun create --name tun0 \
  --local 192.0.2.1 --remote 192.0.2.2 \
  --encap fou --encap-dport 7777 \
  --tunnel-ip 100.64.0.1/30
```

All commands support `--json` and `--verbose`.

## Metrics

`--metrics-addr :9100` exposes Prometheus counters at `/metrics`:

* `gretun_peers{state="direct|relay|punching|..."}`
* `gretun_disco_pings_sent_total`, `gretun_disco_pongs_received_total`
* `gretun_hole_punch_duration_seconds`

## Limitations

* No tunnel encryption. GRE + FOU are plaintext. For confidentiality, pair gretun with WireGuard or IPsec at the inner layer. Encrypting the outer would just reinvent WireGuard.
* No data-plane relay yet. If both peers sit behind symmetric NAT with aggressive-punch off, the daemon reaches `state=relay` and logs; data does not flow. DERP-style relay is a natural follow-up.
* Linux only. Data plane is kernel FOU+GRE. Userspace-netstack mode (like `tailscaled --tun=userspace-networking`) is a possible next step.
* No ACLs or tailnet-style policy. The coordinator is a dumb registry.

---

[View on GitHub](https://github.com/HueCodes/gretun)
