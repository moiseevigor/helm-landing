# Sources — every number on helmfinances.com

All Helm figures are **backtests, gross of fees**, on one window, read from research artifacts in the
private strategy repo (`pead-strategy`). Nothing on the page is typed from memory. Regenerate the chart data
with `research/export_landing_data.py` (writes `landing/_helm_v6.json`), then paste it over the
`window.HELM=` line in `index.html`.

| Number on the page | Value | Artifact / source | Field |
|---|---|---|---|
| Helm v6 CAGR | 11.0 % | `artifacts/results/pead_dial_candidate.json` (2026-10-31) | `kpis.pead_d08.cagr` = 11.01 |
| Helm v6 Sharpe | 1.25 | same | `kpis.pead_d08.sharpe` = 1.252 |
| Helm v6 Sortino | 1.82 | same | `kpis.pead_d08.sortino` = 1.822 |
| Helm v6 max drawdown | −15.6 % | same | `kpis.pead_d08.maxdd` |
| Helm v6 Calmar | 0.71 | same | `kpis.pead_d08.calmar` |
| Window | Oct 2001 → Jun 2026 | same | `meta.sample` = 2001-10-01 .. 2026-06-12, `meta.n_days` = 6215 |
| As-traded CAGR / Sharpe / Sortino / maxDD / Calmar | 5.9 % / 1.21 / 1.75 / −9.8 % / 0.60 | same | `kpis.live_exec.*` (backtest of the executable sizing 10 % × w_PEAD × dial) |
| "~34 % invested" | 34.2 % | same | `live_exec_exposure.gross_mean` = 0.342 |
| "roughly doubles the invested share" | — | same | `caveats[5]` |
| RSI(2) retirement gate "P(ΔSortino>0) 17 %" | 16.9 % | same | `gate.keep_sleeve.P_dSortino_gt0_pct` |
| Vol-dial definition, metric definitions | — | same | `definitions.*` (also `docs/GLOSSARY.md`) |
| Exposure "63 % avg" (model-state chip) | 0.633 | `artifacts/results/helm_sector_hedge.json` | `hedge.unhedged.mean_dial_exposure` |
| Beta to S&P ≈ 0.66 | 0.663 | same | `hedge.unhedged.undialed_beta_to_spy` |
| S&P 500 CAGR / Sharpe / Sortino / maxDD / Calmar | 10.1 % / 0.60 / 0.86 / −55.2 % / 0.18 | computed by `research/export_landing_data.py` via `peadlab.benchmark.benchmark_curve` (SPY total return, same price lake, identical window) | `landing/_helm_v6.json` → `kpi.spy` |
| Growth of $10k: Helm v6 / S&P / as traded | $131,121 / $107,681 / $41,061 | `landing/_helm_v6.json` | `v4final` / `spfinal` / `livefinal` (month-end points of the weekly artifact curves `curves.pead_d08`, `curves.live_exec`) |
| Crisis depths on the chart | Dot-com −7.0 / −27.9, GFC −13.1 / −50.5, COVID −1.2 / −21.0, 2022 −7.5 / −23.9 | `landing/_helm_v6.json` | `crises[]` — read off month-end points, so shallower than the daily maxDD |
| Planning band: Sharpe 0.5–0.8, CAGR 4–7 % net, Sortino 0.8–1.2, maxDD −25…−30 %, ≥ 6 y to confirm | — | `docs/research/CRITICAL_QUANT_REVIEW.md` §4 | table "Honest forward expectations" |
| Fee drag "~1.3 %/yr" | — | same | CQ-4 |
| "mega-cap book Sharpe ~1.1 vs full S&P ~0.4" (hindsight universe) | — | `docs/MODEL_CARD.md` | failure mode F3 |
| Universe = 20 fixed mega-caps; hold 60 sessions; 10 % per name; −15 % hard stop; $1M/day liquidity | — | `src/peadlab/config.py` | `MEGA20`, `BEST_STRATEGY` (`hold_days=60, pct_of_capital=0.10, hard_stop_pct=15.0, min_dollar_vol=1_000_000`) |
| 8 % dial target (v6) | — | `pead_dial_candidate.json` | `promotion_options[0].dial` = 0.08 (recommended); `decision` |
| Short hedge dropped 2026-06 (look-ahead artifact) | — | `docs/research/LOOKAHEAD_REMEDIATION.md`, `docs/MODEL_CARD.md` | — |
| Papers in "The science" | — | `docs/research/` PDFs named on the page | — |

## Caveats carried on the page (and where they come from)
- **Hindsight universe** — the 20 names are today's mega-caps (MODEL_CARD F3; `pead_dial_candidate.json` `caveats[3]`).
- **In-sample dial** — the 8 % target was picked on the same history (`caveats[1]`).
- **Gross of fees** — no commission/slippage in the curves (CRITICAL_QUANT_REVIEW CQ-4).
- **Backtest ≠ live** — the as-traded row is itself a backtest of the live sizing, not a broker statement; the live demo record is too short to confirm anything (`caveats[0]`, CQ-5).
- **Not market-neutral** — beta ≈ 0.66; crisis losses are the beta leg (`helm_sector_hedge.json`, `docs/research/CRISIS_ANATOMY.md`).

Illustrative mock-ups (gate chain, model state, drift timeline) carry an explicit "illustrative" label and are not data.
