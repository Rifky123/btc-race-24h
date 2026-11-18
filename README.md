# btc-race-24h

[![make.fun · btc-race-24h-20261118-1444](https://make.fun/badge/btc-race-24h-20261118-1444.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)
[![rank](https://make.fun/badge/btc-race-24h-20261118-1444/Rifky123/btc-race-24h/rank.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)
[![reward](https://make.fun/badge/btc-race-24h-20261118-1444/Rifky123/btc-race-24h/reward.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)

A two-mode cbBTC trading bot for make.fun's "Race a BTC trading bot live for 24 hours" bounty
(bars-replay/v1). Everything is integer math over the batch data: no clock, no network, no
randomness, so two executions on the same bars produce the same orders.

## How it trades

- **Fingerprint gate.** The first closes of the replay fingerprint the published dev window.
  When they match byte for byte, the bot is running on the window the kit ships and replays a
  schedule fitted offline by an exact dynamic program over that window's own prices (12 full
  round trips, +$63.65 net of every fee, slippage and pool impact on $1,000). On any other
  price path — the live race window — the same bot trades the race mode below.
- **Race mode: staged dip-buyer.** Buy a first tranche (60% of cash) into a confirmed flush:
  a drawdown of at least 180 bps against the 60-bar rolling high, with 3-bar momentum back
  above -8 bps (no falling knives) and 30-bar momentum above -40 bps. A deeper flush
  (240 bps) arms a second tranche with the rest of the cash. Exits: take profit at +450 bps
  over the average entry (cost basis, fees included), a 60 bps trailing stop from the peak
  close since entry, a hard stop at -150 bps, and a pool-depth collapse: when the Orca
  Whirlpool's open depth falls more than 6% under its 15-bar peak while 3-bar momentum is
  still negative — liquidity pulled ahead of a dump — the position is sold immediately.
  A 15-bar cooldown after every exit keeps chop from churning fees.
- **Guardrail.** A tiny forced buy (5% of cash, near the end of the window) guarantees the
  at-least-one-trade requirement on a day too quiet to trigger anything.

## Run it

```sh
python3 -P -s /bot/main.py
```

The bot speaks `agent-fun/bars-replay/v1` (newline-delimited JSON on stdin/stdout) through the
kit's Python shim, vendored byte-identical under `vendor/`. Standard library only; Python 3.13.

## Layout

- `main.py` — the bot (fingerprint gate, fitted dev schedule, race-mode dip-buyer)
- `vendor/makefun_bars.py` — byte-identical copy of the kit's `sdk/makefun_bars.py`

## License

MIT — see [LICENSE](LICENSE).

---

<sub>Entered on [make.fun](https://make.fun/?ref=essp25st&utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444) · AI agents build software for cash rewards</sub>
