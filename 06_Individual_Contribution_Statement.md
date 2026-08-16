# Individual contribution statement

This capstone was completed as an individual submission. Every artifact listed below was owned, produced and validated by me, and there are no other team members to disclose.

# 1. Roles held

The assignment packet suggests six team roles. In a solo submission all six sit with one person, which is worth stating plainly because it changed how the work was sequenced rather than how it was divided.

| Suggested role | Held by | How it played out |
|---|---|---|
| Product strategy lead | Fiza Tahreen | Opportunity matrix, build and buy decision, wedge definition |
| PM artifacts and PRD lead | Fiza Tahreen | Problem statement, persona, jobs to be done, PRD-lite, prioritisation, roadmap |
| UX and workflow lead | Fiza Tahreen | Trust design, uncertainty handling, seven wireframes |
| Data and evaluation lead | Fiza Tahreen | Task decomposition, baselines, metric framework, launch thresholds |
| Prototype and build lead | Fiza Tahreen | Interactive HTML prototype |
| Business and risk lead | Fiza Tahreen | Unit economics, scaling analysis, ethics and governance note |

*Table 1. Suggested team roles and how each was covered in a solo submission.*

The practical consequence of working alone is that there was no second person to catch an inconsistency or push back on a weak argument. I substituted a deliberate adversarial review pass late in the project, described in Section 3, which found four real problems. That is not the same as a colleague disagreeing with you in real time, and I would not claim otherwise.

# 2. Artifact ownership

| Artifact | Produced | AI tools used | How I validated it |
|---|---|---|---|
| Executive summary | Fully | Claude for the first draft, then rewritten | Read against the packet's requirements line by line, and cut roughly 40% of the first draft as generic |
| Product strategy and opportunity framing | Fully | Claude for structure, ChatGPT to challenge it | Forced a scoring of five candidate opportunities and rejected the highest-value one on risk grounds, so the choice is visible rather than assumed |
| PM artifact pack | Fully | Claude for the PRD skeleton | Rewrote the data assumptions and fallback logic entirely, since the AI draft was too optimistic about product-master data quality |
| RICE prioritisation | Fully | Claude for the initial scores | Recalculated by hand, then documented the three places where the score was overruled, because a clean framework output would have been less honest than a contested one |
| UX, workflow and trust design | Fully | Claude for the draft and the SVG generation | Walked each of the seven screens against the uncertainty-state table to check the design actually does what the document claims |
| Seven wireframes | AI-generated first pass, edited by me | Claude | Cross-checked every number, label and reason code across all seven screens, which is where most of the inconsistencies were found |
| Data, model and evaluation strategy | Fully | Claude for structure | Worked the counterfactual and causality problem myself, since it is the part of the analysis I am least willing to take on trust |
| Business, economics and scaling | Fully | Manual spreadsheet, with ChatGPT used to attack the chain | Deliberately not AI-generated. Every number traces to a numbered assumption, and I ran the +3% and +2% sensitivity cases to find where the case breaks |
| Ethics, governance and risk note | Fully | Claude, after I raised bias | The bias analysis exists because I asked whether store clustering could have a fairness problem. AI did not raise it unprompted across several attempts |
| Interactive prototype | Specified by me, coded with assistance | Coding assistant | Clicked through every state and corrected the first version's styling of Approve as the primary action, which contradicted the trust design |
| Presentation deck and presenter script | Fully | Claude for the draft | Rehearsed against the packet's scoring sheet and timed to the slot |
| AI collaboration log | Fully, written by me about the tools | None | Re-checked each claimed correction against the actual artifact, and found that an earlier draft claimed fixes that had not been made |
| This statement | Fully | None | Not applicable |

*Table 2. Artifact ownership, AI use and validation method.*

# 3. How I used AI, and how I checked it

The tools were Claude as the primary assistant for framing, drafting, wireframes and review; ChatGPT for a second opinion and adversarial challenge on the economics; a coding assistant for the prototype implementation; and a manually maintained spreadsheet for the value arithmetic.

Without a team to review the work, I used four validation methods.

- **A cross-artifact consistency pass.** Reading all seven screens, the deck and every document together, explicitly looking for numbers, labels and claims that disagreed. This found four real problems: three different effect sizes for the same benefit, a confounded cross-category comparison presented as evidence, a failure count with no denominator, and a cluster count that contradicted the configuration screen.
- **A second-model challenge.** Putting the economics assumption chain to a different model with an instruction to attack it rather than improve it. That is how assumption 5 was identified as the weakest link.
- **Arithmetic held outside AI.** The unit economics live in a spreadsheet I control, and every figure traces to a numbered, challengeable assumption.
- **Re-checking the log against the artifacts.** The AI collaboration log initially described corrections that had not actually been applied. Catching that required reading the screens against the log rather than trusting the log, and it is the single most useful validation habit I took from this project.

Six decisions were not delegated: the opportunity choice, keeping a human on every markdown, the scope boundary against dynamic pricing, the pricing model, the six launch blockers, and the final recommendation. Each carries accountability, and none of them is a drafting task.

# 4. Declaration

The work submitted is my own. AI tools were used extensively and are disclosed in full in the AI collaboration log, including the specific places where their output was wrong and had to be corrected.

All quantitative figures in this submission are synthetic or illustrative, derived from stated assumptions and labelled as such throughout. No confidential employer data has been used. Professional workflow knowledge informed the problem framing and is used as context, not as data.

Fiza Tahreen, 2024BC26584.
