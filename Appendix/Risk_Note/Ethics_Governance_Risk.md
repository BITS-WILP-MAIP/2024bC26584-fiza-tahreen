# Ethics, Governance and Risk Note: ClearSight

*What could go wrong, who it would hurt, what stops it, and what would stop the launch.*

---

## Framing

ClearSight is not a high-risk AI system in the regulatory sense. It does not make decisions about people, it does not touch consumer data, and a human approves every action. It would be easy to write a short, comfortable risk note on that basis.

That would miss the two risks that actually matter. One is technical and obvious in hindsight: **a confidently wrong recommendation, trusted and acted on at scale.** The other is structural and easy to overlook: **the store-clustering layer, which is where a pricing product quietly becomes a product that prices by neighbourhood.**

The rest of this note is organised around those two, with the remaining risks treated properly but more briefly.

---

## 1. Main risks

| # | Risk | Severity | Likelihood without controls | Type |
|---|---|---|---|---|
| **R1** | **Confidently wrong recommendation acted on at scale** | **Critical, project-ending** | Medium | Model / trust |
| **R2** | **Cluster bias: demographic proxies drive systematically different pricing by catchment** | **High, reputational and regulatory** | **Medium-high, and it is silent** | Fairness |
| R3 | Planners rubber-stamping, making human-in-the-loop nominal | High | High | Workflow |
| R4 | Feedback loops: the model retrains on outcomes of prices it influenced | Medium, compounding | High if unmanaged | Model lifecycle |
| R5 | Confident extrapolation beyond the data (the non-LLM analogue of hallucination) | Medium | Medium | Model |
| R6 | Scope creep from markdown into dynamic or full-price setting | **High** | Medium | Misuse |
| R7 | Override logs used to performance-manage planners | Medium | Medium | Privacy / workplace |
| R8 | Attribute and cost data quality failures producing silently mis-ranked worklists | Medium | Medium | Data |

---

## 2. R2 in full: bias, and why a pricing product has a fairness problem

This is the risk the first draft of this project missed entirely, and it is worth setting out properly.

### The mechanism

ClearSight does not price by customer. It prices by **store cluster**. Clusters are built from climate zone, store format and size, historical seasonal curve, local competitor density, and catchment demographics. The last of those is switched on for the reference retailer because it materially improves forecast quality.

Catchment demographics is a proxy for income. So the causal path is short and entirely unintentional:

> catchment income → cluster assignment → forecast of price elasticity → recommended discount depth and timing → **different prices, by neighbourhood income, for the same item**

No protected attribute is used as a model input. No one decides to price by income. The outcome arrives anyway, through a variable that was included because it is genuinely predictive.

### What the harm actually looks like

It runs in **both directions**, and both are defensible-sounding from inside the business:

- **Shallower or later discounts in lower-income catchments.** If those stores show slower sell-through, the model may recommend holding price longer to avoid clearing at cost. The result: customers in poorer areas wait longer for markdowns and see less choice by the time prices fall. The optimisation is locally rational and the distributional outcome is regressive.
- **Deeper, earlier discounts in lower-income catchments.** If those stores show high price elasticity, the model recommends cutting harder there. This looks pro-consumer and reads very differently in a newspaper: *"retailer's AI routes deepest discounts on leftover stock to poorest neighbourhoods."* It also entrenches a two-tier assortment where some catchments systematically receive end-of-line stock at clearance while others do not.

Either pattern would emerge gradually, would be invisible in the aggregate margin metrics that the product reports, and would be **entirely rational from the model's point of view.** That combination of invisible, gradual and locally optimal is what makes it dangerous.

### Who could be harmed

| Who | How |
|---|---|
| **Shoppers in specific catchments** | Systematically different access to discounted stock, correlated with income, without anyone having chosen that |
| **The retailer** | Reputational damage disproportionate to the margin gained, plus pricing-fairness scrutiny in markets where it is regulated |
| **Planners** | Asked to defend a pattern they did not design and cannot see from inside a single item's recommendation |
| **Private-label suppliers** | Downstream: consistently deeper markdowns on a supplier's lines shape next season's buy and squeeze that supplier, based on a model output no one audited for it |

### Controls for R2

1. **Disparity testing as a standing metric, not a one-off audit.** Every backtest and every pilot cycle reports recommended discount depth and timing, aggregated by catchment income decile. If a monotonic relationship with income appears in either direction, it goes to review before the cycle proceeds. **This runs from Phase 1, on the backtest, before any planner ever sees a recommendation.**
2. **Demographics is a switchable input with the switch visible.** The configuration screen shows which clustering inputs are active. A retailer can run without catchment demographics and see the forecast-quality cost of that choice explicitly. Making the trade-off visible is the point: it turns an invisible default into a decision someone owns.
3. **Cluster composition is reviewed by a human at onboarding**, and reviewed for demographic concentration specifically rather than only for statistical validity. A cluster that turns out to be "the low-income stores" under a geographic name is caught here.
4. **A stated ceiling on cluster-to-cluster divergence.** Where recommended depth for the same item differs across clusters by more than a configured threshold, the item escalates for review rather than being recommended. This costs some optimisation and buys a hard limit on how far apart neighbourhood pricing can drift.
5. **The disparity report goes to the retailer, not just to us.** The retailer owns pricing policy and must be able to see this pattern in its own governance forum.

