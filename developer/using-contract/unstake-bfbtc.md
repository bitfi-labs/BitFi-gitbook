# Unstake bfBTC

{% hint style="warning" %}
Currently, we support **RedStone** Oracle for price feeds. BitFi plans to integrate more underlying asset oracles in the future.
{% endhint %}

***

## Introduction

This document provides a technical guide for developers on how to interact with the bfBTC smart contract to unstake (withdraw) bfBTC tokens and receive the underlying BTC assets. The unstaking process is a multi-step procedure involving a request, an administrative approval, and a final claim.

## 1. Previewing a Withdrawal

Before initiating a withdrawal, it's recommended to use this view function to estimate the amount of the underlying asset you will receive and the associated fees.

**`previewWithdraw`**

```
function previewWithdraw(uint256 share, bool native) public view returns (uint256 underlyingAmount, uint256 fee)
```

* Description: This function previews the amount of underlying asset (e.g., BTC, BTCB) you will receive when unstaking a specific amount of bfBTC, and it also calculates the total fee.
* Parameters:
  * `share`: The amount of bfBTC you intend to withdraw (8 decimals).
  * `native`: A boolean indicating the withdrawal destination.
    * `true` for the native Bitcoin network;
    * `false` for an EVM network.&#x20;
    * On the Bitlayer contract, this parameter is currently unused.
* Returns:
  * `underlyingAmount`: The estimated net amount of the underlying asset you will receive after fees.
  * `fee`: The total withdrawal fee, combining both percentage and fixed fees. Fee is calculated as bfBTC.
* Notes: If the calculated fee is greater than or equal to the withdrawal amount, the function will return an `underlyingAmount` of 0. The final received amount may vary slightly due to ratio changes between the preview and the actual claim.

***

## 2. Requesting a Withdrawal

The first step in the unstaking process is to submit a withdrawal request. This locks your bfBTC in the contract and creates a pending request.

**`requestWithdraw` (for EVM chains)**

This function initiates a withdrawal to the user's address on the same EVM chain.

```
function requestWithdraw(uint256 amount) external
```

* Description: Initiates a withdrawal request for bfBTC, locking the tokens in the contract while awaiting approval.
* Parameters:
  * `amount`: The amount of bfBTC to be withdrawn.
*   Notes: The specified amount must be greater than the `minWithdrawBfbtcAmount`. The request's initial status is set to

    `PENDING`.



**`requestWithdrawNative` (for native Bitcoin withdrawal)**

This function initiates a withdrawal directly to a Bitcoin network address.

```
function requestWithdrawNative(uint256 amount, uint8 btcAddressType, bytes32 btcAddress) external
```

* Description: Initiates a withdrawal request where the final underlying asset will be sent to a specified native Bitcoin address.
* Parameters:
  * `amount`: The amount of bfBTC to be withdrawn.
  * `btcAddressType`: An integer representing the BTC address type (1 for P2TR, 2 for P2WPKH, 3 for P2SH\_P2WPKH, 4 for P2PKH).
  * `btcAddress`: The user's native Bitcoin address, formatted as `bytes32`.

***

## 3. Administrative Approval

After a request is submitted, it must be approved by an authorized multisig manager. This is an administrative, off-chain process.

**`approveWithdraw` (for EVM claims)**

This function is called by the multisig manager to approve requests that will be claimed on an EVM chain.

```
function approveWithdraw(uint256 epoch, uint256[] calldata withdrawIds) external
```

*   Description: An administrator-only function to approve multiple pending withdrawal requests. Approved requests have their status updated to

    `CLAIMABLE`.
* Parameters:
  * `epoch`: A historical epoch number used for the exchange rate calculation. This must be less than the current epoch and satisfy the cooldown period.
  * `withdrawIds`: An array of withdrawal request IDs to approve.



**`nativeWithdraw` (for native Bitcoin claims)**

This function is called by the multisig manager after the BTC has been sent on the Bitcoin network.

```
function nativeWithdraw(uint256 epoch, uint256[] calldata withdrawIds, bytes32[] calldata nativeTxs) external
```

* Description: An administrator-only function to finalize native Bitcoin withdrawals. It records the Bitcoin transaction hash and updates the status to `SENT_ON_NATIVE`, effectively burning the corresponding bfBTC shares.
* Parameters:
  * `epoch`: The historical epoch for rate calculation.
  * `withdrawIds`: An array of withdrawal request IDs.
  * `nativeTxs`: An array of corresponding Bitcoin transaction hashes.

***

## 4. Claiming the Withdrawal

Once a request has been approved and its status is `CLAIMABLE`, the user can execute the final step to receive their underlying assets.

**`claim`**

```
function claim(uint256[] calldata withdrawIds) external
```

* Description: Allows a user to claim their approved withdrawals and receive the underlying asset in their wallet.
* Parameters:
  * `withdrawIds`: An array of the user's approved withdrawal request IDs to claim.
* Notes:
  * This function can only be called by the user who originally made the request.
  *   The request status must be

      `CLAIMABLE`.
  *   After a successful claim, the status is updated to

      `CLAIMED`.
  *   The final amount received is calculated based on the exchange ratio of the

      `settleEpoch` recorded during approval. You can check the final claimable amount using the

      `claimable(withdrawId)` view function.



***

## Withdrawal Process Summary

* Step 1: Request: User calls `requestWithdraw` or `requestWithdrawNative` to lock bfBTC. Status becomes `PENDING`.
* Step 2: Approve: The multisig manager calls `approveWithdraw` (for EVM) or `nativeWithdraw` (for Native BTC) after a cooldown period (typically 1 epoch).
*   Step 3: Claim (EVM only): If approved via `approveWithdraw`, the user calls `claim` to receive the underlying ERC20 asset. Status becomes

    `CLAIMED`.

***

## **Important Notes**

* Stakes and Unstakes must be made while the contract is not paused.
* Unstake require a cooldown period. (1 epoch)
* The actual amount of BTC received depends on the current epoch's ratio and price.
* The displayed receive amount may differ.
* Unstake approval time may vary depending on Ceffu's processing speed. BitFi aims to process unstake request within 5 epochs.
