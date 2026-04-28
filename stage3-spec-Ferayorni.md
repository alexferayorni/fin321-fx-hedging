# Stage 3 Technical Specification — FX Hedging of EUR Receivable

**Author:** Alex Ferayorni
**Course:** FIN 321
**Date:** 2026-04-24
**Scope:** Defines the computational specification for the Stage 2 model populated with locked inputs, and prepares the structured backbone for the Stage 4 AI-assisted recommendation.

---

## 1. Problem Statement

The firm will receive a single euro-denominated payment of **EUR 10,869,565** in **360 days**, with a USD-equivalent of approximately **$12.5 million** at the current spot rate of 1.15 USD/EUR. This receivable exposes the firm to EUR depreciation between trade date and settlement: a 1% decline in EUR translates to roughly $125,000 in foregone USD proceeds, and a 5% decline to roughly $625,000 — a material swing on a single cash flow. Treasury's mandate is to select a hedging instrument that stabilizes USD proceeds against this downside without imposing unacceptable upfront cost or speculative risk.

## 2. Inputs (Known Variables)

All inputs are named ranges in the Stage 2 model; Stage 3 calculations reference them by name only.

| Named Range | Value | Unit | Source |
|---|---|---|---|
| FC_AMT | 10,869,565 | EUR | Derived from $12.5M / S0_in (Stage 1 memo) |
| T_DAYS | 360 | days | Stage 1 memo ("one year") |
| S0_in | 1.15 | USD/EUR | Stage 1 memo |
| F0_in | 1.0910 | USD/EUR | Stage 1 memo |
| R_USD | 3.713% | annual | Stage 1 memo |
| R_FC | 2.722% | annual | Stage 1 memo |
| K_PUT | 1.0910 | USD/EUR | Stage 3: ATM-forward convention |
| PREM_PUT | 0.0350 | USD/EUR | Stage 3: ~8–9% 1Y implied-vol calibration |
| K_CALL | 1.2000 | USD/EUR | Stage 3: OTM ~4.3% above spot |
| PREM_CALL | 0.0200 | USD/EUR | Stage 3: ~8–9% 1Y implied-vol calibration |
| DAY_CT | 360 | days/yr | Derived: ACT/360 convention |
| T_YRS | 1.0000 | years | Derived: T_DAYS / DAY_CT |

## 3. Assumptions & Constraints

- **Day-count.** ACT/360, matching money-market quoting convention and the Stage 2 model's hard-coded denominator.
- **Compounding.** Simple interest throughout: `FV = PV × (1 + r × T_YRS)`. Applies to MM construction and option-premium gross-ups. Continuous compounding (`e^(r·T)`) flagged in §6 as a Stage 4 refinement.
- **IRP (in principle).** Under covered IRP the MM hedge equals the Forward hedge; the model embeds a parity residual and an IRP-implied forward to surface any inconsistency. See Data Consistency Note below.
- **Excluded effects.** Bid-ask spreads, transaction costs, commissions, taxes, hedge accounting (ASC 815 / IFRS 9), and counterparty credit risk. Option premiums are assumed paid at inception and financed at R_USD to a maturity-date comparable.
- **Option conventions.** European-style exercise. K_PUT at the forward (ATM-forward); K_CALL ~4.3% above spot; premiums sized to ~8–9% 1Y implied vol. Black-Scholes calibration is out of scope for Stage 3.
- **Hedge horizon.** Single 1-year static hedge. No rolling, layering, or dynamic rebalancing.

### Data Consistency Note — IRP Residual

The locked inputs violate covered IRP. Implied forward: `S0_in × (1 + R_USD × T_YRS) / (1 + R_FC × T_YRS) ≈ 1.1611 USD/EUR`. Quoted `F0_in = 1.0910` — a discrepancy of approximately 0.0701 in the spot rate (≈700 pips, or ~6% of the quoted forward).

This is a documented finding, not a modeling error. Plausible causes: (a) rates and forward quote drawn from different trade dates; (b) a liquidity or counterparty-specific adjustment in the quoted forward not captured by the two risk-free rates; (c) an unobserved cross-currency basis. In each case the quoted forward is the executable hedge rate, so the model uses `F0_in = 1.0910` for the Forward strategy. The MM parity diagnostic therefore produces a non-trivial residual — a deliberate output, not a bug, whose magnitude is itself risk intelligence for the CFO and should be reported, not suppressed.

## 4. Calculation Flow

