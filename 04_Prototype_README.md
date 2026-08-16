# ClearSight prototype and MVP

## Prototype type

An interactive browser prototype in a single self-contained HTML file. There is no build step, no
dependency and no install: it opens in any browser. Seven annotated wireframes in
`Appendix/Wireframes/` cover the screens outside the MVP walkthrough, including the Phase 1
backtest gate and the per-retailer configuration layer.

## What it demonstrates

The complete planner decision loop, end to end.

1. **Exit worklist.** 184 items ranked by value at stake, totalling $1.2m, with 31 routed to manual. Recommendation and confidence appear per row before the planner clicks into anything, because the ranking is where most of the value sits.
2. **High-confidence item detail.** The recommendation stated as an action, projected sell-through against the current flat schedule, four options compared, sell-through by store cluster, and a plain-language explanation with the largest driver named. Three actions of equal visual weight.
3. **Low-confidence abstain.** The same layout when the model has no signal, with no recommendation at all, three specific reasons why confidence is low, a statement of what would improve it, and all the underlying evidence still shown.
4. **Adjust and reject with reason capture.** The override path, with the reason picker that makes human-in-the-loop real rather than nominal.
5. **Session decision log.** Every decision captured as it is made, showing what the audit trail would actually contain.

## Where the AI sits

The AI creates value at exactly one point: forecasting residual sell-through per store cluster at
candidate price points, which produces the ranking, the option comparison and the confidence
signal. Everything else in the interface is conventional software built around that estimate.

The prototype runs on fixed synthetic data rather than a live model. That is deliberate, because
what needs testing at this stage is whether the workflow and the trust model hold, not whether the
forecast is accurate. Forecast accuracy is what the Phase 1 backtest tests, and it is tested first.

## What it does not yet prove

- **Not forecast accuracy.** The numbers are synthetic, and Phase 1 tests this.
- **Not enterprise integration.** There is no POS or ERP connection, deliberately deferred to Phase 4.
- **Not cross-category generalisation.** One category, one exit window.
- **Not confidence calibration.** The High, Medium and Low bands are illustrative, and whether they would be calibrated in practice is the most important open technical question in the project.
- **Not adoption.** Whether a planner under time pressure genuinely reads the reasoning rather than clicking Approve is what Phase 2 shadow mode exists to find out, and no prototype can answer it.

## Why the scope is narrow

The prototype covers screens 1 to 4 and omits 5 to 7, which is a product decision rather than a
scoping shortcut. Screens 1 to 4 are the decision loop, and if the trust model fails there then
nothing downstream matters. Screen 6, the backtest gate, belongs to Phase 1 and has no planner
interface at all. Screen 5 needs live decisions before it has any content. Screen 7 runs once per
retailer at onboarding. The real risk is trust and workflow fit, and it lives entirely in the four
screens that were built.

## How to run it

```
git clone https://github.com/BITS-WILP-MAIP/2024bC26584-fiza-tahreen.git
cd 2024bC26584-fiza-tahreen
open ClearSight_Prototype.html
```

No server, no npm, no Python. It is one file, and double-clicking it works.

Suggested walkthrough: open the worklist, click *Kids' rash vest, 5 colours* for the
high-confidence case, read the explanation and the options compared, go back, click *Cooler bag
24L* for the low-confidence case where no recommendation is offered, go back, then open any item
and choose **Adjust** or **Reject** to see reason capture. The decision log is in the top right.

## Links

- Repository: https://github.com/BITS-WILP-MAIP/2024bC26584-fiza-tahreen
- Prototype file: [`ClearSight_Prototype.html`](ClearSight_Prototype.html)
- Wireframes: [`Appendix/Wireframes/`](Appendix/Wireframes/), screens 01 to 07 plus a grouped overview
- Screenshots: [`Appendix/Screenshots_or_Video/`](Appendix/Screenshots_or_Video/)

## Data note

Prototype and evaluation use synthetic or public retail data only. No confidential employer data.
