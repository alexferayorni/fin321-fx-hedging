# Stage 4 — FX Hedge Recommendation

**Author:** Alex Ferayorni
**Course:** FIN 321
**Date:** 2026-04-28

---

## Section A — Exposure Summary

The firm holds a 12-month EUR receivable of €10,869,565, equivalent to approximately $12.5 million at today's spot rate of 1.15 USD/EUR. Realized USD proceeds depend entirely on the EUR/USD rate at settlement: a 1% EUR depreciation translates to roughly $125,000 of foregone proceeds, and a 5% depreciation to roughly $625,000 — a material swing on a single budgeted cash flow. Without a hedge, the firm absorbs full FX volatility into reported earnings, creating budget variance, P&L noise, and the risk that proceeds fall short of the planning assumption underpinning Q4 cash forecasting. Treasury's task is to select an instrument that stabilizes USD proceeds against the downside without imposing unacceptable upfront cost or speculative risk.

## Section B — Summary of Hedge Outcomes

USD proceeds at S_T = S0 = 1.15 for each modeled strategy:

| Strategy | USD Proceeds at S_T = 1.15 | Risk Profile |
|---|---|---|
| Unhedged | $12,500,000 | Full exposure to EUR depreciation |
| Forward (locked at 1.0910) | $11,858,695 | Certainty, but locks in $640K below spot |
| Money Market | $12,620,592 | Synthetic forward; $762K above quoted Forward — IRP residual |
| Put on EUR (K = 1.0910) | $12,105,440 | Floor near forward rate; full upside; $394K premium FV |
| Call on EUR (K = 1.2000) | $12,274,540 | Speculative comparison only — adds long-EUR exposure |

The **Forward** delivers certainty, but at a punitive rate. The quoted F0 = 1.0910 sits roughly 6% below the IRP-implied forward of 1.1611 (Stage 3 §3), so locking the forward in today crystallizes that pricing gap as a permanent reduction in proceeds. At spot, the Forward gives up $640K against the unhedged outcome.

The **Money Market** hedge produces $762K more than the Forward at every S_T — but only because the quoted Forward violates covered IRP. That gap is not free money. It exists either because (a) the rate quotes and the forward quote are non-contemporaneous, in which case the gap is an artifact of stale data, or (b) the market is pricing a real cross-currency basis that a synthetic MM construction does not replicate at execution. Either reading frames the MM "advantage" as a data finding, not a tradeable spread.

The **Put on EUR** floors realized proceeds near the forward rate while preserving full upside. Break-even sits at S_T = 1.0547 — below this the put strictly outperforms unhedged net of premium. Premium cost is bounded and paid at inception: $380K spot, $394K FV.

The **Call on EUR** is not a hedge for this exposure. The receivable is already long EUR; layering a long EUR call doubles the upside bet rather than protecting the downside. The model includes it for visual comparison only.

Unhedged is the riskiest path and is rejected on its face.

## Section C — Sensitivity Interpretation

The Stage 2 sensitivity table varies S_T from 0.90 × S0 to 1.10 × S0 (1.0350 to 1.2650) in 1% steps.

**EUR depreciation (S_T < 1.15).** Unhedged proceeds fall linearly with S_T. At the −10% endpoint of S_T = 1.0350, unhedged drops to $11.25M — about $609K below the Forward. The **Forward** stays flat at $11.86M across every scenario; its protective value materializes on the downside. The **Money Market** stays flat at $12.62M, holding its $762K parity gap over the Forward because it is itself a synthetic forward. The **Put hedge** now exhibits its full protective shape inside the visible grid. At S_T = K_PUT = 1.0910 (~5.13% below spot), the Put delivers exactly K_PUT × FC_AMT − FV_PREM_PUT = $11,464,135. For every row at or below K_PUT — the rows beyond roughly −5.13% — the Put line goes flat at $11,464,135 while Unhedged continues falling. That horizontal Put line is the protective shape the hedge buys: a known floor regardless of how deep EUR depreciation runs. Importantly, the Forward sits $394K above the Put floor across the deep downside: Forward proceeds are K_PUT × FC_AMT = $11,858,695 (since F0 = K_PUT), versus the Put's $11,464,135. The Forward strictly dominates the Put on a single-point downside comparison by exactly the premium FV; the Put pays for that $394K gap by preserving upside. The **Call** falls with S_T in this region — an unhedged position with a premium drag, the worst of both worlds in a downside scenario.

