# Epoch Updates

Epoch updates are triggered when:

* The value of open positions changes beyond a predefined threshold.
* A specified time interval elapses.

Upon meeting these conditions, the manager triggers an epoch update. The management wallet updates the contract to the next epoch and updates the exchange ratio (`Ratio`).&#x20;

After the update, all deposit and withdrawal transactions use the latest epoch's value to calculate bfBTC amounts.

Due to performance differences between Binance Smart Chain and Ethereum, Ethereum epoch updates might lag behind BSC updates. The delay typically does not exceed one minute.

## **Note**

Calling the contract's `currentEpoch` function returns the latest epoch, but this epoch does not yet have a `Ratio`. `updateEpoch` updates the `Ratio` for that epoch.
