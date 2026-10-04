# PBD + Market Profile indicator (TradingView Pine Script v5)

File: [`pbd_market_profile.pine`](pbd_market_profile.pine)

This indicator turns the process Patrick Nill describes in his Workless Edge interview into rules:

> "I have the market profile and volume base for the direction … then I draw my zones, support and resistance … then I go deeper and draw my PBDs, and if they match the market profile and it all comes together, then I put my trades."
>
> "You wait for a push, and after the push there comes most times a range. You wait till it goes out of the range to follow the trend or counter it."
>
> "I love 15 minutes … I look at 4 hours for the big picture."

## How the rules are coded

| Step in the interview | What the indicator does |
|---|---|
| **Big picture on 4H** | Pulls the 4H EMA (default 50) without repainting. Bullish = 4H close above a rising EMA, bearish = below a falling EMA. |
| **Market profile gives the direction** | Builds the previous day's volume profile (or TPO profile) and works out **POC, VAH and VAL** (70% value area). If value moved up compared with the day before, the bias is bullish. If it moved down, the bias is bearish. If the two days' value overlaps, it uses where price sits against yesterday's value area. |
| **Support / resistance zones** | Takes 4H swing highs and lows and draws them as zones (thickness = a fraction of the 4H ATR). Yesterday's VAH, VAL, POC, high and low also count as levels. |
| **P – Push** | A strong move: at least `1.5 × ATR` within at most 3 bars, closing near its extreme. |
| **B – Base** | The range that follows: at least 2 and at most 12 bars, no taller than `1.2 × ATR`, and retracing no more than 61.8% of the push. |
| **D – Decision (breakout)** | The first close outside the base. |
| **Follow the trend** | The breakout goes the same way as the push → **TREND** signal (triangle). It only shows when it agrees with the market-profile / 4H bias. |
| **Counter it (his favourite)** | The breakout goes against the push → **COUNTER** signal (diamond). It only shows when the push ran into a zone or profile level. The stop goes just beyond the base and the target is **the origin of the push**, which gives the "clear take profit and clear stop" he talks about. |
| **1–2 trades a day, 1 hour in the morning** | Optional session window (default 08:00–12:00 Berlin, off by default) and a cap on signals per day (default 2). |

Every valid signal draws the base box, entry / stop / target lines and a label with the R:R, and fires an alert.
The dashboard in the top-right corner shows the 4H bias, the market-profile bias, the combined filter and whether a base is forming right now.

## Using it

1. In TradingView, open **Pine Editor**, paste in the contents of `pbd_market_profile.pine`, then click **Add to chart**.
2. Use a **15-minute** chart. Patrick traded crude oil (CL / WTI / USOIL) and the DAX (FDAX / GER40).
3. Alerts: **Create alert → Condition: PBD-MP → "Any alert() function call"** (or one of the specific conditions), and choose **Once per bar close**.

## Settings worth tuning

- **Push: min size (× ATR)**: raise it to see fewer, cleaner pushes. On the DAX, which is more volatile, try 1.8–2.0.
- **Base: max bars / max height**: these control how tight the consolidation has to be.
- **Trend-trade filter**: `MP + HTF` is the strictest. Set it to `Off` to see every trend breakout.
- **Counter trades need a zone**: turning this off shows every counter breakout. Expect many more signals, and lower quality.
- **Min reward : risk**: hides trades whose target is too close. This matters most for counter trades, where the target is the push origin.

## Notes and limits

- "PBD" is read here as **Push → Base → Decision/breakout**, which is how Patrick explains it in the interview. His paid masterclass may define the details differently, for example how big a push has to be. Treat the defaults as a starting point.
- The market profile is rebuilt from the chart bars of the previous calendar day. Accuracy depends on the chart timeframe: 15m works well, 1m is the most precise.
- Signals are calculated on the bar close. A signal on the live bar can appear and disappear until that bar closes.
- This is an analysis tool, not financial advice. Backtest it and forward-test it on a demo account before you risk money.
