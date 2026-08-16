# AI collaboration log

This log records how AI tools were used to produce the ClearSight capstone submission, where they helped, where they were wrong, and what judgement was applied to their output. It is written in the first person because the submission is individual.

# 1. Tools used

| Tool | What it was used for |
|---|---|
| Claude (Anthropic) | Primary tool. Problem framing and pressure-testing, drafting and restructuring the PM artifacts, generating the wireframe SVGs, drafting the deck and presenter script, and a full cross-artifact consistency review near the end |
| ChatGPT (OpenAI) | Second opinion on framing decisions, alternative phrasings, and sanity-checking the economics arithmetic against a separate model rather than trusting one tool's numbers |
| Coding assistant, Copilot and Cursor class | Building the interactive HTML prototype: layout scaffolding, CSS, and the state-handling JavaScript for the worklist, detail and decision flow |
| Spreadsheet, manual and with no AI involvement | The value assumptions and the unit-economics chain. This arithmetic was kept in a sheet I controlled so that every number could be traced to an assumption and challenged line by line |

*Table 1. AI tools used, and the work each was used for.*

The last row is deliberate. The economics section is the part of this submission most likely to contain a plausible-sounding fabricated number, so it is the part I refused to generate.

# 2. Where AI helped

**Discovery and framing.** Pressure-testing whether markdown timing was a genuine product opportunity or a one-retailer project, and sharpening the position that the product recommends while a person decides until it functioned as a design constraint rather than a slogan.

**Drafting.** First drafts of the executive summary, the PRD-lite and the UX and trust write-up. AI was fast at producing structure I could then cut down and correct. Roughly 60% of what it produced survived; the rest was either too generic or too optimistic.

**Wireframing.** Generating the seven screens as SVG starting points from a written description of each screen's purpose. This was the single biggest time saving in the project, since producing seven annotated screens by hand would have taken days.

**Prototype code.** I specified the screens, the states, the data and the interaction rules, and the assistant wrote most of the CSS and JavaScript. I reviewed and corrected the behaviour, particularly around making Approve, Adjust and Reject visually and interactionally equal, which the first version got wrong by styling Approve as the primary button.

**Metrics structure.** Proposing the three-layer split across technical, business and trust metrics. The acceptance-rate ceiling was the one genuinely good idea AI contributed that I would not have reached alone, and I tightened it into a stated band of 50% to 85%.

**Risk enumeration.** Listing failure modes, matching each to a control, and stress-testing the question of what would make me kill the project.

**Consistency review.** A late pass reading every screen, the deck and all documents together, looking for numbers and claims that disagreed with each other. It found more than I expected, and Section 4 records what.

# 3. Example prompts and workflows

**Prompt 1, framing.**

> Here is a markdown-timing problem in seasonal retail. Argue both sides of whether AI is genuinely the right lever, or whether a rule or a dashboard would do. Be concrete about what a rule cannot do.

I kept the structure of the argument and replaced the generic examples with private-label seasonality, which is the actual reason rules fail in this category. Asking for both sides rather than for a justification mattered: a prompt asking why AI is right for this problem would have produced a confident answer I could not have trusted.

**Prompt 2, artifact drafting.**

> Draft a PRD-lite for a markdown recommendation copilot. Treat confidence display, low-confidence routing and override capture as first-class requirements rather than polish. Include data assumptions and explicit fallback behaviour.

This gave me a usable PRD skeleton. I rewrote the data-assumptions and fallback sections entirely, because the first version assumed product attribute data would be clean, which is the least safe assumption available to anyone who has worked with a retail product master.

**Prompt 3, wireframe generation.**

> Produce a wireframe for the low-confidence state where the product shows no recommendation, states why confidence is low, and still shows the underlying evidence. Annotate the design reasoning in the margin.

This produced Screen 3, which I consider the core of the trust design. AI produced a strong first version that needed only light content edits, and it was the best single output of the project.

**Prompt 4, adversarial review.**

