# UX, Workflow and Trust Design: ClearSight

*How the product behaves when it is confident, when it is not, and when it is wrong.*

> An interactive walkthrough of screens 1–4 is in `ClearSight_Prototype.html`. **All figures synthetic or illustrative.**

---

## Design principle

ClearSight is a review assistant, not a controller. Every design decision follows from one observation about the user: Priya is accountable for markdowns she takes, and one confidently wrong recommendation that she followed and had to explain upward would end her trust in the tool permanently.

That makes this a trust problem before it is a forecasting problem. Three rules follow:

1. **Overriding must be as easy as agreeing.** If rejecting costs more clicks than approving, the review is nominal and acceptance rates become meaningless.
2. **Silence is a valid output.** Where the model has no signal, it says so rather than producing a low-quality number that looks like every other number.
3. **Explanations use the planner's vocabulary,** not the model's. Cluster sell-through gaps and analogue lines, not feature importances.

---

## Main workflow

```
Exit worklist                Item detail                  Decision
─────────────                ───────────                  ────────
Items ranked by       →      Recommendation          →    Approve  (1 click)
value at stake               Confidence level             Adjust   (+ reason)
                             Why, in plain terms         Reject   (+ reason)
                             Options compared
                                                          ↓
Low confidence?       →      No recommendation.      →    Manual decision,
                             Reason for low                logged, outcome
                             confidence stated.            still tracked
```

The planner enters at the worklist, works down it, and leaves. Most of the value is in the ordering: attention is the scarce resource, so items are ranked by value at stake rather than by discount size or alphabetically.

---

## Key screens

| # | Screen | Purpose | File |
|---|---|---|---|
| 1 | Exit worklist | What needs a decision, ordered by what it costs to get wrong | `01_exit_worklist.svg` |
| 2 | Item detail, high confidence | The decision: recommendation, why, alternatives, three actions | `02_item_detail_high_confidence.svg` |
| 3 | Item detail, low confidence | What the product does when it does not know | `03_low_confidence_abstain.svg` |
| 4 | Adjust and reason capture | The override path, where human-in-the-loop actually lives | `04_adjust_and_reason_capture.svg` |
| 5 | Outcome tracking | Whether it is working, including the metrics designed to expose failure | `05_outcome_tracking.svg` |
| 6 | Backtest evidence (Phase 1) | The go/no-go gate, internal, no planner interface | `06_backtest_gate.svg` |
| 7 | Store cluster configuration | The customisation layer, what changes per retailer | `07_store_cluster_config.svg` |

All files are in `Appendix/Wireframes/`. Annotations in the margin of each screen record the reasoning behind specific choices.

**Screens 1–4 are the MVP.** Screen 6 exists before any interface is built, it is the evidence that decides whether screens 1–4 are worth building at all.

**Screen 5 is Phase 3, not Phase 2.** In shadow mode decisions are not applied, so there is no margin recovered and no control comparison to show. A reduced Phase 2 version of this screen shows engagement only: how many recommendations were reviewed, override rate, and the distribution of override reasons. Margin, holdout comparison, and high-confidence error counts appear only once decisions are live.

**Screen 7 runs once per retailer at onboarding.** The MVP does not include this interface, Phase 1 and 2 use a fixed cluster definition set up directly. The configuration screen is P1, built when a second retailer makes hand-configuration untenable.

### Why each screen earns its place

- **1 and 2** are the core loop. Without them there is no product.
- **3** is the trust design made concrete. Most AI products degrade into worse predictions; this one degrades into silence.
- **4** matters because the override is the control the whole governance claim rests on. Showing Approve without showing what rejecting actually costs the planner would be hiding the important half.
- **5** is where the acceptance-rate ceiling and holdout categories stop being claims in a document and become something a planner sees weekly.
- **6** makes the Phase 1 gate visible, including a "where it failed" panel. A backtest that reports only wins is marketing, not evidence.
- **7** is the visual answer to *"how is this a product and not a project for your employer?"*, everything on it is configuration, and nothing on it is code.

---

## Trust and explainability

**What the planner must be able to see**

