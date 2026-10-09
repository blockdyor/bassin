# Frequently asked questions

[Back to Bassin](../README.md)

## Which version does this documentation cover?

The redesigned community release, Bassin 2.1.10 with CKPool 1.2.0. The official store can ship a different version. Check the app header and the [release channels](getting-started.md#choose-a-release-channel) before following version-specific instructions.

## What do I need, and what credentials go in my miner?

A running, fully synchronized Bitcoin node and a miner that can connect to Bassin’s Stratum endpoint. The standard Umbrel package connects to Umbrel’s Bitcoin node for you.

Use `stratum+tcp://<your-Umbrel-address>:3456`, username `<your-bitcoin-address>.<worker-name>`, and password `x`. Supply your own valid receiving address, not the literal word `user`. The miner password is separate from the Bitcoin node’s private RPC password. See [Getting started](getting-started.md).

## Is there a pool fee, and who receives a found block?

Bassin’s standard solo configuration charges no pool commission. The successful miner’s configured Bitcoin address receives the block subsidy and transaction fees. Rewards are not divided among the other connected workers, and ordinary shares do not earn periodic payouts. Custom CKPool donation settings can change the payout; this description applies to the standard package.

## Why does Bassin now look like the Umbrel Bitcoin app?

The UI was refactored from the actual Umbrel Bitcoin UI components, not just given similar colours. It retains Bassin’s water palette while adopting the familiar layout, typography, dock, cards, charts, dialogs, and globe. This gives the app a consistent Umbrel feel and a stronger foundation for additional mining features. See [What’s new](whats-new.md).

## Why do Settings show different values from my pool?

The settings form initially contains a starter template. It does not read your running configuration. Import the active `data/config/ckpool.conf` to edit the values your pool uses. The UI cannot apply or restart the pool automatically. Follow the [configuration workflow](settings.md#apply-an-intentional-change).

## Do I need to change block notifications after updating?

No, not for an existing working standard Umbrel configuration. It already includes the node’s ZMQ endpoint. In 2.1.10 the switch is called **Poll for new blocks**: on adds periodic RPC checks, while off disables those checks for the first node. It does not turn ZMQ on or off.

You do not need Bitcoin Core’s separate `blocknotify` command alongside the standard ZMQ setup. Read [ZMQ notifications and polling](settings.md#zmq-notifications-and-polling) before changing the configuration.

## Does `btcd[0].pass` mean I’m using a different Bitcoin implementation?

No. In CKPool’s JSON, `btcd` is the list of Bitcoin node connections, `[0]` selects the first node, and `pass` is its RPC password. The regular form uses plain Bitcoin Core RPC labels; the underlying keys remain visible in Advanced because CKPool expects them.

## Is IPC enabled because the header says CKPool 1.2.0?

No. The current packaged build supports ZMQ but does not include the optional mining IPC dependencies. A version number alone does not establish that optional capability. See [IPC availability](settings.md#ckpool-120-and-ipc).

## What are Round Best Share and Best Share Ever?

**Round Best Share** comes from CKPool’s saved pool `bestshare`. It survives pool restarts and resets when the pool finds a block or receives an explicit share reset. It is not a browser-session record.

**Best Share Ever** is the highest `bestever` in the retained user and worker records available to Bassin. Removing those records can remove historical highs; this is not a separate permanent database of every share the pool has ever seen.

## Why does the hashrate chart start over?

The chart collects timestamped samples during the current browser visit, with space for up to 61 samples at the normal one-minute polling interval. Closing or reloading the page clears that chart history. Background tabs can be throttled by the browser, so uninterrupted sampling is not guaranteed.

The 1-minute, 5-minute, 1-hour, 24-hour, and 7-day averages come from CKPool’s statistics. They are separate from the browser’s chart history. Mining continues when the browser is closed.

## How often do statistics and logs refresh?

The UI polls pool statistics every 60 seconds. The log viewer refreshes every five seconds unless paused; it displays up to the latest 400 lines from a bounded read of the log. Filtering and downloading visible lines does not necessarily include the entire log file.

For the full log, use `data/www/ckpool.log` in the app’s data directory. **Settings → Logs** supports search, pause/resume, follow, and download.

## Which timezone is used?

UTC. Insights includes a UTC clock; chart labels and tooltips, worker last-share timestamps, and refresh times use UTC. Raw CKPool log lines are shown unchanged and already use UTC in the Bassin deployment.

## What does the globe’s blue marker represent?

The approximate location of a public IP reported by your Bitcoin node. You can drag the globe to rotate it. It does not detect the dashboard visitor’s IP or request browser location permission.

The server-side helper sends the node’s public IP to `ipwho.is` for approximate coordinates and caches the result. The browser reads the resulting local metadata file. If the node only reports private, loopback, or Tor addresses, or no usable metadata is available, the globe has no marker. A remotely hosted Bitcoin node produces a marker at that node’s approximate location, not your miner’s location.

## Why does the header show `CKPool —`?

The UI could not obtain valid detected version metadata. It does not guess a version from the image tag. The package’s startup helper publishes the binary’s version separately from pool statistics, so missing version metadata alone does not prove the pool is offline.

## What changed for iPhone and iPad users?

The redesigned UI releases globe graphics resources when leaving Home, reduces graphics resolution on touch devices, pauses the globe when the page is hidden, and falls back to a static image if its graphics context is lost. Mobile overflow, labels, and tooltip contrast were also improved.

Chromium and WebKit checks cover narrow layouts and browser behaviour. They do not guarantee that every physical iOS device will avoid memory-pressure crashes. If you still see a crash, report your device, iOS/browser version, Bassin version, and the screen or action that triggered it.

## How do I know whether a block was found?

The UI does not currently provide a dedicated block-found alert. Inspect the pool logs and verify a suspected block with your Bitcoin node. A submitted-block message or a reset share statistic alone is not confirmation that a block was accepted into the chain.

The current community template adds `/mined by Bassin on Umbrel/` to the coinbase. Older installations or customized configurations can use another signature; check `btcsig` in the active configuration. A signature is a useful identifier, not proof of who mined a block.

## Can I add the Umbrel dashboard widget?

Yes. Add Bassin’s statistics widget from the umbrelOS dashboard. Its data comes from the same pool statistics used by Bassin.

## Is the terminal dashboard still available?

The community repository contains an optional [terminal dashboard](https://github.com/blockdyor/blockdyor-umbrel-community-app-store/blob/master/blockdyor-bassin/tools/dashboard.cjs). It is separate from the web UI and requires Node.js in the environment where you run it. It expects `../www` relative to the script, so place it in the app’s `data/tools/` directory when using the standard `data/www/` layout. Run `node dashboard.cjs` from that location; the base Umbrel data path varies by installation.

## What does “Bassin” mean?

A *bassin de nage* is a swimming pool designed for swimming laps.

## Where should I report a problem?

Use [Bassin issues](https://github.com/blockdyor/bassin/issues), including the app-store channel, versions, device, browser, and redacted logs. For a security vulnerability, use the [private reporting instructions](../SECURITY.md).