> Read all seven screens, the deck and every document together. List every place where a number, label or claim disagrees with another, ranked by how badly it would land in a review.

Framing the request as how badly something would land in a review, rather than as a request to check for errors, produced sharper output. This surfaced the inconsistencies in Section 4.

**Prompt 5, economics challenge, put to ChatGPT rather than Claude.**

> Here is my assumption chain for the value case. Attack it. Which link is weakest, and what is the smallest change that breaks the conclusion?

This is how I identified assumption 5, the share of full retail realised under the flat rule, as the softest link, and it is why the report states the +3% and +2% sensitivity cases rather than only the headline figure.

# 4. Where AI was weak or wrong

This section is longer than Section 2, which is the honest ratio.

**Inconsistent effect sizes across artifacts.** AI-generated screens stated ClearSight's benefit three different ways: a 9.4% backtest headline, an indexed bar chart on the same screen implying roughly 47%, and a margin-per-unit comparison on a different screen implying about 42%. Three numbers, three stories, and none of them flagged as being in tension with the others. In a review, someone would have spotted this on the slide and the credibility of every other number would have gone with it.

**A confounded comparison presented as clean.** The outcome-tracking screen compared margin per unit in the treated category against different holdout categories and attributed the whole gap to the product. Different categories have different margin structures, so it is not a like-for-like comparison. AI presented it as evidence. This one bothered me most, because it is the kind of error that looks like analysis.

**Fabricated but plausible numbers, unflagged.** Every figure AI produced, including sell-through percentages, cluster sizes, error counts and margin figures, was realistic and invented. They are perfectly acceptable as synthetic illustration, but AI did not label them as unvalidated, so I had to add the synthetic-data statements myself. This is the failure mode the assignment packet warns about, and it does not announce itself.

**Detail drift between artifacts.** The backtest screen referred to 3 of 14 clusters while the configuration screen defined 5. A reason code appeared on one screen and nowhere else. Small individually, corrosive in aggregate, and exactly what a careful reviewer picks up on.

**Claiming corrections that had not been made.** An earlier draft of this log stated that the comparison logic had been fixed and the numbers aligned. When I re-checked the artifacts, they had not been. The log described the intention rather than the state. I only caught it because I re-read the screens against the log rather than trusting the log, and the lesson is that an AI-drafted account of your own work is not evidence about your work. That is an uncomfortable thing to discover in a document whose entire purpose is disclosure.

**Generic first drafts.** The first persona was a planner with goals and no error cost. It became useful only once I added the asymmetric risk and the observation that one wrong recommendation would end her trust, which is the point the whole design rests on and which came from knowing the workflow rather than from the model.

**Over-confidence on the recommendation.** The first draft recommended piloting immediately. AI does not naturally price irreversibility. It took a deliberate prompt about what cannot be undone to reach the backtest-first plan, and that plan is the strongest product decision in the submission.

**House-style tells.** Drafts overused long dashes, ran to a uniform cadence, and reached repeatedly for words like robust and leverage. I revised the prose into my own voice, and removing the dashes properly meant rewriting the sentences rather than deleting the characters.

# 5. Human corrections

| What I changed | Why |
|---|---|
| Standardised on one benefit number, re-indexing the backtest chart so the flat rule is 100, analogue matching 104 and ClearSight 109.4 | Makes the chart agree with the 9.4% headline on the same screen. One defensible number beats three impressive ones |
| Removed the confounded comparison, replacing the cross-category margin-per-unit claim with a like-for-like statement against the matched control | The original could not be defended if questioned, and it was the most likely thing to be questioned |
| Added denominators, so that eleven high-confidence calls going wrong became eleven of 340 | A failure count without a denominator is not information. It reads as alarming or reassuring depending only on what the reader assumes |
| Reconciled cluster counts, so the backtest reports 1 of 5 clusters, matching the 5 the configuration screen defines | Removes the detail drift |
| Reconciled the pilot margin figure with the assumption chain, so that the dashboard number and the economics section derive from the same arithmetic | The two had been produced independently and did not agree. Deriving the smaller defensible number was better than keeping the impressive one |
| Labelled everything synthetic, adding an explicit data statement to every document | The packet requires not fabricating data. Synthetic illustration is legitimate; unlabelled synthetic illustration is not |
| Rewrote the value case as a numbered assumption chain with sensitivities | So that a reviewer can attack link 5 or link 6 specifically rather than dismissing the whole thing as invented |
| Added the bias analysis, which was missing from the risk note entirely until late | The most substantive gap AI did not surface on its own. Section 6 explains why |
| Made the recommendation more conservative, replacing pilot now with backtest first and a hard stop | A markdown, once taken, cannot be reversed |

