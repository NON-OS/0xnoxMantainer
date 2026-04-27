# Terminal

Trading UI. Charts, buy/sell, comments, token discovery.

---

Layout:

```
+-----------+------------------+
| token     |                  |
| list      |  chart           |
| (sidebar) |                  |
|           +------------------+
|           | buy/sell panel   |
|           +------------------+
|           | comments         |
+-----------+------------------+
```

---

Pre-grad tokens trade against curve contract. Post-grad routes thru uniswap.

```js
Trading.executeBuy(token, ethAmt)
Trading.executeSell(token, tokenAmt)
```

Slippage default 1%, configurable.

---

Charts built with canvas, no lib. Supports 1m/5m/15m/1h/4h/1d. Candlestick + line modes. Volume overlay.

---

Discovery filters:
- New (recent launches)
- Trending (24h volume)
- Graduated (on uniswap)
- Search by name/symbol/address

---

Comments per token, stored in DB not on-chain. Need wallet connected to post.

---

Keyboard:

```
/     search
Esc   clear
1-9   quick select
B     buy
S     sell
```

---

Files:

```
js/trading/
  core.js            main obj
  terminal.js        tabs
  chart.js           drawing
  chart-data.js      fetch
  trade.js           panel
  trade-execute.js   txs
  comments.js        render
  discovery.js       list
```

---

Estimate before execute:

```js
const est = await Trading.estimateBuy(token, eth)
// { tokensOut, priceImpact, fee }
```

Shows warning if impact >5%

---

Errors show toast. Check console for details. Common: slippage exceeded, insufficient balance, rpc timeout.
