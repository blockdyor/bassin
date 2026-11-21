# Getting started

[Back to Bassin](../README.md)

## Choose a release channel

As of 21 November 2026:

| Channel | Version | App ID | Web port | Stratum port |
| --- | --- | --- | --- | --- |
| [Blockdyor community store](https://github.com/blockdyor/blockdyor-umbrel-community-app-store/tree/master/blockdyor-bassin) | Bassin 2.1.12 / CKPool 1.2.0 | `blockdyor-bassin` | `5009` | `3456` |
| [Official Umbrel store](https://github.com/getumbrel/umbrel-apps/tree/master/bassin) | Bassin 2.1.4 / CKPool image v1.1.1; 2.1.12 proposed in [PR #6404](https://github.com/getumbrel/umbrel-apps/pull/6404) | `bassin` | `5008` | `3456` |

The linked store manifests are the source of truth for later releases. The `duckaxe-bassin` package retained in the main project repository is legacy packaging, not the current community installation source.

## Install the community release

1. Make sure Umbrel’s Bitcoin node is fully synchronized and running.
2. On your Umbrel, open **App Store → Community App Stores** and add `https://github.com/blockdyor/blockdyor-umbrel-community-app-store`. Use the store root, not this documentation repository or its legacy `duckaxe-bassin` directory.
3. Install Bassin from the added store.
4. Open the app from Umbrel and select **Connect → Worker Setup**.
5. Point your miner at `stratum+tcp://<your-Umbrel-address>:3456`, using `<your-bitcoin-address>.<worker-name>` as its username and `x` as its password.

The Connect QR code uses the hostname in your browser’s address bar. If that name is only reachable through a proxy, enter your Umbrel’s LAN address on the miner instead. Miners use host TCP port `3456`, not the web port or the container’s internal `3333`. Give each miner a distinct worker name so you can identify it in Insights. Look for accepted shares on the miner; the dashboard may take a polling interval to show the worker.

On official 2.1.4, there is no **Connect → Worker Setup**, Insights, or Settings editor/log viewer. Use the same miner fields in step 5 and check the version in the Umbrel app listing rather than looking for the community UI’s header.

The standard package fills in the node’s RPC credentials and ZMQ hashblock endpoint on first installation. You do not need to configure Bitcoin Core’s separate `blocknotify` command for this setup. Leave an existing working configuration in place unless you intend to change it.

## Updating an existing installation

Use the update offered by the store from which you installed Bassin. After updating, close and reopen the app and reload the browser page if an old interface remains cached. Check the Bassin version in the header; the separate CKPool version is detected from the running pool installation.

If you use a public Bitcoin RPC endpoint for the globe marker, configure HTTPS for the location helper. The standard private Umbrel connection needs no change.

The current community package creates `data/config/ckpool.conf` only when it is missing, preserving an existing configuration on restart. A new template is not automatically applied to that existing file. The [settings guide](settings.md) explains how to make an intentional configuration change.

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
| `data/www/pool/` | Pool statistics and published metadata |
| `data/www/users/` | Retained user and worker statistics |
| `data/www/ckpool.log` | Pool log |

Keep configuration files and backups out of `data/www`: the web server serves that directory, and the standard package disables Umbrel’s proxy authentication for it. Keep the dashboard on a trusted network. Logs and user records can reveal payout addresses and worker names; redact those and RPC credentials before sharing diagnostics.

## Need help?

Start with the [FAQ](FAQ.md) and [Settings and notifications](settings.md). For an issue report, include the release channel, Bassin and CKPool versions, hardware, browser, and relevant redacted logs. Device emulation tests do not replace testing on the actual device: the maintainer has tested the redesigned app through the community store on a CMRat with Raspberry Pi CM5, on an earlier release. No physical umbrelOS install, update, restart, or persistence test is reported for 2.1.12.