**EUR appreciation (S_T > 1.15).** Unhedged rises linearly. The Forward and MM stay flat at $11.86M and $12.62M — opportunity cost begins here. The **Put** rises one-for-one with S_T net of the $394K premium, overtaking the Forward at S_T ≈ 1.1273 and crossing the MM line at S_T ≈ 1.1974 (~+4.2%); above that point the Put dominates every other strategy. The **Call** rises faster than unhedged once S_T crosses K_CALL = 1.20, but only after recovering the premium drag from the lower scenarios.

The decisive insight: the Forward strictly dominates the Put on the downside (by the premium), and the Put strictly dominates the Forward on the upside (above ~1.1273). The choice between them is therefore a directional question — and the Put is the right answer for a firm that views the quoted F0 = 1.0910 as understating the true forward. The MM line dominates both on the downside but inherits the IRP-residual data risk in full.

## Section D — Strategic Recommendation

**Recommendation: Hedge with a long EUR put option (K_PUT = 1.0910, premium = $0.0350/EUR).**

1. **Floors net realized USD proceeds at $11,464,135** — equivalent to the Forward's $11.86M gross floor less the $394K premium FV. Downside protection is comparable to the Forward; upside is preserved.
2. **Avoids exposure to the IRP residual.** Buying protection at K = F0 lets the firm benefit from any reversion if the quoted Forward proves to be mispriced or stale; the Forward and MM strategies both crystallize the residual into realized proceeds.
3. **Premium cost is bounded and known at inception.** $394K FV (~3.2% of exposure) is paid upfront, requires no margin or collateral, and is fully accounted for in cash and earnings at trade date.

**Alternative.** If treasury policy mandates absolute certainty over flexibility, the Forward at 1.0910 is acceptable — but it should be executed only after re-querying contemporaneous EUR/USD rate and forward quotes to confirm whether the IRP residual reflects a genuine pricing dislocation or a non-contemporaneous data input. Locking in a 6% IRP gap on stale data is not a hedge; it is a documented loss.

## Section E — Executive Justification

Decision criteria a CFO cares about, applied to the put recommendation:

- **Cash flow stability.** The put floors net realized proceeds at $11.46M. The downside is bounded and budgetable; treasury can plan Q4 cash on a known worst-case rather than a stochastic distribution.
- **Budget certainty.** Less rigid than the Forward, but the worst-case is fixed and the upside is preserved. The Forward delivers more headline certainty at a known $640K cost relative to spot — and a potentially larger cost if the IRP residual reflects stale data.
- **Liquidity impact.** Premium of $380K spot ($394K FV) is paid at inception. Minor liquidity drag relative to the Forward's zero-cost execution, but the put carries no margin posting, no daily mark-to-market settlement, and no collateral arrangements.
- **Optionality value.** The put preserves participation in EUR appreciation. With current spot below historical mean, mean reversion is a plausible scenario — and the Forward locks the firm out of that outcome entirely.
- **Premium cost in context.** ~3.2% of exposure is modest against the $640K opportunity cost the Forward locks in at current spot and the $762K IRP-residual exposure both Forward and MM inherit.
- **Robust to the IRP finding.** The put recommendation does not depend on resolving the Stage 3 §3 data discrepancy; the Forward and MM recommendations both do.

**Next steps.** Re-query EUR/USD rates contemporaneous with the forward quote to validate the IRP residual; confirm hedge accounting designation under ASC 815 (cash-flow hedge of a forecasted foreign-currency receivable) if applicable; execute the put with a tier-1 counterparty.

---

## Section F — Structured AI Prompt

The prompt below is a self-contained instruction set that an AI coding assistant could execute to regenerate the Stage 2 FX hedging workbook from scratch using the Stage 3 locked inputs. It specifies inputs, named-range conventions, per-strategy formulas, the sensitivity table and chart, verification diagnostics, and formatting rules — anything outside this prompt is unnecessary to reproduce the model.

