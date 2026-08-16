# ClearSight: PM Artifact Pack

*An AI-assisted markdown and exit-stock decision copilot for multi-store seasonal retailers.*

> Companion documents: strategy and opportunity framing in `Product_Strategy.md`; data, model and evaluation in `../Metrics/Data_Model_Evaluation.md`; economics in `../Economics/Business_Economics_Scaling.md`; ethics and risk in `../Risk_Note/Ethics_Governance_Risk.md`. All are integrated in `02_Final_Report.md`.

> **All figures synthetic or illustrative.** No confidential employer data.

---

## 1. Problem Statement

Seasonal merchandise planners at multi-store retailers must decide how deeply to discount end-of-season stock, and when. Today this is done in spreadsheets, at national level, using last season's analogues and experience.

Two failures repeat every season:

- **Discounting too early**: margin given away on stock that would have sold closer to full price
- **Discounting too late**: terminal stock cleared at or below cost, consuming store and DC space, then written off

The decision is made nationally because analysing thousands of SKUs across hundreds of stores manually is not feasible. So one discount schedule is applied to stores with very different demand, and it is wrong, in one direction or the other, for most of them.

**Who has this problem:** seasonal merchandise planners and category buyers at retailers with 100+ stores and defined seasonal ranges: discount department stores, apparel and footwear chains, homewares.

**Why now:** markdown is one of the largest controllable margin levers in this segment, the decision repeats every season for every seasonal SKU, and the data needed to do it better (sales history, stock on hand, product attributes) already exists in every retailer's systems.

---

## 2. Persona

**Priya, Seasonal Merchandise Planner.** Owns 3–4 categories, roughly 2,000 seasonal SKUs a year across 312 stores. Runs exit planning in Excel exported from the merchandise system. Has been burned in both directions: a range marked down 30% in week 8 that was selling out in coastal stores anyway, and a bulky outdoor line held too long that ate DC space into the next season's intake.

