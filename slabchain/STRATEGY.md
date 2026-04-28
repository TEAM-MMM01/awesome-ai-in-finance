# Strategy

## One-line thesis

A vault + 1:1 NFT layer for graded Pokémon slabs lets collectors trade authenticated cardboard at the speed of a token transfer, without ever shipping the physical asset until they redeem.

## The model

### 1. Custody

All slabs are held by an insured third-party vault. Founder does not self-custody consigned inventory at any point.

Candidate vault partners:
- **PWCC Vault** — already specializes in TCG; integrated with eBay; known to the collector market.
- **Brinks Global Services** — generalist precious-asset vault; used by Courtyard.
- **Goldin Vault** — auction-house-affiliated, strong in high-end singles.

Vault MSA must include:
- Per-asset insurance binder (cert # → policy line)
- 24-hour intake photographs (front, back, slab edge)
- Tamper-evident packaging on every inbound and outbound shipment
- Right of audit and proof-of-reserves attestation

### 2. Legal entity

Wyoming or Delaware LLC. Operating agreement frames the relationship as **bailment**: the holder of the NFT is the bailor (depositor); SlabChain LLC is the bailee (custodian via the vault). The NFT *is* the bearer claim on a specific cert # in the vault.

This framing — explicitly *not* a fund, basket, or pooled investment — is what keeps SlabChain out of securities law. See [LEGAL.md](LEGAL.md) for detail.

### 3. Tokenization

| Property | Choice | Why |
| --- | --- | --- |
| Chain (v1) | Solana | Mature NFT tooling, deepest collector liquidity, low fees |
| Standard | Metaplex Core | Single account, native delegate/freeze, lowest mint cost |
| Royalty | 2.5% (best-effort) | Realistic given Magic Eden / Tensor royalty enforcement |
| Authority | 3-of-5 multisig (Squads v4) | Compromise resistance; clear governance |
| Freeze delegate | Vault company key | Allows TOS-compliant re-issuance on lost-key claims |

One slab → one NFT, ever. No fractions, no editions, no copies.

### 4. Liquidity

- Primary listing: Magic Eden (broadest reach), Tensor (sniping/serious traders).
- No AMM / fraction pool — the collectibles thesis needs price discovery per cert, not pool-priced commodity.
- Optional: a SlabChain-branded landing page that aggregates Magic Eden + Tensor listings for the inventory.

### 5. Revenue

| Stream | Rate (target) | Notes |
| --- | --- | --- |
| Intake fee | 2–3% of FMV | Charged once per slab on mint |
| Vault pass-through | At cost | Insurance + storage; no margin |
| Admin fee | 1% of FMV / yr | Covers TOS, custody coordination, audits |
| Royalty | 2–2.5% | On secondary sales (best-effort enforcement) |
| Redemption | Flat $40–$80 | Covers shipping, insurance, packing, handling |

Worked example — Pristine 10 Eevee CHR (s8b 210/184), assumed FMV $1,200:
- Intake: $30 (one-time, paid by depositor)
- Storage + insurance: ~$24/yr (pass-through)
- Admin: $12/yr
- A trade at $1,400 generates ~$35 in royalty
- A redemption costs the holder ~$60

### 6. Defensible edge

Most card-RWA platforms (Courtyard, Collector Crypt) operate on bulk, lower-grade inventory. SlabChain's edge is **curated, top-pop, slabbed Japanese moderns + select vintage** — a higher quality-per-listing thesis aimed at collectors who already understand slab provenance. Lower throughput, higher AUM-per-card, stronger brand.

## Explicitly out of scope

- Fractionalization or "fund" products.
- Pooled-asset NFTs (a single NFT representing a basket of slabs).
- Crypto-collateralized lending against slabs (introduces money-transmission and lending-license exposure).
- Self-grading or in-house authentication (we trust PSA/CGC/BGS; we do not compete with them).
- Card insurance underwriting (we pass through vault insurance; we do not underwrite).
