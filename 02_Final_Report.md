# Executive summary

Merchandise planners at multi-store seasonal retailers decide how deeply to discount leftover stock, and when. The decision is made in spreadsheets, at national level, using last season's analogues and experience. It fails in both directions: cut too early and margin is given away on stock that would have sold closer to full price; cut too late and stock clears at or below cost after occupying space that next season's intake needs.

The decision is made nationally because evaluating thousands of items across hundreds of stores by hand is not feasible. One schedule is therefore applied to stores with very different demand, and it is wrong, in one direction or the other, for most of them. Markdown is one of the largest controllable margin levers in seasonal retail, and this decision repeats for every seasonal item, every season. That repetition is what makes it a product rather than a one-off analysis.

ClearSight forecasts residual sell-through per store cluster at candidate price points, ranks at-risk items by value at stake, and recommends a discount depth and timing with a stated confidence level. The planner approves, adjusts or rejects, and every override is captured with a reason. Below a confidence threshold the product shows no recommendation at all, routes the item to manual decision, and states why confidence is low. ClearSight never changes a price.

The recommendation to the review panel is to fund a backtest, not a build. On synthetic held-out seasons the ranking recovers 9.4% more margin than the retailer's current flat schedule and 5.2% more than last-season analogue matching, both above the +5% gate fixed before the test was run. On the stated assumption chain that is worth roughly $1.35 million a year to a 312-store banner against a $300,000 licence, so the retailer keeps about 78% of the value created.

Two findings are worth flagging up front because both reverse a natural assumption. The first is commercial: working the unit economics shows inference at under 0.5% of cost to serve, with onboarding, monitoring and support accounting for more than 85%. This is the opposite of the generative-AI economics the course examines, and it is the reason per-recommendation pricing was rejected rather than adopted. The second is ethical: the store-clustering layer takes catchment demographics as an input, which creates a path from neighbourhood income to recommended discount depth without any protected attribute appearing in the model. That exposure is structurally the same as the Apple Card case, and disparity testing is therefore scheduled from Phase 1 rather than from pilot.



# Contents

*(Contents are generated in the Word and PDF versions.)*



# 1. Introduction and problem framing

Seasonal retailers with large private-label ranges carry stock that has a deadline. Swimwear does not sell in October, garden furniture does not sell in July, and stock that misses its window is cleared at or below cost. The lever that manages this is markdown: reducing price far enough, and early enough, to clear the stock while giving away as little margin as possible.

The lever is currently pulled at national level. A planner owning three or four categories and roughly 2,000 seasonal items sets one discount schedule for the whole estate, typically a fixed percentage at a fixed week, and applies it to every store. The reason is capacity rather than conviction: nobody believes a coastal store and a cold-climate store should clear beachwear on the same schedule, but evaluating every item against every store by hand is not possible in the time available.

ClearSight addresses that specific decision. It is a review assistant for markdown depth and timing, not an autonomous repricing engine, and the distinction is load-bearing throughout this report. Table 1 maps each capstone requirement to the section that addresses it.

| Capstone requirement | Where it is addressed |
|---|---|
| Executive summary | Front matter, and `01_Executive_Summary.pdf` |
| Product strategy and opportunity framing | Section 2 |
| PM artifact pack | Section 3 |
| UX, workflow and trust design | Section 4 |
| Data, model and evaluation strategy | Section 5 |
| Business, economics and scaling view | Section 6 |
| Ethics, governance and risk note | Section 7 |
| Functional prototype / MVP | Section 8, Appendix A |
| How PM artifacts change for AI products | Section 9 |
| AI collaboration log | `05_AI_Collaboration_Log.pdf` |
| Individual contribution disclosure | `06_Individual_Contribution_Statement.pdf` |
| Case lenses | Sections 5.7, 6.6, 7.9 |

*Table 1. Mapping of capstone requirements to report sections.*

## 1.1 Data statement

Every quantitative figure in this report is synthetic or illustrative. Each is derived from an assumption chain that is written down so it can be challenged and replaced with measured data during a pilot. Professional workflow knowledge informed the problem framing and is used as context, never as data. No confidential employer data appears anywhere in this submission.

# 2. Product strategy and opportunity framing

## 2.1 Product vision

Give merchandise planners a trusted second opinion on every markdown decision, so that discounts are set by evidence about what will actually sell in each store cluster rather than by a single national rule.

ClearSight is a review assistant. If the product ever succeeds by making planners stop thinking, it has failed, and Section 7 treats that outcome as a launch blocker rather than an acceptable side effect.

## 2.2 Job to be done

> When I am approaching the end of a season with stock still on hand across many stores, I want to know which items need discounting, how deep, and when, so that I recover as much margin as possible without being left with unsellable stock.

A secondary job belongs to the category buyer: understanding which ranges consistently need rescuing, so that next season's buy is better. It is real and valuable, and it is deferred to P2 because it only becomes answerable once a full season of decision data exists.

One job is declined deliberately. Some planners would accept an offer to set prices automatically. Accepting it would remove the human control that the entire governance case rests on, and would convert a defensible assistant into an automation nobody could sign off.

## 2.3 Why the current alternatives are weak

| Alternative | Why it falls short |
|---|---|
| Flat markdown schedule (current practice) | Assumes demand is homogeneous across stores. Weather, demographics and local competition make the same item a winner in one cluster and dead stock in another |
| Threshold rules, such as "if sell-through is below X% by week Y, cut Z%" | Rules need history to calibrate. Seasonal private-label ranges are substantially new each year, so there is no item-level history to write the rule from |
| Dashboards and business intelligence | Show what happened. They cannot estimate what will happen under a 20% cut now versus a 35% cut in three weeks, and that counterfactual is the decision |
| More analysts | The volume is the problem. Adding people does not scale and does not make the decision consistent |
| Existing price-optimisation vendors | Mature on the forecasting side, but sold as optimisation engines that output prices. They solve the arithmetic and leave the trust and workflow problem, which is where adoption actually fails, to the retailer |

*Table 2. Alternatives to an AI approach, and why each is insufficient.*

The AI task is narrow and well posed: predict residual sell-through per store cluster at candidate price points, generalising from product attributes and analogue lines where direct history does not exist. That generalisation step is the entire justification for using a model, because it is precisely what a rule cannot do when the range is new. Everything else in the product is conventional software built around that one probabilistic estimate.

## 2.4 Why now

Four conditions make this buildable today rather than three years ago. The data already exists, since sales history, stock on hand and product attributes sit in every retailer's systems and no new instrumentation is required. The method is commoditised, because gradient-boosted models on tabular panel data are well understood and cheap to run, which puts the technical risk in data quality and evaluation discipline rather than in the algorithm. Inference cost has stopped being a constraint, at under 0.5% of cost to serve on the figures in Section 6.3. And margin pressure gives the pitch a hearing, because seasonal retailers are examining the levers they already control.

## 2.5 AI opportunity matrix

Five candidate opportunities in the markdown and exit-stock space were scored before one was selected.

| Idea | Business value | Feasibility | Risk | Decision |
|---|---|---|---|---|
| Markdown depth and timing by store cluster | High | Medium | Medium | **Chosen** |
| Exit-stock risk ranking only, with no recommendation | Medium | High | Low | Folded in as a P0 feature rather than a product |
| Inter-store stock rebalancing before markdown | High | Low | Medium | Rejected. Genuinely attractive, but it is a second product |
| Full-season dynamic pricing | Very high | Low | Very high | Rejected, and named as an out-of-scope boundary in Section 7.5 |
| Next-season buy-quantity recommendation | High | Low | Medium | Rejected. One learning cycle per year makes it nearly unevaluable |

*Table 3. AI opportunity matrix. Scored on business value, feasibility with realistically available data, and risk.*

The selected idea is not the highest-value one. Dynamic pricing scores higher on value and was rejected on risk and evaluability. The winner is the highest-value idea that is feasible with data the retailer already holds, evaluable inside a single season, and carries a risk profile that a human-in-the-loop design can actually control.

## 2.6 Build, buy, partner or API

The recommendation is to build the forecast in-house for the MVP using open-source tooling, buy nothing, partner with nobody yet, and keep the buy option explicitly open for Phase 4.

