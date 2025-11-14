# Redeem bfUSD

## Overview

This page describes how to redeem bfUSD back into USDT using the BitFiStablecoinRedeemer contracts. There are two modes: a standard redemption flow with a waiting period and no protocol fee, and an instant redemption flow with a small fee and a quota.

## Standard Redemption

Standard redemption is handled by the BitFiStablecoinStandardRedeemer contract. A typical flow is:

1. Use the redeemer's preview helper to estimate how much USDT you will receive for a given bfUSD amount.
2. Submit a standard redemption request to the standard redeemer, which queues the request for settlement after the configured number of epochs (currently 3).
3. After the request reaches its claimable epoch, call the claim function to receive USDT in your wallet.

Standard redemption does not charge a protocol fee, but the user must wait for the settlement window to pass and then claim manually. The exact function names and parameters are available in the verified ABI for the standard redeemer contract.

## Instant Redemption

Instant redemption is handled by the BitFiStablecoinInstantRedeemer contract. In this mode, bfUSD is burned and USDT is sent immediately to the user, subject to a quota and a fixed fee:

* A 0.5% fee is charged on the redeemed amount.
* The instant redeemer maintains a configurable quota; when the quota is exhausted, instant redemptions revert until capacity is replenished.

Instant redemption is suitable for users and integrations that require immediate liquidity and are willing to pay the fee instead of waiting for the standard settlement window.

## Notes

* Both standard and instant redeemers operate on bfUSD balances; to unwind Horizon or Pulsar positions, first unstake back to bfUSD and then redeem.
* Always read the latest ABI and on-chain configuration (fees, quotas, supported tokens) from the verified redeemer contracts before integrating.
