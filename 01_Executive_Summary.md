# Executive Summary: ClearSight

*An AI-assisted markdown and exit-stock decision copilot for multi-store seasonal retailers.*

---

## Problem

Every season, merchandise planners at multi-store retailers decide how deeply to discount leftover seasonal stock, and when. The decision is made in spreadsheets, at national level, using last season's analogues and experience.

It goes wrong in both directions. Discount too early and margin is given away on stock that would have sold closer to full price. Discount too late and stock clears at or below cost, having consumed store and warehouse space that next season's intake needs.

The decision is made nationally because evaluating thousands of items across hundreds of stores by hand is not feasible. So a single discount schedule is applied to stores with very different demand, and it is wrong, in one direction or the other, for most of them.

Markdown is one of the largest controllable margin levers in seasonal retail, and this decision repeats for every seasonal item, every season.

---

## Target User and Customer

**User:** the seasonal merchandise planner, who owns several categories and thousands of seasonal items across the store network, and answers to a trading manager for every markdown taken.

**Customer:** retailers with 100+ stores and defined seasonal ranges: discount department stores, apparel and footwear chains, homewares. The first customer segment is large-format discount retail, where seasonal volume is high and private-label ranges change substantially year to year.

---

## Product Concept

ClearSight forecasts how much of an item will still sell in each group of stores at different discount depths and timings, then recommends one option with a stated confidence level.

The planner sees a ranked list of at-risk items, the projected outcome of each discount option, and a recommendation. They approve, adjust, or reject, and rejections are captured with a reason. Where confidence is low, ClearSight shows no recommendation at all and routes the item to manual decision, stating why.

It does not change prices. It does not act. It recommends, and a person decides.

**Core product** (identical across customers): forecasting, recommendation and ranking, confidence routing, planner review interface, override capture, backtesting.
**Customisation** (per retailer): category structure, store clustering rules, system integration, markdown approval rules.

---

## Why AI

| Alternative | Why it is not enough |
|---|---|
| Flat markdown schedule (current practice) | Assumes stores behave alike. Weather, demographics and local competition make the same item a winner in one cluster and dead stock in another |
| Rules based on sell-through thresholds | Rules need history to calibrate. Seasonal private-label ranges are substantially new each year, so there is no item-level history to write the rule from |
| Dashboards and BI | Show what happened. Cannot estimate what *will* happen under a 20% cut now versus a 35% cut in three weeks, and that counterfactual is the decision |
| More analysts | The volume is the problem. Adding people does not scale and does not make the decision consistent |

The AI task is to predict residual sell-through per store cluster at candidate price points, generalising from product attributes and comparable lines where direct history does not exist. That generalisation is what rules and dashboards cannot do.

---

## Value and Cost Logic

Value is margin recovered on exit stock, measured against the retailer's own current markdown rule. It is directly measurable, which makes outcome-anchored pricing possible.

Proposed model: an annual platform fee per retail banner, plus a per-category fee, sized as a clear fraction of conservatively estimated margin recovered. Deliberately **not** per-recommendation pricing: inference cost per item scored is small and variable, but usage-based billing would create cost anxiety and discourage exactly the careful review behaviour the product depends on.

Cost shape: inference and data pipeline vary with catalogue size; store clustering setup, integration and planner onboarding are fixed per customer. The core-versus-customisation split is what allows fixed development cost to be spread across customers rather than carried by the first one.

---

## Main Risks

1. **A confidently wrong recommendation acted on at scale.** This is the failure mode that ends the product. Not a slightly inaccurate forecast, but a high-confidence recommendation the planner trusted and should not have.
2. **Planners rubber-stamping.** If recommendations are accepted without review, the human-in-the-loop control is nominal and the risk above becomes unmanaged.
3. **Feedback loops.** After the first season, the model observes outcomes of prices it influenced. Naive retraining bakes in its own past errors.

Controls: confidence thresholds with mandatory manual routing below them, no automatic price execution, reason capture on every override, acceptance rate monitored as a health signal rather than maximised, and holdout categories retained to detect drift.

---

## Recommendation

**Prototype, then pilot, with a hard gate in between.**

**Phase 1 (now):** backtest the recommendation against historical seasons. Does it beat the current flat markdown rule on margin recovered? No interface required. *If it does not beat the baseline, stop here.* That outcome is a finding, not a failure, and it costs very little to discover.

**Phase 2:** planner-facing MVP in shadow mode, one category, one exit window, one market. Planners use it alongside their normal process; decisions are not applied. This tests whether the workflow and the trust model hold, which is the real adoption risk.

**Phase 3:** controlled live pilot with a matched control group of categories.

Do not fund integration, additional categories, or a second customer until Phase 2 shows planners engaging with recommendations and giving substantive reasons when they override.

---

*MBA ZG583, Management of AI Products | Capstone | Prototype and evaluation use synthetic or public retail data only.*