| Path | Decision | Reasoning |
|---|---|---|
| Build, using gradient-boosted trees | **Chosen** | The differentiator is the decision workflow and the trust model, not forecast accuracy. Owning the model is what makes the confidence signal controllable, and a confidence signal that cannot be controlled is a product that cannot be governed |
| Buy a price-optimisation vendor and wrap it | Rejected for MVP, open at Phase 4 | Faster to a demo, but the abstention behaviour the design rests on would be a wrapper around a third-party black box |
| Partner with a retail data or consultancy firm | Rejected now, revisit at go-to-market | Would help onboarding throughput, which is the real scaling constraint, but adds a stakeholder to every product decision at the wrong moment |
| LLM API for the core forecast | Rejected outright | The wrong tool for numeric estimation over structured panel data: more expensive, slower, non-deterministic, no calibrated confidence, and it introduces hallucination risk into a number that moves real money |
| LLM API for the explanation layer | Accepted in principle, deferred past MVP | A genuine language task, but a fluent explanation of a wrong number is worse than a terse one, and at roughly 1.15 million items scored per year it is the one place inference cost becomes material |

*Table 4. Build versus buy versus partner versus API, with the reasoning for each path.*

Building costs roughly a quarter more time to first pilot than wrapping a vendor. What it buys is control over the confidence signal and the ability to make the model abstain on terms the product owns. For a product whose central claim is that it knows when it does not know, that control is not optional.

## 2.7 Platform versus feature

ClearSight is a feature-shaped product sold as a platform-shaped one, and stating that plainly is what keeps the strategy honest. It is not an extensible ecosystem and should not pretend to be. What makes it a product rather than a project for one retailer is the split in Table 5.

| Core, identical for every customer | Customisation, configuration rather than code |
|---|---|
| Forecasting engine | Category taxonomy |
| Recommendation and ranking logic | Store clustering rules and active inputs |
| Confidence routing and abstention | POS and ERP integration |
| Planner review interface | Markdown approval thresholds and chain |
| Override and reason capture | Reason-code vocabulary |
| Backtest harness | Retention and audit settings |

*Table 5. Core product versus per-customer customisation.*

The commercial consequence is that fixed development cost spreads across N customers. The operational consequence matters more: the single greatest threat to the business model is agreeing to write retailer-specific code. Every such request has to be answerable with configuration, or refused. That is a discipline rather than an architecture, and Section 6.6 treats it as the main scaling risk.

Whether this could instead be a feature inside an existing merchandising suite is a fair challenge, and the honest answer is yes, which is also the most likely acquisition path. It argues for building the workflow and trust layer well, since that is the part a suite vendor cannot easily replicate, rather than competing on forecast accuracy where an incumbent with more data eventually wins.

## 2.8 First wedge

One seasonal category, one exit window, one market, one retailer: a high-volume seasonal category at a large-format discount retailer, run in shadow mode alongside the planner's normal process.

The wedge is narrow for four reasons. It is evaluable in a single season, and a wider pilot would not produce a cleaner answer, only a slower and more expensive one. It isolates the real risk, which is not whether the arithmetic works but whether a planner will trust and use the output. It fails cheaply, because a backtest that cannot beat analogue matching ends the project having consumed weeks. And neighbouring categories left on the flat rule provide a matched control, which is what makes any later margin claim defensible rather than anecdotal.

Expansion runs category, then banner, then second retailer. Adding categories inside a banner carries near-zero marginal cost and the highest contribution margin, so land-and-expand within an account is more profitable than winning a new one. Integration investment starts at Phase 4, because it is the most expensive thing to build and the least informative thing to learn from.

# 3. PM artifact pack

The full artifact pack is in `Appendix/PRD/PM_Artifact_Pack.md`. This section carries the parts a reviewer needs in order to follow the argument.

## 3.1 Persona

Priya is a seasonal merchandise planner. She owns three or four categories, roughly 2,000 seasonal items a year across 312 stores, and runs exit planning in Excel exported from the merchandise system. She has been burned in both directions: a range marked down 30% in week 8 that was selling out in coastal stores anyway, and a bulky outdoor line held too long that consumed warehouse space into the next season's intake.

What she needs from a tool is a defensible reason for each decision, because she answers to a trading manager; speed, because exit reviews compete with next season's buy for her time; and the ability to say no to it. What would make her abandon it is one confidently wrong recommendation that she followed and then had to explain upward.

Her error cost is asymmetric and she knows it. Over-discounting a winner loses real margin immediately. Holding a loser too long loses less per unit but clogs stores and warehouses, and the cost lands in the following season where it is harder to attribute. The tool has to respect that asymmetry rather than simply forecasting accurately.

## 3.2 PRD summary

Planners cannot evaluate markdown options at store-cluster level, so they default to a national rule that is wrong for most stores. The core workflow addresses that in six steps: the planner opens the exit-stock worklist for a category and season; ClearSight ranks items by value at stake multiplied by likelihood of not clearing; for each item it shows projected sell-through at several discount depths and timings with a confidence level; it recommends one option; the planner approves, adjusts or rejects, with adjustments and rejections requiring a reason code; and the decision and reason are logged so that outcomes feed back into evaluation.

The AI role is bounded to forecasting residual sell-through per store cluster at candidate price points, then ranking and recommending. It does not set prices and does not execute them. Automatic price changes, in-season promotional pricing, competitor price matching, replenishment and full-price setting are all out of scope, and Section 7.5 explains why the last of those is a governance boundary rather than a backlog item.

The data assumptions are two years of sales history by item, store and week; current stock on hand; product attribute data clean enough to match analogues; and past markdown events recorded with their dates as well as their depths. Attribute quality is the weakest of these. Where it degrades, analogue matching degrades, more items route to manual, and the system produces less coverage rather than worse recommendations. That behaviour is enforced in the model layer rather than in the interface.

Fallback logic is specified before success logic. Below a confidence threshold ClearSight shows no recommendation, routes the item to manual decision, and states why confidence is low: no analogue, sparse history, or conflicting cluster signals. New products with no usable analogue are flagged as such rather than guessed at.

## 3.3 Feature prioritisation

RICE scores were computed on the training of candidate features, where reach is the share of markdown decisions the feature touches, impact is on decision quality, confidence is in the estimate itself, and effort is in person-months.

| Feature | Reach | Impact | Confidence | Effort | RICE | Call |
|---|---|---|---|---|---|---|
| Ranked exit-stock worklist | 1.0 | 2 | 0.9 | 0.8 | **2.25** | P0 |
| Backtest harness against the flat rule | 1.0 | 3 | 0.9 | 1.2 | **2.25** | P0, ships first |
| Override with reason capture | 0.35 | 2 | 0.9 | 0.5 | **1.26** | P0 |
| Confidence display and low-confidence routing | 1.0 | 2 | 0.8 | 1.5 | **1.07** | P0 |
| Markdown depth and timing recommendation | 1.0 | 3 | 0.6 | 2.0 | **0.90** | P0 |
| Sell-through forecast by store cluster | 1.0 | 3 | 0.6 | 4.0 | **0.45** | P0 |
| Outcome tracking dashboard | 0.2 | 2 | 0.8 | 1.5 | 0.21 | P1 |
| Store clustering configuration interface | 0.1 | 1 | 0.8 | 1.5 | 0.05 | P1 |
| Buyer range-performance view | 0.15 | 2 | 0.5 | 2.0 | 0.08 | P2 |
| POS and ERP write-back | 1.0 | 0.5 | 0.4 | 5.0 | 0.04 | P2 |

*Table 6. RICE prioritisation of the candidate feature set. P0 is the MVP.*

RICE sorted the list usefully and decided it badly, and three rankings were overruled by judgement. Override capture scores only 0.35 on reach because reach counts the decisions where an override actually happens, but override capture is what makes human-in-the-loop real rather than nominal, so it protects 100% of decisions while being exercised on roughly a third. Confidence display would be polish in a deterministic tool; in a probabilistic one it is the trust mechanism, and RICE's impact scale has no way to express that a feature is the reason the other features are allowed to ship. The backtest harness is not user-facing at all, so a user-centred framework struggles to score it, yet it ties for the highest score and ships before everything else because it is the gate that decides whether the rest is worth building.

Nothing in P1 is built until planners demonstrably act on P0 recommendations.

## 3.4 MVP definition

The MVP covers one seasonal category, one exit window and one market. It gives the planner a view in which she can see at-risk items ranked by value at stake, see projected sell-through at different discount depths, see a recommended depth and timing with a confidence band, approve or adjust or reject with a reason, and see how the recommendation compares against the current flat rule.

It succeeds if it beats the flat-schedule baseline on margin recovered in held-out historical seasons, and if planners can explain why they trusted or overrode any given recommendation. It does not attempt to prove enterprise integration, cross-category generalisation, or production-grade forecast accuracy.

## 3.5 Roadmap

