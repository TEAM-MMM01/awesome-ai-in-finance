# SlabChain

> Trade authenticated, vaulted Pokémon slabs as on-chain collectibles — without ever shipping the cardboard.

SlabChain mints a 1:1 NFT for every professionally graded Pokémon card (PSA, CGC, BGS) held in a third-party insured vault. The NFT functions as a digital warehouse receipt: holders can trade it on Solana NFT marketplaces and burn it at any time to claim physical delivery of the slab.

This repository is the founding scaffold — strategy, custody SOP, token specification, legal posture, and roadmap. **Code (mint scripts, redemption portal) lands after legal entity, vault MSA, and insurance are in place.**

## Why this exists

Slabbed Pokémon cards are an established collectibles market with a real authentication layer (PSA / CGC cert numbers, tamper-evident slabs). The pain points are:

- Cardboard moves slowly and gets damaged in transit.
- Collector liquidity is fragmented across eBay, PWCC, TCGPlayer, Yahoo Auctions JP.
- Trustless transfer is impossible — every trade requires shipping, insurance, and counterparty verification.

A vault + NFT layer collapses that friction. Trades happen in seconds; the physical asset only moves on redemption.

## What this is *not*

- **Not an investment product.** No yield, no fund, no fractionalization, no expectation of profit from the efforts of others. SlabChain serves *collectors*, not investors.
- **Not a custody-free system.** The slabs are physically held in an insured third-party vault. The NFT is a contractual claim, not a self-sovereign asset.
- **Not a grading service.** SlabChain only mints against slabs already graded by PSA, CGC, or BGS.

## Repo map

| File | Purpose |
| --- | --- |
| [STRATEGY.md](STRATEGY.md) | Business model, revenue, edge |
| [ROADMAP.md](ROADMAP.md) | Phased plan: Solana → XRPL |
| [LEGAL.md](LEGAL.md) | Collector-product legal posture (TOS, bailment, FTC) |
| [VAULTING_SOP.md](VAULTING_SOP.md) | Custody, intake, redemption procedure |
| [TOKEN_SPEC.md](TOKEN_SPEC.md) | NFT metadata schema & on-chain↔physical binding |
| [ASSETS.md](ASSETS.md) | Founding 13-card inventory |
| [docs/solana-architecture.md](docs/solana-architecture.md) | Metaplex Core mint design |
| [docs/xrpl-architecture.md](docs/xrpl-architecture.md) | XLS-20 mint design (Phase 3) |

## Status

Pre-launch. No mints exist. No vault contract is signed. The cards listed in `ASSETS.md` are owned by the founder and have not been deposited into any custody arrangement.

## License

[Apache-2.0](LICENSE)
