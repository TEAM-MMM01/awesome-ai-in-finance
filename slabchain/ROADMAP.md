# Roadmap

A deliberately slow, capital-light path from a personal slab collection to a working collector RWA product.

## Phase 0 — Foundations (Weeks 0–6, ~$5–10k)

**Goal:** legal + custody + insurance signed before any code or mint.

- [ ] Form Wyoming or Delaware LLC (`SlabChain LLC` or similar; verify name availability).
- [ ] Open business banking (Mercury, Relay).
- [ ] Engage a consumer-protection / e-commerce attorney to draft:
  - [ ] Terms of Service (bailment, redemption, limits of liability)
  - [ ] Redemption Agreement (per-mint contract)
  - [ ] Privacy Policy (light KYC at redemption only)
- [ ] Sign Master Services Agreement with one vault (PWCC Vault is the default candidate; alternates: Brinks, Goldin Vault).
- [ ] Bind insurance — either via the vault's blanket policy (most common) or a standalone collectibles policy.
- [ ] Validate every cert # in `ASSETS.md` against PSA / CGC public lookup.
- [ ] Publish v1 of all docs in this repo.

**Exit gate:** signed MSA + insurance binder in hand. No mint until this is true.

## Phase 1 — Whole-card NFTs on Solana (Weeks 6–14)

**Goal:** ship one full intake → mint → trade → redeem cycle end-to-end before scaling.

- [ ] Provision Squads v4 multisig (3-of-5) for mint authority. Hardware keys only — Ledger or Trezor.
- [ ] Deposit 2–3 founder cards (start small; do NOT mint all 13 at once) into the vault. Use the lower-value cards for the first cycle (e.g., Charizard ex SVP 056 PSA 7, Magnemite 1st Ed PSA 7, Kingambit PSA 9 AR).
- [ ] Mint 1:1 NFTs via Metaplex Core. Metadata per [TOKEN_SPEC.md](TOKEN_SPEC.md).
- [ ] Build minimal redemption portal:
  - Next.js + Solana Wallet Adapter (Phantom / Backpack / Solflare)
  - Burn-and-claim flow + KYC step (Persona) at burn
  - Status dashboard for in-flight redemptions
- [ ] List on Magic Eden + Tensor with disclosure language ("collectible bailment receipt; no investment intent").
- [ ] Run one full redemption end-to-end. Document what broke.

**Exit gate:** 1+ full redemption completed without dispute. SOP updated based on what failed.

## Phase 2 — Open intake (Months 4–9)

**Goal:** become a real custodial service for outside collectors.

- [ ] Build consignor onboarding flow (KYC at deposit, intake-fee collection, vault routing).
- [ ] Publish first quarterly proof-of-reserves attestation (independent appraiser walks the vault, signs cert-#-by-cert-# audit, posts hash on-chain).
- [ ] Add 25–50 outside slabs to the vault. Curated only — top-pop Japanese moderns + select vintage.
- [ ] Negotiate a Magic Eden / Tensor "verified collection" badge; consider a SlabChain-branded landing page that aggregates listings.
- [ ] Add support for additional graders (BGS) once volume justifies the workflow change.

**Exit gate:** 50+ slabs under custody, 100+ trades cleared, zero unresolved redemption disputes.

## Phase 3 — XRPL leg (Months 9–15)

**Goal:** open a second venue for compliance-first trading without diluting Solana focus.

- [ ] Mint XLS-20 NFTs as **independent issuances** backed by the same vault. (Cleaner than bridging — no ambiguity about which token is canonical.)
- [ ] List on XRPL native DEX. Optional: XLS-30 AMM pool only after volume warrants.
- [ ] Add RLUSD as a settlement currency option for compliance-conscious institutions / international buyers.
- [ ] Each new vault deposit gets minted on Solana + XRPL simultaneously; legacy Solana-only inventory does not auto-mint to XRPL (avoids accidental double-claim).

**Exit gate:** XRPL inventory has independent liquidity (not just bridged Solana volume).

## Phase 4 — Scale (Month 15+)

- [ ] Geographic expansion: vault partners in Japan and EU (closer to high-end Japanese inventory and EU collectors).
- [ ] Curated drop partnerships with notable graders, illustrators, or known collectors.
- [ ] Optional: cNFT-based "bulk slab" tier for raw cards graded ≤ PSA 7 — lower fee, higher throughput, separate brand line.
- [ ] Begin fee parameter governance experiments (DAO-lite for fee tuning, NOT for custody decisions).

## Things this roadmap deliberately does not do

- No fractionalization, ever. Re-evaluating it would mean re-engaging securities counsel and rebuilding the legal posture from scratch.
- No "fund," "basket," or pooled-asset NFT.
- No SlabChain-branded card grading service (we depend on PSA / CGC / BGS; we do not compete).
- No lending / borrowing against deposited slabs.
- No fiat custody for users.
