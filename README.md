# Awesome Bitcoin BLAKE [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated list of nodes, wallets, servers, explorers, pools, tools and docs for the Bitcoin BLAKE2b chain.

On 8 August 2026 Bitcoin Knots nodes enforcing BIP-110 (the Reduced Data Temporary Softfork, RDTS) parted from the SHA256d chain at block 961,632. On 30 August 2026, at block **961,640**, that chain changed proof-of-work from SHA256d to BLAKE2b (Knots [PR #359](https://github.com/bitcoinknots/bitcoin/pull/359)). Block headers are now 164 bytes (header v2), so most existing wallets, Electrum servers and explorers need patches to follow the chain. This list tracks what works today.

Everything here is community maintained. **Verify releases and signatures yourself.** Listing is not an endorsement. Entries marked *testnet only* do not run on mainnet.

## Contents

- [Chain parameters](#chain-parameters)
- [Nodes](#nodes)
- [Wallets](#wallets)
- [Electrum servers](#electrum-servers)
- [Explorers](#explorers)
- [Mining](#mining)
- [Node packaging](#node-packaging)
- [Libraries and tooling](#libraries-and-tooling)
- [Cross-chain and replay](#cross-chain-and-replay)
- [Specs and documentation](#specs-and-documentation)
- [Community and news](#community-and-news)
- [Contributing](#contributing)

## Chain parameters

Read from `src/kernel/chainparams.cpp` at Knots tag `v29.4.1.knots20260508`.

| Parameter | Mainnet | Testnet4 |
|---|---|---|
| Proof-of-work | BLAKE2b-256, header v2 (164 bytes), target shift 22 | same |
| Last shared block with SHA256d chain | 961,631 | |
| First RDTS-only block | 961,632 (8 August 2026) | |
| First BLAKE2b block | 961,640 (30 August 2026), hash `0000000000000050c1e5f69672f459293be14f46e5a494e7a8c8541396f18eeb` | 150,308 |
| Headline committed at the fork | `8-30 NYPost Deride And Conquer` | |
| RDTS block weight limit | 800 kWU from 961,640 until median time past 2027-09-01 00:00 UTC | |
| Replay protection | Opt-in `SIGHASH_UNIFIED` (0x20) per signature, Knots [PR #357](https://github.com/bitcoinknots/bitcoin/pull/357), shipped in 29.4.1 | same |
| P2P magic | `f9 be b4 d9` (unchanged) | `1c 16 3f 28` |
| P2P / RPC ports | 8333 / 8332 (unchanged) | 48333 |
| Address prefixes | unchanged (`1`, `3`, `bc1`) | unchanged (`tb1`) |
| DNS seeds | `dnsseed.bitcoin.dashjr-list-of-p2p-nodes.us`, `seed.bitcoin.haf.ovh` | |
| Reference implementation | Bitcoin Knots `v29.4.1.knots20260508` | same |

## Nodes

- [Bitcoin Knots](https://github.com/bitcoinknots/bitcoin) - Reference full node. Release `v29.4.1.knots20260508` carries the BLAKE2b hardfork, flag-day RDTS and the unified opt-in sighash.
- [Bitcoin Knots website](https://bitcoinknots.org/) - Binaries, signatures, and the [2026 BLAKE2b explainer](https://bitcoinknots.org/learn/2026-blake2b).
- [Knots release notes](https://github.com/bitcoinknots/bitcoin/blob/v29.4.1.knots20260508/doc/release-notes.md) - What changed in the hardfork release, including the new header fields exposed over RPC.
- [Knots Guix attestations](https://github.com/bitcoinknots/guix.sigs) - Reproducible build signatures for the release.

## Wallets

- [Sparrow (BLAKE2b build)](https://github.com/paulscode/sparrow/releases) - Paul Lamb's Sparrow 2.5.5 on the `blake2b` branch. Follows the BLAKE2b chain and signs with the unified opt-in sighash for replay protection. Needs a BLAKE2b Electrum server such as electrs-pruned or Shulcrum.
- [drongo (BLAKE2b build)](https://github.com/paulscode/drongo) - Sparrow's Java Bitcoin library, patched to read variable-length headers and to produce and verify the unified sighash.
- [Shrike](https://github.com/AcesHigh70/sparrow) - The first Sparrow fork for the chain. On 25 August 2026 its author marked it unmaintained and withdrew the binaries. Its sighash work was ported into the Sparrow BLAKE2b build above.
- [Bitcoin Knots wallet](https://github.com/bitcoinknots/bitcoin) - The built-in node wallet works on the new chain with no extra software and supports the unified sighash.

## Electrum servers

- [Shulcrum](https://github.com/Kilombino/Shulcrum) - Fulcrum fork that indexes and serves chains with 164-byte headers and BLAKE2b proof-of-work.
- [electrs-pruned](https://github.com/paulscode/electrs-pruned) - electrs for a pruned node that follows the header-v2 chain. Pairs with the Sparrow BLAKE2b build.
- [blake2b-light-clients](https://github.com/Kilombino/blake2b-light-clients) - Notes on what breaks in the Electrum protocol when headers stop being 80 bytes, and what it takes to fix.

## Explorers

- [mempool.guide](https://mempool.guide/) - Mempool explorer on the BLAKE2b chain with a full Esplora API, including 164-byte headers via `/api/block/<hash>/header`.
- [mempool.kilombino.com](https://mempool.kilombino.com/) - Self-hosted mempool instance following the BLAKE2b chain.
- [Kilombino/mempool-bip110](https://github.com/Kilombino/mempool-bip110) - Mempool fork (Retropex lineage, v3.4-dev) with RDTS "reduced data" violation detection and a node map. Self-hostable.
- [paulscode/mempool-pruned](https://github.com/paulscode/mempool-pruned) - Mempool v3.3.1 for a pruned node via electrs-pruned, keeps up with the BLAKE2b chain.
- [paulscode/mempool-bip110](https://github.com/paulscode/mempool-bip110) - Mempool v3.2.1 fork that visualises BIP-110 signalling and rule violations. Last updated 8 August 2026, before the BLAKE2b change.
- [Maveth/mempool-blake](https://github.com/Maveth/mempool-blake) - Thin deploy recipe for stock mempool on Knots BLAKE2b with the 800 kWU limit.
- [blakerunner (forever21.lol)](https://github.com/satoshipuzzles/blakerunner) - Synthwave block explorer plus Nostr lounge for the BLAKE2b chain.

## Mining

Sia-family BLAKE2b ASICs can mine this chain. SHA256d hardware cannot. Solo mining needs Knots 29.4.1 plus a DATUM Gateway that produces header-v2 work.

### Pools

- [AlphaPool BLAKE2b](https://knots.alphapool.tech/) - Live pool on the BLAKE2b chain with public stats, 2.5% fee.
- [CONVOY](https://github.com/CONVOYMining) - Luke Dashjr's mining venture, founded 29 August 2026 after he left OCEAN. Publishes the BLAKE2b DATUM Gateway. No public pool endpoint announced yet.
- [tides-pool](https://github.com/Maveth/tides-pool) - DATUM Prime plus TIDES payout pool server for BLAKE2b. *Testnet4 lab.*
- [c2pool](https://github.com/frstrtr/c2pool) - P2Pool rebirth in C++. Has an active BIP-110 lane with a BLAKE2b sharechain and share explorer.

### Gateways and miner software

- [CONVOY DATUM Gateway](https://github.com/CONVOYMining/datum_gateway) - The BLAKE2b and header-v2 DATUM Gateway recommended by btc-blake2b.org for mainnet solo mining. Fork of Luke Dashjr's gateway.
- [innerhat DATUM Gateway](https://github.com/innerhat-dev/datum_gateway) - Gateway fork with BLAKE2b and BLAKE2b-Sia miner support, parent of several packaged builds.
- [paulscode/datum_gateway](https://github.com/paulscode/datum_gateway) - innerhat fork with per-miner difficulty and BLAKE2b share-time fixes, used by the StartOS and Umbrel packages.
- [xorpool/datum-gateway](https://github.com/xorpool/datum-gateway) - Gateway fork adding per-miner payouts in non-pooled mode and a pool fee. Its [landing page](https://github.com/xorpool/xorpool.github.io) is a design reference, no public pool is planned.
- [cpuminer-bip110](https://github.com/Antisys/cpuminer-bip110) - CPU miner for BLAKE2b v2 headers over Stratum v1 to a DATUM Gateway. Verified against PR #359 test vectors.
- [blake2b-gpu-datum-lab](https://github.com/Maveth/blake2b-gpu-datum-lab) - CUDA lab notes for BLAKE2b pointed at DATUM and TIDES. Informational, not a product miner.
- [goldshell-config](https://github.com/Maveth/goldshell-config) - Control-plane notes for Goldshell BLAKE2b ASICs (SC Lite, HS Box): fans, temperature, auth, ports.
- [hashgg](https://github.com/paulscode/hashgg) - Expose a DATUM Gateway stratum port to the internet without port forwarding, for StartOS.
- [pickhash](https://github.com/paulscode/pickhash) - Rent BLAKE2b Siacoin hashrate from MiningRigRentals and point it at your own gateway.

### Mining guides

- [Solo mining on mainnet](https://btc-blake2b.org/mining) - Knots 29.4.1 plus CONVOY DATUM Gateway walkthrough with a working config.
- [blake2bminer.com](https://blake2bminer.com/) - What it takes to mine the chain, with a running block log and a [blog](https://blog.blake2bminer.com/).
- [Mining the BLAKE2b hardfork on testnet4](https://github.com/AcesHigh70/blake2b-testnet4) - Node, gateway and miner setup verified on testnet4 during the release candidates.
- [Knots and DATUM setup with Umbrel](https://www.youtube.com/watch?v=c5KA1Tl1nE0) - Video walkthrough.

## Node packaging

### StartOS

- [knots-rdts-startos](https://github.com/paulscode/knots-rdts-startos) - Bitcoin Knots following the RDTS chain, installable alongside the official Bitcoin service.
- [knots-blake2b-startos](https://github.com/paulscode/knots-blake2b-startos) - Knots with BLAKE2b proof-of-work. *Regtest and testnet4 only*, refuses mainnet.
- [electrs-pruned-startos](https://github.com/paulscode/electrs-pruned-startos) - Electrum server that indexes from a pruned node and follows the header-v2 chain.
- [mempool-pruned-startos](https://github.com/paulscode/mempool-pruned-startos) - Mempool explorer for a pruned node, installs alongside official Mempool.
- [mempool-bip110-startos](https://github.com/paulscode/mempool-bip110-startos) - Mempool BIP-110 fork for StartOS.
- [datum-blake2b-startos](https://github.com/paulscode/datum-blake2b-startos) - DATUM Gateway serving BLAKE2b work to Sia ASICs. *Regtest and testnet4.*
- [Maveth/datum-gateway-startos](https://github.com/Maveth/datum-gateway-startos) - DATUM Gateway packaging with the multi-output coinbaser fix, sideload.

### Umbrel

- [PaulsCode community app store](https://github.com/paulscode/umbrel-store) - Knots (BLAKE2b) 29.4.1, DATUM (BLAKE2b), mempool-pruned and Pickhash for Umbrel.
- [mempool-bip110-umbrel](https://github.com/paulscode/mempool-bip110-umbrel) - Mempool BIP-110 fork for Umbrel.
- [hashgg-umbrel](https://github.com/paulscode/hashgg-umbrel) - HashGG for Umbrel.

## Libraries and tooling

- [Kilombino/drongo](https://github.com/Kilombino/drongo) - drongo fork implementing v2 header parsing and BLAKE2b proof-of-work verification, with test fixtures.
- [Knots PR #359 test vectors](https://github.com/bitcoinknots/bitcoin/pull/359) - Header v2 and BLAKE2b hash vectors used by cpuminer-bip110 and other implementations.

## Cross-chain and replay

The two chains share history up to 961,631. A transaction signed the usual way is valid on both chains and can be replayed. Signing with `SIGHASH_UNIFIED` makes it invalid on the SHA256d chain.

**The safe way to split coins** is what the Knots release notes describe: send your coins to yourself on the BLAKE2b chain using a wallet that signs with `SIGHASH_UNIFIED` (Knots wallet or the Sparrow BLAKE2b build), wait for confirmation, and only then spend on the other chain.

**Be very wary of cross-chain swap tools.** Anything that takes your keys or funds to trade across the split with strangers deserves the same scrutiny as a new exchange. For example, the browser-based "bip110-splittor" HTLC swap DEX was written in a single day, has no reviewers, and was only tested on regtest against a pre-BLAKE2b Knots build. It is deliberately not listed here. Use unified-sighash self-sends instead.

- [bip110-replay-checker](https://github.com/TIROBITCOIN/bip110-replay-checker) - Read-only web app: enter a SHA256d-chain txid and see whether it is observed in BLAKE2b-chain blocks or mempool. Not observed does not mean replay safe.
- [Knots PR #357](https://github.com/bitcoinknots/bitcoin/pull/357) - Opt-in unified signature hash, the replay-protection rule shipped in 29.4.1.

## Specs and documentation

- [Knots PR #359](https://github.com/bitcoinknots/bitcoin/pull/359) - The hardfork pull request: header v2, BLAKE2b PoW, test vectors. [PR #385](https://github.com/bitcoinknots/bitcoin/pull/385) sets the mainnet and testnet4 parameters, [PR #358](https://github.com/bitcoinknots/bitcoin/pull/358) activates RDTS at the fork.
- [BIP-110: Reduced Data Temporary Softfork](https://bips.dev/110/) - The original RDTS proposal.
- [bitcoinknots.org/learn/2026-blake2b](https://bitcoinknots.org/learn/2026-blake2b) - Official explainer linked from the release notes.
- [btc-blake2b.org](https://btc-blake2b.org/) - Fork site. [Getting started](https://btc-blake2b.org/getting-started), [developers](https://btc-blake2b.org/developers), [FAQ](https://btc-blake2b.org/faq), [mining](https://btc-blake2b.org/mining). Source at [innerhat-dev/bitcoin-blake2b.org](https://github.com/innerhat-dev/bitcoin-blake2b.org).
- [bitcoinbip110.org](https://github.com/Bitcoin-BIP110/bitcoinbip110.github.io) - Technical reference and network observatory that keeps BIP 110, the enforcement branch, and the BLAKE2b chain terminologically distinct. Cites primary sources.
- [docs.mempool.guide](https://docs.mempool.guide/) - Guides on Knots, DATUM, datacarrier, migrating from Core, and [connecting Sparrow to Knots](https://docs.mempool.guide/page/sparrowknots.html).
- [Bitcoin Knots devwiki](https://github.com/bitcoinknots/bitcoin-devwiki) - Development wiki.
- [The Bitcoin Manual: What is the BLAKE2b hard fork?](https://thebitcoinmanual.com/articles/blake2b-hard-fork/) - Plain-language explainer.

## Community and news

- [PaulsCode forum: Knots 29.4 BIP-110 blocks](https://paulscode.com/t/knots-29-4-8-05-2026-bip-110-blocks/1240) - Long-running community thread on the fork.
- [forever21.lol](https://github.com/satoshipuzzles/blakerunner) - Nostr lounge attached to the blakerunner explorer.
- [Bitcoin BLAKE2b hard fork explained (CryptoDad Q&A)](https://www.youtube.com/watch?v=_dZQ-I2OEo4) - Video Q&A.
- [Joint statement of OCEAN and Luke Dashjr](https://www.prnewswire.com/news-releases/joint-statement-of-ocean-and-luke-dashjr-302864347.html) - Background on the OCEAN split and CONVOY.
- [crypto.news: fork faces Sept. 1 launch test](https://crypto.news/bitcoin-blake2b-fork-faces-sept-1-launch-test/) - Press coverage.

## Contributing

Pull requests welcome. Keep it to one line per entry: `[Name](url) - What it does.` Add new entries to the end of the relevant section.

Please include projects that:

- Actually follow the BLAKE2b chain today, or exist specifically to support it. Plain forks of upstream projects with no chain-specific work do not qualify.
- Are open source, or are a public service anyone can use.

Mark anything experimental or testnet-only as such, and say when a project is unmaintained. Descriptions should come from the project's own README or commits, not from press.

## License

[CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/). To the extent possible under law, the contributors have waived all copyright and related rights to this work.