*Table 2. Corrections applied to AI output, and the reasoning behind each.*

# 6. The one thing AI missed entirely

Every risk register AI produced covered confidently wrong recommendations, rubber-stamping and feedback loops, which are the three technical risks. None of them raised bias, across several attempts, until I asked directly whether a store-clustering product could have a fairness problem.

It is worth being precise about why that gap existed. The system does not use protected attributes, does not make decisions about people, and has no consumer-facing surface, so on every surface test it looks like a low-risk system. The exposure is one step removed: catchment demographics is a clustering input, cluster assignment drives the recommended discount, and the result is different prices by neighbourhood income for the same item. Nobody decides that. It emerges.

Once asked directly, AI developed the analysis well and found the Apple Card parallel quickly. But it did not know to ask the question, and neither would a risk note written by pattern-matching to what an AI risk section usually contains. That is the clearest illustration in this project of where human judgement has to sit: not in producing the analysis, but in knowing which analysis is missing.

# 7. What AI did not decide

Six decisions were mine, and none of them is a drafting task.

- **The opportunity choice.** Five candidates were considered. Dynamic pricing scored highest on value and was rejected on risk and evaluability, and that trade-off was a judgement rather than a calculation.
- **Keeping a human on every markdown.** The decisive constraint in the whole design.
- **The scope boundary against full-price and dynamic pricing.** A commercially expensive line to draw, drawn deliberately.
- **The pricing model.** Rejecting per-recommendation pricing was a judgement about behaviour, specifically that metering would suppress review, rather than an output of the cost analysis, although the cost analysis supported it.
- **The launch blockers.** Choosing what stops a launch is an accountability decision, and it cannot be delegated to a tool that bears no consequence for being wrong.
- **The recommendation.** Backtest first, hard gate, stop if it fails.

# 8. Final reflection

**Where AI accelerated the work.** Structure and speed, overwhelmingly. First drafts, seven annotated wireframes, a working interactive prototype, enumerated risk-and-control pairs, and a consistency review across a dozen artifacts that would have been slow and unreliable to do by hand. As a solo submission, this artifact set would not exist at this scope without it, and that is the honest headline rather than a hedge.

**Where AI could not replace PM reasoning.** Three places, and they turned out to be related.

The first is deciding what matters. AI is fluent about everything at once. It produced good material on metrics, risks and economics simultaneously, with no sense that the confidence-calibration metric is load-bearing while weighted MAPE is not. Weighting is judgement.

The second is pricing irreversibility. AI consistently under-weighted the fact that a markdown cannot be undone, and every draft leaned toward moving faster than the risk warranted. That single asymmetry, cheap to be slow and expensive to be wrong, is what shaped the roadmap, the abstention design and the launch blockers, and I had to keep reasserting it.

The third is knowing what is missing. The bias gap is the clean example. AI answers the question it is asked, comprehensively and confidently. It does not tell you that you are asking the wrong set of questions.

**The uncomfortable observation.** AI was most confident in exactly the places that most needed scrutiny: invented numbers stated flatly, a confounded comparison presented as evidence, and an account of corrections that had not actually been made. Its fluency is uncorrelated with its reliability, and nothing in the output signals which one you are getting. The working method that followed is the thing I would carry into a real product role: treat AI output as a fast, tireless first draft from a collaborator who is never wrong out loud, and check the numbers yourself.