### The honest limitation

None of this makes the product fair in an absolute sense. Store-level pricing variation already exists in retail, and ClearSight does not create it. What it does is make that variation systematic, faster, and traceable to a model. That is a real change in kind, not just degree. **Traceability cuts both ways: it is easier to audit, and it is easier to subpoena.** The right posture is to measure the disparity, show it to the customer, and let a human own the policy, rather than to claim the problem has been designed away.

---

## 3. Privacy

Privacy exposure is genuinely low, and it is worth being precise about why rather than waving it through.

**What the system does not touch:** no customer identifiers, no transaction-level customer data, no loyalty data, no payment data. ClearSight operates on aggregate units sold by item, store and week. A basket is never linked to a person. This is a deliberate scope boundary and it should stay one.

**The two exposures that do exist:**

1. **Catchment demographic data is about areas rather than individuals**, which reduces the privacy question without removing it. Fine-grained catchment data at small geographies can approach identifiability in sparse areas, and it carries third-party licensing terms that constrain how derived outputs may be used and shared. Control: use the coarsest geography that preserves forecast quality; hold licence terms in the data register; never persist catchment attributes at store-record level beyond the cluster assignment they produce.

2. **The override log is a record of employee judgement.** Every decision, every override, every reason code, attributed to a named planner and time-stamped. This is the most personally sensitive data in the product, and it is generated by the product itself. It is easy to miss because it does not look like personal data, but it is a detailed longitudinal record of how well one identifiable person does their job. Controls are in R7 below, because the real risk there is misuse rather than exposure.

**Data handling.** Retailer data stays in the retailer's tenancy; no pooling across customers without an explicit, separately negotiated agreement. Retention on the decision log is configurable and defaults to three seasons, which is what evaluation needs and no more.

---

## 4. R5: unsafe output, and why "hallucination" is the wrong word here

The MVP uses a gradient-boosted tree, not a language model, so hallucination in the LLM sense does not apply. The structurally equivalent failure is **confident extrapolation**: producing a plausible-looking forecast for an item the model has no real basis for, because the model has no native notion of "I have not seen anything like this."

Controls: confidence is derived from prediction-interval spread, analogue count and cross-cluster agreement, so thin evidence produces low confidence by construction; below threshold the product shows **no recommendation at all** rather than a hedged one; items with no usable analogue are flagged as new lines rather than scored; and stale or missing data marks an item not assessable rather than falling back on an old number.

**Where genuine hallucination risk enters:** the LLM explanation layer, if it is ever built. A fluent, confident, wrong explanation attached to a correct number is bad; attached to a wrong number it is much worse, because it manufactures exactly the trust the confidence design is trying to calibrate. This is a specific reason the explanation bullets are template-generated from the model's own drivers in the MVP. If the layer is ever added, it must be constrained to the model's actual drivers, evaluated for faithfulness rather than fluency, and shipped behind the same confidence gate as the recommendation itself.

---

## 5. Misuse risks

| Risk | Scenario | Control |
|---|---|---|
| **R6: scope creep into dynamic pricing** | The clustering and elasticity machinery is one product decision away from setting full-price by neighbourhood, or from in-season dynamic pricing. It would be commercially tempting and technically straightforward | **A stated product boundary, written into the PRD as out of scope and repeated here: ClearSight applies to exit and markdown decisions only.** Extending it is a new product with a new risk assessment, not a feature. The R2 disparity concern becomes far more acute at full price, where the customer-facing effect is continuous rather than confined to end-of-season stock |
| **Post-hoc justification** | A planner decides first, then finds the recommendation that agrees and cites the tool as evidence. The audit trail then launders a judgement call as an analytical one | Decisions are logged with the recommendation *as presented*, before the action. Override reasons are captured at the moment of override. Acceptance patterns that look like selective citation surface in the reason distribution |
| **R7: surveillance of planners** | Override logs become a performance-management instrument. Planners then override less to protect themselves, acceptance rate rises, and the human-in-the-loop control quietly becomes nominal, destroying the thing the governance case depends on | **Override data is reported in aggregate for product and model evaluation, not per planner for performance review.** This is written into the customer contract, not left to convention. Individual-level access is restricted and logged. Planners are told what is recorded and what it is used for. *This control depends on the customer honouring it, which is a real limitation and should be stated in the sales process rather than glossed* |
| **Supplier pressure** | Markdown patterns are used to squeeze private-label suppliers on terms, using model output as leverage | Out of the product's control, but flagged to the customer at onboarding as a foreseeable secondary use of the outputs |

---

## 6. R1, R3, R4, R8: the operational risk set

