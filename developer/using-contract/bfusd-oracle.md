# bfUSD Oracle

## Overview

bfUSD uses price feeds to convert between USDT, USDC, and the dual staking pools. USDT deposits are always treated as 1:1, while USDC conversions consult a Chainlink oracle to keep pricing fair when the stablecoin drifts slightly.

## USDC Chainlink Feed

* **Oracle address (test):** `0x30d56c01d6367747009e7bc2509000627444de59` – front-end contracts query this aggregator when pricing USDC deposits, redemptions, or instant conversions.
* **Timing:** The oracle must be polled regularly; stale data should be considered invalid, so the UI should verify the `latestRoundData` timestamp before presenting rates.
* **Precision:** Chainlink denominates USDC in 8 decimals, so the vault multiplies the price by the USDC amount before calling `mint`.

## hbfUSD / pbfUSD Ratio Tracking

* **`currentRatio()`** – exposes the current bfUSD-to-share ratio with 1e8 precision.
* **`epochRatios(uint256 epoch)`** – returns `(startRatio, endRatio, startTime, endTime)` so that front-ends can reconstruct how a share price changed inside an epoch.
* **`convertToShares` / `convertToAssets`** – reuse the ratio logic, so once the oracle provides the bfUSD value, the vault can translate between assets and shares deterministically.

## UI Recommendations

* Always call `previewDeposit`, `previewMint`, `previewWithdraw`, or `previewRedeem` to show the expected amounts before sending a transaction.
* For USDC flows, read the Chainlink feed first, then apply the vault ratio to determine the share amount to mint or burn.
* Cache the `currentRatio` or the most recent `epochRatios` to avoid hammering the contract for every display update; the ratio only changes when a new epoch closes.
