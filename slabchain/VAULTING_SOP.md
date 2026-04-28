# Vaulting Standard Operating Procedure

The custody layer is the trust anchor of the entire product. This SOP defines exactly how a slab moves into, lives in, and moves out of the vault.

## Custody principles

1. **Never self-custody consigned inventory.** Founder personal collection is separate; SlabChain inventory lives only at a contracted vault.
2. **Cert # is the unit of identity.** Every photograph, attestation, NFT, and dispute references the PSA / CGC / BGS cert # — never just "the Eevee CHR."
3. **No mint without intake completion.** A slab is not eligible to be tokenized until the full intake checklist below is signed and timestamped.
4. **Insurance binder is per-asset.** The vault's blanket policy must list each cert # individually with a declared value; bulk-only riders are not acceptable.

## Where the slabs live

**Vault partner candidates (Phase 0 selection):**

| Vault | Strength | Trade-off |
| --- | --- | --- |
| PWCC Vault | TCG-native, integrated with eBay, known to collectors | More expensive; PWCC has had collector-trust controversies |
| Brinks Global Services | Highest insurance limits, used by Courtyard | Generalist; less TCG-fluent staff |
| Goldin Vault | Auction-house-affiliated, strong vintage handling | Smaller throughput |

Pick **one** for Phase 0–1. Multi-vault complicates audits, insurance, and redemption routing.

**Founder personal collection** (the 13 cards in `ASSETS.md`):
- Stays at home or in a bank safety deposit box until the company is formed and the vault MSA is signed.
- Then a deliberate subset (start with 2–3 lower-value cards) is transferred to the vault as the *first founder consignment*. Documented as an arms-length deposit, not an in-kind contribution to the LLC, to avoid commingling personal and company property.

## Intake procedure (per slab)

When a slab arrives at the vault:

| Step | Owner | Output |
| --- | --- | --- |
| 1. Inbound shipment receipt | Vault | Tracking # + tamper-evidence check |
| 2. Slab integrity inspection | Vault | Photo of any cracks, dings, residue, or refinish marks |
| 3. Photograph (front) | Vault | High-res JPEG, ≥ 12 MP, archived to S3 + IPFS |
| 4. Photograph (back) | Vault | Same |
| 5. Photograph (slab edge w/ barcode) | Vault | Same |
| 6. Cert # cross-reference | Vault | PSA / CGC public lookup screenshot, archived |
| 7. Declared value attestation | Consignor + Vault | FMV in USD, anchored to comp source (eBay sold / PSA APR / TCGplayer) |
| 8. Insurance binder line item | Vault | Declaration page entry naming the cert # at FMV |
| 9. Vault attestation signed | Vault officer | PDF signed by vault, includes cert #, date, photo hashes, declared value |
| 10. Intake completion notification | SlabChain ops | Trigger to mint authority |
| 11. Mint NFT against cert # | SlabChain mint authority | NFT issued; metadata references attestation hash |
| 12. NFT delivered to consignor wallet | SlabChain | Consignor receives NFT (less intake fee) |

**No step is skippable.** Intake fees are not refundable once step 11 completes.

## Storage conditions

Per vault contract, conditions must include:
- Climate control: 18–22 °C, 40–55% RH (industry standard for graded slabs).
- Fire suppression: clean-agent (FM-200 or NOVEC 1230), not water sprinkler.
- 24/7 monitoring with seismic + smoke + smoke-vapor sensors.
- Physical access via two-person rule for any drawer pulled.
- Annual independent audit access (right of audit must be in MSA).

## Redemption procedure (per slab)

Triggered when an NFT holder calls `burn_and_redeem` on the SlabChain redemption portal:

| Step | Owner | SLA |
| --- | --- | --- |
| 1. Burn transaction confirmed on-chain | Holder + Solana | T+0 |
| 2. KYC check (Persona) — name, address, government ID | Holder | T+0–3d |
| 3. Sanctions screening | Persona | T+1d |
| 4. Redemption order generated | SlabChain ops | T+1d |
| 5. Vault pulls slab, verifies cert # against burn-event metadata | Vault | T+2–4d |
| 6. Tamper-evident packaging | Vault | T+3–5d |
| 7. Insured shipment (FedEx Priority Overnight, signature required, declared value) | Vault | T+4–6d |
| 8. Delivery confirmation, redemption marked complete | SlabChain ops | T+5–10d |

Stated SLA: **14 business days** from burn to delivery within continental US. International redemption: 21 business days.

**Failure modes:**
- Cert # mismatch → halt shipment; trigger arbitration per TOS §7.
- Slab damage discovered at pull → trigger insurance claim; holder elects cash settlement or replacement.
- Lost in transit → vault insurance pays declared value; SlabChain refunds redemption fee.

## Proof of reserves

Quarterly, an **independent third-party appraiser** (not affiliated with the vault, not affiliated with SlabChain LLC):

1. Visits the vault.
2. Pulls every cert # listed as outstanding-NFT-backed.
3. Verifies physical match against intake photographs.
4. Signs an attestation listing every cert # and confirming presence + condition.
5. The attestation hash is posted on-chain and the full PDF posted to IPFS + the SlabChain website.

If a quarterly attestation cannot be produced on time, **mints are paused** until it is.

## Multisig key management

SlabChain mint authority = Squads v4 multisig, 3-of-5 threshold.

Signers (suggested allocation; tune to team size):
- Founder, Ledger Nano X
- COO / ops lead, Ledger Nano X
- Outside director / advisor, Ledger Nano X
- Cold backup #1, Trezor in bank deposit box A
- Cold backup #2, Trezor in bank deposit box B (different bank, different city)

Hard rules:
- No software-only keys.
- No signer's seed phrase ever entered into a computer.
- Quarterly drill: simulate a 3-of-5 ceremony to confirm all signers can still produce a signature.
- If any signer is unreachable for 60+ days, rotate.

## Operational separation

The wallet that **holds NFTs awaiting first sale** is separate from the wallet that **collects fees in stablecoins**, which is separate from the **multisig that has mint authority**. Mixing these is the path to a single-key compromise wiping out everything.
