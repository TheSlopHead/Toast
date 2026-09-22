# Toast
[Русский](README.ru.md)

Toast is a virtual LAN tool for playing local-network games over the
internet, with built-in voice chat and no manual network configuration.

## Problem

Many games only support multiplayer through their LAN mode: local network
discovery via UDP broadcast or multicast, or a direct connection to an IP
address on the same subnet. This includes titles where the original online
matchmaking service is unavailable (deprecated, region-locked, or broken in
unlicensed copies) and titles like Minecraft that support LAN play by
design. Reaching a friend's LAN game over the internet normally requires
router port forwarding, a third-party relay service, or a general-purpose
VPN client that does not forward broadcast or multicast traffic and
therefore does not make the game visible to other players at all.

Existing solutions solve part of this problem. General-purpose mesh VPNs
(Tailscale, ZeroTier) provide secure point-to-point connectivity but are
built on layer-3 (IP) tunnels that do not carry broadcast or multicast
traffic, so LAN discovery does not work through them out of the box.
Dedicated LAN emulation tools (Hamachi, Radmin VPN) solve the discovery
problem but ship no voice chat, so players still need a separate
application such as Discord or TeamSpeak alongside them.

## What Toast Does

Toast creates a virtual Ethernet segment between participants' machines
over the internet, so that games see each other exactly as they would on a
physical local network, including broadcast and multicast-based discovery.
A voice channel between the same participants is established automatically
as part of joining the network, without any separate setup.

A session is created and joined through a single invite link. No account,
manual configuration, or router changes are required on the participant's
side.

## How It Works

- **Virtual network layer**: a TAP-based virtual network adapter on each
  machine, bridged into a single virtual Ethernet segment across
  participants. All Ethernet frames, including broadcast and multicast, are
  relayed between connected peers.
- **Connectivity**: NAT traversal (ICE) establishes a direct peer-to-peer
  path where possible, falling back to a relay server (TURN) when a direct
  path cannot be established (for example, behind carrier-grade NAT or a
  symmetric NAT).
- **Transport security**: traffic between peers is encrypted end-to-end,
  with keys derived from the invite link rather than trusted to the
  signaling server.
- **Voice**: a WebRTC-based voice channel is established over the same
  session, using the operating system's or WebView runtime's built-in echo
  and noise suppression.
- **Invites**: sessions are joined via a link containing a session secret;
  the session owner controls who is admitted.

## Status

Early development. The project is currently validating the core
assumption that a small (2–8 peer), broadcast/multicast-capable virtual
LAN segment can be built reliably over the internet, before further
features are built on top of it. It is not yet ready for general use.

## Target Games

Toast is being developed and validated against games that rely on LAN-mode
discovery, initially:

- Minecraft (Java Edition) — multicast-based LAN world discovery.
- Factorio — subnet broadcast-based LAN server discovery.

Support for additional titles that rely on the same underlying mechanisms
(subnet broadcast or direct connection within a shared subnet) is expected
to follow from the same virtual network layer, without game-specific code.

## Requirements

- Windows 10 or later.
- Administrator rights are required once, during installation, to install
  the virtual network adapter. Toast does not require running the
  application itself as administrator afterward.

## Non-Goals (current phase)

- Cross-platform support (Linux, macOS) is not planned for the initial
  release; macOS in particular does not support a native TAP-style
  adapter, which would require a different approach.
- Sessions larger than roughly eight participants.
- Compatibility with games whose anti-cheat systems block third-party
  network drivers or hooks.
- Bedrock Edition Minecraft (different discovery protocol).

## Contributing

The project is in an early, architecture-validation stage and not yet
structured for external contributions. Issues and feedback are welcome.

## License

To be determined.