```
# GOAL

Build a 1-year EUR/USD hedge analysis workbook for a U.S. firm with a single EUR receivable settling in 360 days. Produce one .xlsx file containing two sheets ("Main Model" and "Notes & Assumptions") that models five strategies — Forward, Money Market, Put on EUR, Call on EUR, and Unhedged — across an eleven-scenario sensitivity grid in ending spot, with verification diagnostics and a comparison chart. The output is a decision-support tool for treasury leadership, not a black-box calculator: every formula must reference named ranges, every diagnostic must be visible, and every assumption must be explicit on the Notes sheet.

# INPUT VARIABLES

All ten user inputs and two derived values are entered or computed in the Inputs section of the Main Model sheet. Rates are stored as decimals (3.713% = 0.03713), not percent. Strikes and premiums are quoted in USD per EUR.

| Named Range | Value | Unit | Cell |
|---|---|---|---|
| FC_AMT | 10869565 | EUR | C6 |
| T_DAYS | 360 | days | C7 |
| S0_in | 1.15 | USD/EUR | C10 |
| F0_in | 1.0910 | USD/EUR | C11 |
| R_USD | 0.03713 | annual decimal | C12 |
| R_FC | 0.02722 | annual decimal | C13 |
| K_PUT | 1.0910 | USD/EUR | C16 |
| PREM_PUT | 0.0350 | USD/EUR | C17 |
| K_CALL | 1.2000 | USD/EUR | C18 |
| PREM_CALL | 0.0200 | USD/EUR | C19 |
| DAY_CT | 360 | days/yr | C22 |
| T_YRS | =T_DAYS/DAY_CT | derived | C23 |

# NAMED RANGE CONVENTIONS

Create a workbook-scoped named range for every input cell above, using the exact names in the table. The full set of twelve names to define: FC_AMT, T_DAYS, S0_in, F0_in, R_USD, R_FC, K_PUT, PREM_PUT, K_CALL, PREM_CALL, DAY_CT, T_YRS. Every formula in the workbook must reference these named ranges; raw cell addresses (e.g., $C$10) are not permitted in the strategy, sensitivity, or summary blocks. This produces formulas that read as financial expressions and lets a reviewer audit logic without tracing cell references.

# MODEL LOGIC

Simple interest is used throughout: FV = PV × (1 + r × T_YRS). All option payoffs are European-style at maturity. Sign convention: positive numbers are USD inflows to the firm.

**Forward Hedge** (rows 25–27)
- Locked USD proceeds: `=FC_AMT*F0_in`
- Effective USD/EUR rate: `=F0_in`

**Money Market Hedge** (rows 29–36) — three construction steps plus two diagnostics
1. Borrow PV of EUR receivable at R_FC: `=FC_AMT/(1+R_FC*T_YRS)`
2. Convert proceeds to USD at spot: `=<step1>*S0_in`
3. Invest USD at R_USD to maturity: `=<step2>*(1+R_USD*T_YRS)`
4. MM final USD proceeds = step 3
5. Parity diagnostic (MM − Forward, in USD): `=<MM_final>-<Forward_proceeds>`
6. IRP-implied forward: `=S0_in*(1+R_USD*T_YRS)/(1+R_FC*T_YRS)`

**Put Option Hedge** (rows 38–46) — protects against EUR depreciation; floors USD rate at K_PUT
- Premium spot cost (USD): `=PREM_PUT*FC_AMT`
- Premium FV at maturity: `=<spot_cost>*(1+R_USD*T_YRS)`
- Break-even ending spot: `=IFERROR(K_PUT - <FV_premium>/FC_AMT, "")`

**Call Position on EUR** (rows 38–47) — speculative comparison, not a hedge for a long-EUR receivable; included for visual reference only
- Premium spot cost (USD): `=PREM_CALL*FC_AMT`
- Premium FV at maturity: `=<spot_cost>*(1+R_USD*T_YRS)`
- Break-even ending spot: `=IFERROR(K_CALL + <FV_premium>/FC_AMT, "")`

# SENSITIVITY ANALYSIS

Build a twenty-one-row sensitivity table covering ending spot S_T from 0.90 × S0_in to 1.10 × S0_in in 1% increments (−10% to +10%). Columns:

- Column B: % change from spot (−10% to +10% in 1% steps)
- Column C: S_T = `=S0_in*(1+B)`
- Column D: Unhedged = `=C*FC_AMT`
- Column E: Forward = `=FC_AMT*F0_in` (flat across all rows)
- Column F: Money Market = `=FC_AMT*S0_in*(1+R_USD*T_YRS)/(1+R_FC*T_YRS)` (flat across all rows)
- Column G: Put on EUR = `=MAX(K_PUT, C)*FC_AMT - <FV_PREM_PUT>`
- Column H: Call on EUR = `=C*FC_AMT + MAX(C - K_CALL, 0)*FC_AMT - <FV_PREM_CALL>`

Insert a line chart titled "USD Proceeds by Hedging Strategy across S_T". X-axis: column C (S_T). Five series: columns D, E, F, G, H. Legend labels: Unhedged, Forward, Money Market, Put on EUR, Call on EUR.

# VERIFICATION

The model must surface the following diagnostic outputs as labeled cells on the Main Model sheet, not buried in formulas:

1. **Forward vs. MM parity diagnostic** (MM final − Forward proceeds, in USD). Expected to be non-zero (~+$762K) under the locked inputs because the quoted F0_in violates covered IRP — see §3 of the Stage 3 spec. The residual is a deliberate output, not a bug, and its magnitude is risk intelligence for the CFO.
2. **IRP-implied forward** displayed adjacent to the quoted F0_in for visual comparison. Implied ≈ 1.1611; quoted = 1.0910.
3. **Put break-even S_T** and **Call break-even S_T** as standalone labeled cells.
4. **Summary Outputs block** (rows 65–72): one row per strategy showing USD proceeds, plus a final row labeled `HEDGE RECOMMENDATION` left blank for human input in the written deliverable.

# FORMATTING & EXPORT

- **Sheet 1 — "Main Model":** six vertical sections in this order — Inputs, Forward, Money Market, Options, Sensitivity, Summary.
- **Sheet 2 — "Notes & Assumptions":** pure-text documentation of day-count (ACT/360), simple-interest convention, European-exercise assumption, IRP residual note, and exclusions (bid-ask, taxes, hedge accounting, counterparty credit).
- **Color coding:** Yellow = user inputs (editable); Blue = assumptions (editable); Green = formula / intermediate cells; Gray = output / KPI cells.
- **Number formats:** currency cells as USD with thousands separators ($#,##0.00); FX rates to four decimals (0.0000); rates displayed as percent with 2 decimals (0.03713 → 3.71%) but stored as decimals.
- **File output:** single .xlsx workbook.
```

