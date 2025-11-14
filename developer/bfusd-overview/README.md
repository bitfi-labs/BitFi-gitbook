---
icon: dollar-sign
---

# bfUSD Overview

## Introduction

bfUSD (BitFi USD) is a collateralized, Chainlink-backed stablecoin that mints against USDT or USDC. Instead of remaining idle, minted bfUSD can be staked into dual ERC-4626 vaults to earn protocol revenue, insurance buffer distributions, and boosted yields sourced from the Horizon pool.

## Dual Yield Model

* **Horizon bfUSD (hbfUSD):** A low-risk vault that targets ~15% APY with smooth, non-drawdown ratio growth. Its supply is capped at 100 million shares, and the ratio only moves upward between epochs.
* **Pulsar bfUSD (pbfUSD):** A higher-yield vault with a 2 million share cap that targets ~22% APY. Pulsar receives additional boosted yield from Horizon and acts as a capital buffer when low-risk positions rebalance.
* hbfUSD and pbfUSD share a security perimeter: Pulsar principal steps in before Horizon experiences drawdowns, while Horizon traffics a percentage of its revenue back to Pulsar.

## Redemption Flexibility

bfUSD supports both standard redemptions (three-epoch wait plus manual claim, zero fees) and instant redemptions (0.5% fee, quota-limited). Redeemer contracts can emit USDT or USDC immediately, making liquidity available for front-end users while preserving the peg.

## Technical Footprint

* **ERC-4626 compatible:** Both vaults implement the vault standard, exposing `previewDeposit`, `previewWithdraw`, `convertToShares`, and `convertToAssets` helpers.
* **Chainlink pricing:** USDC flows consult the Chainlink feed before conversion, while USDT stays at 1:1.
* **LayerZero aware:** Deposits, withdrawals, and share transfers can cross chains via LayerZero helpers (e.g., `send`, `quoteSend`).
* **Insurance buffers:** Multiple vaults, an on-chain buffer, and a project-funded insurance pool help keep hbfUSD stable even when Pulsar trades sharply.

## Deployment Status

* **Appearance:** Currently deployed on Base Sepolia (ChainId 84532) for testing; mainnet addresses will be highlighted once released.
* **Frontend governance:** `BitFiStablecoinManager` coordinates epoch updates, multiple claims, and boosted yield distributions.
* **Audit posture:** All bfUSD-related contracts align with the existing CeDeFi controls and undergo the same audit and monitoring workflows as bfBTC.
