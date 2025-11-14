# Deployed Contracts

## Base Sepolia (ChainId 84532)

* **bfUSD (ERC20):** `0xEc7aEc5b60b3F23177Ee7BbBedBD73A5fa24BE21` – the stablecoin itself.
* **Staked pools:**
  * **sbfUSD (Horizon / low-risk):** `0xf6b46c3c177d2256da671b8b51c88f26b40ff06e`
  * **hybfUSD (Pulsar / high-yield):** `0x25ffc79c6a796cfa42d4be6ffb44d603c66a341f`
* **Minters:**
  * **bfUSD-Minter-USDT:** `0x81EC39D8c87D2d2efbCE55C7455ca2503a511903`
  * **bfUSD-Minter-USDC:** `0x9873477a4cFe519Da3DBa365156A6ac88e2028D6`
* **Redeemers:**
  * **InstantRedeemer (fast exits):** `0x1e5F61CDC9665A568C56A34aD887D4BaF1Ed61cb`
  * **StandardRedeemer:** `0x96c3725eb6054f7c4b05bfff65317dfe0761e9a0`
* **Utility contracts:**
  * **BitFiStablecoinZap:** `0x44Cd5Da392Af57420C44b3b7bbF1890381237a86`
  * **USDC Oracle (Chainlink):** `0x30d56c01d6367747009e7bc2509000627444de59`
  * **bfUSD-Manager:** `0xEa4d76cb95231276A7b4D0358A8fD33006DA7aFe`
  * **bfUSD-Merkle-Stash:** `0x4d60258a462Eba590AE6A9220CCd9AC7919f9fC5`
* **Test tokens (for UI/testing):**
  * **USDC (ERC20):** `0x4d69cA7CfB1C16CFf14527C72fF80Ac8A2bf532a`
  * **USDT (ERC20):** `0xDa7fE7E2dc820D8D771c4Ae150A2dDf6aefD88F3`

## Notes

* Addresses above currently point to the Base Sepolia testnet deployment; mainnet addresses will be shared in release notes.
* Refer to the BitFi contract repository (`contracts/bfusd`) for the latest ABI files and to confirm the address mappings for each environment.
