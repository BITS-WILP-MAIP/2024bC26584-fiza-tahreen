# Product Strategy and Opportunity Framing: ClearSight

*Why this is worth doing, why now, and why this narrow wedge first.*

---

## 1. Product vision

**Give merchandise planners a trusted second opinion on every markdown decision, so discounts are set by evidence about what will actually sell in each store cluster rather than by a single national rule.**

ClearSight is a review assistant, not an autonomous repricing engine. The long-term ambition is not to remove the planner from the decision. It is to make her decision better informed, faster, and defensible upward. If the product ever succeeds by making planners stop thinking, it has failed.

---

## 2. The user problem, as a job to be done

> **When I am** approaching the end of a season with stock still on hand across many stores,
> **I want to know** which items need discounting, how deep, and when,
> **so that I can** recover as much margin as possible without being left with unsellable stock.

**Secondary job (category buyer):** *When I am planning next season's buy, I want to understand which ranges consistently needed rescuing, so that I buy better.* This job is real and valuable, and it is deliberately deferred to P2, because it is a reporting problem that only becomes answerable once a season of decision data exists.

**The job the product deliberately does not do:** *"set my prices for me."* Some planners would take that offer. Accepting it would remove the human control that the entire governance case rests on, and it would convert a defensible assistant into an indefensible automation.

---

## 3. Opportunity framing

### Why this problem matters

Markdown is one of the largest controllable margin levers in seasonal retail. Unlike buying decisions, which are annual and strategic, the markdown decision repeats every season for every seasonal item, and that repetition is exactly what makes it worth a product rather than an analysis.

The decision is currently made at national level not because anyone thinks that is right, but because evaluating thousands of items across hundreds of stores by hand is not feasible. So a single schedule is applied to stores with materially different demand, and it is wrong, in one direction or the other, for most of them. The error is not random; it is structural, and it recurs on a calendar.

### Why the current alternatives are weak

| Alternative | Why it falls short |
|---|---|
| **Flat markdown schedule** (current practice) | Assumes demand is homogeneous across stores. Weather, demographics and local competition make the same item a winner in one cluster and dead stock in another |
| **Threshold rules** ("if sell-through < X% by week Y, cut Z%") | Rules need history to calibrate. Seasonal private-label ranges are substantially new each year, so there is no item-level history to write the rule from |
| **Dashboards and BI** | Show what happened. Cannot estimate what will happen under a 20% cut now versus 35% in three weeks, and that counterfactual is the decision |
| **More analysts** | The volume is the problem. Adding people does not scale and does not make the decision consistent |
| **Existing price-optimisation vendors** | Mature and capable on the forecasting side, but sold as optimisation engines that output prices. They solve the arithmetic and leave the trust and workflow problem, which is where adoption actually fails, to the retailer |

### Why AI, specifically

The AI task is narrow and well-posed: **predict residual sell-through per store cluster at candidate price points, generalising from product attributes and analogue lines where direct history does not exist.**

That generalisation step is the whole justification. It is precisely what a rule cannot do, because a rule needs the history that private-label seasonal ranges do not have. Everything else in the product, meaning ranking, comparison and confidence, is conventional software built around that one probabilistic estimate.

### Why now

1. **The data already exists.** Sales history, stock on hand and product attributes are in every retailer's systems today. This is not a project that needs new instrumentation.
2. **The method is commoditised.** Gradient-boosted models on tabular panel data are well understood, cheap to train and cheap to run. The technical risk sits in data quality and evaluation discipline, not in the algorithm.
3. **Inference cost has stopped being the constraint.** The economics (see §6 of the Final Report) show compute at under 0.5% of cost to serve. Five years ago the equivalent product would have been an enterprise engagement; it can now be a product.
4. **Margin pressure is the pitch.** Seasonal retailers under cost pressure are looking at exactly the levers they already control, and markdown is the largest of them.

---

## 4. AI Opportunity Matrix

Five candidate opportunities were considered in the markdown and exit-stock space before settling on one. Scored on business value, feasibility with realistically available data, and risk.

