# Data, Model and Evaluation Strategy: ClearSight

*Whether the AI logic is operationally credible, and what result would stop the project.*

> All figures in this section are **synthetic or illustrative**. The backtest described here is designed against public and synthetic multi-store retail data. No confidential employer data is used anywhere in this submission.

---

## 1. The problem as an AI task

The product question is *"how deep should we cut this item, and when?"* That is a decision, not a prediction, so it has to be decomposed before any model can be pointed at it.

**Primary task: forecasting.** Predict residual sell-through for item *i*, in store cluster *c*, over the remaining weeks of the exit window *w*, conditional on a candidate price point *p*.

> `sell_through(i, c, w | p) → [0, 1]`

**Secondary task: ranking.** Order the worklist by value at stake, which is the margin swing between the best and worst plausible outcome for that item, not by the size of the discount or by units on hand. Attention is the scarce resource; the ranking is what makes the tool usable at 184 items.

**Third task: recommendation.** Given the forecast at each candidate price point, choose the depth-and-timing option that maximises expected recovered margin, subject to clearing the stock by the end of the window.

**Explicitly not an AI task:** setting or executing the price. That is a human decision, always.

The decomposition matters because the three tasks fail differently and are evaluated differently. A forecast can be well calibrated while the ranking is useless. The recommendation can be right while the forecast that produced it is wrong for compensating reasons. They are measured separately below.

### Why this is a counterfactual problem, and why that is hard

The quantity the planner needs is *what will happen under a price we have not set*. Historical data contains outcomes only for prices that were actually charged, chosen by planners using the very rule the model is trying to beat. Prices in the training data are therefore not random; they correlate with the planner's own judgement about which items were struggling.

This is the central methodological risk, and it has three consequences that shape everything below:

1. Naive supervised learning on `(price, outcome)` pairs will partly learn *"items that got deep discounts sold badly"*, which is backwards causality.
2. Evaluation must be done on **held-out seasons**, not held-out rows, so the model cannot exploit within-season leakage.
3. Any observational estimate needs a randomised check eventually. Phase 3's matched-control design is that check, and it is scheduled deliberately, not as an afterthought.

We do not claim to have solved the causal inference problem. We claim to have designed around it: a conservative baseline comparison, a gate that must be cleared on held-out seasons, and a live pilot with a control group before anything scales.

---

## 2. Data sources

| Layer | Source | What it provides | Assumed availability |
|---|---|---|---|
| **Internal, core** | Merchandise / EPOS system | Weekly units sold by item × store, two seasons back | High, this is standard retail reporting |
| **Internal, core** | Stock system | Current stock on hand by item × store, cover weeks | High |
| **Internal, core** | Price and markdown history | Every price change with date, depth, and item | High, but often in a separate system |
| **Internal, core** | Product master | Category, sub-category, attributes (colour, size curve, material, pack), cost, full retail | **Medium, this is the weak link** |
| **Internal, context** | Store master | Format, size, region, opening dates | High |
| **External** | Climate zone by store location | Cluster input | High, public data |
| **External** | Catchment demographics | Cluster input | Medium, licensed data. **See the bias treatment in the Ethics note, this input is the main fairness exposure in the product** |
| **External** | Local competitor density | Cluster input | Low, often not licensed. The configuration screen shows it switched off for this retailer |
| **User-generated** | Planner decisions and override reason codes | Evaluation signal and future feature source | Created by the product itself, from Phase 2 |

### Data assumptions, stated so they can be challenged

1. **Two full seasons of weekly item × store sales exist and are trustworthy.** One season is not enough to separate seasonality from trend.
2. **Product attribute data is complete enough to match analogues.** This is the assumption most likely to fail. Retail product masters are notoriously patchy on exactly the attributes that predict seasonal demand.
3. **Markdown history records the *date* of each price change,** not just the final price. Without dates, timing cannot be learned at all, only depth.
4. **Stock on hand is accurate at store level.** Shrinkage and mis-scanned returns degrade this, and the error is larger in exactly the small-format stores where the forecast is already weakest.

