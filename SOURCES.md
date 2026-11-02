# Sources — every number on helmfinances.com

All Helm figures are **backtests, gross of fees**, on one window, read from research artifacts in the
private strategy repo (`pead-strategy`). Nothing on the page is typed from memory. Regenerate the chart data
with `research/export_landing_data.py` (writes `landing/_helm_v6.json`, incl. the public-benchmark rows fetched
from Yahoo Finance), then paste it over the `window.HELM=` line in `index.html`.

**Numbers move when the data vendor re-bases adjusted prices.** The 2026-11-02 refresh of the price panel (the one the
production artifact is built on) moved the v6 backtest by about half a point — CAGR 11.0 → 10.4 %, Sharpe 1.25 → 1.19,
Sortino 1.82 → 1.72, max drawdown unchanged at −15.6 % — and the panel's first bar moved from 2001-10-01 to 2001-07-20, so
the window and every same-window benchmark moved with it (S&P 500 CAGR 10.1 → 9.5 %: the new window includes the
Sep 2001 fall). Shifts of this size are data revisions, not strategy changes; the page always carries the production
artifact's current figures.

| Number on the page | Value | Artifact / source | Field |
|---|---|---|---|
| Helm v6 CAGR | 10.4 % | `artifacts/results/pead_dial_candidate.json` (generated 2026-10-31; the production copy `gs://experimental-341211-cc-artifacts/results/pead_dial_candidate.json`, regenerated 2026-11-02 on the refreshed price panel) | `kpis.pead_d08.cagr` = 10.38 |
| Helm v6 Sharpe | 1.19 | same | `kpis.pead_d08.sharpe` = 1.187 |
| Helm v6 Sortino | 1.72 | same | `kpis.pead_d08.sortino` = 1.721 |
| Helm v6 max drawdown | −15.6 % | same | `kpis.pead_d08.maxdd` |
| Helm v6 Calmar | 0.67 | same | `kpis.pead_d08.calmar` |
| Window | Jul 2001 → Jun 2026 | same | `meta.sample` = 2001-07-20 .. 2026-06-12, `meta.n_days` = 6261 |
| (removed 2026-11-01) "as traded" row = `kpis.live_exec` — a backtest of the v4.2 executable sizing, not a live record; dropped from the page so no backtest reads as live | — | same | still in `_helm_v6.json` (`kpi.live_exec`, `livefinal`, `m[].live`) for the record, not drawn |
| Live record block | fetched at page load from `https://storage.googleapis.com/helmfinances-public/live_record.json` (static "pending" text when the fetch fails) | written daily by the strategy job, pipeline stage `live_record` (`peadlab.automation.live_record`), from the owner's forward record `GET /api/account/forward` (`api/account.py: forward_record`) — see the "Live record fields" table below | `schema` = `helm-live-record/1` |
| RSI(2) retirement gate "P(ΔSortino>0) 17 %" | 16.9 % | same | `gate.keep_sleeve.P_dSortino_gt0_pct` |
| Vol-dial definition, metric definitions | — | same | `definitions.*` (also `docs/GLOSSARY.md`) |
| Exposure "63 % avg" (model-state chip) | 0.633 | `artifacts/results/helm_sector_hedge.json` | `hedge.unhedged.mean_dial_exposure` |
| Beta to S&P ≈ 0.66 | 0.663 | same | `hedge.unhedged.undialed_beta_to_spy` |
| S&P 500 CAGR / Sharpe / Sortino / maxDD / Calmar | 9.5 % / 0.57 / 0.81 / −55.2 % / 0.17 | computed by `research/export_landing_data.py` via `peadlab.benchmark.benchmark_curve` (SPY total return, same price lake, identical window) | `landing/_helm_v6.json` → `kpi.spy` |
| Growth of $10k: Helm v6 / S&P | $116,407 / $95,516 | `landing/_helm_v6.json` | `v4final` / `spfinal` / `livefinal` (month-end points of the weekly artifact curves `curves.pead_d08`, `curves.live_exec`) |
| Crisis depths on the chart | Dot-com −7.3 / −30.3, GFC −13.1 / −50.5, COVID −1.2 / −21.0, 2022 −7.5 / −23.9 | `landing/_helm_v6.json` | `crises[]` — read off month-end points, so shallower than the daily maxDD |
| Nasdaq-100 (QQQ) | 12.9 % / 0.64 / 0.91 / −54.3 % | Yahoo Finance auto-adjusted close via `yfinance`, identical window, same formulas (`research/export_landing_data.py`) | `landing/_helm_v6.json` → `bench.QQQ` |
| 60/40 (VFINX / VBMFX, monthly rebalanced) | 7.3 % / 0.69 / 0.98 / −35.8 % | same | `bench.60/40` |
| US bonds (Vanguard Total Bond, VBMFX) | 3.4 % / 0.77 / 1.12 / −18.8 % | same (VBMFX chosen because AGG starts 2003-09) | `bench.VBMFX` |
| Berkshire Hathaway (BRK-B, price only) | 10.1 % / 0.56 / 0.84 / −53.9 % | same | `bench.BRK-B` |
| Russell 2000 (IWM) | 8.9 % / 0.48 / 0.68 / −58.6 % | same | `bench.IWM` |
| Developed ex-US (EFA) | 6.5 % / 0.41 / 0.57 / −61.0 % | same | `bench.EFA` |
| Cross-check: VFINX (S&P 500 fund) vs the lake's SPY | 9.5 / 0.57 / 0.81 / −55.3 vs 9.5 / 0.57 / 0.81 / −55.2 | same | `bench.VFINX` — the two data sources agree to 0.1 pp |
| Medallion: 66 % gross / 39 % net avg per year, 1988–2018, no negative gross year | reported | Cornell, *Medallion Fund: The Ultimate Counterexample?* (Dec 2019), via [Institutional Investor, 2020-01-26](https://www.institutionalinvestor.com/article/2bswymr8cih3jeaslxc00/portfolio/famed-medallion-fund-stretches-explanation-to-the-limit-professor-claims) (66 % gross, 1988–2018, no negative gross year) and [CXO Advisory's summary](https://www.cxoadvisory.com/?p=33535) (39.2 % net) | not comparable; shown in the "reported elsewhere" strip only |
| Medallion dispute: "< 35 % compounded before fees" | reported | [Guo & Liu 2024, arXiv:2405.10917](https://arxiv.org/abs/2405.10917) | same strip |
| Berkshire Sharpe 0.79 (full public history) | reported | Frazzini, Kabiller & Pedersen, *Buffett's Alpha*, Financial Analysts Journal 74(4), 2018, [doi:10.2469/faj.v74.n4.3](https://doi.org/10.2469/faj.v74.n4.3) (abstract) | same strip; AQR's summary page quotes 0.76 — the abstract figure is used |
| Hedge-fund index (HFRI) | — | omitted: since-inception statistics are licensed HFR data, not freely citable | — |
| Planning band: Sharpe 0.5–0.8, CAGR 4–7 % net, Sortino 0.8–1.2, maxDD −25…−30 %, ≥ 6 y to confirm | — | `docs/research/CRITICAL_QUANT_REVIEW.md` §4 | table "Honest forward expectations" |
| Fee drag "~1.3 %/yr" | — | same | CQ-4 |
| "mega-cap book Sharpe ~1.1 vs full S&P ~0.4" (hindsight universe) | — | `docs/MODEL_CARD.md` | failure mode F3 |
| Universe = 20 fixed mega-caps; hold 60 sessions; 10 % per name; −15 % hard stop; $1M/day liquidity | — | `src/peadlab/config.py` | `MEGA20`, `BEST_STRATEGY` (`hold_days=60, pct_of_capital=0.10, hard_stop_pct=15.0, min_dollar_vol=1_000_000`) |
| 8 % dial target (v6) | — | `pead_dial_candidate.json` | `promotion_options[0].dial` = 0.08 (recommended); `decision` |
| Short hedge dropped 2026-06 (look-ahead artifact) | — | `docs/research/LOOKAHEAD_REMEDIATION.md`, `docs/MODEL_CARD.md` | — |
| Papers in "The science" | — | `docs/research/` PDFs named on the page | — |

## Live record fields (`live_record.json`, public, NON-identifying — no NAV, currency, broker, account or user)

Rendered only when `long_enough` is true; before that the block shows `months_live` and stays "pending". The
"NOT A BACKTEST" label and the `as_of` date are always shown. Returns are time-weighted (TWR): the chain-linked
daily NAV growth with the broker-reported deposits/withdrawals removed end-of-day (`api/returns.py: twr_index`).

| Field | Meaning | Source |
|---|---|---|
| `as_of` | last day of the record (the latest day with both an account snapshot and a model close) | `forward.end` |
| `live_since` | the live promotion date the record is rebased at | `forward.since` (`LivePointer.since`, scope US-prod) |
| `months_live` | `(as_of − live_since) / 30.4375 days`, 1 decimal | computed in the stage |
| `min_years`, `long_enough` | the bar before any return is shown: `months_live ≥ 12 · min_years`; `min_years` = job env `LIVE_RECORD_MIN_YEARS`, default 6 | `docs/research/CRITICAL_QUANT_REVIEW.md` CQ-5 ("≥ 6 years") |
| `available` | false until there are ≥ 2 days with both a snapshot and a model close | `forward.available` |
| `account_twr_pct` | account flow-adjusted TWR since `live_since`, % | `forward.account_pct` |
| `model_twr_pct` | the executable model curve (`production_equity.csv` column `v21`, USD, gross of fees) over the same days, % | `forward.model_pct` |
| `invested_share` | mean over the window of `AccountSnapshot.invested / value` (days with both > 0), 0–1 | `forward.invested_share` |
| `worst_dd_pct` | worst peak-to-trough fall of the account TWR index over the window, % (≤ 0) | `forward.worst_dd_pct` |
| `n_days` | trading days in the record | `forward.n` |
| `flows_adjusted` | whether the broker reported flows (false ⇒ the account return is a naive chain) | `forward.flows_adjusted` |
| `version` | the promoted model version string | `forward.version` |

## Caveats carried on the page (and where they come from)
- **Hindsight universe** — the 20 names are today's mega-caps (MODEL_CARD F3; `pead_dial_candidate.json` `caveats[3]`).
- **In-sample dial** — the 8 % target was picked on the same history (`caveats[1]`).
- **Gross of fees** — no commission/slippage in the curves (CRITICAL_QUANT_REVIEW CQ-4).
- **Backtest ≠ live** — the Live-record block shows a live number only once `long_enough` (≥ `min_years`, default 6, CQ-5); until then "pending" + months live (`caveats[0]`).
- **Not market-neutral** — beta ≈ 0.66; crisis losses are the beta leg (`helm_sector_hedge.json`, `docs/research/CRISIS_ANATOMY.md`).

Illustrative mock-ups (gate chain, model state, drift timeline) carry an explicit "illustrative" label and are not data.