Variable names only; the Stage 2 model implements each line as a named-range formula.

**Forward hedge.** Locked USD: `FC_AMT × F0_in`. Effective rate: `F0_in`. Flat across S_T.

**Money market hedge.** Three steps: (1) borrow the PV of the EUR receivable at R_FC — `FC_AMT / (1 + R_FC × T_YRS)` — repaid by the receivable; (2) convert to USD at `S0_in`; (3) invest at R_USD: final USD = step-2 × `(1 + R_USD × T_YRS)`. Diagnostics: **parity residual** (MM − Forward) and **IRP-implied forward** `S0_in × (1 + R_USD × T_YRS) / (1 + R_FC × T_YRS)`. Divergence indicates input inconsistency, not model error (see §3).

**Put hedge on EUR.** Payoff: `MAX(K_PUT, S_T) × FC_AMT − FV(PREM_PUT × FC_AMT)`, with `FV(x) = x × (1 + R_USD × T_YRS)`. Floors the USD rate at K_PUT; preserves upside above K_PUT. Break-even: `K_PUT − FV(PREM_PUT × FC_AMT) / FC_AMT`.

**Call position on EUR** (speculative comparison, not a hedge). Payoff: `S_T × FC_AMT + MAX(S_T − K_CALL, 0) × FC_AMT − FV(PREM_CALL × FC_AMT)`. A long EUR call layered on an already-long-EUR receivable adds exposure; included for comparison, not risk reduction. Break-even: `K_CALL + FV(PREM_CALL × FC_AMT) / FC_AMT`.

**Unhedged baseline.** `S_T × FC_AMT`. Linear reference payoff.

## 5. Outputs

The Stage 2 model produces:

- **Forward hedge:** locked USD proceeds and effective USD/EUR rate.
- **Money market hedge:** final USD proceeds; parity residual (USD); IRP-implied forward (USD/EUR).
- **Options:** put premium in USD (spot and FV); call premium in USD (spot and FV); put break-even S_T; call break-even S_T.
- **Sensitivity table:** USD proceeds by strategy across twenty-one ending-spot scenarios from 0.90 × S0_in to 1.10 × S0_in in 1% increments. Columns: Unhedged, Forward, Money Market, Put on EUR, Call on EUR.
- **Line chart:** "USD Proceeds by Hedging Strategy across S_T" — five series matching the sensitivity columns, plotted against realized ending spot on the x-axis.
- **Summary outputs block:** per-strategy USD proceeds KPIs plus a blank `HEDGE RECOMMENDATION` row reserved for the Stage 4 deliverable.

## 6. Model Review — What Worked and What to Improve

**Framing evolution from Stage 1 to Stage 2.** The Stage 1 memo framed analysis around six ratio categories — performance, profitability, efficiency, leverage, liquidity, and a DuPont ROE decomposition. Ratio analysis is the right lens for comparing the firm's financial health under competing hedge programs; it integrates hedge impact with balance-sheet structure and capital returns. It is the wrong lens for comparing the hedge instruments themselves. Forwards, money-market hedges, and options produce different USD proceeds as a function of a single uncertain variable (the ending spot rate), and the relevant comparison is the shape of that payoff. Stage 2 correctly pivoted to a USD-proceeds-and-sensitivity framework. Stage 3 formally ratifies that pivot: hedge instrument selection proceeds from scenario analysis of USD proceeds, and ratio-based evaluation is deferred to Stage 4, where the chosen hedge's impact on firm-level ratios becomes the strategic discussion.

**What worked in the Stage 2 build.** The named-range architecture produces auditable formulas that read as financial expressions rather than cell addresses. The parity residual and IRP-implied forward diagnostics make inconsistency between the quoted forward and the two risk-free rates visible at a glance — a critical sanity check that separates input error from model error. Break-even calculations for both options are in place. The sensitivity table and chart share a common x-axis, ensuring visual comparability. The color convention (yellow inputs, blue assumptions, green formulas, gray outputs) supports reviewer navigation.

**What to improve.**

