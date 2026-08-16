# Business, Economics and Scaling View: ClearSight

*Whether the idea survives commercially and operationally, and where it breaks if it grows.*

> Every number in this section is **synthetic or illustrative**, built from a stated assumption chain rather than from any retailer's actual figures. The chain is set out explicitly so each link can be challenged and replaced with a real number during a pilot. No confidential employer data is used.

---

## 1. Monetisation model

**Annual platform fee per retail banner, plus a per-category fee.** Enterprise B2B SaaS, sold to the merchandise or trading function, with a term commitment.

**Why not the alternatives:**

| Model | Why not |
|---|---|
| **Per recommendation / per item scored** | The obvious "AI-native" choice and the wrong one. Inference cost here is negligible, so usage pricing would not be recovering a real cost. Worse, it prices the exact behaviour the product depends on: a planner who is charged per item scored will score fewer items, review less carefully, and route more to manual. It would make the product's own success metric expensive for the customer |
| **Pure outcome / gain-share on margin recovered** | Commercially attractive and philosophically right, but operationally fragile. Attributing margin recovery cleanly requires a control group the retailer has to maintain every season, and every disputed attribution becomes an invoice argument. Kept as a **Phase 4 option** for a confident second or third customer, once a clean measurement protocol exists |
| **Per seat / per planner** | Perverse. It penalises the retailer for putting more human review on the decision, which is the control the whole governance case rests on |
| **Flat platform fee only** | Simple, but disconnects price from value: a 4-category retailer and a 40-category retailer pay the same |

**Why the hybrid works.** The platform fee covers the fixed cost of having the retailer as a customer at all: clustering setup, integration, onboarding and monitoring. The per-category fee tracks the value driver, because margin recovered scales with categories in scope. The customer can start narrow and expand, which matches the phased rollout the product recommends anyway. Price grows with adoption without ever charging per decision.

**Value anchor.** The fee is quoted as a fraction of *conservatively estimated* margin recovered, measured against the retailer's own baseline rule. The estimate is deliberately conservative because the pricing conversation and the launch gate use the same number, and a number inflated to win a deal would corrupt the evaluation that decides whether the product should ship at all.

---

## 2. Value logic: the assumption chain

The value case is a chain, and it is only as strong as its weakest link. Every link is numbered so it can be attacked individually.

**Reference customer:** one retail banner, 312 stores, five store clusters, 12 seasonal categories in markdown scope, two exit windows per category per year.

| # | Assumption | Value used | Confidence | How a pilot would replace it |
|---|---|---|---|---|
| 1 | Seasonal categories in markdown scope | 12 | **High** | Read directly from the retailer's category tree |
| 2 | Exit windows per category per year | 2 | **High** | Read from the trading calendar |
| 3 | Exit stock at full retail value, per category-window | $4.0m | **Medium** | Measured in week 1 of the first pilot window |
| 4 | Cost-to-retail ratio on that stock | 50% | **Medium** | From the product master, exact |
| 5 | Share of full retail actually realised under the current flat rule | 68% | **Low, this is the softest link** | Measured from last season's markdown history |
| 6 | Relative uplift in margin recovered vs the flat rule | +9.4% | **Low until backtested. This is the number Phase 1 exists to test** | The backtest gate, before any of the above matters |
| 7 | Share of value the product actually covers (rest routes to manual) | 83% | **Medium** | Coverage metric, observable from week 1 of shadow mode |

### Working the chain

**Per category-window**

- Exit stock at full retail: **$4.0m**; cost of that stock at 50%: **$2.0m**
- Flat rule realises 68% of full retail → revenue **$2.72m** → **gross margin recovered $0.72m**
- ClearSight at +9.4% → **$0.788m**
- **Uplift: +$68k per category-window**

**Per banner, per year**

- 12 categories × 2 windows = **24 category-windows**
- 24 × $68k = **+$1.63m gross margin per year**
- Apply the coverage haircut (83%, since 17% of value routes to manual and gets no help) → **≈ $1.35m per year**

