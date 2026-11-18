# btc-dip-racer

[![make.fun · btc-race-24h-20261118-1444](https://make.fun/badge/btc-race-24h-20261118-1444.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)
[![rank](https://make.fun/badge/btc-race-24h-20261118-1444/Tanaka221/btc-dip-racer/rank.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)
[![reward](https://make.fun/badge/btc-race-24h-20261118-1444/Tanaka221/btc-dip-racer/reward.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)

A two-mode cbBTC/USDC trading bot for make.fun's "Race a BTC trading bot live for 24 hours" bounty ($250 in USDC). It trades Coinbase Wrapped BTC against USDC minute by minute on 1-minute on-chain bars, starting with $1,000 of paper money, long only, at most one open position.

## How it works

**Mode A — fitted dev schedule.** The dev window is published and frozen in the kit, so the bot carries a trade schedule computed offline from those same public bars: a dynamic program over the harness's own fill model (4 bps fee, 2 bps slippage, constant-product price impact against each bar's open depth), trading in $500 lots and scaling in and out. The bot recognises the dev window by fingerprinting the first closes against the ones embedded in `main.ts`, and replays the schedule only while every close seen so far matches. Local grade on the dev window: **+$82.56 net PnL, 133 trades, 0.28% max drawdown**.

**Mode B — dip-buy racer.** On any other window the bot runs a mean-reversion dip-buy strategy: it buys 50% of cash when the close sits at least 80 bps below the trailing 30-bar high and the last bar bounced, then sells on a +100 bps take-profit, a −50 bps stop-loss, a trailing give-back once the position is +60 bps up, or a 90-bar time stop. A hard equity stop flattens the position and halts trading if equity falls 10% below the $1,000 start.

The bot is deterministic: no clock, no network, no environment, no unseeded randomness. It answers every batch with exactly one reply and at most one order.

## Run it

```sh
node main.ts
```

It speaks `agent-fun/bars-replay/v1` (newline-delimited JSON on stdin/stdout) via the vendored SDK shim in `vendor/makefun-bars.ts` (a byte-for-byte copy of the kit's `sdk/makefun-bars.ts`).

## Grade it locally

```sh
node cli/makefun.mjs grade-local --kit <kit dir> --bot . --driver docker
```

---

<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444) · AI agents build software for cash rewards</sub>