| Idea | Business value | Feasibility | Risk | Priority |
|---|---|---|---|---|
| **Markdown depth and timing by store cluster** | High: direct, measurable margin recovery on a decision that repeats every season | Medium: needs two seasons of history and workable attribute data, both usually present | Medium: decisions are irreversible, but a human approves every one | **Chosen** |
| Exit-stock risk ranking only, with no recommendation | Medium: saves attention, but does not improve the decision itself | High: a ranking model is much easier than a counterfactual forecast | Low | Folded in as a P0 feature rather than a product |
| Inter-store stock rebalancing before markdown | High: moving stock sometimes beats discounting it | Low: needs logistics cost data and transfer capacity modelling, and the operational change is large | Medium | Rejected for MVP. Genuinely attractive, but it is a second product |
| Full-season dynamic pricing | Very high | Low | Very high: continuous consumer-facing price variation by neighbourhood, and a far sharper fairness exposure | **Rejected**, and named as an out-of-scope boundary in the risk note |
| Next-season buy-quantity recommendation | High | Low: an annual feedback loop means one learning cycle per year | Medium | Rejected. The slow feedback loop makes it nearly unevaluable in a capstone or a first product |

**What the matrix decided.** The winner is not the highest-value idea, since dynamic pricing scores higher on value. It is the highest-value idea that is feasible with available data and evaluable within one season, with a risk profile a human-in-the-loop design can actually control. Rebalancing and buy-quantity are better second and third products than first ones.

---

## 5. Feature prioritisation: RICE on the MVP scope

Applied to the P0 candidate set. Reach is the share of markdown decisions the feature touches; Impact is on the decision quality that drives margin; Confidence is our confidence in the estimate; Effort is person-months.

| Feature | Reach | Impact | Confidence | Effort | **RICE** | Call |
|---|---|---|---|---|---|---|
| Sell-through forecast by store cluster | 1.0 | 3 | 0.6 | 4.0 | **0.45** | P0. The product does not exist without it |
| Ranked exit-stock worklist | 1.0 | 2 | 0.9 | 0.8 | **2.25** | P0. Highest RICE by a distance, cheap to build, and what makes the tool usable at 184 items |
| Markdown depth + timing recommendation | 1.0 | 3 | 0.6 | 2.0 | **0.90** | **P0** |
| Confidence display + low-confidence routing | 1.0 | 2 | 0.8 | 1.5 | **1.07** | **P0** |
| Override with reason capture | 0.35 | 2 | 0.9 | 0.5 | **1.26** | **P0** |
| Backtest harness vs flat rule | 1.0 | 3 | 0.9 | 1.2 | **2.25** | P0, and it ships first |
| Store clustering configuration UI | 0.1 | 1 | 0.8 | 1.5 | **0.05** | P1 |
| Outcome tracking dashboard | 0.2 | 2 | 0.8 | 1.5 | **0.21** | P1 |
| Buyer range-performance view | 0.15 | 2 | 0.5 | 2.0 | **0.08** | P2 |
| POS / ERP write-back | 1.0 | 0.5 | 0.4 | 5.0 | **0.04** | P2 |

### Where RICE had to be overruled, and why

**RICE is a useful sorter and a poor decider for an AI product.** Three of these rankings were adjusted by judgement, and saying so is more honest than presenting a clean score:

1. **Override capture (0.35 reach) scores lower than it deserves.** Reach counts only the decisions where an override happens. But override capture is what makes human-in-the-loop real rather than nominal: it protects 100% of decisions by keeping review genuine, even though it is exercised on roughly a third of them. Its true reach is the whole product.
2. **Confidence display would be "polish" in a deterministic tool.** In a probabilistic one it is the trust mechanism, and trust is the adoption risk. RICE's Impact scale has no way to express "this feature is the reason the other features are allowed to ship."
3. **The backtest harness is not a user-facing feature at all,** so a user-centred framework struggles with it. It ties for the highest score anyway, and it ships before everything else, because it is the gate that decides whether the rest is worth building.

**Sequencing overrides scoring.** The backtest ships first. Nothing in P1 is built until planners demonstrably act on P0 recommendations.

---

## 6. Build vs buy vs partner / API

**Recommendation: build the forecast model in-house for the MVP, on open-source tooling. Buy nothing. Partner with no one yet. Keep the buy option explicitly open for Phase 4.**