| Risk | Control |
|---|---|
| **R1 Confidently wrong at scale** | Confidence thresholds with mandatory manual routing below them; **no automatic price execution, ever**; second approver required above a configurable value-at-stake threshold; individual post-mortem on every high-confidence failure; the standing launch blocker in §8 |
| **R3 Rubber-stamping** | Acceptance rate monitored with a **ceiling (85%), not a target**; Approve is not visually dominant over Adjust and Reject; overriding costs no more clicks than agreeing; no bulk approve in the MVP, deliberately, because batch approval and meaningful review are in direct tension; planner-reported confidence surveyed each cycle to catch quiet disengagement |
| **R4 Feedback loops** | Holdout categories run on the flat rule for full seasons and are maintained permanently, not just at pilot; drift monitoring from Phase 3; retraining sets record which observations came from ClearSight-influenced prices; periodic re-benchmark against the *original* flat-rule baseline, not against the model's own previous version, so the bar cannot drift upward with the model |
| **R8 Data quality** | Coverage and analogue-match rate monitored as leading indicators; cost-data validation at ingest, because a wrong cost silently mis-ranks the entire worklist and produces no visible error; degradation routes items to manual rather than lowering recommendation quality |

---

## 7. Governance

**Decision rights.** ClearSight recommends. The planner decides. The retailer owns pricing policy, cluster configuration, and the approval chain. Nothing in the product changes a price.

**Audit trail.** Every recommendation as presented, every decision, every override with reason and timestamp, every configuration change, and the model version in force. This is what makes a markdown defensible upward and what makes a post-mortem possible.

**Review cadence.**

| Frequency | Review | Owner |
|---|---|---|
| Weekly, during a window | Coverage, override reasons, any high-confidence failure | Product + planner lead |
| Per exit window | Margin vs control, acceptance rate against the band, **catchment-disparity report** | Product + retailer trading |
| Per season | Drift, holdout comparison, retraining decision, model card update | Data + product |
| Annual | Full model review, cluster composition re-examined for demographic concentration, scope boundary reaffirmed | Retailer governance forum |

**Documentation.** A model card per customer covering training data and seasons used, excluded seasons and why, cluster definitions and active inputs, calibration by confidence band, known failure modes, and disparity test results. Written to be read by the retailer's governance function, not only by us.

**Escalation.** A high-confidence failure on a high-value item goes to post-mortem within the cycle, not at season end. Its findings can hold the next phase gate. A confirmed disparity pattern goes to the retailer's governance forum, not to a product backlog.

---

## 8. Launch blockers

**These stop a launch. They are not risks to be weighed against benefits.**

1. **Expected loss from acting on high-confidence wrong recommendations exceeds expected gain from the rest.** The standing blocker. It applies permanently, at every phase, not only at gates.
2. **The confidence signal is not calibrated.** If realised error in the High band is not materially below Medium, the entire trust design is decorative and nothing else in this document holds.
3. **A material catchment-disparity pattern with no explanation and no remediation.** Monitoring it in pilot is not an acceptable answer: either the pattern is explained and bounded, or the demographic input comes out and the forecast-quality cost is accepted.
4. **Any high-confidence failure on a high-value item that the post-mortem cannot explain.** One is enough. An unexplained failure means the confidence signal is not understood, which is blocker 2 arriving through a different door.
5. **Override reasons are systematically blank or uniform in shadow mode.** That means the human-in-the-loop control is not real, and the governance claim in this document would be false.
6. **The customer will not accept the override-data restriction in §5 (R7).** If planner-level override data is going to be used for performance management, the control that protects review quality does not exist and the rest of the governance case collapses.

**And the quieter one:** if the Phase 1 backtest cannot beat analogue matching by +5%, the project ends. That is a project blocker rather than a launch blocker, and it is listed here because it is the cheapest of all these findings to discover, which is why the roadmap is sequenced to find it first.

---

## 9. Case lens: bias in lending / the Apple Card

**What transfers.** The Apple Card episode is the cleanest illustration of the failure mode in R2: no protected attribute was used as a model input, the outcomes diverged along one anyway through correlated proxies, and the organisation could not explain its own decisions when asked. The absence of a protected variable from the feature list turned out to be no defence at all, technically or reputationally. ClearSight's catchment-demographics input is structurally the same exposure, and recognising that is the reason disparity testing runs from the backtest rather than from the pilot.

**What is different.** ClearSight prices *stock in stores*, not credit for individuals. There is no individual adverse decision, no legal right to an explanation, and the harm is diffuse and statistical rather than concentrated on one person who can point at it. That makes it both less severe and, in one specific way, harder to catch: nobody complains, because nobody experiences a decision that was made about them.

**What we do differently.** We test for the disparity we cannot be sued over. Aggregate disparity reporting by catchment income runs as a standing metric from Phase 1, the demographic input is switchable with its cost made visible, and the report goes to the retailer's governance forum rather than staying in a model report. The lesson taken from the case is that "we did not use that variable" is not a control, and that measuring the outcome is.

---

*ClearSight | MBA ZG583, Management of AI Products | Capstone. All figures synthetic or illustrative.*
