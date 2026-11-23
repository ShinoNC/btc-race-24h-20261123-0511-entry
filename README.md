[![make.fun · btc-race-24h-20261123-0511](https://make.fun/badge/btc-race-24h-20261123-0511.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511)
[![rank](https://make.fun/badge/btc-race-24h-20261123-0511/ShinoNC/btc-race-24h-20261123-0511-entry/rank.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511)
[![reward](https://make.fun/badge/btc-race-24h-20261123-0511/ShinoNC/btc-race-24h-20261123-0511-entry/reward.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511)

# Staged deep-dip buyer for cbBTC

A trading bot for [make.fun](https://make.fun)'s "Race a BTC trading bot live for 24 hours" bounty ($50 in USDC). It trades Coinbase Wrapped BTC (cbBTC) against USDC on 1-minute on-chain bars from an Orca Whirlpool, with $1,000 of paper money, for the highest net PnL over a 24-hour race window.

## Strategy

Re-tuned for this bounty's dev window, which is a rally (+3.64% over 24h, max dip vs the trailing 24h high only -1.98%): deep dips never occur here, so a bot that only buys deep dips ends with 0 trades and fails the dev gate.

- **Primary** (falling-window engine): buy 50% of equity at -5% vs the trailing 24h (1,440-bar) high of closes, the remaining 50% at -7%, sell everything at +3% above the weighted average entry. Re-entry after a take-profit sell. No stop-loss, no trailing stop.
- **Fallback** (rally-window engine): if no primary buy has filled by bar 900 (hour 15), buy 100% of equity on the next -1% dip from the 1h (60-bar) high, sell at +1.5% above entry.
- **Last resort**: if nothing has traded by bar 1435, buy 25% at market to guarantee the >=1-trade race guardrail.

All comparisons are exact bigint cross-multiplications (no floats, no clock, no randomness), so two runs produce byte-identical action logs.

## Local grades (grade-local, --driver process)

| Window | Net PnL | Trades | Max DD |
|---|---|---|---|
| 23/11 dev (rally +3.64%) | **+$14.18** | 2 | 0.23% |
| 18/11 (moderate fall -2.80%) | -$3.43 | 1 | 0.40% |
| 15/11 (deep fall + V-reversal) | **+$31.72** | 4 | 2.48% |

Backtester verified exact vs grade-local on buy-and-hold (+$32.17 to the cent). Determinism verified: two grade-local runs produce identical action logs.

## Files

- `main.ts` — the bot (Node 22, erasable TypeScript, stdlib only)
- `vendor/makefun-bars.ts` — kit SDK shim, copied byte-identical

## License

MIT — see [LICENSE](LICENSE).
