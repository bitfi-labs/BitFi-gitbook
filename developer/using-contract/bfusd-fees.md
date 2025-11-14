# bfUSD Fees

## Overview

Fee configurations for bfUSD follow a silent rule: staking, minting, and standard redemption are fee-free, while instant redemption and cross-chain movement carry small factory-set charges. All fee details are exposed through the `FeeLibrary` helper so that front-ends can display transparent costs.

## Fee Library Structures

* `struct FeeConfig { uint128 percentageFee; uint128 fixedFee; }` – each fee type is represented as a percentage (with precision) plus an optional fixed amount.
* `redemptionFee()` – returns the applicable fee for standard or instant redemptions, depending on the caller (a whitelist may bypass the percentage portion).
* `crossChainFee()` – calculates the share burn required when sending funds via LayerZero, covering cross-chain messaging and settlement costs.

## Redemption Fees

* **Standard redemption:** `redemptionFee()` returns zero for the default path; the only cost is the epoch wait time and manual claim.
* **Instant redemption:** Charges 0.5% of the redeemed bfUSD and is capped by the redeemer's quota. The fee is collected in bfUSD contracts and emitted through `WithdrawalClaimed` or immediate redemption events.

## Cross-Chain & Other Flows

* **LayerZero transfers:** `crossChainFee()` estimates the share-level fee prior to calling `send`; the vault also emits `CrossChainFeeCollected` so UIs can highlight the exact cost.
* **Fee events:** `FeeUpdated`, `FeeWhitelistUpdated`, and `FeeCollected` allow tracking when rates change, when an address is granted discounted treatment, and how much was collected per operation.

## Fee Calculation Formula

```
percentageFee = amount * percentageFeeConfig / FEE_PRECISION
totalFee = percentageFee + fixedFee
```

The vault reverts when the total fee exceeds the requested output, ensuring users never receive a negative amount.