| Phase | What it proves | Gate |
|---|---|---|
| 1. Prove the decision | Backtest against historical seasons. No interface is built | Beat both baselines by at least +5% on margin recovered, or stop |
| 2. Prove the workflow | Planner MVP in shadow mode, with decisions not applied | Planners engage, and override reasons are substantive, or the UX is revised |
| 3. Controlled pilot | One category, one season, live, with a matched control group | Margin beats control; acceptance rate inside band; no unexplained high-confidence failures |
| 4. Widen | More categories and clusters; configuration for a second retailer | Integration investment starts here, not before |

*Table 7. Roadmap phases and the gate that must be cleared to leave each one.*

Each gate is a real stop point. A backtest that cannot beat a flat schedule ends the project, and that outcome is a finding rather than a failure.

# 4. UX, workflow and trust design

The full write-up is in `Appendix/UX_Trust/UX_Workflow_Trust.md`, and the seven annotated screens are in `Appendix/Wireframes/`.

## 4.1 Design principle

Every design decision follows from one observation about the user. Priya is accountable for the markdowns she takes, and one confidently wrong recommendation that she followed and had to explain upward would end her trust in the tool permanently. That makes this a trust problem before it is a forecasting problem, and three rules follow.

Overriding must be as easy as agreeing. If rejecting costs more clicks than approving, the review is nominal and acceptance rates stop meaning anything.

Silence is a valid output. Where the model has no signal it says so, rather than producing a low-quality number that looks like every other number on the screen.

Explanations use the planner's vocabulary rather than the model's: cluster sell-through gaps and analogue lines, not feature importances.

*Figure 1. The seven ClearSight screens grouped by phase. Screens 1 to 4 are the MVP decision loop; screen 6 exists before any interface is built; screens 5 and 7 arrive with live decisions and a second customer respectively. See `Appendix/Wireframes/clearsight_seven_screen_flow_grouped.png`.*

## 4.2 The seven screens

| # | Screen | Purpose | Phase |
|---|---|---|---|
| 1 | Exit worklist | What needs a decision, ordered by what it costs to get wrong | MVP |
| 2 | Item detail, high confidence | The decision: recommendation, reasoning, alternatives, three actions | MVP |
| 3 | Item detail, low confidence | What the product does when it does not know | MVP |
| 4 | Adjust and reason capture | The override path, where human-in-the-loop actually lives | MVP |
| 5 | Outcome tracking | Whether it is working, including the metrics designed to expose failure | Phase 3 |
| 6 | Backtest evidence | The go/no-go gate. Internal, with no planner interface | Phase 1 |
| 7 | Store cluster configuration | The customisation layer: what changes per retailer | P1 |

*Table 8. The seven screens and the phase each belongs to.*

Screen 6 exists before any interface is built, because it is the evidence that decides whether screens 1 to 4 are worth building at all. Screen 5 is deliberately Phase 3: in shadow mode there is no margin recovered and no control comparison to display, so a reduced Phase 2 version shows engagement metrics only.

## 4.3 Trust and explainability

The planner must be able to see the recommended action stated as an action, such as "cut 25% this week", rather than as a probability she has to interpret. She must see the projected outcome alongside the projected outcome of the current flat rule, so that the comparison is explicit rather than implied. She must see the alternatives that were considered and their projected sell-through, because showing only the chosen option converts a recommendation into an instruction. She must see three or four reasons in domain language with the largest driver named, and she must see what evidence the recommendation rests on: how many analogue lines, and how many prior seasons.

Three things are deliberately withheld. Model internals, feature weights and architecture do not help the planner decide, and they create false confidence in people who cannot evaluate them. Precision beyond what the model supports is suppressed, so sell-through is shown to the nearest percent. And the confidence score is never shown as a raw number, because "High" with the supporting evidence is more honest than "0.87", which invites over-interpretation.

The test for including anything is whether it changes what a planner would do. Feature weights do not. Analogue count does, because it tells her whether the model has seen anything resembling this item before.

## 4.4 Uncertainty handling

| State | System behaviour | Planner experience |
|---|---|---|
| High confidence | Full recommendation with depth, timing, projected outcome and comparison against baseline | One-click approve, with override available at equal cost |
| Medium confidence | Recommendation shown and flagged, with the specific source of uncertainty named | Encouraged to review the reasoning before acting |
| Low confidence | No recommendation at all. Item routed to manual decision, reason for low confidence stated, underlying data still shown | Makes the call herself, with evidence but no suggested answer |
| Failure, data missing or stale | Item marked not assessable, with the missing input named. No fallback to a stale recommendation | Sees the gap explicitly rather than acting on an outdated number |

*Table 9. Product behaviour at each confidence state.*

Most AI products degrade gracefully into worse predictions. ClearSight degrades into silence instead. That choice costs coverage, since some items receive no help, but it protects the one failure the product cannot recover from. Naming why confidence is low does double duty: it tells the planner what to examine, and over time it teaches her when the model is reliable elsewhere, which is what calibrated trust actually means in practice.

## 4.5 Human in the loop

Review is mandatory on every recommendation without exception, and ClearSight never changes a price. Items above a value-at-stake threshold require a second approver, configurable per retailer, which is a stated requirement rather than a designed screen and is deferred to Phase 3 when decisions become live. All low-confidence items reach manual review by routing rather than by policy, which means the control cannot be switched off administratively.

Adjust and Reject both open a reason picker offering competitor activity, range repeating next season, store feedback, stock quality issue, and other, plus free text. Reason capture serves three purposes simultaneously. As evaluation, it reveals systematic model failures that aggregate metrics hide, since fifteen rejections all citing competitor activity is a missing feature rather than fifteen unrelated disagreements. As governance, it produces the audit trail that makes a markdown defensible upward. As product signal, clustering reasons across a season shows where the model needs work, and it is the cheapest discovery channel the system has.

Rubber-stamping is guarded against directly. Acceptance rate is monitored with a ceiling rather than a target, and sustained acceptance approaching 100% triggers a review of whether planners are still engaging. Two design consequences follow: the Approve button is not visually dominant over Adjust and Reject, and the MVP has no bulk approve, because batch approval and meaningful review are in direct tension.

## 4.6 What the design does not resolve

Three questions are open, and stating them is more useful than implying the design is finished. Bulk actions are the first: with 184 items in a worklist, planners will want to approve in batches, and Phase 2 shadow mode is where that tension gets tested honestly rather than designed around. Notification cadence is the second: whether ClearSight pushes alerts as items become at-risk or waits to be opened weekly should follow observed planner behaviour rather than a design assumption. Mobile access for store and area managers is the third, and it is out of scope for the MVP because the decision belongs to the planner at a desk.

# 5. Data, model and evaluation strategy

The full treatment is in `Appendix/Metrics/Data_Model_Evaluation.md`.

## 5.1 The problem as an AI task

The product question, how deep to cut an item and when, is a decision rather than a prediction, so it decomposes before any model is applied to it.

The primary task is forecasting: predict residual sell-through for item i, in store cluster c, over the remaining weeks of exit window w, conditional on candidate price p. The second task is ranking: order the worklist by value at stake, meaning the margin swing between the best and worst plausible outcome, rather than by discount size or units on hand. The third is recommendation: choose the depth-and-timing option that maximises expected recovered margin subject to clearing the stock by window close. Setting or executing the price is not an AI task and never becomes one.

The decomposition matters because the three tasks fail differently and are measured separately. A forecast can be well calibrated while the ranking is useless, and a recommendation can be right while the forecast that produced it is wrong for compensating reasons.

## 5.2 Why this is a counterfactual problem

The quantity the planner needs is what will happen under a price that has not been set. Historical data contains outcomes only for prices that were actually charged, and those prices were chosen by planners using the very rule the model is trying to beat. Prices in the training data are therefore not random: they correlate with the planner's own judgement about which items were struggling.

Three consequences follow. Naive supervised learning on price and outcome pairs partly learns that items receiving deep discounts sold badly, which is backwards causality. Evaluation must use held-out seasons rather than held-out rows, so that the model cannot exploit within-season leakage. And any observational estimate eventually needs a randomised check, which is what the matched-control design in Phase 3 provides.

This report does not claim to have solved the causal inference problem. It claims to have designed around it, using a conservative baseline comparison, a gate that must be cleared on held-out seasons, and a live pilot with a control group before anything scales.

## 5.3 Data sources and assumptions

| Layer | Source | Availability |
|---|---|---|
| Weekly units sold by item and store, two seasons | Merchandise and EPOS systems | High |
| Stock on hand by item and store | Stock system | High |
| Price and markdown history, with dates | Pricing system | High, though often held separately |
| Product attributes, cost, full retail | Product master | Medium. This is the weak link |
| Store format, size, region | Store master | High |
| Climate zone by store location | External, public | High |
| Catchment demographics | External, licensed | Medium. The main fairness exposure, treated in Section 7.2 |
| Local competitor density | External, licensed | Low. Switched off for the reference retailer |
| Planner decisions and override reasons | Generated by the product itself | Available from Phase 2 |