---

**Word count of the prompt itself:** 717 words (content inside the fenced code block, excluding the Markdown table delimiters as separate tokens).

**Ambiguities flagged but not resolved:**

1. **Exact row layout of the Options block.** The spec gives Put as rows 38–46 and Call as rows 38–47, which overlap. The prompt preserves both row ranges as written but does not prescribe whether the Call sits to the right of the Put in the same rows or stacks below — an AI building from this prompt would need to choose a layout.
2. **Chart type for the Money Market and Forward series.** Both are flat lines across S_T. A line chart will render them as horizontal lines, which is correct, but a reviewer might expect them as reference bands. The prompt specifies a line chart per the Stage 3 spec without resolving this presentation choice.
3. **Sensitivity table starting row.** The Stage 3 spec does not lock a starting row for the sensitivity block; the prompt inherits this and leaves it to the implementer.
4. **Whether the Notes sheet should mirror Stage 3 §3's IRP residual narrative verbatim** or paraphrase it. The prompt asks for documentation of the residual but does not pin the exact wording.

---

## Extra Credit — Areas for Further Study & Improvement

### GitHub & Version Control

This deliverable was built in four discrete, version-controlled stages — Stage 1 memo, Stage 2 populated workbook, Stage 3 technical specification, Stage 4 final memo — each landed as a named commit on `main`. The result is an auditable trail in which every analytical decision is timestamped and reversible: the IRP residual finding documented in Stage 3 §3, the Stage 1→2 framing pivot from ratio analysis to USD-proceeds sensitivity, and the Stage 4 expansion of the sensitivity grid from ±5% to ±10% each appear as a discrete diff against the prior state. A hedge accounting auditor reviewing ASC 815 evidence would have line-level visibility into when each assumption was made and how it evolved — provenance that a flat .xlsx-by-email workflow cannot reproduce. Diff-based review also surfaces unintended modifications: extending the sensitivity range touched two lines of the Stage 3 spec, ruling out scope creep at a glance. The natural extension for ongoing treasury work is feature branches: keep the live hedge on `main`, explore alternatives (collar payoffs, Black-Scholes-derived premiums, two-dimensional sensitivity over rate differentials) on isolated branches, and merge only what survives review. The Stage 3 §3 IRP residual is preserved in the repo exactly as identified; any future analyst can verify whether subsequent contemporaneous quotes resolved it or whether it persisted as a cross-currency basis signal worth flagging.

### AI Skills & Automation

The Stage 3 spec's locked input table and named-range conventions are themselves a machine-readable contract: an AI coding agent can rebuild the populated workbook from that spec without human intervention, which is exactly what the Section F prompt formalizes. The more valuable extension is live market data — a tool-augmented agent could pull contemporaneous EUR/USD spot, forward, and risk-free rate quotes on demand and re-run the model end-to-end, directly addressing the IRP residual by replacing the locked Stage 1 inputs with synchronized quotes and reporting whether the ~$762K parity gap collapses or persists. The deterministic 21-row sensitivity grid is also a natural candidate for stochastic extension: wrapped in a 10,000-path Monte Carlo on EUR/USD with an implied-volatility input, it would produce a distribution of USD proceeds per strategy rather than point estimates — a more honest representation of uncertainty for a CFO audience. Multi-file reasoning is the third frontier already in evidence here: Stage 4 was drafted with Claude Code reading the Stage 3 spec and populated workbook in tandem, surfacing analytical errors in real time — including the Forward-vs-Put dominance correction in Section C that a human drafter working from a single document could plausibly have missed.
