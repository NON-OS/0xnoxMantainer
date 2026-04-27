# Launchpad

Token launch with bonding curves. No LP needed until graduation.

---

Factory contract creates tokens. Each token has built-in curve. Buy from curve, price goes up. Sell back, price goes down. When curve fills up (hits ETH target), token graduates to Uniswap.

Factory: `0xed32A86f95c5593Aac4dB1937Ed21078da200d7c`

---

Creation flow:

Connect wallet -> fill name/symbol/desc -> pay NOX fee -> factory deploys token

Token params locked at creation: supply, graduation threshold, curve steepness

---

Trading pre-graduation:

```js
// buy from curve
token.buy(minTokensOut, { value: ethAmount })

// sell to curve
token.approve(tokenAddr, amount)
token.sell(amount, minEthOut)
```

Curve math: `price = k * supply_sold^n`

Early = cheap, late = expensive

---

Graduation:

Someone calls `token.graduate()` when threshold hit. Creates Uniswap pair, seeds LP, burns LP tokens (locked forever). After that trade on uni like normal.

---

NFT Pass holders get reduced fees + early access

NFT: `0x7b575DD8e8b111c52Ab1e872924d4Efd4DF403df`

---

Frontend stuff:

```
js/contracts.js              contract calls
js/contracts-trade.js        buy/sell
js/contracts-graduation.js   grad check
js/launchpad/analytics.js    stats
```

API:

```
GET /api/v1/tokens              list
GET /api/v1/tokens/:addr        single
GET /api/v1/tokens/:addr/trades history
GET /api/v1/tokens/:addr/chart  ohlc
```

---

Warning: curves dump hard. early in = profit, late in = bags. graduation threshold helps but doesnt guarantee anything post-grad. dyor.
