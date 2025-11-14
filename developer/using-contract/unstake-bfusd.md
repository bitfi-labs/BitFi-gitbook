---
icon: arrows-down-up
---

# Unstake bfUSD

## Overview

Unstaking bfUSD can follow the standard (multi-epoch) redemption path or the instant redemption path. The standard path burns hbfUSD/pbfUSD shares and waits for the configured epoch cooldown before claiming bfUSD, while the instant path burns bfUSD directly through a redeemer contract in exchange for USDT/USDC with a small fee.

## Standard Redemption Flow

* **previewWithdraw(uint256 assets)** – calculates the share amount that must be burned to unlock a target bfUSD amount, rounding up to ensure sufficient coverage.
* **previewRedeem(uint256 shares)** – returns the bfUSD amount that will be received when burning a given share amount, rounding down to avoid over-delivering assets.
* **withdraw(uint256 assets, address receiver, address owner)** – burns enough shares to create a withdrawal request, capturing the withdrawalId and claimableEpoch. The vault waits for settlementDelay epochs before the withdrawal becomes claimable.
* **edeem(uint256 shares, address receiver, address owner)** – similar to withdraw but expresses the intention in share units instead of bfUSD.
* **claimWithdrawals(uint256[] withdrawalIds)** – called once the claimableEpoch is reached; it finalizes the withdrawal, transfers bfUSD to the eceiver, and emits WithdrawalClaimed events.

During this process, the vault emits WithdrawalRequested and WithdrawalClaimed, which expose the request metadata, fees, and the exact bfUSD amount available for claim.

## Instant Redemption

Instant redemption is handled by the BitFiStablecoinInstantRedeemer contract, which burns bfUSD and immediately sends USDT or USDC (subject to the redeemer's remaining quota). The key characteristics are:

* **Fee:** 0.5% of the redeemed amount.
* **Quota:** A protocol-managed limit that depletes as instant redemptions occur and is refreshed periodically.
* **Tokens:** Some redeemers are USDT-only or USDC-only to minimize on-chain swaps (see the dedicated USDT and USDC redeemer addresses).
* **Use case:** Suitable for users who need immediate liquidity; when the quota is exhausted, calls revert and users must use the standard redemption path.

The BitFiStablecoinStandardRedeemer exposes a similar interface without the same urgency; it can be used by backend services that batch redemption requests and do not require the instant liquidity guarantee.

## Cross-Chain & LayerZero

The ERC-4626 vaults expose LayerZero helpers (send, quoteSend, quoteOFT) for cross-chain withdrawals. When invoked, a portion of the shares is burned on the source chain, crossChainFee() is collected, and the destination chain receives a message to mint the requested amount of shares or bfUSD. These functions mirror the bfBTC cross-chain patterns and rely on LayerZero v2's MessagingFee structures.

## Key Events

* **WithdrawalRequested** – logs withdrawalId, equestEpoch, claimableEpoch, share amounts, and the target bfUSD.
* **WithdrawalClaimed** – indicates when the withdrawal was claimed and how much bfUSD (and fee) was delivered.
* **AssetDeposited / CrossChainFeeCollected / RatioUpdated** – useful for dashboards tracking deposits, cross-chain movements, and epoch ratio changes.

## Practical Notes

* Standard redemptions require a manual claim after the settlement delay (typically 1 epoch) and do not levy fees.
* Instant redemptions revert if the redeemer's quota is reached or liquidity runs out, so fall back to the standard path when necessary.
* The vault's pochRatios and currentRatio should be polled to translate shares into bfUSD for UI previews. LayerZero cross-chain users should also request quoteSend before calling send to ensure sufficient gas coverage.