*Table 10. Data sources, with an availability assessment for each.*

Four assumptions carry the data layer. Two full seasons of weekly item-by-store sales must exist and be trustworthy, because one season cannot separate seasonality from trend. Product attribute data must be complete enough to match analogues, and this is the assumption most likely to fail, since retail product masters are notoriously patchy on exactly the attributes that predict seasonal demand. Markdown history must record the date of each price change and not only the final price, because without dates only depth can be learned and timing cannot. Stock on hand must be accurate at store level, and shrinkage degrades this most in the small-format stores where the forecast is already weakest.

## 5.4 Labelling and ground truth

There is no human labelling task, which is a genuine advantage over most AI products: the ground truth arrives on its own. For a completed exit window the label for an item, cluster and price path is realised sell-through, meaning units sold divided by units on hand at the start of the window. It is observed rather than annotated.

Three derived labels do need construction, and each carries a risk. The analogue set for a new item is built from attribute similarity within category and season, restricted to items with a full prior-season history; similarity is a modelling choice, so a poor analogue set produces confident nonsense, which is why analogue count and match quality are surfaced to the planner rather than hidden. Value at stake depends on cost data being correct, and a wrong cost silently mis-ranks the worklist while producing no visible error. The definition of terminal stock differs by retailer and must be configured rather than assumed.

Human judgement enters the label in exactly one place: deciding whether a season was normal. A season disrupted by extreme weather or a supply failure is excluded from training, and the exclusion is recorded. Excluding the seasons where the model looked bad is the obvious way to cheat, so the process has to be visibly resistant to it.

## 5.5 Baselines and model choice

The gate is not whether the model is accurate. It is whether the model beats what the retailer already does, and there are two of those rather than one.

| Baseline | What it is | Why it is in the comparison |
|---|---|---|
| Flat markdown schedule | The retailer's current rule, for example 30% at week 8 and 50% at week 11 | This is what the product replaces. Failing to beat it removes the product's reason to exist |
| Last-season analogue matching | Apply the price path taken last season by the closest-matching item | The harder bar. A competent planner with a spreadsheet approximates this, so beating it proves the model adds something a person could not do by hand |
| Retrospective optimum | The best achievable price path with hindsight | Not a bar to clear. An upper bound, included to show how much of the available margin is actually captured |

*Table 11. Baselines used in the Phase 1 backtest.*

The model is a gradient-boosted tree over an item-by-cluster-by-week panel. It is strong on tabular retail data, trains in minutes, produces feature attributions that can be expressed in domain terms, handles the sparse-history case gracefully, and is cheap enough that inference cost is not a business constraint. Classical time series per item was rejected because it requires the per-item history that seasonal private-label ranges do not have, which is the exact failure the product exists to solve. Deep learning is deferred to Phase 4 with multi-retailer data. An LLM API for the forecast was rejected outright as the wrong tool for numeric estimation over structured panel data.

Confidence is a first-class model output rather than a post-hoc score, derived from three inputs the planner can understand: quantile spread across the prediction interval, the number and quality of analogue matches, and agreement between clusters. A model that is accurate but badly calibrated is more dangerous than one slightly less accurate that knows when it does not know, and Section 7.8 treats poor calibration as a launch blocker for that reason.

## 5.6 Evaluation framework

Every technical metric has a business metric it answers to. A model metric with no line to margin is not reported.

| Layer | Metric | Target or band | Why it matters |
|---|---|---|---|
| Technical | Weighted MAPE on residual sell-through, held-out season, weighted by value at stake | Report | Unweighted error over-rewards getting small items right |
| Technical | Calibration of the confidence signal | High-band realised error materially below Medium | The most important technical metric. An uncalibrated confidence signal makes every other guardrail cosmetic |
| Technical | Coverage, the share of items given a recommendation | 75% to 90% | Below 75% the product is not doing enough work to justify the licence. Above 90% abstention is probably decorative |
| Technical | Analogue match rate for new lines | Report by category | Early warning on the weakest data assumption |
| Business | Margin recovered on exit stock against baseline | Must beat both baselines by at least +5% | The gate |
| Business | Sell-through at window close | Improve or hold against control | Guards against recovering margin by holding price and leaving stock unsold |
| Business | Terminal stock as a share of season receipts | Improve or hold against control | With the row above, stops a margin gain that is really a cost deferred into next season |
| Business | Time to complete a markdown review cycle | Reduce against pre-pilot | Planner time is the operational cost the product spends |
| Trust | High-confidence recommendations with bad outcomes | Rare and falling. The project-ending metric | Counted individually and reviewed case by case, never reported only as a rate |
| Trust | Acceptance rate | Band of 50% to 85%, a ceiling rather than a target | Above 85%, planners may have stopped reading. Below 50%, the model is not useful or the UX is not persuading |
| Trust | Override reason distribution | Monitored for concentration | Concentration signals a missing feature rather than fifteen unrelated disagreements |
| Trust | Planner-reported confidence | Surveyed each cycle | Catches quiet disengagement that acceptance rate alone would hide |

*Table 12. Evaluation framework across technical, business and trust layers.*

The protocol has three fixed rules. The split is temporal, training on seasons n minus 2 and n minus 1 and testing on season n, because a random row split leaks within-season information and flatters the model badly. There is no look-ahead: recommendations at week k use only data available at week k, so the backtest replays the season week by week rather than scoring it in one pass. And thresholds were fixed before the run, since the obvious failure mode of an internal evaluation is moving the bar after seeing the result.

## 5.7 Illustrative Phase 1 result

| Approach | Margin recovered, indexed | Against flat rule |
|---|---|---|
| Flat 30% at week 8, current practice | 100.0 | Baseline |
| Last-season analogue matching | 104.0 | +4.0% |
| **ClearSight** | **109.4** | **+9.4%** |
| Retrospective optimum, upper bound | 127.5 | +27.5% |

*Table 13. Illustrative Phase 1 backtest result on synthetic held-out season data, indexed on the flat rule.*

The gate is cleared: ClearSight beats the flat rule by 9.4% and analogue matching by 5.2%, both above the +5% threshold that was fixed in advance.

Where it failed is reported alongside where it won, because a backtest that reports only wins is marketing rather than evidence. The model lost to baseline in 1 of 5 clusters, specifically Small format at 27 stores, which is the smallest and least predictable. Eleven of 340 high-confidence calls went wrong. Weather-driven demand is not modelled at all, and that limitation is carried into Phase 2 unresolved rather than quietly dropped.

The retrospective optimum row is included deliberately. ClearSight captures roughly a third of the theoretically available margin, and stating that is more useful than a headline implying the problem has been solved.

*Figure 2. Screen 6, the Phase 1 backtest gate. This screen has no planner interface: it exists to decide whether the planner interface should be built at all. The stop condition was written down before the result was known. See `Appendix/Wireframes/06_backtest_gate.png`.*

## 5.8 Launch thresholds

| Gate | Passes if | Stops if |
|---|---|---|
| Phase 1 to 2 | Beats both baselines by at least +5% on a held-out season, with confidence calibrated well enough that High-band error is materially below Medium | Fails to beat analogue matching. The project ends here, at a cost of weeks rather than quarters |
| Phase 2 to 3 | Coverage between 75% and 90%; planners review a substantial share of recommendations; override reasons are substantive; review cycle time does not increase | Planners do not engage, or override reasons are blank. That is a UX failure, fixed before going live rather than after |
| Phase 3 to 4 | Margin beats the matched control; sell-through and terminal stock not worse; acceptance rate inside band; zero unexplained high-confidence failures on high-value items | Any high-confidence failure on a high-value item that the post-mortem cannot explain. One is sufficient to hold the gate |
| Standing, all phases | Not applicable | Expected loss from acting on high-confidence wrong recommendations exceeds expected gain from the rest |

*Table 14. Launch thresholds and stop conditions per phase gate.*

The asymmetry is intentional. Passing a gate requires several conditions to hold together, while failing one requires a single condition. A markdown, once taken, cannot be untaken, so the evaluation design is deliberately biased toward stopping.

## 5.9 Case lens: Netflix, metrics and evaluation

What transfers from the Netflix case is the discipline of connecting model metrics to business outcomes, and of treating a metric that looks too good as a signal to investigate rather than celebrate. ClearSight's acceptance rate is handled in exactly that way, as a health signal with a ceiling rather than a number to maximise.

What differs is the experimental budget. Netflix can A/B test continuously against millions of sessions, and a wrong recommendation costs one bad viewing evening. ClearSight gets one exit window per category per season, so the number of natural experiments available per year is in the tens rather than the millions, and a wrong recommendation is written in ink because stock sold cheap does not come back.

