# Token Specification

## Solana (Phase 1)

### Standard

**Metaplex Core** (formerly Metaplex Asset Standard / MPL-Core).

Why Core, not legacy Metadata Token Standard or cNFT:
- Single account per asset → lower rent + simpler indexing.
- Native plugins (Freeze Delegate, Permanent Freeze Delegate, Royalty enforcement).
- First-class delegation primitives — supports the redemption flow without custom programs.
- cNFTs are inappropriate for high-value singles in v1: Merkle proof footprint is fragile under legal-evidence scrutiny, and cNFT-to-NFT decompression adds a step that confuses non-technical users.

### Asset metadata schema

```jsonc
{
  "name": "Eevee — VMAX Climax 210/184 CHR (CGC Pristine 10)",
  "symbol": "SLAB",
  "description": "Bailment receipt for a CGC Pristine 10 graded Pokémon card. Holder may redeem the physical slab at any time per SlabChain Terms of Service.",
  "image": "ipfs://<bafy-front-cid>",
  "external_url": "https://slabchain.xyz/asset/<cert>",
  "animation_url": null,
  "attributes": [
    { "trait_type": "Card",         "value": "Eevee" },
    { "trait_type": "Set",          "value": "VMAX Climax (s8b) — Japanese" },
    { "trait_type": "Number",       "value": "210/184" },
    { "trait_type": "Rarity",       "value": "Character Rare Holo (CHR)" },
    { "trait_type": "Year",         "value": "2021" },
    { "trait_type": "Language",     "value": "Japanese" },
    { "trait_type": "Grader",       "value": "CGC" },
    { "trait_type": "Grade",        "value": "Pristine 10" },
    { "trait_type": "Cert",         "value": "6098823066" },
    { "trait_type": "Vault",        "value": "PWCC-VAULT-OR-A" },
    { "trait_type": "Vault Region", "value": "US" }
  ],
  "properties": {
    "category": "image",
    "files": [
      { "uri": "ipfs://<bafy-front-cid>",   "type": "image/jpeg" },
      { "uri": "ipfs://<bafy-back-cid>",    "type": "image/jpeg" },
      { "uri": "ipfs://<bafy-edge-cid>",    "type": "image/jpeg" }
    ]
  },
  "slabchain": {
    "spec_version": "1.0.0",
    "vault_attestation_hash": "0x<sha256-of-signed-pdf>",
    "vault_attestation_uri":  "ipfs://<bafy-attestation-cid>",
    "redemption_uri":         "https://slabchain.xyz/redeem",
    "rights_uri":             "https://slabchain.xyz/terms",
    "intake_date":            "2025-12-XX",
    "declared_value_usd":     500
  }
}
```

### Plugins applied at mint

| Plugin | Configuration | Purpose |
| --- | --- | --- |
| Royalties | 250 bps, basisPoints | 2.5% creator royalty (best-effort enforcement on ME/Tensor) |
| Permanent Freeze Delegate | SlabChain multisig | Allows TOS-compliant freeze on confirmed-fraud holdings or court-ordered intervention; **never used to censor legitimate trades** |
| Update Delegate | SlabChain multisig | Lets the company update vault location field if the slab is moved between vaults |

### Freeze policy

Permanent Freeze Delegate is a powerful primitive and a real liability if abused. Hard rules:

- Freeze is invoked **only** in two scenarios:
  1. Court order, subpoena, or law-enforcement request with proper jurisdiction.
  2. Confirmed-fraud holdings (e.g., NFT was minted from a slab later proven to have a forged cert # — extremely rare, but the redemption fails the cert match in step 5 of the SOP).
- Every freeze action is published to a public on-chain log with reason code.
- Freeze never used for "policy" reasons, marketplace disputes, or regulatory soft requests without a court order.

### One slab → one NFT

Hard invariant. The mint authority refuses to mint a second NFT against an existing cert # under any circumstance. Re-mints (e.g., if a buyer loses keys) require a court-ordered or contractually-permitted burn of the lost NFT first; the cert # is not eligible for re-mint until the prior NFT is provably burned or frozen + voided.

### Burn-and-redeem flow

```
1. Holder calls `redeem_initiate(asset_id)` on SlabChain redemption portal
2. Portal returns a burn transaction targeting the Core asset
3. Holder signs + submits burn tx via wallet
4. Solana indexer detects burn, posts redemption ticket to backend
5. KYC step (Persona)
6. SlabChain ops triggers vault pull per VAULTING_SOP §"Redemption procedure"
7. Vault ships; tracking + delivery confirmation closes the ticket
```

The asset is burned, not transferred to a "redemption wallet" — full burn ensures no orphan tokens persist that could be confused with an active claim.

## XRPL (Phase 3)

### Standard

**XLS-20** (native NFT object on XRPL ledger) for the NFT itself. Optional trading pools via **XLS-30** AMM.

### Issuance model

Each Phase 3+ slab is minted as an **independent XRPL XLS-20 NFT**, backed by the same physical slab as a parallel-but-equivalent claim. **Not a bridged Solana token.** Reasons:

- Bridging a single asset across two chains creates ambiguity about which token is canonical at redemption.
- XRPL's NFT model has different transfer semantics (issuer can configure transfer fees, no separate marketplace contract); independent issuance lets us tune those per chain.
- Independent issuance is legally cleaner — each chain's NFT is its own bailment receipt under TOS, distinguishable in the event of a smart-contract failure on the other chain.

**Important:** at any moment, a given cert # has **either one Solana NFT or one XRPL NFT outstanding, never both.** Switching chains requires burn-and-remint via SlabChain ops, with a 7-day cooldown to prevent bridging exploits.

### XRPL metadata

XRPL NFTs use a 256-bit URI field. Point it at IPFS to a JSON document with the same schema as the Solana NFT (sans the Solana-specific `properties` block):

```jsonc
{
  "name": "...",
  "description": "...",
  "image": "ipfs://...",
  "attributes": [...],
  "slabchain": { ... }
}
```

### Trading

- **Native XRPL DEX** for primary listing (no smart contract; built-in offer book).
- **XLS-30 AMM** considered only after XRPL inventory is large enough (50+ slabs) for pool depth to make sense.
- **RLUSD** as a settlement currency option for international + compliance-conscious buyers.

### Royalty

XRPL supports a transfer fee on NFTokenMint (up to 50%, but practical setting is 2–2.5% to match Solana). Set at mint, immutable per token.

## Versioning

The `slabchain.spec_version` field in metadata follows semver. Breaking changes (e.g., schema rename, new required field) bump the major version. The redemption portal must support all spec versions ever issued.

`1.0.0` is the launch spec. Bump to `1.1.0` when adding XRPL fields, `2.0.0` only if the schema breaks for legacy holders.