What she needs from a tool: a defensible reason for each decision (she answers to a trading manager), speed (exit reviews compete with next season's buy for her time), and the ability to say no to it. What would make her abandon it: one confidently wrong recommendation she followed and had to explain upward.

Error cost is asymmetric and Priya knows it: over-discounting a winner loses real margin now; holding a loser too long loses less per unit but clogs stores and DCs. The tool has to respect that asymmetry, not just forecast accurately.

---

## 3. Product Vision

Give merchandise planners a trusted second opinion on every markdown decision, so discounts are set by evidence about what will actually sell in each store cluster rather than by a single national rule.

ClearSight is a review assistant, not an autonomous repricing engine. The planner always decides.

---

## 4. Jobs To Be Done

> When I am approaching the end of a season with stock still on hand across many stores,
> I want to know which items need discounting, how deep, and when,
> so that I recover as much margin as possible without being left with unsellable stock.

Secondary job (category buyer): *understand which ranges consistently need rescuing, so next season's buy is better.*

---

## 5. Why AI, and why not something simpler

| Alternative | Why it falls short |
|---|---|
| Flat markdown schedule (current practice) | Assumes demand is homogeneous across stores. It is not, weather, demographics, and local competition make the same SKU a winner in one cluster and dead stock in another |
| Rules ("if sell-through < X% by week Y, cut Z%") | Rules need history to calibrate. Seasonal private-label ranges are substantially new each year, so there is no per-SKU history to write the rule from |
| Dashboard / BI | Shows what happened. Cannot estimate what happens next under a 20% cut now versus a 35% cut in three weeks, the counterfactual is the decision |
| More analysts | The volume is the problem: thousands of SKUs × store clusters × weekly decisions. Adding people does not scale and does not standardise |

The AI task: predict residual sell-through per store cluster at candidate price points, generalising from product attributes and analogue lines where direct history does not exist. That generalisation step is what rules and dashboards cannot do.

---

## 6. PRD Summary

**User problem.** Planners cannot evaluate markdown options at store-cluster level, so they default to a national rule that is wrong for most stores.

**Core workflow.**

1. Planner opens the exit-stock worklist for a category and season
2. ClearSight ranks SKUs by risk: value at stake × likelihood of not clearing
3. For each SKU, it shows projected sell-through at several discount depths and timings, with a confidence level
4. It recommends one option
5. Planner approves, adjusts, or rejects, adjustments and rejections require a reason code
6. Decision and reason are logged; outcomes feed back for evaluation

**AI role.** Forecast residual sell-through per store cluster at candidate price points, then rank and recommend. Not price setting, not execution.

**Deliberately out of scope.** Automatic price changes, in-season promotional pricing, competitor price matching, replenishment.

**Data assumptions.** Two years of sales history by SKU / store / week, current stock on hand, product attribute data clean enough to match analogues, and past markdown events with outcomes. If attribute data is poor, analogue matching degrades and more SKUs route to manual, the system must degrade honestly rather than guess.

*Capstone note: prototype and backtest use synthetic or public retail data only (e.g. an open multi-store sales dataset). No confidential employer data. Workflow knowledge is used as context, not as data.*

**Fallback logic.** Below a confidence threshold, ClearSight shows no recommendation and routes the SKU to manual decision, stating why confidence is low (no analogue, sparse history, conflicting signals). New products with no usable analogue are flagged as such rather than guessed at.

**Key risks.** Confident-but-wrong recommendations at scale; planners either rubber-stamping or ignoring output; feedback loops, after season one, the model observes outcomes of prices it influenced, which biases naive retraining.

---

## 7. Feature Prioritisation

| Feature | User value | Effort | Confidence | Priority |
|---|---|---|---|---|
| Sell-through forecast by store cluster | High | High | Medium | **P0** |
| Ranked exit-stock worklist | High | Low | High | **P0** |
| Markdown depth + timing recommendation | High | Medium | Medium | **P0** |
| Confidence display and low-confidence routing | High | Medium | High | **P0** |
| Override with reason capture | High | Low | High | **P0** |
| Backtest against current flat-schedule rule | High | Medium | High | **P0** |
| Store clustering configuration | Medium | Medium | Medium | P1 |
| Outcome tracking dashboard | Medium | Medium | High | P1 |
| Buyer-facing range performance view | Medium | Medium | Medium | P2 |
| POS / ERP write-back | Low for MVP | High | Low | P2 |

P0 is the MVP. Confidence display and override capture are P0, in a deterministic tool they would be polish; in a probabilistic one they are the trust mechanism, and trust is the adoption risk. Nothing in P1+ is built until planners act on P0 recommendations.

---

## 8. MVP Definition

**Scope:** one seasonal category, one exit window, one market.

A planner-facing web view where the planner can:

- see at-risk SKUs ranked by value at stake
- see projected sell-through curves at different discount depths
- see a recommended depth and timing, with a confidence band
- approve, adjust, or reject with a reason
- see how the recommendation compares to the current flat markdown rule

**The MVP succeeds if:** it beats the flat-schedule baseline on margin recovered in held-out historical seasons, and planners can explain why they trusted or overrode any given recommendation.

**It does not attempt to prove:** enterprise integration, cross-category generalisation, or production-grade forecast accuracy.

---

## 9. Roadmap

**Phase 1, Prove the decision.** Backtest against historical seasons: does the recommendation beat the current rule on margin recovered? No UI. *Gate: beat baseline on backtest, or stop.*

**Phase 2, Prove the workflow.** Planner-facing MVP in shadow mode, planners use it alongside their normal process; decisions are not applied. *Gate: planners engage with recommendations and override reasons are substantive, or revise the UX.*

**Phase 3, Controlled pilot.** One category, one season, live, with a matched control group of categories. *Gate: margin outcome vs control, override rate in a healthy band, no high-value failures.*

**Phase 4, Widen.** More categories and clusters; configuration for a second retailer. Integration investment starts here, not before.

Each phase gate is a real stop point. A backtest that cannot beat a flat schedule ends the project, that is a finding, not a failure.

---

## 10. Success Metrics

**Product**

- Recommendation acceptance rate, should rise over time but never reach 100%; full acceptance signals rubber-stamping, not success
- Time to complete a markdown review cycle
- Share of SKUs given a recommendation vs routed to manual

**Business**

- Margin recovered on exit stock vs baseline rule
- Sell-through at season end
- Terminal stock as a share of season receipts

**Trust / guardrail**

- Override rate and reason-code distribution
- High-confidence recommendations that produced bad outcomes (the metric that ends the project if it is not rare)
- Planner-reported confidence, surveyed each cycle

**Launch threshold.** ClearSight moves past pilot only if it beats both baselines, the flat-schedule rule and last-season analogue matching, by at least +5% on backtest margin recovery, with the confidence signal calibrated, and the expected loss from acting on high-confidence wrong recommendations demonstrably smaller than the expected gain from the rest. Full thresholds per phase: `Appendix/Metrics/Data_Model_Evaluation.md` §7.

---

## 11. Stated Assumptions

The value case is built on a proxy chain, stated openly so it can be challenged and updated:

1. Seasonal SKUs per category per season, and seasons per year
2. Share of season receipts still on hand at exit start (proxy from public retail benchmarks)
3. Margin lost per point of mistimed markdown, in both directions
4. Improvement the model must achieve over the flat rule for the economics to clear, this is the number the backtest tests

If assumption 4 fails, the product should not proceed. The plan is designed so that this is discovered in Phase 1, at minimum cost.

---

## 12. Pricing Note

Full treatment in the economics section; the artifact-level logic:

- **Value anchor:** margin recovered per season per category, outcome-linked, measurable against the retailer's own baseline
- **Model:** annual platform fee per banner + per-category fee, sized so the fee is a clear fraction of conservatively-estimated margin recovered. Not per-recommendation pricing, inference cost per SKU scored is variable but small, and per-use pricing would create usage anxiety and discourage exactly the review behaviour the product needs
- **Cost shape:** inference and data pipeline are variable with catalogue size; clustering setup, integration, and planner onboarding are fixed per customer, which is why the core/customisation split matters commercially

---

## 13. How These Artifacts Change Because the Product Is AI

| Artifact | What changed |
|---|---|
| Problem statement | Defines the *decision* being improved, not a feature to build |
| Persona | Carries error cost and trust, not just goals, Priya's asymmetric risk shapes the design |
| PRD | Includes data assumptions, confidence thresholds, and fallback behaviour as first-class requirements |
| Prioritisation | Confidence display and override capture rank P0; in a non-AI tool they would be nice-to-have |
| Roadmap | Phase 1 is a backtest, not a build, proof gates precede investment |
| Metrics | Every technical metric is tied to margin; acceptance rate is a health signal with a ceiling, not a target |
| Pricing note | Anchored to measurable outcome, with inference cost treated as a unit cost |
| Launch logic | Includes shadow mode, rollback, and a defined stop condition |

---

## 14. Case Lens

- **Netflix (metrics and evaluation):** connect model metrics to business outcomes and watch guardrails, not just accuracy. ClearSight's forecast error only matters through margin recovered, and acceptance rate is monitored the way engagement metrics are, with suspicion of "too good."
- **Instacart / Tesla (lifecycle and drift):** demand patterns shift and the model influences the prices it later learns from. ClearSight plans for drift monitoring and holdout categories from Phase 3, not as an afterthought.

**Transferable lesson:** both cases show evaluation as an ongoing operating discipline, not a pre-launch checkbox. **What we do differently:** unlike Netflix-scale automation, ClearSight keeps a human decision on every action, because a wrong markdown is written in ink, stock sold cheap does not come back.

---

## 15. Core Product vs Customisation

**Core product** (identical for every customer): forecasting engine, recommendation and ranking logic, confidence routing, planner review interface, override and reason capture, backtest harness.

**Customer-specific customisation:** category taxonomy, store clustering rules, POS / ERP integration, markdown approval and governance rules.

This split is what makes ClearSight a product rather than a one-retailer project, development cost is spread across N customers, while each retailer's specifics live in configuration, not code.