The consequence is a different evaluation design. ClearSight leans on held-out historical seasons and matched-control categories rather than live experimentation, and keeps a human decision on every action. Where Netflix can learn by acting, ClearSight has to learn before acting.

# 6. Business, economics and scaling

The full working is in `Appendix/Economics/Business_Economics_Scaling.md`.

## 6.1 Monetisation model

The model is an annual platform fee per retail banner plus a per-category fee, sold as enterprise B2B SaaS to the merchandise or trading function on a term commitment.

Three alternatives were rejected. Per-recommendation pricing is the obvious AI-native choice and the wrong one: inference cost is negligible, so usage pricing would not be recovering a real cost, and it would price the exact behaviour the product depends on, since a planner charged per item scored will score fewer items and review less carefully. Pure gain-share on margin recovered is philosophically right and operationally fragile, because clean attribution requires a control group the retailer maintains every season and every disputed attribution becomes an invoice argument; it is kept as a Phase 4 option for a confident second or third customer. Per-seat pricing is perverse, since it penalises the retailer for putting more human review on the decision, which is the control the whole governance case rests on.

The hybrid works because the platform fee covers the fixed cost of having the retailer as a customer at all, while the per-category fee tracks the value driver, since margin recovered scales with categories in scope. The customer can start narrow and expand, which matches the phased rollout the product recommends anyway.

## 6.2 The value assumption chain

The reference customer is one retail banner with 312 stores, five store clusters, 12 seasonal categories in markdown scope, and two exit windows per category per year.

| # | Assumption | Value used | Confidence | How a pilot replaces it |
|---|---|---|---|---|
| 1 | Seasonal categories in markdown scope | 12 | High | Read from the retailer's category tree |
| 2 | Exit windows per category per year | 2 | High | Read from the trading calendar |
| 3 | Exit stock at full retail per category-window | $4.0m | Medium | Measured in week 1 of the first pilot window |
| 4 | Cost-to-retail ratio on that stock | 50% | Medium | From the product master, exact |
| 5 | Share of full retail realised under the current flat rule | 68% | Low. The softest link | Measured from last season's markdown history |
| 6 | Relative uplift in margin recovered against the flat rule | +9.4% | Low until backtested. This is what Phase 1 exists to test | The backtest gate, before anything else matters |
| 7 | Share of value the product covers, with the rest routed to manual | 83% | Medium | The coverage metric, observable from week 1 of shadow mode |

*Table 15. The value assumption chain, numbered so each link can be challenged individually.*

Working the chain for one category-window: exit stock is $4.0m at full retail against a cost of $2.0m; the flat rule realises 68% of full retail, giving revenue of $2.72m and gross margin recovered of $0.72m; ClearSight at +9.4% gives $0.788m. The uplift is therefore $68,000 per category-window.

Across a banner and a year, 24 category-windows at $68,000 gives $1.63m, and applying the coverage haircut of 83% gives approximately $1.35m. Against a fee of $300,000, comprising a $120,000 platform fee plus 12 categories at $15,000, the retailer keeps roughly 78% of the value created and the product is priced at about 22% of conservatively estimated benefit.

Two sensitivity checks matter more than the headline. If assumption 6 lands at +3% rather than +9.4%, annual value falls to roughly $430,000 and the fee has to drop below about $130,000 for the deal to make sense, which leaves the product viable but only as a platform-fee-only offering. If assumption 6 lands below +2%, the value case does not clear the retailer's cost of change, and that outcome is a stop rather than a discount. Phase 1 tests assumption 6 before a single line of interface is written, and that sequencing is the entire logic of the roadmap.

## 6.3 Cost drivers

| Cost | Type | Annual, per banner |
|---|---|---|
| Model inference | Variable | Under $500 |
| Data pipeline, storage and hosting | Semi-variable | Approximately $18,000 |
| Model monitoring, retraining and drift review | Fixed per customer | Approximately $35,000, roughly 0.25 FTE |
| Customer success and planner support | Fixed per customer | Approximately $45,000, roughly 0.3 FTE |
| Onboarding, amortised over a three-year term | One-off of about $90,000 | Approximately $30,000 |
| **Total cost to serve** | | **Approximately $128,000** |

*Table 16. Cost to serve one banner for one year.*

The inference figure covers roughly 1.15 million scores per year, from 24 category-windows multiplied by about 300 items, five clusters, four price points and eight weekly re-scores. A gradient-boosted tree scores these in batch in minutes.

That row is the finding this table exists to produce: inference is not the cost driver, and humans are. Compute is under 0.5% of cost to serve, while monitoring, support and onboarding together account for more than 85% of it. This inverts the economics of the generative-AI products the course examines, and it changes three decisions. Usage-based pricing is not merely unhelpful but unfounded, because there is no meaningful marginal cost to pass on. The scaling problem is an operations problem rather than an infrastructure one, since adding a customer means adding onboarding and monitoring capacity rather than compute. And making the model bigger is a poor instinct here, because additional compute buys very little while better attribute data and better clustering buy a great deal.

Inference cost would become material in exactly one scenario: if the LLM explanation layer moved from templates to per-item generation. Generating a language explanation for 1.15 million scored items a year would move inference from three figures to five or six, which is a cost reason for keeping the explanation layer template-based in the MVP as well as a trust reason.

## 6.4 Unit economics

| Metric | Value |
|---|---|
| ARR per banner | $300,000 |
| Cost to serve | $128,000 |
| Gross margin | Approximately 57% |
| Customer value delivered | Approximately $1.35m per year |
| Value capture ratio | Approximately 22% |
| Onboarding cost, one-off | Approximately $90,000 |
| Payback on onboarding | Approximately 7 months |

*Table 17. Unit economics per banner.*

Gross margin of 57% sits below best-in-class SaaS because of the human onboarding and monitoring load, and that is an honest figure for a vertical AI product with a services tail rather than a number to be optimised on a slide. Value capture is held deliberately below a third, because a retailer needs a visible majority of the benefit in order to defend the spend internally.

The number that decides whether this is a company or a consultancy is onboarding cost. At $90,000 per customer it is a services business with software attached. The core-versus-customisation split exists precisely to drive that figure down, by keeping every retailer-specific behaviour in configuration rather than in code. If onboarding cannot be brought toward $30,000 by the third customer, the model does not scale, and the honest conclusion would be to sell ClearSight as a managed service rather than as a platform.

Contribution margin improves with categories rather than with usage. A retailer expanding from 4 to 12 categories adds $120,000 of revenue against near-zero marginal cost, because the clusters, integration and pipeline already exist. That shapes go-to-market directly: win one banner, prove one category, expand inside the account.

## 6.5 Scaling constraints

| Constraint | Assessment |
|---|---|
| Latency | Not a constraint. The decision cadence is weekly and scoring runs overnight in batch, so a planner opening the worklist reads pre-computed results. This is a genuine architectural advantage and worth stating rather than assuming |
| Cost growth | Not a constraint. Costs scale with customers and categories, both of which are priced |
| Data onboarding | The real constraint. Every retailer has a different product master, markdown history format and definition of terminal stock, and the messiness is genuinely idiosyncratic so it resists automation |
| Cluster quality at the edges | A constraint at the margins. Clusters below roughly 20 stores have no statistical basis, so long-tail formats stay permanently low-confidence. A retailer with many small distinct formats gets structurally less value, and that belongs in the sales conversation rather than the pilot post-mortem |
| Model maintenance across customers | The operational cliff, arriving at roughly five customers. One model per retailer is simple but does not scale, while a shared model needs pooled data retailers will not share. The likely path, a shared architecture with per-retailer training, multiplies monitoring load linearly |
| Governance burden | Grows faster than revenue. Each customer brings its own approval chain, audit expectations and, in some markets, pricing-fairness scrutiny, none of which carries attached revenue |
| Workflow variation | Underestimated. Approval thresholds and sign-off differ by retailer. Configuration absorbs some of this; the rest becomes feature requests that risk turning the product back into a project |

*Table 18. Scaling constraints, ordered from least to most binding.*

## 6.6 Case lens: OpenAI pricing and scaling

What transfers is the method rather than the conclusion. The OpenAI case makes cost-per-inference and its relationship to pricing a first-class product concern rather than an engineering detail, and working that same calculation here is what surfaced the finding that inference is under 0.5% of cost to serve. That finding is what killed per-recommendation pricing, which had been the intuitive default before the arithmetic was done.

What differs is the direction of the result. For a general-purpose LLM API inference cost is the unit economics, so usage-based pricing directly reflects cost and scaling is an infrastructure problem. ClearSight inverts both: its marginal cost is human, so its pricing has to be human-shaped, and its scaling limit is onboarding throughput rather than compute.

