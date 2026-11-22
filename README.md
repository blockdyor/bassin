<p align="center">
  <img src="favicon.svg" width="64" height="64" alt="Bassin">
</p>

# Bassin

Run a Bitcoin solo mining pool on your Umbrel. Your node supplies the block templates. If your miner finds a block, the default solo configuration pays the block reward to your Bitcoin address. Bassin charges 0% pool fees.

Bassin combines [CKPool](https://github.com/ckolivas/ckpool) with a dashboard based on the [Umbrel Bitcoin UI](https://github.com/getumbrel/umbrel-bitcoin/tree/master/apps/ui). Open Home to see your pool’s hashrate, or use Insights to check an individual worker.

**Current community release: package 2.1.14 (Bassin UI 2.1.14) with CKPool `v1.2.0-2` (upstream `be3e1e07` with Stratum V2 & optional Mining IPC).** Install it through the [Blockdyor community app store](https://github.com/blockdyor/blockdyor-umbrel-community-app-store). The official store still ships 2.1.4 as of 22 November 2026; [PR #6404](https://github.com/getumbrel/umbrel-apps/pull/6404) proposes 2.1.14.

![Bassin gallery: mining with your own Bitcoin node](docs/gallery/01-decentralization.jpg)

California is the demo location shown in the gallery. Self-hosting keeps your pool under your control; it does not make mining anonymous. A winning block’s payout address is public.

[View the 0% pool fees panel](docs/gallery/02-fees.jpg) · [View the self-hosting and privacy panel](docs/gallery/03-privacy.jpg)

## What’s inside

These features describe the community dashboard. The official store’s 2.1.4 dashboard does not have the Connect dialog, Insights, or the Settings editor and log viewer. You can still connect a miner using the values below.

- **Home:** interactive globe, accepted/rejected share quality, and animated hashrate averages from one minute to seven days.
- **Insights:** workers, round best share, best share ever, share rate, uptime, a hashrate chart, live **Stratum V2 & Job Declaration** telemetry, and searchable worker details.
- **Stratum V2 (Noise-encrypted):** built-in Stratum V2 mining (`3457`) and optional Job Declaration (`3458`) alongside Stratum V1 (`3456`), with persistent private Noise keys stored in `data/sv2/` and live authority public-key discovery in **Connect → Stratum V2**.
- **Settings:** dedicated **Mining**, **Stratum V2**, **Bitcoin Node**, **Advanced**, and **Logs** tabs covering difficulty, coinbase signature, donation, version rolling mask, idle timeout, SV2 listeners & key paths, Bitcoin RPC/ZMQ/polling, optional Bitcoin Core v32.x Mining IPC, extranonce lengths, connection limits, and an advanced JSON editor.
- **Logs:** searchable pool logs with pause, follow, and download controls.
- **Worker setup:** Stratum V1 and Noise-encrypted Stratum V2 connection details with QR codes. The default solo configuration pays the successful miner’s Bitcoin address the full block subsidy and transaction fees, with no pool commission.
- **Bitcoin Core & Bitcoin Knots 29.3+ compatible:** standard solo mining (both Stratum V1 and Stratum V2) uses JSON-RPC + ZMQ and works out of the box with both Bitcoin Core and Bitcoin Knots 29.3+.
- **UTC throughout:** a UTC clock in Insights, UTC chart and worker timestamps, and the original UTC pool logs.
- **Version visibility:** the header shows the Bassin version and detects the installed CKPool version when metadata is available.
- **Mobile improvements:** responsive layouts, readable chart tooltips, reduced graphics load on touch devices, and a static globe fallback if graphics are interrupted.
- **Umbrel widget:** pool statistics on your umbrelOS dashboard.

**Settings currently imports, edits, and exports a configuration file. It does not load or apply the running pool configuration.** Import your existing `ckpool.conf` before editing; see [Settings and notifications](docs/settings.md) for the complete workflow.

## Install and connect

1. Run a fully synchronized Bitcoin node on Umbrel (Bitcoin Core or Bitcoin Knots 29.3+).
2. For the current community release, open **App Store → Community App Stores** on your Umbrel and add the following store URL (not this documentation repository):

   ```text
   https://github.com/blockdyor/blockdyor-umbrel-community-app-store
   ```

3. Install Bassin from that store and open **Connect → Stratum** (V1), **Connect → Stratum V2**, or **Connect → Worker Setup**.
4. Configure your miner with the following values:

   | Miner setting | Stratum V1 | Stratum V2 (Noise-encrypted) |
   | --- | --- | --- |
   | Pool URL | `stratum+tcp://<your-Umbrel-address>:3456` | `stratum2+tcp://<your-Umbrel-address>:3457/<authority-pubkey>` |
   | Username / User Identity | `<your-bitcoin-address>.<worker-name>` | `<your-bitcoin-address>.<worker-name>` |
   | Password | `x` | *(unused)* |

Copy the pool’s Base58Check authority public key (or scan its QR code) directly from **Connect → Stratum V2**. Use a receiving address from your own Bitcoin wallet. The address before the dot selects the payout; the suffix identifies the miner. An address alone also works. Never enter a wallet seed or private key. Solo mining does not provide regular payouts for submitted shares.

The [official Umbrel App Store listing](https://apps.umbrel.com/app/bassin) is also available, on its own release schedule. Read the [installation guide](docs/getting-started.md) before switching channels: the apps have different IDs and data directories, and both use Stratum port `3456` (community 2.1.14 also binds `3457` and `3458` for Stratum V2).

## Documentation

- [Getting started, updates, and release channels](docs/getting-started.md)
- [Settings, ZMQ, polling, and applying a configuration](docs/settings.md)
- [FAQ: shares, history, UTC, versions, privacy, and troubleshooting](docs/FAQ.md)
- [What changed in the redesigned Bassin](docs/whats-new.md)
- [GitHub Wiki](https://github.com/blockdyor/bassin/wiki)

## Project repositories

| Repository | Purpose |
| --- | --- |
| [Bassin](https://github.com/blockdyor/bassin) | Project overview, documentation, support, and shared app assets |
| [Bassin UI](https://github.com/blockdyor/bassin-ui) | Dashboard source, builds, and UI development instructions |
| [CKPool Solo container](https://github.com/blockdyor/docker-ckpool-solo) | Blockdyor-maintained packaging of upstream CKPool |
| [Bassin widget](https://github.com/blockdyor/umbrel-bassin-widget) | Umbrel dashboard widget |
| [Blockdyor community package](https://github.com/blockdyor/blockdyor-umbrel-community-app-store/tree/master/blockdyor-bassin) | Current community deployment and release manifest |
| [Official Umbrel package](https://github.com/getumbrel/umbrel-apps/tree/master/bassin) | Official deployment, reviewed and released separately |

The `duckaxe-bassin/` directory and `umbrel-app-store.yml` in this repository are legacy packaging. Use the maintained store repositories above for installation; the legacy package does not represent the current community release. Existing icon and gallery paths remain available for store integrations.

## How it works

```mermaid
flowchart LR
  M[Miners] -->|Stratum| P[CKPool solo]
  P -->|RPC| B[Bitcoin node]
  B -->|ZMQ block notifications| P
  P -->|Statistics and logs| F[Shared data files]
  F --> S[Static web server]
  S --> U[Bassin UI]
  S --> W[Umbrel widget]
```

The pool continues mining when the browser is closed. The UI reads pool statistics and logs; its live chart samples are collected during your visit. See the [FAQ](docs/FAQ.md) for the difference between chart history and CKPool’s saved statistics.

## Credits and licensing

Bassin was created by [duckaxe](https://github.com/duckaxe) and is now maintained by [blockdyor](https://github.com/blockdyor). Thanks to [Umbrel](https://umbrel.com) for the Bitcoin app UI and app platform, and to [Con Kolivas and CKPool contributors](https://github.com/ckolivas/ckpool) for the mining backend.

This repository retains its [GPLv3 license](LICENSE). The UI includes upstream code with its own licensing terms; consult [Bassin UI’s third-party notices](https://github.com/blockdyor/bassin-ui/blob/main/THIRD_PARTY_NOTICES.md) before reusing or redistributing it.

For academic and research purposes only.

For bugs and feature requests, [open an issue](https://github.com/blockdyor/bassin/issues). For vulnerabilities, follow the [security policy](SECURITY.md) and keep credentials out of public reports.
