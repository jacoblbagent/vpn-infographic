# How a VPN Works — web infographic

**🔗 Live:** https://jacoblbagent.github.io/vpn-infographic/
**Local:** http://localhost:5185/  ·  **Tailnet:** http://jlb-hermes.tail1caa84.ts.net:5185/

A single-file, dependency-free web infographic that shows what actually happens
to a packet when a VPN is on. Same visual language as the Energy Value Chain
project: data-driven SVG built at load time, no build step, no framework.

**Purpose:** one screen that answers "where does my traffic go, and what can
each party on the path still see" — minimal chrome, minimal text.

**Stack:** plain HTML + inline CSS + vanilla JS; the whole diagram is generated
SVG. `index.html` is the entire app.

**Servers:** `python3 -m http.server 5185 --bind 127.0.0.1`, started by the
Hermes web UI Projects tab via `~/Code/hermes-web-ui/scripts/start-vpn-infographic.sh`.

## What it shows

Five stages, read left to right — **01 Your Device → 02 Encryption →
03 Encrypted Tunnel → 04 VPN Server → 05 The Open Internet** — with six stage
nodes and 11 short labels:

| Stage | Node | Labels |
|---|---|---|
| 01 Your Device | Your Device | browser · apps · os stack |
| 02 Encryption | Encrypt | aes-256 · handshake · session key |
| 03 Encrypted Tunnel | Encrypted Tunnel | encapsulation · ciphertext |
| 04 VPN Server | VPN Server | exit node · new IP |
| 05 The Open Internet | Open Internet | routers · dns |
| 05 The Open Internet | Website | sees VPN IP |

Three edge styles carry the meaning: **solid arrows** = the flow of your data
(`data → ciphertext → packets → your traffic → request`), **dashed grey** = what
an observer sees, and **dashed orange** = the encrypted response coming back.

Below the flow sits an **OBSERVERS** band — your ISP, a public Wi-Fi network, and
trackers & ads — connected by a dashed guide to every stage up to the VPN
server, labelled *"sees ciphertext only"*. It is the point of the diagram: the
ciphertext is the same on both sides of the tunnel, but the address on it
changes at the server.

A dashed orange loop runs from the website back to your device, labelled
*"THE RESPONSE COMES BACK, STILL ENCRYPTED"*.

## Interaction

- Hovering a stage card dims everything unrelated, thickens the traced edges and
  reveals their flow labels (only short labels are ever drawn — none collide
  with a card).
- **Clicking a card or a label opens the detail panel** on the right: the stage
  (`02 · ENCRYPTION`), what happens there, a three-row step list, **what an
  observer sees** at that point, and links to a reference. Esc, a background
  click, or the × closes it.
- The panel is a real flex column, not an overlay, so opening it reflows the
  diagram instead of covering it; below 1180px it becomes a right-side overlay.
- **The rail** on the right has two tabs. **Steps** is the walkthrough, one row
  per stage — hovering a row traces it on the diagram, clicking holds it.
  **Glossary** defines the nine terms the diagram uses (AES-256, handshake,
  session key, tunnel, encapsulation, exit node, ciphertext, no-logs, kill
  switch). The `›` control collapses the rail; below 1180px it slides away.

## Verified

Rendered in headless Chromium at 1660×1000 (the SVG viewBox is 1660×748):
6 stage nodes, 16 chips, 12 edge paths, 11 labels, 6 walkthrough rows, 9
glossary entries, no console or page errors, no horizontal overflow with or
without the panel open.

Trace checked by dispatching `mouseenter`/`click`: hovering the VPN Server card
dims the 3 unrelated nodes and 3 unrelated edges and lights 2 labels; clicking
the Encrypt card opens the panel (`Encrypt`, `02 · ENCRYPTION`, 3 step rows, 2
links), pins 1 node; Esc on a card click or a rail-row click leaves 0 `.dim`,
0 `.hl`, 0 `.sel` and 0 `.pin` behind. Clicking an observer chip opens the same
panel (`Your ISP`); clicking a rail row pins the matching stage. Labels were
shortened (`packets`, not `encrypted packets`) after the longer string was found
clipped behind the VPN Server card.

## Note

Illustrative only — the shapes and labels are simplified, and encryption
strength depends on the protocol in use (WireGuard, OpenVPN, IKEv2). A VPN
hides where you connect from, not who you are.