**Against a fee of $300k per year** ($120k platform + $15k × 12 categories), the retailer keeps roughly **78% of the value created**, and the product is priced at about **22% of conservatively estimated benefit**.

**The honesty checks that matter more than the headline:**

- If assumption 6 lands at +3% instead of +9.4%, annual value falls to about **$430k** and the fee has to drop below roughly $130k for the deal to make sense. **The product is still viable, but only just, and only as a platform-fee-only offering.**
- If assumption 6 lands below +2%, the value case does not clear the retailer's cost of change. **That is a stop, not a discount.**
- Phase 1 tests assumption 6 before a single line of interface is built. That sequencing is the whole point of the roadmap.

---

## 3. Cost drivers, and the thing that is counter-intuitive here

| Cost | Type | Annual, per banner | Note |
|---|---|---|---|
| **Model inference** | Variable | **< $500** | ~1.15m scores per year (24 category-windows × ~300 items × 5 clusters × 4 price points × 8 weekly re-scores). A gradient-boosted tree scores these in batch in minutes |
| Data pipeline, storage, hosting | Semi-variable | ~$18k | Scales with catalogue size, not with usage intensity |
| Model monitoring, retraining, drift review | Fixed per customer | ~$35k | ≈0.25 FTE data scientist. Rises with clusters and categories, not with decisions |
| Customer success and planner support | Fixed per customer | ~$45k | ≈0.3 FTE. Highest in year one, falls thereafter |
| Onboarding: clustering setup, integration, training | One-off, amortised | ~$30k | ~$90k spread over an assumed 3-year term |
| **Total cost to serve** | | **≈ $128k** | |

At $300k ARR, **gross margin per banner is roughly 57%**, improving with each additional customer as core R&D is spread wider and onboarding gets faster.

### The insight this table exists to make

**Inference is not the cost driver. Humans are.** Compute is under 0.5% of cost to serve; monitoring, support and onboarding are over 85% of it.

This is the opposite of the economics in the generative-AI products the course examines, and it changes three decisions:

1. **Usage-based pricing is not just unhelpful, it is unfounded.** There is no meaningful marginal cost to pass on.
2. **The scaling problem is an operations problem, not an infrastructure one.** Adding a customer means adding onboarding and monitoring capacity, not GPUs.
3. **Making the model bigger is a bad instinct.** More compute buys very little here; better attribute data and better clustering buy a lot. Spend goes to data quality, not model size.

**Where inference cost *would* become material:** if the LLM explanation layer moves from templates to per-item generation. At ~1.15m items scored per year, generating a language explanation for each would move inference from three figures to five or six. This is a specific reason the explanation layer stays template-based in the MVP, and it is a cost decision as much as a trust one.

---

## 4. Unit economics view

| Metric | Value | Reasoning |
|---|---|---|
| ARR per banner | $300k | $120k platform + 12 × $15k per category |
| Cost to serve | $128k | Above |
| **Gross margin** | **~57%** | Below best-in-class SaaS because of the human onboarding and monitoring load. Honest for a vertical AI product with a services tail |
| Customer value delivered | ~$1.35m/yr | Conservative, coverage-adjusted |
| **Value capture ratio** | **~22%** | Deliberately well under a third. A retailer needs a visible majority of the benefit to defend the spend internally |
| Onboarding cost (one-off) | ~$90k | Clustering, integration, training. **The main barrier to a fast second customer** |
| Payback on onboarding | ~7 months | At $300k ARR against $90k plus first-year run cost |

**The number that decides whether this is a company or a consultancy is onboarding cost.** At $90k per customer it is a services business with software attached. The core-versus-customisation split exists precisely to drive that number down: every retailer-specific behaviour lives in configuration, never in code. If onboarding cannot be brought toward $30k by the third customer, the model does not scale and the honest conclusion is to sell it as a managed service rather than a platform.