The lesson taken from the case is therefore to work the unit economics before choosing the pricing model, and not to import the pricing model itself.

# 7. Ethics, governance and risk

The full note is in `Appendix/Risk_Note/Ethics_Governance_Risk.md`.

## 7.1 Framing

ClearSight is not a high-risk AI system in the regulatory sense. It makes no decisions about people, touches no consumer data, and a human approves every action. Writing a short and comfortable risk note on that basis would be straightforward, and it would miss the two risks that matter.

The first is technical and obvious in hindsight: a confidently wrong recommendation, trusted and acted on at scale. The second is structural and easy to overlook: the store-clustering layer, which is where a pricing product quietly becomes a product that prices by neighbourhood.

| # | Risk | Severity | Type |
|---|---|---|---|
| R1 | Confidently wrong recommendation acted on at scale | Critical, project-ending | Model and trust |
| R2 | Cluster bias: demographic proxies drive different pricing by catchment | High, reputational and regulatory | Fairness |
| R3 | Planners rubber-stamping, making human-in-the-loop nominal | High | Workflow |
| R4 | Feedback loops from prices the model influenced | Medium, compounding | Model lifecycle |
| R5 | Confident extrapolation beyond the data | Medium | Model |
| R6 | Scope creep into dynamic or full-price setting | High | Misuse |
| R7 | Override logs used to performance-manage planners | Medium | Privacy and workplace |
| R8 | Attribute and cost data failures mis-ranking the worklist | Medium | Data |

*Table 19. Risk register.*

## 7.2 Cluster bias, and why a pricing product has a fairness problem

ClearSight does not price by customer. It prices by store cluster, and clusters are built from climate zone, store format and size, historical seasonal curve, local competitor density, and catchment demographics. The last of those is switched on for the reference retailer because it materially improves forecast quality.

Catchment demographics is a proxy for income, so the causal path is short and entirely unintentional: catchment income drives cluster assignment, cluster assignment drives the forecast of price elasticity, and that forecast drives recommended discount depth and timing. The result is different prices, by neighbourhood income, for the same item. No protected attribute is used as a model input, and nobody decides to price by income. The outcome arrives anyway, through a variable included because it is genuinely predictive.

The harm runs in both directions, and both directions sound defensible from inside the business. If lower-income catchments show slower sell-through, the model may recommend holding price longer to avoid clearing at cost, so customers in poorer areas wait longer for markdowns and see less choice by the time prices fall; the optimisation is locally rational and the distributional outcome is regressive. If instead those stores show high price elasticity, the model recommends cutting harder there, which looks pro-consumer and reads very differently in a newspaper, and which entrenches a two-tier assortment where some catchments systematically receive end-of-line stock at clearance while others do not.

Either pattern would emerge gradually, would be invisible in the aggregate margin metrics the product reports, and would be entirely rational from the model's point of view. That combination of invisible, gradual and locally optimal is what makes it dangerous.

Four groups could be harmed. Shoppers in specific catchments face systematically different access to discounted stock, correlated with income, without anyone having chosen that. The retailer faces reputational damage disproportionate to the margin gained, plus pricing-fairness scrutiny in markets where it is regulated. Planners are asked to defend a pattern they did not design and cannot see from inside a single item's recommendation. Private-label suppliers face a downstream effect, because consistently deeper markdowns on a supplier's lines shape next season's buy on the basis of a model output nobody audited for it.

Five controls follow. Disparity testing runs as a standing metric rather than a one-off audit: every backtest and every pilot cycle reports recommended depth and timing aggregated by catchment income decile, and a monotonic relationship with income in either direction goes to review before the cycle proceeds. This runs from Phase 1, on the backtest, before any planner sees a recommendation. Demographics is a switchable input with the switch visible on the configuration screen, so a retailer can run without it and see the forecast-quality cost of that choice explicitly, which turns an invisible default into a decision somebody owns. Cluster composition is reviewed by a human at onboarding specifically for demographic concentration, so that a cluster which turns out to be "the low-income stores" under a geographic name is caught there. A stated ceiling on cluster-to-cluster divergence escalates any item whose recommended depth varies across clusters beyond a configured threshold, which costs some optimisation and buys a hard limit on how far neighbourhood pricing can drift apart. And the disparity report goes to the retailer rather than staying internal, because the retailer owns pricing policy and must be able to see the pattern in its own governance forum.

None of this makes the product fair in an absolute sense, and claiming otherwise would be dishonest. Store-level pricing variation already exists in retail, and ClearSight does not create it. What it does is make that variation systematic, faster, and traceable to a model, which is a change in kind rather than only in degree. Traceability cuts both ways, since it is easier to audit and equally easier to subpoena. The defensible posture is to measure the disparity, show it to the customer, and let a human own the policy.

*Figure 3. Screen 7, store cluster configuration. Catchment demographics appears as a switchable clustering input, which is what makes the fairness trade-off in Section 7.2 a visible decision rather than a hidden default. See `Appendix/Wireframes/07_store_cluster_config.png`.*

## 7.3 Privacy

Privacy exposure is genuinely low, and it is worth being precise about why rather than waving it through.

The system touches no customer identifiers, no transaction-level customer data, no loyalty data and no payment data. It operates on aggregate units sold by item, store and week, and a basket is never linked to a person. That is a deliberate scope boundary and it should remain one.

Two exposures nonetheless exist. Catchment demographic data is about areas rather than individuals, which reduces the privacy question without removing it: fine-grained catchment data at small geographies approaches identifiability in sparse areas, and it carries third-party licensing terms that constrain how derived outputs may be used and shared. The controls are to use the coarsest geography that preserves forecast quality, hold licence terms in the data register, and never persist catchment attributes at store-record level beyond the cluster assignment they produce.

The second exposure is easier to miss. The override log is a record of employee judgement: every decision, every override and every reason code, attributed to a named planner and time-stamped. It is the most personally sensitive data in the product, and the product generates it itself. It does not look like personal data, but it is a detailed longitudinal record of how well one identifiable person does their job, and Section 7.5 treats the misuse of that record as a named risk.

Retailer data stays in the retailer's tenancy, with no pooling across customers absent an explicit and separately negotiated agreement. Retention on the decision log is configurable and defaults to three seasons, which is what evaluation requires and no more.

## 7.4 Unsafe output, and why hallucination is the wrong word here

The MVP uses a gradient-boosted tree rather than a language model, so hallucination in the LLM sense does not apply. The structurally equivalent failure is confident extrapolation: producing a plausible-looking forecast for an item the model has no real basis for, because the model has no native notion of never having seen anything like this before.

The controls are structural. Confidence is derived from prediction-interval spread, analogue count and cross-cluster agreement, so thin evidence produces low confidence by construction rather than by policy. Below threshold the product shows no recommendation at all rather than a hedged one. Items with no usable analogue are flagged as new lines rather than scored. Stale or missing data marks an item not assessable rather than falling back on an old number.

Genuine hallucination risk enters at one point: the LLM explanation layer, if it is ever built. A fluent, confident, wrong explanation attached to a correct number is bad. Attached to a wrong number it is considerably worse, because it manufactures exactly the trust the confidence design is trying to calibrate. If that layer is added it must be constrained to the model's actual drivers, evaluated for faithfulness rather than fluency, and shipped behind the same confidence gate as the recommendation itself.

## 7.5 Misuse

| Risk | Scenario | Control |
|---|---|---|
| Scope creep into dynamic pricing | The clustering and elasticity machinery is one product decision away from setting full price by neighbourhood, or from in-season dynamic pricing. It would be commercially tempting and technically straightforward | A stated product boundary, written into the PRD as out of scope and repeated here: ClearSight applies to exit and markdown decisions only. Extending it is a new product with a new risk assessment. The bias concern becomes far more acute at full price, where the customer-facing effect is continuous rather than confined to end-of-season stock |
| Post-hoc justification | A planner decides first, then finds the recommendation that agrees and cites the tool as evidence. The audit trail then launders a judgement call as an analytical one | Decisions are logged with the recommendation as presented, before the action. Override reasons are captured at the moment of override. Acceptance patterns resembling selective citation surface in the reason distribution |
| Surveillance of planners | Override logs become a performance-management instrument. Planners then override less to protect themselves, acceptance rate rises, and the human-in-the-loop control quietly becomes nominal, destroying the thing the governance case depends on | Override data is reported in aggregate for product and model evaluation, not per planner for performance review, and this is written into the customer contract rather than left to convention. Individual-level access is restricted and logged, and planners are told what is recorded and why. This control depends on the customer honouring it, which is a real limitation and belongs in the sales process rather than glossed over |
| Supplier pressure | Markdown patterns are used to squeeze private-label suppliers on terms, with model output as leverage | Outside the product's control, but flagged to the customer at onboarding as a foreseeable secondary use of the outputs |