**What happens when an assumption fails.** The system does not silently degrade. Poor attribute coverage means fewer analogue matches, which means lower confidence, which means the item routes to manual. Bad data produces *less coverage*, not *worse recommendations*. This is the single most important design property in the product and it is enforced in the model layer, not the UI.

---

## 3. Labelling and ground truth

There is no human labelling task here, which is a genuine advantage over most AI products: **the ground truth arrives on its own.**

**Target variable.** For a completed exit window, the label for `(item, cluster, price path)` is the realised sell-through: units sold divided by units on hand at the start of the window. It is observed, not annotated.

**Derived labels that do need construction:**

| Label | How it is constructed | Risk in the construction |
|---|---|---|
| **Analogue set** for a new item | Attribute similarity within category and season, restricted to items with a full prior-season history | Similarity is a modelling choice. A bad analogue set produces confident nonsense, so analogue count and match quality are surfaced to the planner, not hidden |
| **Value at stake** | Margin difference between the best and worst plausible outcome under the candidate price set | Depends on cost data being correct; a wrong cost silently mis-ranks the worklist |
| **"Terminal" stock** | Stock unsold at window close, valued at final clearance or write-off | Retailers define the cut-off date differently; must be configured per customer, not assumed |
| **Override reason codes** | Chosen by the planner from a fixed list plus free text | Self-reported and therefore noisy. Treated as a directional product signal, never as a training label |

**The one place human judgement enters the label:** deciding whether a season was "normal". A season disrupted by an extreme weather event or a supply failure is excluded from training with the exclusion recorded. This is a documented, reviewable decision, not a silent filter, because "exclude the seasons where the model looked bad" is the obvious way to cheat and the process has to be visibly resistant to it.

---

## 4. Baselines

The gate is not "is the model accurate". It is whether the model beats what the retailer already does, and there are two of those rather than one.

| Baseline | What it is | Why it is in the comparison |
|---|---|---|
| **Baseline 1: flat markdown schedule** | The retailer's current rule, e.g. 30% at week 8, 50% at week 11 | This is what the product actually replaces. If ClearSight cannot beat it, the product has no reason to exist |
| **Baseline 2: last-season analogue matching** | Apply the price path that the closest-matching item took last season | The harder bar. A competent planner with a spreadsheet approximates this, so beating it is what proves the model adds something a person could not do by hand |
| **Reference ceiling: retrospective optimum** | The best achievable price path with hindsight | Not a baseline to beat, an upper bound. It shows how much of the available margin the model actually captures and keeps the headline honest |

Reporting against two baselines rather than one is deliberate. A model that beats a naive flat schedule but not analogue matching has demonstrated that the retailer needs a better spreadsheet, not a product.

---

## 5. Model and API choice

**Recommendation: build a small, in-house tabular model. Do not use a general-purpose LLM API for the forecast.**

| Option | Verdict | Reasoning |
|---|---|---|
| **Gradient-boosted trees** (LightGBM / XGBoost) on item × cluster × week panel features | **Chosen for MVP** | Strong on tabular retail panel data, trains in minutes on a laptop, feature attributions are inspectable in domain terms, and it handles the sparse-history case gracefully. Cheap enough that inference cost is not a business constraint |
| Classical time series (ARIMA, Prophet) per item | Rejected | Requires per-item history that seasonal private-label ranges do not have. This is the exact failure the product exists to solve |
| Deep learning (temporal fusion, sequence models) | Deferred | Plausibly better at scale, but needs more data than one retailer has and is far harder to explain to a planner. Revisit at Phase 4 with multi-retailer data, and only if the backtest shows a ceiling the tree model cannot reach |
| **LLM API for the forecast** | **Rejected** | Wrong tool. This is a numeric estimation problem over structured panel data. An LLM would be more expensive, slower, non-deterministic, and unable to give a calibrated confidence, and it introduces hallucination risk into a number that moves real money |
| **LLM API for the explanation layer** | **Accepted, narrowly, and not in the MVP** | Turning model output into the plain-language "why" bullets is a genuine language task. Deferred past MVP: the bullets are template-generated from the model's own drivers for now, because a fluent explanation of a wrong number is worse than a terse one |

