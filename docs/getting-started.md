# Getting started

[Back to Bassin](../README.md)

## Choose a release channel

As of 22 November 2026:

| Channel | Version | App ID | Web port | Stratum ports |
| --- | --- | --- | --- | --- |
| [Blockdyor community store](https://github.com/blockdyor/blockdyor-umbrel-community-app-store/tree/master/blockdyor-bassin) | Package 2.1.18 / UI 2.1.18 / CKPool `v1.2.0-3` (`be3e1e07`) | `blockdyor-bassin` | `5009` | `3456` (SV1), `3457` (SV2), `3458` (SV2 JD) |
| [Official Umbrel store](https://github.com/getumbrel/umbrel-apps/tree/master/bassin) | Bassin 2.1.4 / CKPool image v1.1.1; 2.1.18 proposed in [PR #6404](https://github.com/getumbrel/umbrel-apps/pull/6404) | `bassin` | `5008` | `3456` (2.1.18 adds `3457`, `3458`) |

The linked store manifests are the source of truth for later releases. The `duckaxe-bassin` package retained in the main project repository is legacy packaging, not the current community installation source.

## Install the community release

1. Make sure Umbrel’s Bitcoin node is fully synchronized and running (both **Bitcoin Core** and **Bitcoin Knots**, including 29.3 and earlier versions, work out of the box via JSON-RPC + ZMQ).
2. On your Umbrel, open **App Store → Community App Stores** and add `https://github.com/blockdyor/blockdyor-umbrel-community-app-store`. Use the store root, not this documentation repository or its legacy `duckaxe-bassin` directory.
3. Install Bassin from the added store.
4. Open the app from Umbrel and select **Connect → Stratum** (V1), **Connect → Stratum V2**, or **Connect → Worker Setup**.
5. Point your miner at:
   - **Stratum V1:** `stratum+tcp://<your-Umbrel-address>:3456`, username `<your-bitcoin-address>.<worker-name>`, password `x`.
   - **Stratum V2 (Noise-encrypted):** `stratum2+tcp://<your-Umbrel-address>:3457/<authority-pubkey>` (shown with a QR code in **Connect → Stratum V2**), `user_identity` `<your-bitcoin-address>.<worker-name>`.

The Connect QR codes use the hostname in your browser’s address bar. If that name is only reachable through a proxy, enter your Umbrel’s LAN address on the miner instead. Miners use host TCP port `3456` (SV1) or `3457` (SV2) — and `3458` if Job Declaration is enabled — not the web port or the container’s internal `3333`/`3336`/`3337` ports. Give each miner a distinct worker name so you can identify it in Insights. Look for accepted shares on the miner; the dashboard may take a polling interval to show the worker.

On official 2.1.4, there is no **Connect → Worker Setup**, Insights, or Settings editor/log viewer. Use the same miner fields in step 5 and check the version in the Umbrel app listing rather than looking for the community UI’s header.

The standard package fills in the node’s RPC credentials and ZMQ hashblock endpoint on first installation. You do not need to configure Bitcoin Core’s separate `blocknotify` command for this setup. Leave an existing working configuration in place unless you intend to change it.

## Updating an existing installation

Use the update offered by the store from which you installed Bassin. After updating, close and reopen the app and reload the browser page if an old interface remains cached. Check the Bassin version in the header; the separate CKPool version is detected from the running pool installation.

If you use a public Bitcoin RPC endpoint for the globe marker, configure HTTPS for the location helper. The standard private Umbrel connection needs no change.

The current package creates `data/config/ckpool.conf` only when it is missing, preserving an existing configuration on restart. Fresh installs enable Stratum V1 (`serverurl: ["0.0.0.0:3333"]`) and configure `ipcmining` by default, leaving Stratum V2 opt-in via **Settings → Stratum V2**. On upgrade from earlier installs, `hooks/pre-start` runs one-time migrations (`IPC_MIGRATED`, `SV1_SERVERURL_MIGRATED`) that add `ipcmining` without overriding existing values and restore `serverurl: ["0.0.0.0:3333"]` for any 2.1.17 install that had `sv2url` without `serverurl`. See the [settings guide](settings.md).

## Switching between official and community installations

Treat a channel switch as a migration, not an ordinary in-place update. The app IDs and data directories differ, and both packages publish Stratum port `3456`, so they cannot both listen on that port at the same time.

Record the miner connection details, then stop the existing pool before copying files so CKPool is not writing statistics during the backup. Make a private copy of the app’s `data/` directory outside both installations, preserving ownership and permissions. Check that you can read the copy and that it includes the active configuration and any retained pool/user records before removing anything. A Settings download is only a configuration file, not a backup of mining history.

If startup reports that port `3456` is already in use, leave the old pool stopped and check which app owns that host port. Changing the dashboard port or just the port entered on the miner will not free it. Do not uninstall either copy to clear a conflict. Before restarting the original pool for rollback, stop the replacement.

The store does not automatically transfer `bassin` data into `blockdyor-bassin`. Preserve the pool and user statistics if you want to retain their recorded best shares. Review node addresses, credentials, and volume paths before reusing a configuration in another installation.

## Data and configuration

Under Umbrel’s data root, use `app-data/blockdyor-bassin/` for the community app or `app-data/bassin/` for the official app. The absolute data root depends on your installation. These are installed directories, not the copy of the package in the community store checkout.

| Relative path inside the app data directory | Purpose |
| --- | --- |
| `data/config/ckpool.conf` | Active pool configuration, including private RPC credentials |
| `data/config/ckpool.conf.template` | First-install template |
| `data/sv2/` | Private persistent Stratum V2 Noise keys (`sv2_authority.key`, `sv2_static.key`), chmod `0700`, mounted only in CKPool (never in the UI container) |
| `data/www/pool/` | Pool statistics and published metadata (`pool.status`, `version.json`, `location.json`, `sv2.json`) |
| `data/www/users/` | Retained user and worker statistics |
| `data/www/ckpool.log` | Pool log |

Starting in 2.1.15, Umbrel’s built-in proxy authentication protects the Bassin dashboard and its live configuration API. Keep configuration backups and `data/sv2/` keys out of `data/www`. Logs and user records can reveal payout addresses and worker names; redact those and RPC credentials before sharing diagnostics.

## Need help?

Start with the [FAQ](FAQ.md) and [Settings and notifications](settings.md). For an issue report, include the release channel, Bassin and CKPool versions, hardware, browser, and relevant redacted logs. Device emulation tests do not replace testing on the actual device: the maintainer has tested the redesigned app through the community store on a CMRat with Raspberry Pi CM5, on an earlier release. No physical umbrelOS install, update, restart, or persistence test is reported for 2.1.15.

## Interrupted first setup

The package does not guess whether an existing `data/config/ckpool.conf` is partial. If CKPool reports a configuration error after an interrupted **first** setup:

1. Stop Bassin in Umbrel. In the installed app directory (`blockdyor-bassin` for community, `bassin` for official), back up the stopped app data to a private location outside `data/www`, preserving permissions. Configs may contain RPC credentials. Do not uninstall or remove `data/www`.
2. Inspect the config privately. Invalid JSON or a truncated final line can indicate an incomplete render; a startup error alone does not. If the config is valid, customized, or its status is uncertain, leave it in place and investigate the logs. Prefer restoring a known-good private backup.
3. Only if you confirm a partial config and want fresh defaults, move **only** `data/config/ckpool.conf` to a unique, unused private backup path while stopped. Leave `ckpool.conf.template`, pool history, users and logs in place. Start the patched package, verify the node, payout and mining settings and check pool activity before discarding any backup.
