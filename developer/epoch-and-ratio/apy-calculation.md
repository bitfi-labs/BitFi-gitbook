# APY Calculation

## Introduction

This document describes how BitFi computes and reports APY for bfBTC. We publish two complementary metrics:

* 7‑Epoch Rolling APY (“7D APY”)
* Cumulative Project APY (since genesis)

Both are derived from the on-chain exchange rate between bfBTC and BTC.



## Terminology

* Epoch N: The N‑th rebasing or accounting interval at which the bfBTC/BTC exchange rate is finalized.
* T\_N: Unix timestamp (in seconds) when epoch N is finalized.
* R\_N: Exchange rate at epoch N, expressed as BTC per 1 bfBTC. In words, “1 bfBTC = R\_N BTC.”
* S\_year: Number of seconds in one year. Unless otherwise noted, S\_year = 31,536,000.
* Percent vs fraction: All APY formulas below return percentage values (i.e., multiplied by 100).

Notes:

* The exchange rate R\_N is expected to be non‑decreasing over time if bfBTC accrues yield in BTC.
* Timestamps must be strictly increasing: T\_N > T\_{N-1}.



## 7‑Epoch Rolling APY (“7D APY”)

### Definitio

A linearized, annualized rate based on the change in the bfBTC/BTC exchange rate over the last seven epochs. Because epochs are not guaranteed to be exactly 24 hours, this “7D APY” can differ from a strict 7‑calendar‑day APY.



### Formula&#x20;

$$
\mathrm{APY}^{(7)}(N)
=
100 \times S_{\text{year}} \times
\frac{\displaystyle \frac{R_{N}}{R_{N-7}} - 1}{\displaystyle T_{N} - T_{N-7}},
\qquad N \ge 7
$$

**Where:**

* R\_N / R\_{N-7} − 1 is the relative change over the last seven epochs.
* The fraction is scaled to an annual rate by S\_year, then converted to percent by 100.

### Notes:

* Require N ≥ 7.
* If T\_N − T\_{N-7} is very small or zero, skip the update or fallback to a safe default.



## Cumulative Project APY (since genesis)

### Definition

A linearized, annualized rate computed from genesis (epoch 0) to epoch N.



### Formula

$$
\mathrm{APY}_{\text{cumulative}}(N)
=
100 \times S_{\text{year}} \times
\frac{\displaystyle \frac{R_{N}}{R_{0}} - 1}{\displaystyle T_{N} - T_{0}}
$$

**Where:**

* R\_N / R\_0 − 1 is the total relative appreciation of bfBTC versus BTC since genesis.
* Linearization makes the rate easy to interpret and stable across reporting intervals.