- **Collar strategy is not modeled.** With K_PUT and K_CALL already parameterized, a collar (long put + short call) is a single additional row in the sensitivity table and a natural candidate for Stage 4. It should be added before any recommendation is finalized.
- **Option premiums are selected by convention, not derived.** A rigorous version would compute PREM_PUT and PREM_CALL from a Black-Scholes pricing function given an implied-volatility input, and would display the greeks (delta, vega) at the chosen strikes.
- **Simple interest is used throughout.** Continuous compounding is the market-standard convention for option premium financing and produces slightly different FV figures. At T_YRS = 1.0 and low rates the difference is small but non-zero; the choice should be explicit, not implicit.
- **The call position is a speculative comparison, not a hedge.** For a firm already long EUR through the receivable, a long EUR call adds exposure rather than reducing it. Its inclusion in the sensitivity table serves as a side-by-side reference that illustrates why the put is the natural hedge; a clearer label would be "Long Call (speculative, shown for comparison)."
- **Hedge accounting impact is not modeled.** Forwards and MM hedges under ASC 815 cash-flow designation defer mark-to-market P&L through OCI; undesignated hedges run through earnings. This affects reported earnings volatility and is material to a CFO audience.
- **Sensitivity is one-dimensional.** Varying only S_T ignores the second-order risk of IRP breakdown — the possibility that the rate differential widens or narrows during the hedge horizon. A 2D sensitivity over S_T × (R_USD − R_FC) would expose this, and is recommended for Stage 4.
- **IRP residual reconciliation.** Stage 4 should either re-query market data to obtain contemporaneous rate and forward quotes, or formally document the residual (see §3) as a cross-currency basis signal worth flagging to treasury leadership.

**Auditability improvements.** Add in-cell comments to every formula documenting its financial meaning. Separate the input sheet from the calculation sheet to prevent accidental overwrites. Display the parity residual in basis points rather than USD to make cross-scenario comparison scale-free. Add an explicit input-validation row that flags negative rates, zero FC_AMT, or T_DAYS outside a plausible range.

## 7. Sensitivity Plan

Ending spot rate S_T is varied from **0.90 × S0_in to 1.10 × S0_in in 1% increments**, producing twenty-one scenarios spanning −10% to +10%. This range captures plausible 1-year EUR/USD drift under normal volatility regimes — 1-year realized volatility of roughly 8–9% implies a one-standard-deviation move in the 8–9% range, so ±10% spans slightly more than one standard deviation in either direction. Equally important, ±10% ensures that the Put strike (K_PUT = 1.0910, ~5.13% below spot) sits visibly within the protected region of the chart: at S_T ≤ K_PUT the Put line flattens at the floor of $11,464,135, and that flattening must be visible for the chart to communicate the hedge's protective shape. A ±5% range would have placed the strike at the very edge of the grid, hiding the floor.

The sensitivity chart is designed to make four comparisons immediate:

- **Forward vs. Money Market.** The two lines will NOT overlay given the current inputs — the ~6% forward discrepancy produces a visible gap. That gap is the visual expression of the IRP violation documented in §3 and is itself a diagnostic output.
- **Forward vs. Put.** In upside scenarios (S_T > K_PUT), the put line sits below the forward by the FV of the premium — the cost of retaining optionality. In downside scenarios the put line flattens at the floor and matches or exceeds the forward, depending on where K_PUT sits relative to F0_in.
- **Put vs. Unhedged.** The characteristic insurance payoff: convex, floor-and-participate. The gap on the downside is the hedge's protective value; the gap on the upside is the premium cost.
- **Call vs. Unhedged.** The call line lies below unhedged by the FV of the call premium across scenarios where S_T < K_CALL, and kinks upward past K_CALL. The visual confirms that the call is a speculative position, not a hedge.

## 8. Limitations and Next Steps

**Explicitly out of scope for Stage 3:**

- Dynamic or rolling hedge programs. This specification covers a single 1-year static hedge.
- Credit and counterparty risk on the forward, money-market, or option counterparties.
- Hedge accounting treatment under ASC 815 or IFRS 9; no OCI / P&L designation analysis.
- Bid-ask spreads, broker commissions, or any transaction cost.
- Stochastic modeling (Monte Carlo paths, VaR, CVaR, expected shortfall).
- Basis risk. EUR/USD spot is liquid and the receivable is denominated directly in EUR, so there is no basis between the hedge underlier and the exposure.

**Feeding Stage 4.** Stage 4 should:

- Resolve or formally document the IRP residual identified in §3.
- Populate the Stage 2 Excel with the §2 locked values and verify formula outputs.
- Implement the collar strategy and the auditability improvements listed in §6.
- Produce the final hedge recommendation for the `HEDGE RECOMMENDATION` row, citing USD-proceeds figures from the populated sensitivity table, the parity residual, and the chosen hedge's ratio-level impact per Stage 1's framing.
