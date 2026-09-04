# Awesome Bitcoin BLAKE [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated list of nodes, wallets, servers, explorers, pools, tools and docs for the Bitcoin BLAKE2b chain.

On 30 August 2026, at block **961,640**, Bitcoin Knots changed proof-of-work from SHA256d to BLAKE2b ([BIP-110](https://github.com/bitcoinknots/bitcoin/pull/359)). Block headers are now 164 bytes (header v2), so most existing wallets, Electrum servers and explorers need patches to follow the chain. This list tracks what works today.

Everything here is community maintained. **Verify releases and signatures yourself.** Listing is not an endorsement.

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

| Parameter | Value |
|---|---|
| Proof-of-work | BLAKE2b-256 (header v2, 164 bytes) |
| Mainnet fork height | 961,640 (30 August 2026) |
| Testnet4 fork height | 150,308 |
| Reference implementation | Bitcoin Knots `v29.4.1.knots20260508` |
| Reduced Data Temporary Softfork (RDTS) | 800 kWU block limit until median time past 2027-09-01 |
| Replay protection | Opt-in unified sighash ([Knots PR #357](https://github.com/bitcoinknots/bitcoin/pull/357)) |
| P2P / RPC ports | 8333 / 8332 (unchanged) |

## Nodes

- [Bitcoin Knots](https://github.com/bitcoinknots/bitcoin) - Reference full node. Release `v29.4.1.knots20260508` contains the BLAKE2b hardfork ([PR #359](https://github.com/bitcoinknots/bitcoin/pull/359)).
- [Bitcoin Knots website](https://bitcoinknots.org/) - Binaries, release notes and signatures.
- [Knots release notes](https://github.com/bitcoinknots/bitcoin/blob/v29.4.1.knots20260508/doc/release-notes.md) - What changed in the hardfork release.
- [Maveth/bitcoin-pow-hf-blake2b](https://github.com/Maveth/bitcoin-pow-hf-blake2b) - Community staging tree for the BLAKE2b proof-of-work change.

## Wallets

- [Sparrow (BLAKE2b build)](https://github.com/paulscode/sparrow/releases) - Paul Lamb's Sparrow 2.5.5 built to follow the BLAKE2b chain. Only talks to a BLAKE2b Electrum server. Ships with electrs-pruned.
- [drongo (BLAKE2b)](https://github.com/paulscode/drongo) - Sparrow's Bitcoin library patched for 164-byte headers and the unified sighash.
- Shrike - Kyle Santiago's Sparrow fork, `v2.5.5-blake2b.10`. Origin of the unified opt-in sighash port. Repo link wanted, see [Contributing](#contributing).
- [Bitcoin Knots wallet](https://github.com/bitcoinknots/bitcoin) - The built-in node wallet works on the new chain without any extra software.
- [SeedSigner (paulscode)](https://github.com/paulscode/seedsigner) - Air-gapped signer fork tracking BLAKE2b-chain PSBT changes.
- [embit (paulscode)](https://github.com/paulscode/embit) - Minimal Python Bitcoin library fork used by the SeedSigner build.

## Electrum servers

- [Shulcrum](https://github.com/Kilombino/Shulcrum) - Fulcrum fork that indexes and serves chains with 164-byte headers and BLAKE2b proof-of-work.
- [electrs-pruned](https://github.com/paulscode/electrs-pruned) - electrs for a pruned node that follows the header-v2 chain. Pairs with the Sparrow BLAKE2b build.
- [blake2b-light-clients](https://github.com/Kilombino/blake2b-light-clients) - Notes on what breaks in the Electrum protocol when headers stop being 80 bytes, and what it takes to fix.

## Explorers

- [mempool.guide](https://mempool.guide/) - Mempool explorer on the BLAKE2b chain with a full Esplora API, including 164-byte headers via `/api/block/<hash>/header`.
- [mempool.kilombino.com](https://mempool.kilombino.com/) - Self-hosted mempool instance following BLAKE2b.
- [Kilombino/mempool-bip110](https://github.com/Kilombino/mempool-bip110) - Mempool fork (v3.4-dev) with RDTS "reduced data" violation detection and a node map. Self-hostable.
- [paulscode/mempool-bip110](https://github.com/paulscode/mempool-bip110) - Mempool fork for the BIP-110 chain.
- [paulscode/mempool-pruned](https://github.com/paulscode/mempool-pruned) - Mempool explorer for a pruned node via electrs-pruned.
- [Maveth/mempool-blake](https://github.com/Maveth/mempool-blake) - Thin deploy recipe for stock mempool on Knots BLAKE2b.
- [blakerunner (forever21.lol)](https://github.com/satoshipuzzles/blakerunner) - Synthwave block explorer plus Nostr lounge for the BLAKE2b chain.

## Mining

Sia-family BLAKE2b ASICs can mine this chain. SHA256d hardware cannot.

### Pools

- [OCEAN](https://ocean.xyz/) - Non-custodial pool by Luke Dashjr using DATUM. Check current BLAKE2b support before pointing hashrate.
- [AlphaPool BLAKE2b](https://knots.alphapool.tech/) - Pool with live hashrate, block and balance telemetry.
- [xorpool / xorminer.com](https://github.com/xorpool/xorpool.github.io) - Pool landing page and [datum-gateway fork](https://github.com/xorpool/datum-gateway).
- [tides-pool](https://github.com/Maveth/tides-pool) - DATUM Prime plus TIDES payout pool for BLAKE2b, testnet4 lab. Also at [Kilombino/tides-pool](https://github.com/Kilombino/tides-pool).
- [c2pool](https://github.com/frstrtr/c2pool) - Decentralized, DoS-resistant p2pool successor with BIP-110 work.

### Gateways and miner software

- [DATUM Gateway (OCEAN)](https://github.com/OCEAN-xyz/datum_gateway) - Upstream Decentralized Alternative Templates for Universal Mining. Build your own templates from your own Knots node.
- [paulscode/datum_gateway](https://github.com/paulscode/datum_gateway) - DATUM Gateway fork serving BLAKE2b work.
- [Maveth/datum_gateway-innerhat](https://github.com/Maveth/datum_gateway-innerhat) - DATUM Gateway with the `bip110-pow-v2` branch and multi-output coinbaser fix.
- [cpuminer-bip110](https://github.com/Antisys/cpuminer-bip110) - CPU miner for BLAKE2b v2 headers over Stratum v1 to a DATUM Gateway. Verified against PR #359 test vectors.
- [blake2b-gpu-datum-lab](https://github.com/Maveth/blake2b-gpu-datum-lab) - CUDA lab notes for BLAKE2b pointed at DATUM and TIDES. Informational, not a product miner.
- [goldshell-config](https://github.com/Maveth/goldshell-config) - Configuration notes for Goldshell BLAKE2b ASICs.
- [hashgg](https://github.com/paulscode/hashgg) - Expose a DATUM Gateway stratum port to the internet without port forwarding.
- [pickhash](https://github.com/paulscode/pickhash) - Automate renting BLAKE2b hashrate from MiningRigRentals.

### Mining guides

- [blake2bminer.com](https://blake2bminer.com/) - What it takes to mine the BLAKE2b chain, with a [blog](https://blog.blake2bminer.com/).
- [Solo mining on mainnet](https://btc-blake2b.org/mining) - Solo mining guide from the fork's site.
- [Knots and DATUM setup with Umbrel](https://www.youtube.com/watch?v=c5KA1Tl1nE0) - Video walkthrough.

## Node packaging

### StartOS

- [knots-blake2b-startos](https://github.com/paulscode/knots-blake2b-startos) - Bitcoin Knots with BLAKE2b PoW for StartOS 0.4. Regtest and testnet4.
- [knots-rdts-startos](https://github.com/paulscode/knots-rdts-startos) - Knots (RDTS) companion node installable alongside the official Bitcoin service.
- [knots-prerdts-startos](https://github.com/paulscode/knots-prerdts-startos) - Knots (pre-RDTS) companion node.
- [electrs-pruned-startos](https://github.com/paulscode/electrs-pruned-startos) - Electrum server that indexes from a pruned node and follows the header-v2 chain.
- [mempool-pruned-startos](https://github.com/paulscode/mempool-pruned-startos) - Mempool explorer for a pruned node, installs alongside official Mempool.
- [mempool-bip110-startos](https://github.com/paulscode/mempool-bip110-startos) - Mempool BIP-110 fork for StartOS.
- [datum-blake2b-startos](https://github.com/paulscode/datum-blake2b-startos) - DATUM Gateway serving BLAKE2b work to Sia ASICs.
- [Maveth/datum-gateway-startos](https://github.com/Maveth/datum-gateway-startos) - DATUM Gateway packaging with the multi-out coinbaser fix.
- [Start9 Bitcoin Knots](https://github.com/Start9Labs/bitcoin-knots-startos) - Official Start9 Knots package.

### Umbrel

- [paulscode/umbrel-store](https://github.com/paulscode/umbrel-store) - Community Umbrel store with BLAKE2b-chain apps.
- [mempool-bip110-umbrel](https://github.com/paulscode/mempool-bip110-umbrel) - Mempool BIP-110 fork for Umbrel.
- [hashgg-umbrel](https://github.com/paulscode/hashgg-umbrel) - HashGG for Umbrel.

### Other

- [MiniBolt Bitcoin Knots guide](https://minibolt.minibolt.info/bonus-guides/bitcoin/bitcoin-knots) - Bare-metal Knots install.

## Libraries and tooling

- [Kilombino/drongo](https://github.com/Kilombino/drongo) - Bitcoin library fork with BLAKE2b header support.
- [bitcoin-dashboard](https://github.com/Antisys/bitcoin-dashboard) - Real-time Core/Knots sync dashboard with assumeUTXO support.
- [bitcoin-twin](https://github.com/javierpmateos/bitcoin-twin) - Reproducible testbench for studying upgrades and network events against mainnet telemetry.

## Cross-chain and replay

The two chains share history up to 961,639. Transactions without the opt-in unified sighash can be replayed on both chains.

- [bip110-splittor](https://github.com/Antisys/bip110-splittor) - Atomically exchange post-fork BLAKE2b-chain funds for SHA256d-chain funds, or vice versa.
- [bip110-replay-checker](https://github.com/TIROBITCOIN/bip110-replay-checker) - Check whether a transaction has been replayed on the other chain.
- [Knots PR #357](https://github.com/bitcoinknots/bitcoin/pull/357) - Opt-in unified signature hash, the consensus rule for replay protection.

## Specs and documentation

- [BIP-110 / Knots PR #359](https://github.com/bitcoinknots/bitcoin/pull/359) - The hardfork pull request: header v2, BLAKE2b PoW, test vectors.
- [btc-blake2b.org](https://btc-blake2b.org/) - Fork site. [Getting started](https://btc-blake2b.org/getting-started), [developers](https://btc-blake2b.org/developers), [FAQ](https://btc-blake2b.org/faq), [mining](https://btc-blake2b.org/mining).
- [Bitcoin BIP110 Public Network Reference](https://github.com/Bitcoin-BIP110/bitcoinbip110.github.io) - Public network reference site.
- [docs.mempool.guide](https://docs.mempool.guide/) - Guides on Knots, DATUM, datacarrier, migrating from Core, and [connecting Sparrow to Knots](https://docs.mempool.guide/page/sparrowknots.html).
- [Bitcoin Knots devwiki](https://github.com/bitcoinknots/bitcoin-devwiki) - Development wiki.
- [The Bitcoin Manual: What is the BLAKE2b hard fork?](https://thebitcoinmanual.com/articles/blake2b-hard-fork/) - Plain-language explainer.

## Community and news

- [PaulsCode forum: Knots 29.4 BIP-110 blocks](https://paulscode.com/t/knots-29-4-8-05-2026-bip-110-blocks/1240) - Long-running community thread on the fork.
- [forever21.lol](https://github.com/satoshipuzzles/blakerunner) - Nostr lounge attached to the blakerunner explorer.
- [Bitcoin BLAKE2b hard fork explained (CryptoDad Q&A)](https://www.youtube.com/watch?v=_dZQ-I2OEo4) - Video Q&A.
- [crypto.news: fork faces Sept. 1 launch test](https://crypto.news/bitcoin-blake2b-fork-faces-sept-1-launch-test/) - Press coverage.
- [KuCoin: BLAKE2b fork chain to begin testing](https://www.kucoin.com/news/flash/bitcoin-blake2b-fork-chain-to-begin-test-on-september-1) - Press coverage.

## Contributing

Pull requests welcome. Keep it to one line per entry: `[Name](url) - What it does.` Add new entries to the end of the relevant section.

Please include projects that:

- Actually follow the BLAKE2b chain today, or exist specifically to support it.
- Are open source, or are a public service anyone can use.

Mark anything experimental or testnet-only as such. If you know the canonical repo for Shrike, please open a PR.

## License

[CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/). To the extent possible under law, the contributors have waived all copyright and related rights to this work.
