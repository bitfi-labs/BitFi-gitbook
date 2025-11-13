# bfBTC Fees

{% hint style="warning" %}
Note: fees only be taken while `requestWithdraw` , `requestWithdrawNative`, and cross-chain actions.
{% endhint %}

## Fee Types

```solidity
uint8 private constant FEE_TYPE_EVM = 0;      // For EVM chain operations
uint8 private constant FEE_TYPE_NATIVE = 1;   // For native chain [Bitcoin] operations
uint8 private constant FEE_TYPE_CROSSCHAIN = 2; // For cross-chain operations
```



## Fee Parameters

1. **Withdraws:**&#x20;

* Percentage Fee
  * Fee rate = 0
  * Fee precision = 100000
  * Actual fee = 0 bfBTC
* Fixed Fee
  * Fee rate = 600
  * Fee precision = 100000000 (1e8)
  * Actual fee = 0.000006 bfBTC



2. **WithdrawsNative**

* Percentage Fee
  * Fee rate = 0
  * Fee precision = 100000
  * Actual fee = 0 bfBTC
* Fixed Fee
  * Fee rate = 1200
  * Fee precision = 100000000 (1e8)
  * Actual fee = 0.000012 bfBTC
  * Note: The Fixed Fee on the Hemi network is 2,000 (0.00002 bfBTC).



## Fee Structure

```solidity
struct FeeConfig {
    uint128 percentageFee; // Percentage-based fee (precision: 100000)
    uint128 fixedFee;      // Fixed fee amount
}
```



## Fee Calculation

```solidity
function calculateFee(
    address user,
    uint256 amount,
    uint8 feeType
) public view returns (uint256 percentageFee, uint256 fixedFee)
```

**Description:**\
Calculate fees for a specific operation based on user address and amount.

**Parameters:**

* `user`: Address of the user performing the operation
* `amount`: Amount of the operation
* `feeType`: Type of fee to calculate (EVM/Native/Crosschain)

**Return Values:**

* `percentageFee`: Calculated percentage-based fee
* `fixedFee`: Fixed fee amount

**Calculation Formula:**

$$
\text{percentageFee} = \text{amount} \times \frac{\text{percentageFeeConfig}}{\text{FEE_PRECISION}}
$$

$$
\text{totalFee} = \text{percentageFee} + \text{fixedFee}
$$

**Notes:**

* percentageFee precision is 100000
* Transaction reverts if total fee exceeds operation amount



## Fee Events

```solidity
event FeeUpdated(uint8 indexed feeType, uint256 percentageFee, uint256 fixedFee);
event FeeWhitelistUpdated(address indexed account, bool status);
event FeeCollected(
    address indexed user,
    uint8 indexed feeType,
    uint256 id,
    uint256 amount,
    uint256 percentageFee,
    uint256 fixedFee
);
```



## Additional Information

* Fees are collected in the bfBTC token
* Fee events provide tracking and auditing capabilities
* Whitelist mechanism allows for special cases and promotions
* Fee configuration can be updated based on market conditions

