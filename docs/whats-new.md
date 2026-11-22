# The redesigned Bassin

[Back to Bassin](../README.md)

This guide covers the changes now available in the community release through **2.1.15**. Store release notes and [Bassin UI pull requests](https://github.com/blockdyor/bassin-ui/pulls?q=is%3Apr+is%3Amerged) provide the implementation history.

## 2.1.15 — 22 November 2026 (Live Auto-Saving Settings & Automatic CKPool Reload)

- **Live auto-saving Settings:** Settings now loads your running `/config/ckpool.conf` automatically via the built-in Bassin server (`bassin-server.py`, replacing the static-only file server). Toggling Stratum V2 or changing any valid setting auto-saves atomically (`0600`) without manual export/copy steps.
- **Automatic CKPool reload:** CKPool’s startup supervisor watches `/config/ckpool.conf` for changes and gracefully reloads CKPool (`SIGTERM`) within seconds, automatically publishing any newly generated Stratum V2 authority public key to **Connect → Stratum V2**.
- **Server-side credential protection & Umbrel auth:** Stored Bitcoin RPC passwords are redacted on read and preserved automatically on save (eliminating the false “Bitcoin node 1 needs host:port, RPC username, and RPC password” error when toggling Stratum V2). Umbrel’s built-in `app_proxy` authentication is now enabled to protect the live dashboard and configuration API, while private SV2 Noise keys remain isolated in `data/sv2/` (never mounted in `ui`).
- **Clearer Bitcoin node wording:** Clarifies compatibility with both **Bitcoin Core** and **Bitcoin Knots** (including 29.3 and earlier versions) over standard JSON-RPC + ZMQ.
- UI build [`26117d4`](https://github.com/blockdyor/bassin-ui/actions/runs/45056606663), CKPool build [`v1.2.0-2`](https://github.com/blockdyor/docker-ckpool-solo/actions/runs/45047644486).

## 2.1.14 — 22 November 2026 (Stratum V2, CKPool `v1.2.0-2`, Full Settings)

- **CKPool updated to `v1.2.0-2` (upstream `be3e1e07` + pinned `secp256k1` `ebf59432` on latest Debian Trixie slim):** brings 90 upstream commits beyond tagged `v1.2.0`, including multi-node block submission retries, workbase-matched network difficulty checks, escaped RPC address validation, startup hashrate decay fixes, memory leak & lock fixes, Noise-encrypted **Stratum V2** (`--enable-sv2`), and optional **Cap’n Proto Mining IPC**. Standard JSON-RPC + ZMQ remains the default so both **Bitcoin Knots** (including 29.3 and earlier) and **Bitcoin Core** work out of the box.
- **Stratum V2 in Bassin UI 2.1.14:** adds **Connect → Stratum V2** with QR code and auto-detected public Base58Check authority key (`/pool/sv2.json`, keeping private keys isolated in `data/sv2/`), **Insights → Stratum V2** live telemetry (clients, channels, JD stats), and a dedicated **Settings → Stratum V2** tab.
- **Full CKPool Settings:** adds controls for `highdiff`, `donation`, `version_mask`, `dropidle`, `serverurl`, `nonce1length`, `nonce2length`, `maxclients`, `maxusers`, `maxsubclients`, `maxsendqueue`, `reconnect`, and optional `ipcmining`.
- **Ports & persistence:** exposes host ports `3457` (SV2 Mining → `3336`) and `3458` (SV2 Job Declaration → `3337`) alongside `3456` (SV1), and mounts a dedicated `0700` `data/sv2/` directory owned by `1000:1000` (never exposed by `www`).
- Community package commit [`9876401`](https://github.com/blockdyor/blockdyor-umbrel-community-app-store/commit/987640118ef5b69ff8beea10d049fb9acbd7d393), UI build [`fcfcac3`](https://github.com/blockdyor/bassin-ui/actions/runs/45048014820), CKPool build [`v1.2.0-2`](https://github.com/blockdyor/docker-ckpool-solo/actions/runs/45047644486).

## 2.1.13 — 22 November 2026 (UI reliability, performance & security)

- Fixes CKPool scientific-notation hashrate parsing (`1e+03G` / `1e+03T` near unit boundaries), adds `E`/`Z`/`Y` units, and guards invalid/empty hashrate formatting.
- Handles empty `/ckpool.log` HTTP `416` (`bytes */0`) cleanly without showing an error, and tolerates single vanished/stray files in `/users/` so all remaining workers and Best Share Ever stay visible.
- Eliminates the 1-second global `PoolContext` re-render loop and fixes `Globe` memoization (moving the 1-second UTC clock to Insights and replacing polling staleness with an exact timer).
- Fixes cleared-field edited indicators, non-finite numeric input guards, host/port validation (`1–65535`), worker name sorting, dark toast styling, nested `<header>` HTML, and removes unused vulnerable dependencies (`npm audit`: 0 vulnerabilities).

## 2.1.12-patch.1 — package update

First-time configuration is published only after rendering succeeds. On the community store, the CLI now reads pool and worker statistics from `data/www`. No UI or CKPool image changed. See [recovery after an interrupted first setup](getting-started.md#interrupted-first-setup).

## 2.1.12 — 21 November 2026

The node-location helper uses HTTPS for public Bitcoin RPC endpoints. It allows plain HTTP only to a private or loopback address. This rule applies to the globe marker, not to miner Stratum connections or CKPool’s mining RPC client. If the helper cannot obtain a location, the globe can still display without a marker. Updating does not require replacing your `ckpool.conf` or resetting mining data.

The [community package](https://github.com/blockdyor/blockdyor-umbrel-community-app-store/commit/71787f671c42afc14ba50a871f57127cff3ac82c) pins UI image `1608d5f` and CKPool image `v1.2.0-1`. The [production build](https://github.com/blockdyor/bassin-ui/actions/runs/44946393302) verified the UI and published images for amd64 and arm64. It was not a physical umbrelOS installation test.

## 2.1.11 — Home hashrate averages

Home replaced its five hashrate blocks with water columns on a shared, zero-based scale. The values are still CKPool averages, not browser chart samples. The columns respect reduced-motion preferences.

## A UI built from Umbrel Bitcoin

The original dashboard had become limiting as Bassin grew. The new direction refactors the official [Umbrel Bitcoin app UI](https://github.com/getumbrel/umbrel-bitcoin/tree/master/apps/ui) for solo mining, using its actual components and styling as the foundation.

Bassin keeps its water colours and identity while adopting the familiar Umbrel typography, floating navigation dock, cards, settings layout, dialogs, charts, and interactive globe. The aim is a consistent experience alongside the Bitcoin node app and more room to improve mining information and controls. Source attribution and applicable licenses are recorded in the UI’s [third-party notices](https://github.com/blockdyor/bassin-ui/blob/main/THIRD_PARTY_NOTICES.md).

## Mining information and controls

Home combines the pool’s hashrate, share-quality ring, and five hashrate averages. Insights adds worker details, share rate, uptime, round best share, best share ever, and a chart of samples collected during the visit. The distinction between a round record and retained lifetime worker records is explained in the [FAQ](FAQ.md#what-are-round-best-share-and-best-share-ever).

Connect includes Stratum details, a QR code, and worker setup instructions explaining the full solo block reward and zero pool commission. Settings offers a local configuration editor and a searchable log viewer. GitHub support links are accessible from the interface.

![Bassin 2.1.10 Insights](images/insights.png)

*Bassin 2.1.10 with sample mining data.*

## Readability, time, and mobile behaviour

All displayed dates and times use UTC, with a clock in Insights. Chart tooltips and Bitcoin node fields have explicit readable colours. The header shows Bassin’s version and detects CKPool’s version from installed-binary metadata rather than a manually entered UI value.

The globe supports dragging and can show one pulsing blue marker from the Bitcoin node’s reported public IP. It never locates the dashboard visitor. [The FAQ describes the location lookup and privacy details](FAQ.md#what-does-the-globes-blue-marker-represent).

Mobile work includes small-screen layout corrections, explicit graphics cleanup during navigation, reduced drawing resolution on touch devices, and a static globe fallback after graphics-context loss. Browser checks cover Chromium and WebKit, including repeated navigation and narrow viewports.

## Notification settings in 2.1.10

The old “Block Notifications” switch was ambiguous: its configuration flag actually controlled whether the node needed RPC polling. The new **Poll for new blocks** switch states that behaviour directly and keeps it independent of the ZMQ endpoint.

The polling interval now sits with notification settings. ZMQ has clearer hashblock and port guidance plus inline address validation. Plain Bitcoin Core RPC labels replace raw `btcd[0]` field labels, and settings search can find node and notification options.

These remain file-editing controls. They do not test notification delivery or apply the running configuration. See [Settings and notifications](settings.md).

## The CKPool container

The community release uses [Blockdyor’s CKPool Solo container](https://github.com/blockdyor/docker-ckpool-solo) (`v1.2.0-2`), packaging pinned upstream CKPool `be3e1e07` and `secp256k1` `ebf59432` for amd64 and arm64. The image is pinned by digest in the store manifest. Packaging uses portable compilation settings, a non-root `1000:1000` runtime, ZeroMQ, Noise-encrypted Stratum V2 (`--enable-sv2`), and optional Cap’n Proto Mining IPC.

Maintaining this container lets Bassin’s backend and UI changes be tested and released together. The mining software remains upstream CKPool; this is a maintained packaging path. Work proposed for Umbrel’s container is tracked in [getumbrel/docker-ckpool-solo PR #9](https://github.com/getumbrel/docker-ckpool-solo/pull/9).

Standard JSON-RPC + ZMQ remains the default so both **Bitcoin Core** and **Bitcoin Knots** (including 29.3 and earlier) work out of the box without enabling IPC. Backend improvements do not increase an ASIC’s physical hashrate. Available capabilities are described in the [settings guide](settings.md#ckpool-v120-2-bitcoin-core--knots-and-mining-ipc).

## Official and community releases

The current community release (2.1.15) is available through [Blockdyor’s store](https://github.com/blockdyor/blockdyor-umbrel-community-app-store/tree/master/blockdyor-bassin). As of 22 November 2026, the official store ships 2.1.4. [PR #6404](https://github.com/getumbrel/umbrel-apps/pull/6404) proposes 2.1.15 and supersedes [#6167](https://github.com/getumbrel/umbrel-apps/pull/6167). A community update does not automatically update the official listing.