- The recommended action stated as an action ("cut 25% this week"), not as a probability she has to interpret
- The projected outcome, and the projected outcome of the current flat rule, so the comparison is explicit rather than implied
- The alternatives considered and their projected sell-through. Showing only the chosen option converts a recommendation into an instruction
- Why: three or four reasons in domain language, with the largest driver named
- What evidence the recommendation rests on, how many analogue lines, how many prior seasons

**What is deliberately not shown**

- Model internals, feature weights, architecture. These do not help the planner decide and create false confidence in people who cannot evaluate them
- Precision beyond what the model supports. Sell-through is shown to the nearest percent, not two decimal places
- A confidence score as a raw number. "High / Medium / Low" with the supporting evidence is more honest than "0.87", which invites over-interpretation

**Why this split:** the test for including something is whether it changes what a planner would do. Feature weights do not. Analogue count does, it tells her whether the model has seen anything like this item before.

---

## Uncertainty handling

| State | System behaviour | Planner experience |
|---|---|---|
| **High confidence** | Full recommendation with depth, timing, projected outcome, and comparison to baseline | One-click approve; override available at equal cost |
| **Medium confidence** | Recommendation shown, flagged as medium, with the specific source of uncertainty named (e.g. "conflicting cluster signals") | Encouraged to review the reasoning before acting |
| **Low confidence** | **No recommendation at all.** Item routed to manual decision. Reason for low confidence stated. Underlying data still shown | Makes the call herself, with evidence but no suggested answer |
| **Failure, data missing or stale** | Item marked as not assessable, with what is missing named. No fallback to a stale recommendation | Sees the gap explicitly rather than acting on an outdated number |

**The abstention design is the point.** Most AI products degrade gracefully into worse predictions. ClearSight degrades into silence. This costs coverage (some items get no help), but it protects the one thing the product cannot recover from: a confident recommendation that should not have been trusted.

Naming *why* confidence is low does double duty. It tells the planner what to look at, and over time it teaches her when the model is reliable elsewhere, which is what calibrated trust actually means.

---

## Human-in-the-loop

**Where review is mandatory**

- Every recommendation, without exception. ClearSight never changes a price
- Items above a value-at-stake threshold require a second approver, configurable per retailer. *Not designed yet, this is a stated requirement, not a screen. It is deferred to Phase 3, when decisions become live and the approval chain matters*
- All low-confidence items, by routing rather than by policy

**Override and reason capture**

Adjust and Reject both open a reason picker with predefined codes plus free text: competitor activity, range repeating next season, store feedback, stock quality issue, other.

Reason capture serves three purposes at once:

1. **Evaluation**: override reasons reveal systematic model failures that aggregate metrics hide. Fifteen rejections all citing "competitor activity" is a missing feature, not fifteen unrelated disagreements
2. **Governance**: an audit trail of who decided what and why, which is what makes the markdown defensible upward
3. **Product signal**: clustering reasons over a season shows where the model needs work

**Guarding against rubber-stamping**

Acceptance rate is monitored with a ceiling, not a target. Sustained acceptance approaching 100% triggers review of whether planners are still engaging, because at that point the human-in-the-loop control exists on paper only. A deliberate design consequence: the Approve button is not visually dominant over Adjust and Reject.

---

## What this design does not yet resolve

- **Bulk actions.** With 184 items in a worklist, planners will want to approve in batches. Batch approval and meaningful review are in direct tension; the MVP has no bulk action, and Phase 2 shadow mode is where this gets tested honestly
- **Notification and cadence.** Whether ClearSight pushes alerts as items become at-risk, or waits to be opened weekly, is unresolved and should follow observed planner behaviour rather than a design assumption
- **Mobile.** Store and area managers may want visibility. Out of scope for MVP; the decision belongs to the planner at a desk

---

## Case lens

**Google SGE / assistant UX:** the transferable lesson is that surfacing uncertainty is a product feature rather than an admission of weakness, and that an assistant which occasionally declines is trusted more than one which always answers. **What ClearSight does differently:** the stakes are irreversible. A markdown, once taken, cannot be untaken, so abstention is not just good manners, it is a control.
