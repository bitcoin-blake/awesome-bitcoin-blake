# Awesome Bitcoin BLAKE [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated list of nodes, wallets, servers, explorers, pools, tools and docs for the Bitcoin BLAKE2b chain.

On 8 August 2026 Bitcoin Knots nodes enforcing BIP-110 (the Reduced Data Temporary Softfork, RDTS) parted from the SHA256d chain at block 961,632. That branch produced only eight blocks in three weeks. On 30 August 2026, at block **961,640**, it changed proof-of-work from SHA256d to BLAKE2b (Knots [PR #359](https://github.com/bitcoinknots/bitcoin/pull/359)). Block headers are now 164 bytes (header v2), so most existing wallets, Electrum servers and explorers need patches to follow the chain. This list tracks what works today.

Everything here is community maintained. **Verify releases and signatures yourself.** Listing is not an endorsement. Flags in *italics* mark experimental, testnet-only, closed-source or unmaintained entries.

## Contents

- [Chain parameters](#chain-parameters)
- [Nodes](#nodes)
- [Wallets](#wallets)
- [Electrum servers](#electrum-servers)
- [Explorers](#explorers)
- [Mining](#mining)
- [Node packaging](#node-packaging)
- [Hardware signers](#hardware-signers)
- [Libraries](#libraries)
- [Lightning](#lightning)
- [Cross-chain and replay](#cross-chain-and-replay)
- [Specs and documentation](#specs-and-documentation)
- [Community and news](#community-and-news)
- [Contributing](#contributing)

## Chain parameters

Read from `src/kernel/chainparams.cpp`, `src/kernel/chainparamsbase.cpp` and `src/consensus/params.h` at Knots tag `v29.4.1.knots20260508`. PRs #357, #358, #359 and #385 are in the tag even though they still show as open on GitHub, so check the source, not the PR state.

| Parameter | Mainnet | Testnet4 |
|---|---|---|
| Proof-of-work | BLAKE2b-256, header v2 (164 bytes), target shift 22 | same, target shift 20 |
| Last shared block with SHA256d chain | 961,631 | |
| First RDTS-only block | 961,632 (8 August 2026) | |
| First BLAKE2b block | 961,640 (30 August 2026), hash `0000000000000050c1e5f69672f459293be14f46e5a494e7a8c8541396f18eeb` | 150,308 |
| Headline committed at the fork | `8-30 NYPost Deride And Conquer` | |
| RDTS block weight limit | 800 kWU from 961,640 until median time past 2027-09-01 00:00 UTC | until 2026-10-13 15:00 UTC |
| Replay protection | Opt-in `SIGHASH_UNIFIED` flag 0x20 per signature, so `SIGHASH_ALL` becomes 0x21. Knots [PR #357](https://github.com/bitcoinknots/bitcoin/pull/357), shipped in 29.4.1 | same |
| P2P magic | `f9 be b4 d9` (unchanged) | `1c 16 3f 28` |
| P2P / RPC ports | 8333 / 8332 (unchanged) | 48333 / 48332 |
| Address prefixes | unchanged (`1`, `3`, `bc1`) | unchanged (`tb1`) |
| DNS seeds marked as supporting the hardfork | `dnsseed.bitcoin.dashjr-list-of-p2p-nodes.us`, `seed.bitcoin.haf.ovh` (the other seven upstream seeds remain listed) | `seed.testnet-bitcoin.haf.ovh` |
| Reference implementation | Bitcoin Knots `v29.4.1.knots20260508` | same |

## Nodes

- [Bitcoin Knots](https://github.com/bitcoinknots/bitcoin) - Reference full node. Release `v29.4.1.knots20260508` carries the BLAKE2b hardfork, flag-day RDTS and the unified opt-in sighash. The built-in wallet works on the new chain and signs with the unified sighash.
- [Bitcoin Knots website](https://bitcoinknots.org/) - Binaries and signatures.
- [Knots release notes](https://github.com/bitcoinknots/bitcoin/blob/v29.4.1.knots20260508/doc/release-notes.md) - What changed in the hardfork release, including the new header fields exposed over RPC.
- [Knots Guix attestations](https://github.com/bitcoinknots/guix.sigs) - Reproducible build signatures for the release.

## Wallets

- [Sparrow (BLAKE2b build)](https://github.com/paulscode/sparrow/releases) - Paul Lamb's Sparrow 2.5.5 on the `blake2b` branch. Follows the BLAKE2b chain and signs with the unified opt-in sighash for replay protection. Needs electrs-pruned, which implements the header extension this build expects.
- [Shrike](https://github.com/privkeyio/shrike) - Sparrow fork that follows the BLAKE2b chain and signs with the unified opt-in sighash, with per-keystore replay-protection marking for hardware and watch-only signers. Continues the archived AcesHigh70 work. *Unaudited, binaries unsigned.*
- [Bitcoin Knots wallet](https://github.com/bitcoinknots/bitcoin) - The node's own wallet. Supports `SIGHASH_UNIFIED` from the command line and GUI.
- [pyblock-blake2b](https://github.com/AstrolexisAI/pyblock-blake2b) - Native Android self-custody wallet for the BLAKE2b chain, on-device keys via bdk-ffi, with an unlock step for replay-locked coins. *New, single vendor, unaudited.*

There is **no Electrum wallet fork** for this chain as of 4 September 2026. Electrum verifies 80-byte SHA256d headers itself, so it needs both a protocol extension and a BLAKE2b-aware client. None of the recent forks of spesmilo/electrum contain such work. Treat anything calling itself "Electrum for BLAKE2b" as unverified.

## Electrum servers

- [electrs-pruned](https://github.com/paulscode/electrs-pruned) - electrs for a pruned node that follows the header-v2 chain and serves the extension the Sparrow BLAKE2b build uses. *Author reports the mainnet run unverified as of late August; testnet4 verified.*
- [Shulcrum](https://github.com/Kilombino/Shulcrum) - Fulcrum fork that indexes and serves chains with 164-byte headers and BLAKE2b proof-of-work, via a `blockchain.pow_algorithms` protocol 1.7 extension. *Verified on testnet4 only per its docs; last push 24 August.*
- [privkeyio/Fulcrum](https://github.com/privkeyio/Fulcrum) - Fulcrum 2.1.2 fork that parses the 164-byte v2 header and uses its BLAKE2b hash as the block id, keyed off the header's top version bit. Release `2.1.2-blake2b`. Needs an index rebuild. *Unaudited.*
- [jasonsopko/electrs](https://github.com/jasonsopko/electrs) - romanz/electrs 0.10.10 plus 164-byte header v2 parsing, tag `v0.10.10-blake2b.1`. Needs Knots 29.4.1 or later. Ships a check script that verifies the 80 to 164 byte transition at 961,640.
- [blake2b-light-clients](https://github.com/Kilombino/blake2b-light-clients) - Notes on what breaks in the Electrum protocol when headers stop being 80 bytes, and what it takes to fix.

## Explorers

- [mempool.guide](https://mempool.guide/) - Mempool explorer on the BLAKE2b chain with a full Esplora API, including 164-byte headers via `/api/block/<hash>/header`.
- [mempool.kilombino.com](https://mempool.kilombino.com/) - Self-hosted mempool instance following the BLAKE2b chain, with daily pool tagging of BLAKE2b miners.
- [mempool.maveth.ca](https://mempool.maveth.ca/) - Self-hosted mempool instance following the BLAKE2b chain.
- [Retropex/mempool](https://github.com/Retropex/mempool) - The mempool.guide codebase: BLAKE2b header support and `SIGHASH_UNIFIED` display since 30 August. Self-hostable.
- [jasonsopko/mempool](https://github.com/jasonsopko/mempool) - Retropex mempool on a `knots-blake2b` branch with an operator note, fee thresholds scaled to the 800 kWU limit, and a chain-tips fix.
- [Kilombino/mempool-bip110](https://github.com/Kilombino/mempool-bip110) - Mempool fork (Retropex lineage, v3.4-dev) with RDTS "reduced data" violation detection and a node map. Self-hostable.
- [paulscode/mempool-pruned](https://github.com/paulscode/mempool-pruned) - Mempool v3.3.1 for a pruned node via electrs-pruned, handles 164-byte headers.
- [paulscode/mempool-bip110](https://github.com/paulscode/mempool-bip110) - Mempool v3.2.1 fork that visualises BIP-110 signalling and rule violations. Last updated 8 August 2026, before the BLAKE2b change. StartOS and Umbrel packaging in the same account.
- [Maveth/mempool-blake](https://github.com/Maveth/mempool-blake) - Thin deploy recipe for stock mempool on Knots BLAKE2b with the 800 kWU limit.
- [blakerunner (forever21.lol)](https://github.com/satoshipuzzles/blakerunner) - Synthwave block explorer plus Nostr lounge. Explorer data is proxied from mempool.guide. *Holds a Nostr key in the browser that doubles as a Bitcoin key and signs on-chain spends without `SIGHASH_UNIFIED`. Do not fund it.*

## Mining

Sia-family BLAKE2b ASICs mine this chain, or a CPU or GPU at trivial hashrate. SHA256d hardware cannot. Solo mining needs Knots 29.4.1 plus a DATUM Gateway that produces header-v2 work.

### Pools

- [AlphaPool BLAKE2b](https://knots.alphapool.tech/) - Live pool on the BLAKE2b chain with public stats. PPLNS, 2.5% fee, balances held until maturity. *Closed-source pool software.*
- [Xor Pool](https://xorpool.com/) - Non-custodial pool where each block's coinbase pays the rig that found it. 1.5% fee, `stratum.xorpool.com:23334`. Gateway source at [xorpool/datum-gateway](https://github.com/xorpool/datum-gateway).
- [CONVOY](https://github.com/CONVOYMining) - Luke Dashjr's mining venture, founded 29 August 2026 after he left OCEAN. Its gateway defaults to the beta pool `datum-beta1.mine.convoy.xyz` with pooled mining on. No fee or payout terms published yet and convoy.xyz is a placeholder. Solo miners must clear `pool_host` and set `pooled_mining_only` to false.
- [RIPTIDE (tides-pool)](https://github.com/Maveth/tides-pool) - DATUM Prime plus TIDES payout pool server. Runs a mainnet BLAKE2b instance at [tides.maveth.ca](https://tides.maveth.ca/) and a testnet4 lab. *Experimental.*
- [gridpool-blake2b](https://github.com/gridlabs-science/gridpool-blake2b) - Decentralised reward-sharing protocol for sovereign miners without a custodial pool wallet, with a policy-bound DATUM template scheduler. StartOS and Umbrel packages in the same account. *Experimental, packages pin a release-candidate node.*
- [c2pool](https://github.com/frstrtr/c2pool) - P2Pool rebirth in C++ with a BIP-110 lane, BLAKE2b sharechain and share explorer. Live sharechain at [bip110.voidbind.com](https://bip110.voidbind.com/), 0.1% author donation. *Experimental.*

### Gateways and miner software

- [CONVOY DATUM Gateway](https://github.com/CONVOYMining/datum_gateway) - The BLAKE2b and header-v2 DATUM Gateway recommended by btc-blake2b.org for mainnet solo mining. Fork of Luke Dashjr's gateway. Use commit `56c31f4` or later, earlier builds reported false block-found and high-hash errors.
- [luke-jr/datum_gateway](https://github.com/luke-jr/datum_gateway) - Upstream DATUM Gateway, which merged the `blake2b` branch and rebranded for CONVOY on 1 September. Hides the network target from BLAKE2b hashers.
- [ratum](https://github.com/iohzrd/ratum) - Rust reimplementation of DATUM. The gateway serves v2 headers to BLAKE2b hardware over the Siacoin dialect of Stratum v1, and RATUM Prime is a DATUM pool server with a stats page and an operator fee of at most 1%. *New.*
- [iohzrd/datum_gateway](https://github.com/iohzrd/datum_gateway) - C gateway fork on the `blake2b` branch that mines the BLAKE2b hardfork and nothing else, and enforces RDTS coinbase limits when the node reports `reduced_data`.
- [Maveth/datum_gateway](https://github.com/Maveth/datum_gateway/tree/bip110-pow-v2) - BLAKE2b-only gateway, solo by default, that tides-pool points at. Recent fix for high-hash rejections.
- [innerhat DATUM Gateway](https://github.com/innerhat-dev/datum_gateway) - Gateway fork with BLAKE2b and BLAKE2b-Sia miner support, parent of several packaged builds.
- [paulscode/datum_gateway](https://github.com/paulscode/datum_gateway) - innerhat fork with per-miner difficulty and BLAKE2b share-time fixes, used by the StartOS and Umbrel packages.
- [cpuminer-bip110](https://github.com/Antisys/cpuminer-bip110) - CPU miner for BLAKE2b v2 headers over Stratum v1 to a DATUM Gateway. Author reports it verified against the PR #359 test vectors.
- [Blake2bCudaMiner](https://github.com/tomek2150/Blake2bCudaMiner) - GPL-3.0 CUDA miner for the chain with a built-in Stratum v1 client and a solo stratum proxy. *New, hashrate claims unverified.*
- [blake2b-gpu-datum-lab](https://github.com/Maveth/blake2b-gpu-datum-lab) - CUDA lab notes for BLAKE2b pointed at DATUM and TIDES. Informational, not a product miner.
- [goldshell-config](https://github.com/Maveth/goldshell-config) - Control-plane notes for Goldshell BLAKE2b ASICs (SC Lite, HS Box): fans, temperature, auth, ports.
- [hashgg](https://github.com/paulscode/hashgg) - Expose a DATUM Gateway stratum port to the internet without port forwarding. The Companion variant pairs with the BLAKE2b gateway on StartOS.
- [pickhash](https://github.com/paulscode/pickhash) - Rent BLAKE2b Siacoin hashrate from MiningRigRentals and point it at your own gateway. Spends money through your MiningRigRentals API key. *Pre-1.0, experimental.*

### Mining guides

- [Miners and DATUM](https://btc-blake2b.org/miners) - Why a Knots node plus DATUM Gateway is the mining setup, which hardware works (Antminer A3, Goldshell SC5 Pro and SC Lite and other Sia-style hashers), and the 800 kWU template limit.
- [Solo mining on mainnet](https://btc-blake2b.org/mining) - Knots 29.4.1 plus CONVOY DATUM Gateway walkthrough with a working config and the pitfalls of CONVOY's pooled defaults.
- [blake2bminer.com](https://blake2bminer.com/) - What it takes to mine the chain, with a running block log and a [blog](https://blog.blake2bminer.com/).
- [Knots BLAKE2b and DATUM on Umbrel](https://regenerous.github.io/bitcoin-blake2b-datum-umbrel-guide/) - One-page guide to running Knots with BLAKE2b on Umbrel and connecting a miner through a local DATUM gateway. [Source](https://github.com/regenerous/bitcoin-blake2b-datum-umbrel-guide).

## Node packaging

### StartOS

- [knots-blake2b-startos](https://github.com/paulscode/knots-blake2b-startos) - Companion Knots 29.4.1 (pruned) that follows mainnet BLAKE2b by default, with regtest and testnet4 selectable. The repo README lags the commits.
- [datum-blake2b-startos](https://github.com/paulscode/datum-blake2b-startos) - DATUM Gateway serving BLAKE2b work to Sia ASICs. Follows whichever chain the companion node is on. Solo only. *Experimental.*
- [Retropex/datum-gateway-startos](https://github.com/Retropex/datum-gateway-startos) - Léo Haf's BLAKE2b DATUM Gateway packaging, the base of the other StartOS gateway packages.
- [Maveth/datum-gateway-startos](https://github.com/Maveth/datum-gateway-startos) - Retropex packaging plus a multi-output coinbaser fix and mainnet coinbaser UI, sideload.
- [electrs-pruned-startos](https://github.com/paulscode/electrs-pruned-startos) - Electrum server that indexes from a pruned node and follows the header-v2 chain.
- [mempool-pruned-startos](https://github.com/paulscode/mempool-pruned-startos) - Mempool explorer for a pruned node, installs alongside official Mempool.
- [privkeyio/fulcrum-startos](https://github.com/privkeyio/fulcrum-startos) - The Fulcrum BLAKE2b fork packaged as a StartOS flavour. Rebuilds the index for 164-byte header records.

Not yet on the chain: Start9's official Knots package and paulscode's knots-rdts and knots-prerdts packages ship Knots 29.4 without BLAKE2b and stall at block 961,639. Start9's registry has de-listed its Knots branch, so BLAKE2b Knots on StartOS is sideload only.

### Umbrel

- [Umbrel Bitcoin Node (Knots)](https://github.com/Retropex/umbrel-bitcoin) - The official Umbrel Bitcoin app, powered by Bitcoin Knots, updated to `v29.4.1.knots20260508` on 2 September.
- [PaulsCode community app store](https://github.com/paulscode/umbrel-store) - Knots (BLAKE2b) 29.4.1, DATUM (BLAKE2b), mempool-pruned and Pickhash for Umbrel.
- [Retropex/docker-electrs](https://github.com/Retropex/docker-electrs) - Umbrel-lineage electrs container switched to the jasonsopko BLAKE2b build.

### Docker and distributions

- [bitcoin-knots-docker](https://github.com/rerrl/bitcoin-knots-docker) - Knots 29.4.1, the Fulcrum BLAKE2b fork and Tor in one compose file, with GPG-verified downloads and an optional hidden service.
- [bitcoinknots/packaging](https://github.com/bitcoinknots/packaging) - Official Debian packaging, with the RDTS consent prompt updated for BLAKE2b.

## Hardware signers

All from the privkey.io team behind Shrike. Each signs inputs that declare hash type 0x21 with the fork's unified message and shows the hash type on the approval screen. *None are audited, and the firmware builds are signed with published development keys, so devices will warn.*

- [privkeyio/seedsigner](https://github.com/privkeyio/seedsigner) - SeedSigner with unified-sighash signing, multisig and taproot tests. OS image at [privkeyio/seedsigner-os](https://github.com/privkeyio/seedsigner-os).
- [privkeyio/cc-firmware](https://github.com/privkeyio/cc-firmware) - Coldcard firmware fork.
- [privkeyio/trezor-firmware](https://github.com/privkeyio/trezor-firmware) - Trezor firmware fork for all models, with a `unified_sighash` input field and capability flag.
- [privkeyio/lark](https://github.com/privkeyio/lark) - Sparrow's USB hardware-wallet library with the unified sighash on the Trezor path. Refuses the opt-in when the device cannot honour it.

## Libraries

- [paulscode/drongo](https://github.com/paulscode/drongo) - Sparrow's Java Bitcoin library, patched to read variable-length headers and to produce and verify the unified sighash.
- [privkeyio/drongo](https://github.com/privkeyio/drongo) - drongo with v2 header parsing, BLAKE2b proof-of-work verification and the unified sighash. The library under Shrike.
- [privkeyio/embit](https://github.com/privkeyio/embit) - embit with `sighash_unified()` on transactions and streaming PSBT views, plus hash-type negotiation in signing. Used by the SeedSigner build.

## Lightning

- [dln-node-knots](https://github.com/DarkWebDivingClub/dln-node-knots) - ldk-node Lightning node for the BLAKE2b chain, built on header-aware `rust-bitcoin-knots` and `rust-lightning-knots` crates behind a `blake2b` feature. *Regtest and end-to-end tests only so far.*

## Cross-chain and replay

The two chains share history up to 961,631. A transaction signed the usual way is valid on both chains and can be replayed. Signing with `SIGHASH_UNIFIED` makes it invalid on the SHA256d chain.

**The safe way to split coins** is what the Knots release notes describe: send your coins to yourself on the BLAKE2b chain using a wallet that signs with `SIGHASH_UNIFIED` (Knots wallet, the Sparrow BLAKE2b build, or Shrike), wait for confirmation, and only then spend on the other chain.

**Be very wary of cross-chain swap tools.** Anything that takes your keys or funds to trade across the split with strangers deserves the same scrutiny as a new exchange. For example, bip110-splittor (single-author Taproot HTLC swaps over a Nostr orderbook) and shaswap (HTLC swaps through a coordinator) both exist, have no external review and no track record on mainnet, and are deliberately not listed. Use unified-sighash self-sends instead.

- [bip110-replay-checker](https://github.com/TIROBITCOIN/bip110-replay-checker) - Read-only web app: enter a SHA256d-chain txid and see whether it is observed in BLAKE2b-chain blocks or mempool. Not observed does not mean replay safe. *Unmaintained since 24 August, default explorer backend is dead, Korean-only README.*

## Specs and documentation

- [Knots PR #359](https://github.com/bitcoinknots/bitcoin/pull/359) - The hardfork pull request: header v2, BLAKE2b PoW, test vectors. [PR #385](https://github.com/bitcoinknots/bitcoin/pull/385) sets the mainnet and testnet4 parameters, [PR #358](https://github.com/bitcoinknots/bitcoin/pull/358) activates RDTS at the fork, [PR #357](https://github.com/bitcoinknots/bitcoin/pull/357) is the unified sighash.
- [BIP-110: Reduced Data Temporary Softfork](https://bips.dev/110/) - The original RDTS proposal.
- [BKIP-0001: The BLAKE2b hardfork](https://github.com/DarkWebDivingClub/bkips) - Written spec of the v2 header and unified sighash, checked against the PR test vectors. Draft, and it says so: where Knots and the document disagree, Knots is correct.
- [btc-blake2b.org](https://btc-blake2b.org/) - Community site for the fork, also served as bitcoin-blake2b.org and reached via the bitcoinknots.org/learn/2026-blake2b link in the release notes. Pages for [getting started](https://btc-blake2b.org/getting-started), [how it works](https://btc-blake2b.org/how-it-works), [what you need to know](https://btc-blake2b.org/you-need-to-know), [node operators](https://btc-blake2b.org/nodes), [miners and DATUM](https://btc-blake2b.org/miners), [solo mining](https://btc-blake2b.org/mining), [developers](https://btc-blake2b.org/developers) and [FAQ](https://btc-blake2b.org/faq). Source at [innerhat-dev/bitcoin-blake2b.org](https://github.com/innerhat-dev/bitcoin-blake2b.org).
- [bitcoinbip110.org](https://github.com/Bitcoin-BIP110/bitcoinbip110.github.io) - Technical reference and network observatory that keeps BIP 110, the enforcement branch, and the BLAKE2b chain terminologically distinct. Cites primary sources.
- [The Bitcoin Manual: What is the BLAKE2b hard fork?](https://thebitcoinmanual.com/articles/blake2b-hard-fork/) - Plain-language explainer written 17 August 2026, before activation.

## Community and news

- [PaulsCode forum: Knots 29.4 BIP-110 blocks](https://paulscode.com/t/knots-29-4-8-05-2026-bip-110-blocks/1240) - Support thread, started 30 August, on the Start9 and Umbrel Knots packages across the fork.
- [Bitcoin BLAKE2b hard fork explained (CryptoDad Q&A)](https://www.youtube.com/watch?v=_dZQ-I2OEo4) - Pre-activation video Q&A from 29 August.
- [Joint statement of OCEAN and Luke Dashjr](https://www.prnewswire.com/news-releases/joint-statement-of-ocean-and-luke-dashjr-302864347.html) - Primary source on the OCEAN separation and CONVOY, 29 August.

## Contributing

Pull requests welcome. Keep it to one line per entry: `[Name](url) - What it does.` Add new entries to the end of the relevant section.

Please include projects that:

- Actually follow the BLAKE2b chain today, or exist specifically to support it. Plain forks of upstream projects with no chain-specific work do not qualify.
- Are open source, or are a public service anyone can use. Closed-source services get an *italic* flag.

Mark anything experimental or testnet-only as such, and say when a project is unmaintained. Descriptions should come from the project's own README or commits, not from press.

## License

[CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/). To the extent possible under law, the contributors have waived all copyright and related rights to this work.