**Build, not buy.** Demand-forecasting and price-optimisation vendors exist and are mature. The reason to build is that the product's differentiator is not forecast accuracy, it is the decision workflow and the trust model: confidence routing, abstention and override capture. Those are the parts an off-the-shelf optimiser does not provide, and they are the parts the adoption risk actually lives in. Buying the forecast and building the workflow is a legitimate Phase 4 option and is kept open; for the MVP, owning the model is what makes the confidence signal controllable.

**Confidence estimation** is a first-class model output, not a post-hoc score. It is derived from three inputs the planner can understand: quantile spread across the prediction interval, the number and quality of analogue matches, and agreement between clusters. This matters because the entire trust design depends on the confidence signal being honest. A model that is accurate but badly calibrated is *more* dangerous than one that is slightly less accurate and knows when it does not know.

---

## 6. Evaluation framework

Every technical metric below has a business metric it answers to. A model metric with no line to margin is not reported.

| Layer | Metric | Target / band | Why it matters |
|---|---|---|---|
| **Technical** | Weighted MAPE on residual sell-through, held-out season, weighted by value at stake | Report, no fixed target | Unweighted error over-rewards getting small items right. The weighting makes the metric care about the same things the planner does |
| **Technical** | **Calibration of the confidence signal**, meaning realised error rate within each of High, Medium and Low | High-confidence realised error must be materially below Medium | **The most important technical metric in the product.** Confidence is the control the entire trust design rests on. An uncalibrated confidence signal makes every other guardrail cosmetic |
| **Technical** | Coverage, share of items given a recommendation | 75–90% | Below 75%, the product is not doing enough work to be worth the licence. Above 90%, abstention is probably not working and the low-confidence route is decorative |
| **Technical** | Analogue match rate for new lines | Report by category | Early warning on the weakest data assumption. Falling match rate predicts falling coverage before coverage moves |
| **Business** | **Margin recovered on exit stock vs baseline** | **Must beat both baselines by ≥ +5%** | The gate. Defined as gross margin realised on exit stock, measured against the same items under the baseline rule |
| **Business** | Sell-through at window close | Improve or hold vs control | Guards against the model recovering margin by simply holding price and leaving stock unsold |
| **Business** | Terminal stock as a share of season receipts | Improve or hold vs control | The other side of the same guard. Together these two stop a margin gain that is really a cost deferred into next season |
| **Business** | Time to complete a markdown review cycle | Reduce vs pre-pilot | Planner time is the operational cost the product is spending. If review takes longer, adoption fails regardless of margin |
| **Trust / guardrail** | **High-confidence recommendations with bad outcomes** | **Rare and falling. This is the project-ending metric** | A wrong recommendation the planner was told to trust. Not a quality issue, an existential one, so it is counted individually and reviewed case by case, never reported only as a rate |
| **Trust / guardrail** | Acceptance rate | Healthy band **50–85%** | Has a **ceiling, not a target**. Sustained acceptance above 85% triggers a review of whether planners are still reading. Below 50%, the model is not useful or the UX is not persuading |
| **Trust / guardrail** | Override reason distribution | Monitored for concentration | Fifteen rejections all citing "competitor activity" is a missing feature, not fifteen disagreements. This is the cheapest product-discovery channel the system has |
| **Trust / guardrail** | Planner-reported confidence | Surveyed each cycle | Catches quiet disengagement that acceptance rate alone would hide |

### Evaluation protocol

