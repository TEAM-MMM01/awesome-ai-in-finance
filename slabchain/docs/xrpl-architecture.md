# XRPL Architecture (Phase 3)

XRPL leg design. Not implemented in Phase 1; this is forward-looking spec.

## Stack

| Layer | Choice |
| --- | --- |
| NFT standard | XLS-20 (NFTokenMint, NFTokenBurn, NFTokenCreateOffer) |
| Trading venue | Native XRPL DEX; XLS-30 AMM only after sufficient depth |
| Settlement currency | XRP, RLUSD, USDC (when available natively) |
| Wallets | XUMM, Crossmark, GemWallet |
| Indexer | XRPL public API (rippled WebSocket); optionally Bithomp / Onchain.lol for richer queries |

## Issuance model

Each Phase 3+ slab is minted as **two independent claims**:
- One Solana Metaplex Core asset (existing Phase 1 model)
- One XRPL XLS-20 NFT

Hard invariant: **at any moment, exactly ONE of the two is "active" per cert #.** The inactive chain's NFT is burned. Switching chains:

```
holder calls /switch-chain (cert#, target_chain)
  ↓
backend verifies caller owns the active NFT
  ↓
caller signs burn on active chain
  ↓
backend confirms burn
  ↓
7-day cooldown (anti-exploit)
  ↓
backend mints on target chain to caller's address on that chain
```

The 7-day cooldown is non-negotiable; it prevents bridging-style exploits where a malicious actor with a transient signing key could mint twice during a fork or RPC inconsistency.

## Why independent issuance instead of bridging

Bridging via Axelar / Wormhole / Squid would let a single token "live" on either chain through wrapped representations. We deliberately reject this for SlabChain because:

1. **Ambiguity at redemption.** If a wrapped token is in transit when a redemption is initiated, which chain's contract is authoritative?
2. **Bridge risk is not SlabChain risk we want to underwrite.** Bridge exploits have moved >$2B historically. We do not want our bailment liability tied to a third-party bridge's security.
3. **Legal cleanliness.** The TOS treats each chain's NFT as its own claim; no doctrine of "wrapped equivalent" needs to be litigated.

## Mint flow (XRPL)

```
backend prepares NFTokenMint:
  - URI: ipfs://<metadata-cid> (32-byte hash, hex-encoded)
  - Flags: tfTransferable
  - TransferFee: 2500 (= 2.5%)
  - Issuer: SlabChain XRPL company account
  ↓
multisig signs (XRPL native multisig with SignerList)
  ↓
submit to rippled
  ↓
NFTokenID emitted
  ↓
backend creates NFTokenCreateOffer (sell offer to holder address) for 0 XRP
  ↓
holder accepts offer with NFTokenAcceptOffer
  ↓
holder now owns the NFT
```

## Trading venues

### Native DEX

XRPL's built-in offer book supports NFT offers natively. Listings are created via `NFTokenCreateOffer`. No third-party marketplace needed (xrpl.cafe, onXRPL, and similar are aggregator UIs over the native primitive).

### XLS-30 AMM

Skip in early Phase 3. AMM pools require non-trivial inventory depth to provide meaningful price discovery and don't suit unique-cert collectible economics.

If considered later: it would be for a `SLABCHAIN-INDEX` pseudo-token representing the floor of the collection — but this trends toward the "investment basket" framing we have explicitly excluded. **Do not pursue.**

## Settlement currencies

- **XRP** — default; required for fees regardless.
- **RLUSD** — stablecoin issued on XRPL by Ripple; preferred for compliance-conscious institutions.
- **USDC on XRPL** — when Circle launches native USDC on XRPL, accept it.

Buyers pay in any of the above; the XLS-20 NFTokenCreateOffer specifies the desired currency.

## Multisig on XRPL

XRPL has native multisig via SignerList. Configure 3-of-5 with the same physical hardware-key allocation as the Solana side. Signing ceremony differs from Squads but the security model is equivalent.

The Solana multisig and the XRPL multisig share **no signing key material in common.** A compromise of one chain's signer set does not leak to the other.

## Failure scenarios specific to XRPL

| Scenario | Mitigation |
| --- | --- |
| `rippled` validator outage | Run our own rippled or use Ripple's hosted endpoint + Bithomp as fallback |
| TransferFee enforcement vs. amendments | Monitor amendment voting; the on-ledger transfer fee is enforced for the lifetime of the NFT regardless of marketplace |
| Bridged-from-Solana confusion | Public messaging that SlabChain XRPL NFTs are independent issuances, not bridged tokens |

## Pre-launch checklist for XRPL leg

- [ ] Phase 2 ("open intake") has been live for 6+ months without serious incident.
- [ ] Solana inventory has 50+ active NFTs.
- [ ] Legal review of XRPL leg specifically — does any aspect change the bailment posture? (Should not, but verify.)
- [ ] XRPL company account funded with reserve XRP for issuance.
- [ ] Multisig SignerList configured with five hardware-backed signers.
- [ ] First test mint completed on XRPL testnet.
- [ ] First test mint + burn on mainnet using a low-value slab.
