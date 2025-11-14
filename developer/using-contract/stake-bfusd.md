---
icon: arrows-up-down
---

# Stake bfUSD

## Overview

bfUSD is minted by depositing USDT or USDC into BitFi's stablecoin engine. USDT deposits are priced 1:1, while USDC deposits rely on Chainlink price feeds to keep the peg. After minting bfUSD, users can stake that collateral into the ERC-4626 compliant Horizon (hbfUSD) or Pulsar (pbfUSD) vaults to capture yield while keeping capital liquid.

## Key Contracts

* **BitFiStablecoin:** ERC-20 representation of bfUSD with 6 decimals.
* **BitFiStablecoinMinter (USDT / USDC):** Two dedicated minters that accept the respective stablecoin and mint bfUSD; each minter enforces minimum deposit sizes and interacts with a shared price oracle when needed.
* **StakedBitFiStablecoin (hbfUSD / pbfUSD):** ERC-4626 vaults that issue yield-bearing shares (low-risk Horizon and high-risk Pulsar) based on a per-epoch ratio; each vault exposes offset controls such as `settlementDelay`, `supplyCap`, and `currentRatio`.
* **BitFiStablecoinZap:** Optional helper that deposits the underlying stablecoin directly into the selected vault, combining mint + stake in a single transaction.

## ERC-4626 Deposit Helpers

* **`previewDeposit(uint256 assets)`** – returns share amount for the incoming bfUSD amount using the current epoch ratio (rounded down to avoid overminting).
* **`previewMint(uint256 shares)`** – returns the bfUSD required to mint the target number of shares (rounded up so the vault receives enough assets).
* **`deposit(uint256 assets, address receiver)`** – deposits bfUSD and mints shares to `receiver`; requires an allowance for the vault and checks the `supplyCap`.
* **`mint(uint256 shares, address receiver)`** – requests an exact share amount, automatically calculating the underlying bfUSD needed, then mints those shares once the vault receives payment.
* **`convertToShares` / `convertToAssets`** – expose the same ratio logic as `previewDeposit` / `previewRedeem`, useful for front-ends that show expected yields.
* **`maxDeposit`, `maxMint`, `maxWithdraw`, `maxRedeem`** – enforce per-epoch limits, including the vault's supply cap and available liquidity.

## Deposit Workflow

1. Acquire USDT or USDC and approve the respective `BitFiStablecoinMinter` contract.
2. Mint bfUSD via the minter (`mint` or `deposit` style), mindful that USDT stays at 1:1 while USDC uses the Chainlink price feed for fair conversion.
3. Approve the desired `StakedBitFiStablecoin` vault (horizon or pulsar) to spend bfUSD.
4. Call `deposit` or `mint` on the vault directly, or use `BitFiStablecoinZap` to combine mint-and-stake in one transaction.
5. Use `previewDeposit` / `previewMint` to display expected share amounts and `currentRatio` / `epochRatios` to project future values.

## Yield Notes

hbfUSD (Horizon) is capped at 100 million shares and targets ~15% APY with smooth, non-drawdown ratio growth. pbfUSD (Pulsar) is capped at 2 million shares and targets ~22% APY while the share price may fluctuate; a portion of Horizon's revenue plus an insurance buffer supplements Pulsar, and Pulsar's principal backstops Horizon if the low-risk pool experiences a drawdown.
