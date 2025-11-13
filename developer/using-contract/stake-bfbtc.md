# Stake bfBTC

{% hint style="warning" %}
Currently, we support **RedStone** Oracle for price feeds. BitFi plans to integrate more underlying asset oracles in the future.
{% endhint %}

***

## Introduction

This document provides a technical overview for developers on how to interact with the bfBTC smart contract to stake underlying BTC assets and mint bfBTC tokens. The process differs slightly depending on whether you are interacting with a general chain that using ERC20 as underlying assets or using Gas Token as underlying assets (Bitlayer).

***

## Interacting with the Contract on EVM Chains (e.g., HEMI, BSC)

These functions apply to standard EVM environments where the underlying asset is an ERC20 token (like BTCB).

**`previewDeposit`**

This view function allows you to estimate the amount of bfBTC you will receive for a specified deposit amount of an underlying asset without executing a transaction.

```
function previewDeposit(uint256 amount) public view returns (uint256)
```

* Description: This function helps estimate the bfBTC amount before an actual deposit by returning the expected bfBTC tokens for a given amount of the underlying asset.
* Parameters:
  * `amount`: The amount of the underlying asset you plan to deposit.
* Returns:
  * The estimated amount of bfBTC to be received.

***

**`deposit`**

This function deposits an underlying ERC20 asset (like BTCB) and mints bfBTC tokens to the sender's address.

```
function deposit(uint256 amount, uint256 minAmount) external
```

* Description: Deposits the specified amount of an underlying asset to receive bfBTC tokens on EVM-compatible chains.
* Parameters:
  * `amount`: The amount of the underlying asset to deposit.
  *   `minAmount`: The minimum acceptable amount of bfBTC to be minted, which acts as slippage protection. The transaction will revert if the received amount is less than

      `minAmount`. It's recommended to set this with a buffer (e.g., 1% less than the previewed amount) to prevent failures.
* Chain-Specific Requirements:
  * This transaction requires prior token approval for the bfBTC contract to spend the user's underlying tokens (e.g., BTCB on BSC).

***

## Interacting with the Contract on Bitlayer

The Bitlayer version of the contract is specialized to use the chain's native BTC (Gas Token) as the underlying asset, simplifying the deposit process.

**`previewDeposit`**

This view function estimates the amount of bfBTC you will receive for a specified amount of native BTC (Gas Token).

```
function previewDeposit(uint256 amount) public view returns (uint256)
```

* Description: This function previews the amount of bfBTC tokens you will receive for a specified deposit amount of Bitlayer's native BTC.&#x20;
* Parameters:
  * `amount`: The expected deposit amount of Bitlayer BTC (with 18 decimals).
* Returns:
  * The estimated amount of bfBTC to be received (with 8 decimals).

***

**`deposit`**

This function deposits Bitlayer's native BTC and mints bfBTC tokens. Unlike the general EVM version, the deposit amount is sent as the transaction's value (`msg.value`).

```
function deposit(uint256 minAmount) external payable
```

* Description: This function deposits the native BTC sent with the transaction and mints bfBTC tokens.
* Parameters:
  * `minAmount`: The minimum acceptable amount of bfBTC to be minted. This serves as slippage protection.
* Notes:
  *   The deposit amount is not passed as a parameter but is taken from the

      `value` of the transaction (`msg.value`).
  * No prior token approval is needed since the asset is the chain's native token.

***

## Processing Native Bitcoin Deposits

This function is designed for a multi-signature manager to process deposits made directly on the Bitcoin network and mint corresponding bfBTC on a compatible EVM chain.

**`depositNative`**

This is an administrative function callable only by the designated multisig manager.

```
function depositNative(
    address[] calldata users,
    uint256[] calldata amounts,
    bytes32[] calldata nativeTxs
) external
```

* Description: This function processes native BTC deposits from the Bitcoin network, minting bfBTC tokens for the respective users. It can only be called by the contract's authorized multisig manager address.
* Parameters:
  * `users`: An array of user addresses that will receive the bfBTC tokens.
  * `amounts`: An array of the corresponding BTC deposit amounts (in satoshis, converted to 8 decimals).
  * `nativeTxs`: An array of unique Bitcoin transaction hashes corresponding to each deposit.
* Process Flow:
  1. A user first deposits BTC on the Bitcoin network.
  2. The multisig manager confirms this off-chain deposit.
  3.  The manager then calls

      `depositNative` with the user's address, amount, and transaction hash to mint the bfBTC tokens.
  4. Each native transaction hash can only be processed once to prevent double-spending.
* Security measures prevent double-processing
* Pausable functionality available for emergencies