**Contribution margin improves with categories, not with usage.** A retailer expanding from 4 to 12 categories adds $120k of revenue against near-zero marginal cost, since the clusters, integration and pipeline already exist. Land-and-expand within a banner is far more profitable than a new banner, which shapes the go-to-market: **win one banner, prove one category, expand inside the account.**

---

## 5. Scaling constraints

| Constraint | Assessment | Why |
|---|---|---|
| **Latency** | **Not a constraint** | The decision cadence is weekly. Nothing is real-time. Scoring runs overnight in batch, and a planner opening the worklist reads pre-computed results. This is a genuine architectural advantage and it should be stated rather than assumed |
| **Cost growth** | **Not a constraint** | Inference is trivial. Costs scale with customers and categories, both of which are priced |
| **Data onboarding** | **The real constraint** | Every retailer has a different product master, a different markdown history format, and different definitions of "terminal". This is where onboarding time goes, and it resists automation because the messiness is genuinely idiosyncratic |
| **Store clustering quality** | **Constraint at the edges** | Clusters below ~20 stores have no statistical basis, so long-tail formats stay permanently low-confidence. A retailer with many small, distinct formats gets structurally less value, and that should be said in the sales conversation, not discovered in the pilot |
| **Model maintenance across customers** | **Constraint from customer 3 onward** | One model per retailer is simple but does not scale; a shared model needs pooled data that retailers will not share. The likely path is a shared architecture with per-retailer training, which multiplies monitoring load linearly. **This is the operational cliff, and it arrives at roughly five customers** |
| **Governance burden** | **Grows faster than revenue** | Each customer brings its own approval chain, audit expectations and, in some markets, pricing-fairness scrutiny. Reviews, documentation and model cards scale per customer with no revenue attached |
| **Workflow variation** | **Underestimated** | Approval thresholds and who signs off on a markdown differ by retailer. Configuration absorbs some of this; some of it becomes feature requests that risk turning the product back into a project |

---

## 6. Operations implications

**What the business must be able to do, that is not building the model:**

1. **Run a repeatable onboarding.** Data mapping, cluster definition, backtest, planner training. It has to become a documented four-week process, not a bespoke engagement, or the cliff at customer five arrives early.
2. **Monitor every customer's model every season.** Drift is not optional here. Demand patterns shift, and after season one the model observes outcomes of prices it influenced. Holdout categories must be maintained per customer, which costs the retailer a little margin and has to be sold, not assumed.
3. **Run incident review on high-confidence failures.** Every one gets a post-mortem, individually. This is a standing operational commitment, and it is the reason the guardrail metric is counted rather than averaged.
4. **Support planners, not just admins.** Adoption fails at the planner's desk. Year-one support load is heavy and should be resourced as product work rather than treated as a cost to minimise.
5. **Hold the configuration line.** The single biggest operational risk to the business model is agreeing to code a retailer-specific behaviour. Every such request has to be answerable with configuration or refused.

---

## 7. Case lens: OpenAI pricing and scaling

**What transfers.** The course case makes cost-per-inference and its relationship to pricing a first-class product concern rather than an engineering detail. Working the equivalent calculation here is what surfaced the finding that inference is under 0.5% of cost to serve, and that finding is what killed per-recommendation pricing, which had been the intuitive default.

**What is different.** For a general-purpose LLM API, inference cost *is* the unit economics, so usage-based pricing directly reflects cost and scaling is an infrastructure problem. ClearSight inverts both. Its marginal cost is human, so its pricing has to be human-shaped, and its scaling limit is onboarding throughput rather than compute.

**What we do differently.** We priced against value with a fixed structure and made a deliberate decision *not* to meter the product, having checked that there was no marginal cost worth metering. The lesson taken from the case is the method, meaning work the unit economics before choosing the pricing model, rather than the conclusion, which does not transfer.

---

*ClearSight | MBA ZG583, Management of AI Products | Capstone. All figures synthetic or illustrative.*
