# Contract ABI

The bfUSD suite exposes several ABI surfaces:

* **BitFiStablecoin (bfUSD):** ERC-20 with mint and burn access control plus Chainlink pricing helpers.
* **BitFiStablecoinMinter (USDT / USDC):** Handles deposits of the two underlying stablecoins, enforcing min/max deposits and interacting with the price oracle.
* **StakedBitFiStablecoin (hbfUSD / pbfUSD):** ERC-4626 vaults that expose `deposit`, `mint`, `withdraw`, `redeem`, LayerZero helpers (`send`, `quoteSend`), and detailed view functions such as `currentRatio`, `epochRatios`, and withdrawal bookkeeping.
* **BitFiStablecoinZap:** Bundles minting and staking into a single call for front-end convenience.
* **Instant & Standard Redeemers:** Accept bfUSD and immediately issue USDT/USDC (Instant) or queue a standard redemption (Standard).

A curated ABI bundle (`bfUSD-ABI-1023.zip`) is available via the BitFi documentation package, and all individual ABI files are checked into the [`contracts/bfusd`](https://github.com/bitfi-labs/contract/tree/master/contracts/bfusd) directory in the BitFi contracts repository. Refer to that directory for the latest verified JSON files, including the ERC-4626 vault ABIs and the zap/redeemer helpers.