- **Temporal split.** Train on seasons *n−2* and *n−1*, test on season *n*. Never a random row split; random splits leak within-season information and will flatter the model badly.
- **No look-ahead.** Recommendations at week *k* use only data available at week *k*. The backtest replays the season week by week rather than scoring it in one pass.
- **Report where it fails, not only where it wins.** Every backtest report carries a mandatory "where it failed" panel: which clusters lost to baseline, how many high-confidence calls went wrong, what the model does not model at all. A backtest that reports only wins is marketing.
- **Thresholds fixed before the run.** The +5% gate was written down before the first backtest was executed. This is stated because the obvious failure mode of an internal evaluation is moving the bar after seeing the result.

### Illustrative Phase 1 result (synthetic data)

Indexed on the flat rule = 100:

| | Margin recovered (indexed) | vs flat rule |
|---|---|---|
| Flat 30% at week 8, current practice | 100.0 | Baseline |
| Last-season analogue matching | 104.0 | +4.0% |
| **ClearSight** | **109.4** | **+9.4%** |
| Retrospective optimum (upper bound) | 127.5 | +27.5% |

**Gate: cleared.** Beats the flat rule by 9.4% and analogue matching by 5.2%, both above the +5% threshold fixed in advance.

**Where it failed:** lost to baseline in 1 of 5 clusters (Small format, 27 stores, the smallest and least predictable); 11 of 340 high-confidence calls went wrong; weather-driven demand is not modelled at all and is carried into Phase 2 as a known, unresolved limitation.

The retrospective-optimum row is included deliberately. ClearSight captures roughly a third of the theoretically available margin. Stating that is more useful than a headline that implies the problem is solved.

---

## 7. Launch thresholds and stop conditions

| Gate | Passes if | Stops if |
|---|---|---|
| **Phase 1 → 2** (backtest) | Beats both baselines by ≥ +5% margin recovered on a held-out season, with confidence calibrated well enough that High-confidence error is materially below Medium | Fails to beat analogue matching. **The project ends here.** This costs weeks, not quarters, which is the entire reason it is first |
| **Phase 2 → 3** (shadow mode) | Coverage 75–90%; planners review a substantial share of recommendations; override reasons are substantive rather than blank or uniform; review cycle time does not increase | Planners do not engage, or override reasons are empty. That is a UX failure, and it is fixed before going live, not after |
| **Phase 3 → 4** (live pilot) | Margin recovered beats the matched control; sell-through and terminal stock not worse; acceptance rate inside 50–85%; **zero unexplained high-confidence failures on high-value items** | Any high-confidence failure on a high-value item that the post-mortem cannot explain. One is enough to hold the gate |
| **Any phase** | Not applicable | Expected loss from acting on high-confidence wrong recommendations exceeds expected gain from the rest. This is the standing launch blocker and it applies permanently, not just at gates |

**The asymmetry is intentional.** Passing a gate needs several conditions; failing one needs a single condition. A markdown, once taken, cannot be untaken, so the evaluation design is deliberately biased toward stopping.

---

## 8. Case lens: Netflix, metrics and evaluation

**What transfers.** Netflix's discipline of connecting model metrics to business outcomes, and of treating a metric that looks too good as a signal to investigate rather than celebrate. ClearSight's acceptance rate is handled exactly this way, as a health signal with a ceiling rather than a number to maximise.

**What is different.** Netflix can A/B test continuously against millions of sessions and a wrong recommendation costs one bad viewing evening. ClearSight gets one exit window per category per season, so the number of natural experiments available per year is in the tens, not the millions, and a wrong recommendation is written in ink, because stock sold cheap does not come back.

**What we do differently as a result.** Evaluation leans on held-out historical seasons and matched-control categories rather than live experimentation, and a human decision sits on every action. Where Netflix can learn by acting, ClearSight has to learn before acting.

---

*ClearSight | MBA ZG583, Management of AI Products | Capstone. All figures synthetic or illustrative.*
