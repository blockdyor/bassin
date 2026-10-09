# Getting started

[Back to Bassin](../README.md)

## Choose a release channel

As of 9 October 2026:

| Channel | Version | App ID | Web port | Stratum port |
| --- | --- | --- | --- | --- |
| [Blockdyor community store](https://github.com/blockdyor/blockdyor-umbrel-community-app-store/tree/master/blockdyor-bassin) | Bassin 2.1.10 / CKPool 1.2.0 | `blockdyor-bassin` | `5009` | `3456` |
| [Official Umbrel store](https://github.com/getumbrel/umbrel-apps/tree/master/bassin) | Bassin 2.1.4; redesigned 2.1.9 update proposed in [PR #6167](https://github.com/getumbrel/umbrel-apps/pull/6167) | `bassin` | `5008` | `3456` |

The linked store manifests are the source of truth for later releases. The `duckaxe-bassin` package retained in the main project repository is legacy packaging, not the current community installation source.

## Install the community release

1. Make sure Umbrel’s Bitcoin node is fully synchronized and running.
2. Add `https://github.com/blockdyor/blockdyor-umbrel-community-app-store` through Umbrel’s community app store controls.
3. Install Bassin from the added store.
4. Open the app from Umbrel and select **Connect → Worker Setup**.
5. Point your miner at `stratum+tcp://<your-Umbrel-address>:3456`, using `<your-bitcoin-address>.<worker-name>` as its username and `x` as its password.

Use your Umbrel’s reachable LAN address or hostname. The web port is for the dashboard; miners use port `3456`. Give each miner a distinct worker name so you can identify it in Insights. Worker statistics can take a polling interval and submitted shares to appear.

The standard package fills in the node’s RPC credentials and ZMQ hashblock endpoint on first installation. You do not need to configure Bitcoin Core’s separate `blocknotify` command for this setup. Leave an existing working configuration in place unless you intend to change it.

## Updating an existing installation

Use the update offered by the store from which you installed Bassin. After updating, close and reopen the app and reload the browser page if an old interface remains cached. Check the Bassin version in the header; the separate CKPool version is detected from the running pool installation.

In 2.1.10, **Settings → Bitcoin Node** has a **Poll for new blocks** switch. The overall dashboard design is the same as the preceding redesigned releases.

The current community package creates `data/config/ckpool.conf` only when it is missing, preserving an existing configuration on restart. A new template is not automatically applied to that existing file. The [settings guide](settings.md) explains how to make an intentional configuration change.

## Switching between official and community installations

Treat a channel switch as a migration, not an ordinary in-place update. The app IDs and data directories differ, and both packages publish Stratum port `3456`, so they cannot both listen on that port at the same time.

Before switching, back up the current app’s data and configuration, record the miner connection details, and stop the existing pool. Preserve its data until you have checked the replacement. Do not uninstall the old app as a substitute for making a backup.

The store does not automatically transfer `bassin` data into `blockdyor-bassin`. Preserve the pool and user statistics if you want to retain their recorded best shares. Review node addresses, credentials, and volume paths before reusing a configuration in another installation.

## Data and configuration

Locate the app’s data directory in your Umbrel installation. Its base path depends on your umbrelOS setup; use the directory belonging to the app ID above.

| Relative path inside the app data directory | Purpose |
| --- | --- |
| `data/config/ckpool.conf` | Active pool configuration, including private RPC credentials |
| `data/config/ckpool.conf.template` | First-install template |
| `data/www/pool/` | Pool statistics and published metadata |
| `data/www/users/` | Retained user and worker statistics |
| `data/www/ckpool.log` | Pool log |

Keep configuration files and backups out of `data/www`, which is served by the web server. Do not share RPC passwords in issue reports or screenshots.

## Need help?

Start with the [FAQ](FAQ.md) and [Settings and notifications](settings.md). For an issue report, include the release channel, Bassin and CKPool versions, hardware, browser, and relevant redacted logs. Device emulation tests do not replace testing on the actual device: the maintainer has tested the redesigned app through the community store on a CMRat with Raspberry Pi CM5, but that does not establish every update path or every iOS device combination.