*Table 20. Misuse risks and their controls.*

## 7.6 The operational risk set

R1, confidently wrong recommendations at scale, is controlled by confidence thresholds with mandatory manual routing below them; by the absence of any automatic price execution; by a second approver above a configurable value-at-stake threshold; by an individual post-mortem on every high-confidence failure; and by the standing launch blocker in Section 7.8.

R3, rubber-stamping, is controlled by monitoring acceptance rate against a ceiling of 85% rather than a target; by giving Approve no visual dominance over Adjust and Reject; by making overriding cost no more clicks than agreeing; by omitting bulk approve from the MVP deliberately; and by surveying planner-reported confidence each cycle to catch quiet disengagement.

R4, feedback loops, is controlled by holdout categories that run on the flat rule for full seasons and are maintained permanently rather than only at pilot; by drift monitoring from Phase 3; by recording which observations in a retraining set came from ClearSight-influenced prices; and by re-benchmarking periodically against the original flat-rule baseline rather than against the model's own previous version, so that the bar cannot drift upward with the model.

R8, data quality, is controlled by monitoring coverage and analogue match rate as leading indicators; by validating cost data at ingest, because a wrong cost silently mis-ranks the entire worklist and produces no visible error; and by routing degraded items to manual rather than lowering recommendation quality.

## 7.7 Governance

ClearSight recommends and the planner decides. The retailer owns pricing policy, cluster configuration and the approval chain, and nothing in the product changes a price.

The audit trail records every recommendation as presented, every decision, every override with its reason and timestamp, every configuration change, and the model version in force. That is what makes a markdown defensible upward and what makes a post-mortem possible.

| Frequency | Review | Owner |
|---|---|---|
| Weekly during a window | Coverage, override reasons, any high-confidence failure | Product and planner lead |
| Per exit window | Margin against control, acceptance rate against band, catchment-disparity report | Product and retailer trading |
| Per season | Drift, holdout comparison, retraining decision, model card update | Data and product |
| Annual | Full model review, cluster composition re-examined for demographic concentration, scope boundary reaffirmed | Retailer governance forum |

*Table 21. Governance review cadence.*

A model card is maintained per customer, covering training data and the seasons used, excluded seasons and the reason for each exclusion, cluster definitions and active inputs, calibration by confidence band, known failure modes, and disparity test results. It is written to be read by the retailer's governance function rather than only by the product team.

Escalation is defined rather than assumed. A high-confidence failure on a high-value item goes to post-mortem within the cycle rather than at season end, and its findings can hold the next phase gate. A confirmed disparity pattern goes to the retailer's governance forum rather than to a product backlog.

## 7.8 Launch blockers

Six conditions stop a launch. They are not risks to be weighed against benefits.

- **Expected loss exceeds expected gain.** If the expected loss from acting on high-confidence wrong recommendations exceeds the expected gain from the rest, the product does not launch. This blocker is permanent and applies between gates, not only at them.
- **The confidence signal is not calibrated.** If realised error in the High band is not materially below Medium, the entire trust design is decorative and nothing else in this report holds.
- **A material catchment-disparity pattern with no explanation and no remediation.** Monitoring it in pilot is not an acceptable answer. Either the pattern is explained and bounded, or the demographic input is removed and the forecast-quality cost is accepted.
- **An unexplained high-confidence failure on a high-value item.** One is sufficient. An unexplained failure means the confidence signal is not understood, which is the second blocker arriving through a different door.
- **Override reasons systematically blank or uniform in shadow mode.** That means the human-in-the-loop control is not real, and the governance claim in this report would be false.
- **The customer will not accept the override-data restriction.** If planner-level override data is going to drive performance management, the control that protects review quality does not exist and the rest of the governance case collapses.

A quieter condition sits alongside these: if the Phase 1 backtest cannot beat analogue matching by +5%, the project ends. It is a project blocker rather than a launch blocker, and it is listed here because it is the cheapest of all these findings to discover, which is why the roadmap is sequenced to find it first.

## 7.9 Case lens: bias in lending and the Apple Card

The Apple Card episode is the cleanest available illustration of the failure mode in Section 7.2. No protected attribute was used as a model input, outcomes diverged along one anyway through correlated proxies, and the organisation could not explain its own decisions when asked. The absence of a protected variable from the feature list turned out to be no defence at all, technically or reputationally. ClearSight's catchment-demographics input is structurally the same exposure, and recognising that is why disparity testing runs from the backtest rather than from the pilot.

The differences matter too. ClearSight prices stock in stores rather than credit for individuals, so there is no individual adverse decision, no legal right to an explanation, and the harm is diffuse and statistical rather than concentrated on one person who can point at it. That makes it less severe, and in one specific respect harder to catch: nobody complains, because nobody experiences a decision that was made about them.

The response is to test for the disparity that carries no legal exposure. Aggregate disparity reporting by catchment income runs as a standing metric from Phase 1, the demographic input is switchable with its cost made visible, and the report goes to the retailer's governance forum rather than staying in a model report. The lesson taken from the case is that "we did not use that variable" is not a control, and that measuring the outcome is.

# 8. Functional prototype and MVP

## 8.1 What was built

The prototype is a single self-contained HTML file, `ClearSight_Prototype.html`, with no build step and no dependencies. It opens in any browser. Seven annotated wireframes in `Appendix/Wireframes/` cover the screens outside the MVP walkthrough.

It demonstrates the complete planner decision loop. The exit worklist shows 184 items ranked by value at stake totalling $1.2m, with 31 routed to manual, and it shows recommendation and confidence per row before the planner clicks into anything, because the ranking is where most of the value sits. The high-confidence item detail states the recommendation as an action, shows projected sell-through against the current flat schedule, compares four options, breaks sell-through down by cluster, and gives a plain-language explanation with the largest driver named, above three actions of equal visual weight. The low-confidence screen uses the same layout with no recommendation at all, three specific reasons why confidence is low, a statement of what would improve it, and all the underlying evidence still visible. The reason-capture modal handles adjust and reject. A session decision log records every decision as it is made, showing what the audit trail would actually contain.

*Figure 4. Screen 1, the exit worklist. Items are ranked by value at stake rather than by discount size or units on hand, because attention is the scarce resource. Confidence and recommendation are visible per row before the planner opens anything. See `Appendix/Screenshots_or_Video/01_worklist.png`.*

*Figure 5. Screen 2, high-confidence item detail. The recommendation is stated as an action, the alternatives that were considered are shown alongside it, and Approve, Adjust and Reject carry equal visual weight. See `Appendix/Screenshots_or_Video/02_high_confidence_detail.png`.*

*Figure 6. Screen 3, the low-confidence state. No recommendation is offered. The three reasons for low confidence are named, what would improve confidence is stated, and the underlying cluster evidence remains visible so the planner can still decide. See `Appendix/Wireframes/03_low_confidence_abstain.png`.*

*Figure 7. Screen 4, reason capture on reject. Overriding costs no more clicks than agreeing, and the reason codes are the same five used throughout the product. See `Appendix/Screenshots_or_Video/04_reason_capture.png`.*

## 8.2 Where the AI sits

The AI creates value at exactly one point: forecasting residual sell-through per store cluster at candidate price points, which produces the ranking, the option comparison and the confidence signal. Everything else in the interface is conventional software built around that estimate.

The prototype runs on fixed synthetic data rather than a live model. That is deliberate, because what needs testing at this stage is whether the workflow and the trust model hold, not whether the forecast is accurate. Forecast accuracy is what the Phase 1 backtest tests, and it is tested first.

## 8.3 What the prototype does not prove

A prototype that overclaims is worse than no prototype, so the limits are stated plainly.

- **Not forecast accuracy.** The numbers are synthetic, and Phase 1 tests this.
- **Not enterprise integration.** There is no POS or ERP connection, deliberately deferred to Phase 4.
- **Not cross-category generalisation.** One category, one exit window.
- **Not confidence calibration.** The High, Medium and Low bands are illustrative, and whether they would be calibrated in practice is the most important open technical question in the project.
- **Not adoption.** Whether a planner under time pressure genuinely reads the reasoning rather than clicking Approve is what Phase 2 shadow mode exists to find out, and no prototype can answer it.

## 8.4 Why the scope is narrow

