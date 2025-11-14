# Contract Parameters

| Parameter | Value | Notes |
| --- | --- | --- |
| bfUSD decimals | 6 | Matches USDT/USDC standard precision |
| hbfUSD target APY | ~15% | Low-risk Horizon vault that compounds via the epoch ratio |
| pbfUSD target APY | ~22% | High-risk Pulsar vault with boosted yield from Horizon + insurance buffer |
| hbfUSD supply cap | 100,000,000 shares | Prevents over-allocation of low-risk liquidity |
| pbfUSD supply cap | 2,000,000 shares | Keeps high-yield exposure constrained |
| Ratio precision | 1e8 | `currentRatio()` and `epochRatios()` use this scaling to convert between bfUSD and shares |
| Standard redemption delay | 3 epochs + manual claim | Users must wait for `settlementDelay` epochs before calling `claimWithdrawals` |
| Instant redemption fee | 0.5% | Charges the fee in bfUSD and enforces a quota on the redeemer contract |
| Staking/unstaking fees | 0 | Minting, depositing, withdrawing, and unstaking are free; only instant redemption and cross-chain transfers charge small fees |
| Cross-chain fee | Variable (per `crossChainFee()`) | Charged when sending shares via LayerZero; uses `FeeLibrary.Fees` structure |
| Oracle | Chainlink (USDC) + hardcoded USDT | USDC conversions consult the Chainlink feed; USDT stays at 1:1 |
| Epoch tracking | `epochRatios(uint256 epoch)` | Each epoch stores `(startRatio, endRatio, startTime, endTime)` to rebuild historical share values |

Additional notes:

* `StakedBitFiStablecoin` exposes `maxDeposit`, `maxMint`, `maxWithdraw`, and `maxRedeem` to enforce the supply caps and ensure `supplyCap` is never exceeded.
* `previewDeposit`, `previewMint`, `previewWithdraw`, and `previewRedeem` reuse the vault ratio logic, making it safe for front-ends to quote exact outputs before transactions.
* `pendingWithdrawal` data (requested epochs, claimable epochs, fees) can be read through the `withdrawals(uint256 id)` view and should be shown in any UI that lists queued redemptions.
