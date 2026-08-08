# ClearSight, Prototype / MVP

## Prototype type
A functional, self-contained clickable prototype (single HTML file, no install, works offline). It implements Screens 1 to 4 of the MVP: the planner decision loop.

## How to run it
Open `ClearSight_Prototype.html` in any browser (double-click it). No server or dependencies.
For a live link, rename it to `index.html` and enable GitHub Pages on the repo, or drop it in a `/docs` folder and point Pages there.

## What it demonstrates
The core loop, end to end:

- **Worklist** ranked by value at stake, with live counts (items at risk, value at stake, routed to manual, reviewed today) that update as you decide.
- **Item detail, high and medium confidence** — the recommendation stated as an action, its confidence, the reasons in the planner's own vocabulary, a per-cluster sell-through breakdown, and the alternatives compared. Three equally weighted actions: Approve, Adjust, Reject.
- **Adjust** — change depth and timing and see the projected sell-through move against the recommendation, then capture a reason code.
- **Reject** — capture a reason code (with optional free text).
- **Low confidence (abstention)** — the product shows no recommendation, states why confidence is low, still shows the evidence, and routes to a manual decision. This is the screen the whole trust design rests on.
- **Decision log** — every action this session is recorded with its reason.
- **Design notes toggle** (footer) — reveals the reasoning behind specific choices, mirroring the wireframe annotations.

## AI role
Forecast residual sell-through per store cluster at candidate price points, then rank and recommend. It does not set or change prices, and where it lacks signal it abstains.

## What it does not yet prove
Production forecast accuracy, enterprise integration, and cross-category generalisation. Data is synthetic and internally consistent, chosen to make the workflow and the trust model tangible. The Phase 1 backtest is what tests accuracy; this prototype tests the workflow.

## Consistency note
The prototype is the single source of truth for the numbers: five named store clusters throughout (matching the configuration screen), the worklist counted in stores rather than impossible cluster counts, five reason codes, and low-confidence signals that fit five clusters. Align the static wireframes and deck to it.

## Files
- Prototype: `ClearSight_Prototype.html`
- Wireframes (design source): `Appendix/Wireframes/01_exit_worklist.svg` … `07_store_cluster_config.svg`
