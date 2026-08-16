# Executive summary

## Problem

Merchandise planners at multi-store seasonal retailers decide how deeply to discount leftover stock, and when. The decision is made in spreadsheets, at national level, using last season's analogues and experience, and it fails in both directions. Cut too early and margin is given away on stock that would have sold closer to full price. Cut too late and stock clears at or below cost after occupying space that next season's intake needs.

It is made nationally because evaluating thousands of items across hundreds of stores by hand is not feasible, so one schedule is applied to stores with very different demand and it is wrong, in one direction or the other, for most of them. Markdown is one of the largest controllable margin levers in seasonal retail, and the decision repeats for every seasonal item, every season. That repetition is what makes it a product rather than a one-off analysis.

## Target user and customer

The user is the seasonal merchandise planner, who owns several categories and thousands of seasonal items and answers to a trading manager for every markdown taken. The customer is a retailer with 100 or more stores and defined seasonal ranges: discount department stores, apparel and footwear chains, homewares. The first segment is large-format discount retail, where seasonal volume is high and private-label ranges change substantially year to year.

## Product concept

ClearSight forecasts how much of an item will still sell in each store cluster at different discount depths and timings, then recommends one option with a stated confidence level. The planner sees a ranked list of at-risk items, the projected outcome of each option, and a recommendation, and she approves, adjusts or rejects with every override captured against a reason. Below a confidence threshold ClearSight shows no recommendation at all, routes the item to manual decision, and states why confidence is low. It does not change prices and does not act: it recommends, and a person decides.

Forecasting, ranking, confidence routing, the review interface, override capture and the backtest harness are identical for every customer. Category structure, store clustering rules, integration and approval rules are configuration rather than code, and that split is what makes ClearSight a product rather than a project for one retailer.

## Why AI

| Alternative | Why it is not enough |
|---|---|
| Flat markdown schedule, current practice | Assumes stores behave alike. Weather, demographics and local competition make the same item a winner in one cluster and dead stock in another |
| Sell-through threshold rules | Rules need history to calibrate. Seasonal private-label ranges are substantially new each year, so there is no item-level history to write the rule from |
| Dashboards and business intelligence | Show what happened. They cannot estimate what happens under a 20% cut now versus a 35% cut in three weeks, and that counterfactual is the decision |
| More analysts | The volume is the problem. Adding people does not scale and does not make the decision consistent |

*Table 1. Alternatives to an AI approach, and why each is insufficient.*

The AI task is narrow: predict residual sell-through per store cluster at candidate price points, generalising from product attributes where direct history does not exist. That generalisation is the one thing rules and dashboards cannot do.

## Value and cost logic

Value is margin recovered on exit stock, measured against the retailer's own current rule, which is directly measurable and therefore makes outcome-anchored pricing possible. On the stated assumption chain a 9.4% uplift is worth roughly $1.35 million a year to a 312-store banner, priced at $300,000, so the retailer keeps about 78% of the value created.

Pricing is deliberately not per recommendation. Working the unit economics shows inference at under 0.5% of cost to serve, with onboarding, monitoring and support accounting for the rest. There is no marginal cost worth metering, and metering would suppress the careful review the product depends on.

## Main risks

- **A confidently wrong recommendation acted on at scale.** The failure mode that ends the product. Controlled by confidence thresholds with mandatory manual routing below them, no automatic price execution, a second approver above a value threshold, and a post-mortem on every high-confidence failure.
- **Cluster bias.** Catchment demographics is a clustering input, so the model can price by neighbourhood income without anyone deciding to. Controlled by disparity testing on income decile as a standing metric from Phase 1, a switchable demographic input with its cost made visible, and a report that goes to the retailer's governance forum.
- **Planners rubber-stamping**, which makes human-in-the-loop nominal. Controlled by an acceptance-rate ceiling of 85% rather than a target, by overriding costing no more clicks than agreeing, and by omitting bulk approve from the MVP.
- **Feedback loops**, since the model observes outcomes of prices it influenced. Controlled by permanent holdout categories on the flat rule and by re-benchmarking against the original baseline rather than the model's own previous version.

## Recommendation

Prototype, then pilot, with a hard gate in between.

Phase 1 is a backtest against historical seasons, asking whether the recommendation beats both the current flat rule and last-season analogue matching on margin recovered. No interface is required, and if it does not beat both by 5% the project stops there, which is a finding rather than a failure because it costs weeks to discover. Phase 2 is a planner-facing MVP in shadow mode covering one category, one exit window and one market, with decisions not applied, testing whether the workflow and the trust model hold. Phase 3 is a controlled live pilot with a matched control group of categories.

Integration, additional categories and a second customer should not be funded until Phase 2 shows planners engaging with recommendations and giving substantive reasons when they override. The reason not to pilot immediately is that a markdown, once taken, cannot be untaken, and the most important unknown is also the cheapest to test.

---

*Full analysis in the Capstone Project Report. All figures synthetic or illustrative; no confidential employer data.*
