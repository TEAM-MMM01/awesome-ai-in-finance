# Legal Posture

> **This document is an internal planning artifact, not legal advice.** Engage qualified counsel in your jurisdiction before any of this is implemented.

## The framing that matters

SlabChain is a **digital warehouse-receipt service for graded collectibles.** It is *not* an investment product. The relationship between SlabChain LLC and an NFT holder is **bailment**: the holder is the bailor (depositor), SlabChain is the bailee (custodian via the contracted vault), and the NFT is the bearer claim on a specific certified slab.

This framing is intentional. It keeps SlabChain out of US securities law (no Howey-style investment contract), out of money-transmitter law (no fiat custody, no payments rails), and out of commodities law (no fungible pooled instrument).

## What we deliberately do NOT do

| We don't | Because |
| --- | --- |
| Fractionalize cards | Fractional tokens against a single asset → almost certainly a security under Howey |
| Pool slabs into baskets / funds | Same — pooled investment with expectation of profit from the company's efforts |
| Promise appreciation, yield, ROI, returns | Anti-fraud rules under FTC + state consumer-protection laws |
| Custody fiat for users | Money transmitter exposure (FinCEN MSB + state-by-state licensing) |
| Lend or borrow against slabs | Lending license exposure; rehypothecation risk |
| Self-custody consigned inventory | Counterparty trust + insurance fail; vault is the trust anchor |

## Key legal documents required pre-launch

### Terms of Service

Must address explicitly:

1. **Bailment relationship.** Holder = bailor; SlabChain LLC = bailee via Vault Partner. SlabChain has no ownership claim on the underlying slab.
2. **Redemption right.** Burn the NFT, complete light KYC, slab is shipped within a stated SLA (target: 14 business days from KYC clearance).
3. **Redemption fees and shipping costs.** Pass-through plus a flat handling fee; disclosed at redemption time.
4. **Damaged or lost slab procedure.** Vault insurance pays the appraised FMV; holder elects between (a) cash settlement at FMV minus deductible or (b) replacement of equivalent cert # if available.
5. **Grade challenge / reholdering.** What happens if a holder claims the slab in the vault doesn't match the cert # photographed at intake. (Independent third-party arbitration; vault's intake photographs are evidence of record.)
6. **Wind-down clause.** If SlabChain LLC dissolves, all NFTs enter a mandatory 180-day redemption window; unredeemed NFTs trigger an orderly auction with proceeds escrowed for the original holder.
7. **Smart contract risk disclosure.** Bug or exploit in Solana / XRPL contracts is not a SlabChain liability beyond commercially reasonable engineering practices.
8. **Choice of law and dispute resolution.** Delaware (or wherever LLC is formed); arbitration clause for individual disputes.
9. **NO investment language.** Repeat throughout: collectible utility only.

### Privacy Policy

- KYC required only at **redemption** (not at mint, not at trading).
- Vendor: Persona or Sumsub for ID verification + sanctions screening.
- Light data retention: only what's needed to ship the slab (name, address, ID match), retained per regulatory minimums then purged.
- No on-chain doxxing — wallet addresses are not associated with KYC data publicly.

### Operating Agreement (LLC internal)

- Distinguishes founder personal collection from company inventory. (Critical: founder's pre-deposit personal slabs are *not* SlabChain assets.)
- Defines mint-authority multisig signers and governance.
- Defines distribution waterfall for fees, with a vault-insurance reserve carved out before any distribution.

## FTC marketing standards

Hard rules for all public-facing copy (website, Twitter, Discord, marketplace listing descriptions):

- ❌ "investment," "investor," "yield," "returns," "ROI," "appreciation"
- ❌ "guaranteed," "passive income," "make money," "profit"
- ❌ "fund," "basket," "portfolio" (when describing the product)
- ❌ "fractional," "shares," "stake"
- ✅ "collect," "trade," "own," "redeem," "vaulted," "authenticated"
- ✅ "digital warehouse receipt," "bailment," "custodied"

A founder/team member on any podcast, livestream, or interview must use the approved lexicon. One careless "you can think of it as an investment" on Twitter undoes the entire legal posture.

## Sales tax

Physical redemption may trigger sales tax in the destination state. Engage a sales-tax / nexus consultant (TaxJar, Avalara, or a state-tax CPA) before opening intake to outside consignors. Some states tax tangible personal property on delivery; others don't tax collectibles specifically. This is a state-by-state matrix that needs to be built, not guessed.

## Counsel engagement (minimum viable)

For Phase 0–1:
- One **consumer-protection / e-commerce attorney** (drafts TOS, Privacy Policy, Redemption Agreement)
- One **corporate / LLC formation attorney** (operating agreement, member units, distributions)
- One **insurance broker** familiar with collectibles (binds the vault rider)

Securities counsel is **not required** at this stage and is explicitly avoided as a tell that the product is drifting toward an investment offering. If anyone on the team starts thinking about fractionalization, *that* is when securities counsel comes back in — and the entire posture has to be redesigned.

## Jurisdictional note

This document assumes US founders + US incorporation. If founders or operations are outside the US:
- UK / EU: collectibles bailment is recognized but consumer-protection regimes (UK Consumer Rights Act, EU Consumer Rights Directive) apply.
- Japan: Pokémon cards' largest collector market; Japanese vault partner becomes important; consumer law (特定商取引法) applies; do not market crypto-adjacent products to Japanese retail without local counsel.

## When this posture breaks

Re-engage securities counsel immediately if:
- Any product offering pools multiple holders' capital into a shared upside.
- Marketing language slips into investment territory and isn't promptly corrected.
- A holder sues alleging loss of investment value.
- Volume reaches a level where the product becomes a de facto investment platform regardless of intent.
