# Underlying Asset Price Variation

{% hint style="warning" %}
Currently, we support **RedStone** Oracle for underlying asset price feeds. BitFi plans to integrate more underlying asset oracles in the future.
{% endhint %}

{% hint style="danger" %}
Due to the requirements of quantitative strategies, bfBTC currently does not support WBTC as a deposit asset. Therefore, the content of this section is not applicable at the moment and is for reference only.
{% endhint %}

Different underlying assets are used for deposits on different chains:

* **Bitcoin:** BTC
* **Ethereum:** WBTC
* **Binance Smart Chain:** BTCB
* **Bitlayer:** BTC (native token/gas token)
* **Hemi:** hemiBTC

On Bitcoin, Ceffu mirrors BTC assets to the exchange at a 1:1 ratio. Ideally, depositing 1 BTC yields bfBTC based on the ratio corresponding to 1 BTC.

Binance Smart Chain's BTCB is backed by Binance at a 1:1 exchange rate. Depositing 1 BTCB yields bfBTC based on the ratio corresponding to 1 BTC (1 BTCB = 1 BTC).

* HemiBTC (on Hemi Network) and Bitlayer BTC (on Bitlayer) are each 1:1 pegged to Bitcoin, with:
  * 1 HemiBTC = 1 BTC
  * 1 Bitlayer BTC = 1 BTC

Ethereum's WBTC has price fluctuations; deposits are calculated based on the Redstone Oracle price.