The prototype covers screens 1 to 4 and omits 5 to 7, which is a product decision rather than a scoping shortcut. Screens 1 to 4 are the decision loop, and if the trust model fails there then nothing downstream matters. Screen 6 belongs to Phase 1 and has no planner interface at all. Screen 5 requires live decisions before it has any content. Screen 7 runs once per retailer at onboarding. The real risk is trust and workflow fit, and that risk lives entirely in the four screens that were built.

# 9. How PM artifacts change because the product is AI

| Standard PM artifact | What changed here |
|---|---|
| Problem statement | Defines the decision being improved rather than a feature to build |
| Persona | Carries error cost and trust rather than only goals. Priya's asymmetric risk shapes the ranking, the abstention design and the override path |
| PRD | Data assumptions, confidence thresholds and fallback behaviour are first-class requirements. What the system does when it does not know is specified before what it does when it does |
| Prioritisation | Confidence display and override capture rank P0, where in a deterministic tool they would be polish. RICE had to be overruled to place them there |
| Roadmap | Phase 1 is a backtest rather than a build. Proof gates precede investment, and every gate is a real stop point |
| Metrics | Every technical metric ties to margin, and acceptance rate carries a ceiling rather than a target |
| Pricing note | Anchored to measurable outcome, with inference cost worked as a unit cost, which is how the finding in Section 6.3 emerged and killed usage pricing |
| Risk note | Includes a fairness analysis that a non-AI markdown tool would not need, because the clustering layer creates a proxy path from catchment income to price |
| Launch checklist | Includes shadow mode, rollback, escalation, and six defined blockers, any one of which stops a launch |

*Table 22. How each standard PM artifact changed because the product is AI-powered.*

AI tools helped produce these artifacts by drafting structure, generating wireframe starting points, enumerating risk-and-control pairs, proposing the three-layer metric split, and running a cross-artifact consistency review. The detail is disclosed in `05_AI_Collaboration_Log.pdf`.

AI did not make the decisions that carry accountability: the opportunity choice, keeping a human on every markdown, the pricing model, the six launch blockers, the scope boundary against dynamic pricing, and the recommendation to backtest before building.

# 10. Final recommendation

The recommendation is to prototype, then pilot, with a hard gate in between.

Phase 1 is a backtest against historical seasons, asking whether the recommendation beats both the current flat markdown rule and last-season analogue matching on margin recovered. No interface is required. If it does not beat both by +5%, the project stops there, and that outcome is a finding rather than a failure because it costs weeks to discover.

Phase 2 is a planner-facing MVP in shadow mode covering one category, one exit window and one market, with planners using it alongside their normal process and decisions not applied. This tests whether the workflow and the trust model hold, which is the real adoption risk.

Phase 3 is a controlled live pilot with a matched control group of categories, and the catchment-disparity report running from the first cycle.

Integration, additional categories and a second customer should not be funded until Phase 2 shows planners engaging with recommendations and giving substantive reasons when they override.

## 10.1 Why not pilot immediately

The instinct, and the first draft of this recommendation, was to go straight to a pilot. That was wrong for three reasons. A markdown, once taken, cannot be untaken, so the cost of being wrong in a live pilot is real margin on real stock. The most important unknown is also the cheapest to test, since whether the model beats the baseline requires no interface, no integration and no planner time. And the second most important unknown is not a modelling question at all: whether a planner under time pressure genuinely reads the reasoning is something shadow mode tests without risking margin.

## 10.2 What would change this recommendation

If the backtest fails to beat analogue matching, the project stops. If the backtest clears by a wide margin but confidence is poorly calibrated, Phase 2 does not begin until calibration is fixed, because the trust design depends on it entirely. If shadow mode shows planners approving without reading, the product is not ready regardless of forecast quality, and the UX is revised before Phase 2 is re-run. If a catchment-disparity pattern appears with no explanation, the demographic input is removed and the forecast-quality cost accepted, or the project stops.

In one sentence: fund a four-to-six week backtest rather than a build, and treat the result as a genuine stop condition, because the cheapest thing this project can do is discover early that it should not exist.

# 11. Limitations

Six limitations bound what this submission establishes.

- **All figures are synthetic.** The backtest result, the pilot numbers and the economics are illustrative, derived from the assumption chain in Table 15. They demonstrate that the reasoning closes, not that the product works.
- **The counterfactual problem is designed around rather than solved.** Historical prices were chosen by planners using the rule the model is trying to beat, and only the Phase 3 matched control provides a genuine randomised check.
- **Confidence calibration is asserted rather than demonstrated.** The abstention design, the trust model and four of the six launch blockers all depend on the confidence signal being calibrated, and no evidence in this submission establishes that it would be.
- **No live user research.** The persona and the trust design are built from professional workflow knowledge rather than from interviews with planners at a target customer. Phase 2 shadow mode is the first point at which a real planner tests either.
- **The prototype is not connected to a model.** It demonstrates the workflow and the trust design on fixed data, and it cannot demonstrate behaviour under a real forecast distribution.
- **Onboarding cost is the least evidenced number in the economics.** It carries the most weight in deciding whether ClearSight is a product or a service, and it is the figure a first customer would revise most.

# References

[1] Netflix Technology Blog. Metrics, evaluation and experimentation practice at Netflix. Course case material, MBA ZG583, Management of AI Products.

[2] Vigdor, N. (2019). Apple Card investigated after gender discrimination complaints. The New York Times, 10 November 2019. Course case lens on bias and governance.

[3] Knight, W. (2019). The Apple Card didn't see gender, and that's the problem. WIRED, 19 November 2019.

[4] OpenAI. Pricing and scaling of API-based AI products. Course case material on cost, latency and monetisation, MBA ZG583.

[5] Ng, A. (2021). MLOps: from model-centric to data-centric AI. DeepLearning.AI. Referenced for the position that data quality dominates model choice in applied settings.

[6] Amershi, S., Weld, D., Vorvoreanu, M. et al. (2019). Guidelines for Human-AI Interaction. Proceedings of CHI 2019, 1-13. Basis for the uncertainty-handling and explainability design in Section 4.

[7] Google PAIR (2021). People + AI Guidebook. Google. Referenced for confidence communication and graceful failure patterns.

[8] Fisher, M. & Raman, A. (2010). The New Science of Retailing: How Analytics are Transforming the Supply Chain and Improving Performance. Harvard Business Review Press. Background on markdown optimisation and seasonal retail economics.

[9] Barocas, S., Hardt, M. & Narayanan, A. (2019). Fairness and Machine Learning: Limitations and Opportunities. fairmlbook.org. Basis for the proxy-variable analysis in Section 7.2.

[10] Sculley, D., Holt, G., Golovin, D. et al. (2015). Hidden Technical Debt in Machine Learning Systems. Advances in Neural Information Processing Systems 28. Basis for the feedback-loop and monitoring treatment in Sections 5 and 7.



# Appendix A. Remaining screens

The four MVP screens appear as Figures 4 to 7 in Section 8, and the backtest gate and cluster configuration screens as Figures 2 and 3. The screen below completes the set.

*Figure 8. Screen 5, outcome tracking in Phase 3. Three metric layers appear on one screen: the business result, the product health metric, and the trust metric that is designed to embarrass the product if it is failing. The acceptance-rate ceiling is drawn on the chart as a failure line rather than a goal. See `Appendix/Wireframes/05_outcome_tracking.png`.*

*Figure 9. Screen 4 as a static wireframe, with the annotated design reasoning in the margin. The override path is where human-in-the-loop actually lives, which is why it receives a dedicated screen rather than a modal footnote. See `Appendix/Wireframes/04_adjust_and_reason_capture.png`.*

## A.1 Repository and artefacts

| Artefact | Location |
|---|---|
| GitHub repository | https://github.com/BITS-WILP-MAIP/2024bC26584-fiza-tahreen |
| Interactive prototype | `ClearSight_Prototype.html`, single file, no build step |
| PM artifact pack | `Appendix/PRD/PM_Artifact_Pack.md` |
| Product strategy | `Appendix/PRD/Product_Strategy.md` |
| UX and trust design | `Appendix/UX_Trust/UX_Workflow_Trust.md` |
| Data, model and evaluation | `Appendix/Metrics/Data_Model_Evaluation.md` |
| Business, economics and scaling | `Appendix/Economics/Business_Economics_Scaling.md` |
| Ethics, governance and risk | `Appendix/Risk_Note/Ethics_Governance_Risk.md` |
| Wireframes | `Appendix/Wireframes/`, seven screens plus a grouped overview |
| Prototype screenshots | `Appendix/Screenshots_or_Video/` |
| AI collaboration log | `05_AI_Collaboration_Log.md` |
| Individual contribution statement | `06_Individual_Contribution_Statement.md` |

*Table 23. Deliverables and where each is located in the repository.*