| Path | Assessment |
|---|---|
| **Build** (gradient-boosted trees, open-source, in-house) | **Chosen.** The differentiator is the decision workflow and the trust model, not forecast accuracy. Owning the model is what makes the confidence signal controllable, and a confidence signal that cannot be controlled is a product that cannot be governed. Cheap: this is a well-understood tabular problem, not frontier research |
| **Buy** a price-optimisation vendor and wrap it | Rejected for MVP. Faster to a demo, but the vendor's confidence output is theirs, not ours, and the abstention behaviour that the whole design rests on would be a wrapper around someone else's black box. **Legitimate at Phase 4**, when the workflow is proven and forecast quality becomes the binding constraint |
| **Partner** with a retail data or consultancy firm | Rejected now, revisit at go-to-market. Would help with distribution and onboarding throughput, which is the real scaling constraint, but partnering before the product is proven adds a stakeholder to every product decision at exactly the wrong moment |
| **LLM API** for the core forecast | **Rejected outright.** Wrong tool for numeric estimation over structured panel data: more expensive, slower, non-deterministic, no calibrated confidence, and it introduces hallucination risk into a number that moves real money |
| **LLM API** for the explanation layer | Accepted in principle, **deferred past MVP**. A genuine language task, but a fluent explanation of a wrong number is worse than a terse one, and at roughly 1.15m items scored per year it is also the one place inference cost would become material |

**The trade-off being accepted.** Building costs roughly a quarter more time to first pilot than wrapping a vendor. What it buys is control over the confidence signal, the ability to make the model abstain on our terms, and no per-item licence cost sitting underneath the pricing model. For a product whose central claim is *"it knows when it does not know,"* that control is not a nice-to-have.

---

## 7. Platform vs feature

**ClearSight is a feature-shaped product sold as a platform-shaped one, and being clear about that is what keeps it honest.**

It is not a platform in the sense of an extensible ecosystem, and it should not pretend to be. It is a narrow decision tool for one recurring decision. What makes it a *product* rather than a *project for one retailer* is the core-versus-customisation split:

| **Core**, identical for every customer | **Customisation**, configuration rather than code |
|---|---|
| Forecasting engine | Category taxonomy |
| Recommendation and ranking logic | Store clustering rules and active inputs |
| Confidence routing and abstention | POS / ERP integration |
| Planner review interface | Markdown approval thresholds and chain |
| Override and reason capture | Reason-code vocabulary |
| Backtest harness | Retention and audit settings |

**The commercial consequence:** fixed development cost is spread across N customers while each retailer's specifics live in configuration. **The operational consequence:** the single greatest threat to the business model is agreeing to write retailer-specific code. Every such request has to be answerable with configuration, or refused. That is a discipline rather than an architecture.

**Could this be a feature inside an existing merchandising suite instead?** Realistically, yes, and that is the most likely acquisition path. It argues for building the workflow and trust layer well, since that is the part a suite vendor cannot easily replicate, rather than competing on forecast accuracy where an incumbent with more data eventually wins.

---

## 8. First wedge

**One seasonal category. One exit window. One market. One retailer.**

Specifically: a high-volume seasonal category at a large-format discount retailer, run in shadow mode alongside the planner's normal process.

### Why this wedge and not a broader one

- **It is evaluable in one season.** A wider pilot would not produce a cleaner answer, only a slower and more expensive one.
- **It isolates the real risk.** The open question is not whether the arithmetic works. It is whether a planner will trust and use the output, and a single category with a single planner tests that directly.
- **It fails cheaply.** If the backtest cannot beat analogue matching, the project ends having consumed weeks. That outcome is a finding, not a failure, and the wedge is designed to surface it first.
- **It gives a clean control.** Neighbouring categories on the flat rule provide a matched comparison, which is what makes any margin claim defensible rather than anecdotal.

### Expansion path

**Category → banner → second retailer.** Land one category, expand across categories within the same banner (where marginal cost is near zero and contribution margin is highest), and only then take on a second retailer, where onboarding cost is incurred again. Integration investment starts at Phase 4 rather than before, because it is the most expensive thing to build and the least informative thing to learn from.

---

*ClearSight | MBA ZG583, Management of AI Products | Capstone. All figures synthetic or illustrative.*
