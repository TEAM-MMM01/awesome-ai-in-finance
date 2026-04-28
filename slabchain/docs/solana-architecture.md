# Solana Architecture

Phase 1 mint and redemption design. Code does not yet exist; this is the spec.

## Stack

| Layer | Choice |
| --- | --- |
| NFT standard | Metaplex Core |
| Mint authority | Squads v4 multisig (3-of-5) |
| Indexer | Helius DAS API (NFT search), Triton or Helius RPC for txs |
| Storage (images, attestation PDFs) | IPFS via web3.storage or NFT.Storage; pinned to a paid pinning service for durability |
| Frontend | Next.js (App Router), Solana Wallet Adapter, Tailwind |
| KYC | Persona (or Sumsub) — invoked only at redemption |
| Backend | Node.js (NestJS or Hono); PostgreSQL for redemption tickets |
| Marketplaces | Magic Eden + Tensor (no proprietary marketplace) |

## Mint flow

```
intake completed at vault
        │
        ▼
SlabChain ops API: POST /mint
  • cert#, set, grade, declared_value, attestation_uri, photos[]
        │
        ▼
backend assembles metadata JSON (per TOKEN_SPEC.md)
backend uploads images + attestation PDF to IPFS
backend uploads metadata JSON to IPFS
        │
        ▼
backend builds Metaplex Core CreateV1 instruction
  • plugins: Royalties (250 bps), Permanent Freeze Delegate (multisig),
             Update Delegate (multisig)
  • owner = consignor wallet (or company holding wallet for outside intake)
        │
        ▼
3-of-5 multisig signs via Squads UI
        │
        ▼
tx submitted; Helius indexer picks up new asset
        │
        ▼
SlabChain DB row: { cert#, asset_id, owner, intake_attestation_hash, status: ACTIVE }
        │
        ▼
listing flow (manual on Magic Eden / Tensor or via their APIs)
```

## Redemption flow

```
holder clicks "Redeem" on slabchain.xyz
        │
        ▼
portal: GET /redeem/quote?asset_id=...
  • returns burn instruction, KYC requirement, est. timeline
        │
        ▼
holder signs burn tx (Metaplex Core BurnV1)
        │
        ▼
indexer detects burn → backend opens redemption ticket
        │
        ▼
portal redirects to Persona for KYC + sanctions
        │
        ▼
ticket → vault via SOP §"Redemption procedure"
        │
        ▼
delivery confirmation closes ticket; archive on-chain log entry
```

## Key Solana decisions

### Why Core, not legacy NFTs

- Lower rent: ~0.0015 SOL per asset vs ~0.012 SOL for legacy.
- Single-account model: easier to reason about, cheaper to read.
- First-class Permanent Freeze + Update Delegate → no custom program needed for the freeze invariant.

### Why not cNFTs (compressed NFTs)?

- Merkle-proof requirement adds an indexer dependency for every action; if the indexer is down, the holder cannot prove ownership at redemption time.
- Legal evidence: a compressed proof is harder to present in court than an on-chain account state.
- For high-value singles, the cost savings of cNFTs (~$0.0001 per mint vs ~$0.50) are immaterial relative to the slab's value.

cNFTs may make sense for a future Phase 4 "bulk slab" tier (raw cards graded ≤ PSA 7, ungraded slabs, etc.) — separate brand line.

### Royalty enforcement

Magic Eden honors creator royalties optionally; Tensor does the same. Set 2.5% as a target with awareness that ~50–80% of secondary trades may pay nothing. This is fine for the model — primary intake fees + redemption fees are the durable revenue, royalty is a nice-to-have.

## Indexer + RPC

- **Helius DAS API** for asset queries (own/owned, search by cert in attributes).
- **Helius standard RPC** with paid plan for tx submission.
- Backup RPC: Triton, QuickNode. **Never** rely on a single RPC provider for mint/burn operations.

## Backend services

- `/mint` — internal-only, called by ops after intake completion. Builds and submits via multisig.
- `/redeem/quote` — public, returns burn instruction + KYC requirement.
- `/redeem/start` — public, opens a redemption ticket after burn detection.
- `/redeem/kyc-callback` — Persona webhook handler.
- `/redeem/status/:ticket_id` — public, holder-scoped status view.
- `/proof-of-reserves/latest` — public, returns latest signed attestation hash.

## Database schema (Postgres, sketched)

```sql
CREATE TABLE assets (
  asset_id            VARCHAR PRIMARY KEY,        -- Metaplex Core asset pubkey
  cert_number         VARCHAR NOT NULL UNIQUE,
  grader              VARCHAR NOT NULL,           -- 'PSA' | 'CGC' | 'BGS'
  grade               VARCHAR NOT NULL,
  set_name            VARCHAR NOT NULL,
  card_number         VARCHAR NOT NULL,
  language            VARCHAR NOT NULL,
  declared_value_usd  INTEGER NOT NULL,
  intake_date         DATE NOT NULL,
  vault_location      VARCHAR NOT NULL,
  attestation_hash    VARCHAR NOT NULL,
  attestation_uri     VARCHAR NOT NULL,
  status              VARCHAR NOT NULL,           -- 'ACTIVE' | 'REDEEMING' | 'REDEEMED' | 'FROZEN'
  created_at          TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE redemption_tickets (
  ticket_id           UUID PRIMARY KEY,
  asset_id            VARCHAR NOT NULL REFERENCES assets(asset_id),
  burn_signature      VARCHAR NOT NULL UNIQUE,
  holder_wallet       VARCHAR NOT NULL,
  kyc_status          VARCHAR NOT NULL,           -- 'PENDING' | 'CLEARED' | 'FAILED'
  shipping_address    JSONB,
  carrier_tracking    VARCHAR,
  status              VARCHAR NOT NULL,           -- 'OPEN' | 'KYC' | 'PULLED' | 'SHIPPED' | 'DELIVERED' | 'DISPUTED'
  opened_at           TIMESTAMPTZ DEFAULT now(),
  closed_at           TIMESTAMPTZ
);
```

## Security posture

- Mint authority: hardware-backed multisig only.
- Backend service keys for Helius / Persona: AWS Secrets Manager or Doppler, rotated quarterly.
- DB access: read-only role for ops dashboard; full role only via signed admin sessions.
- All redemption photos + attestation PDFs replicated to two separate cloud regions and one cold archive.

## Failure scenarios

| Scenario | Detection | Mitigation |
| --- | --- | --- |
| RPC outage | Health check on tx submission | Failover to backup RPC |
| Helius indexer outage | DAS query failure | Fallback to direct getAccountInfo per asset |
| Multisig signer compromise | Outside the threshold (1 key gone is fine) | Rotate compromised signer; threshold protects |
| Multisig signer loss (3+) | Cannot mint or transfer | Pause new mints; existing assets unaffected; resume after restoring threshold |
| Vault dispute | SOP triggers | Independent arbitration per TOS §7 |
| IPFS pin loss | Pinning monitor | Re-pin from cloud archive |
