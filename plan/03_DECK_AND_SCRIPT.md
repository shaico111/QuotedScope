# Deck and script plan (revision 2)

Deck: English. Script: Hebrew, spoken by Shai and Liza. Target length 8 minutes, to be confirmed with the lecturer.

Revision 2 applies the Proposal Reviewer bot's findings. The operational details behind every slide are in [05_PROTOCOLS.md](05_PROTOCOLS.md); numbers and wording here must match it.

Colour convention in every figure: the **active** request in the accent colour, the **historical** request in grey.

## 1. Structure

Six core slides. Sasha's template has five and says "adapt to your project, add more slides". The template's slide 3 (models and methods) is split in two, because the pipeline and the four-condition experiment are two ideas and do not fit one readable slide. Each slide carries its template number in the corner so the mapping is visible.

| # | Template part | Headline on our slide | Presenter | Time |
|---|---|---|---|---|
| 1 | 1 Motivating use case | Two requests in one message. Only one needs action. | Shai | 1:10 |
| 2 | 2 Task and novelty | Same requests, swapped roles, different label | Liza | 1:30 |
| 3 | 3 Models and methods (a) | From BANKING77 to a routing decision | Shai | 1:10 |
| 4 | 3 Models and methods (b) | One model, four training sets: does role-aware augmentation help? | Shai | 1:30 |
| 5 | 4 Data | Built from BANKING77, controlled by design, checked by people | Liza | 1:30 |
| 6 | 5 Metrics and KPIs | A pair counts only if both versions are right | Shai, then Liza | 1:10 |
| | | | **Total** | **8:00** |

Backup slides: B1 related work, B2 D2 vs D3 worked example, B3 QA flow and split manifest, B4 schedule and owners, B5 risks with triggers, B6 model choices.

## 2. Slide 1: Motivating use case

**Purpose.** Make the audience see the problem before any model is named.

**On the slide.**
- Left, large: the customer message as a chat bubble, the two requests highlighted in the two colours.
  > "Last week I needed to **activate my card**. That issue is resolved. Today I need to **change my PIN**."
- Right: routing diagram. Message → intent classifier → two queues. The arrow to "Card activation" is dashed and labelled **"potential error"**. The arrow to "PIN change" is solid.
- Three short lines:
  - Who: support teams that route messages by intent.
  - Why hard: both intents are stated clearly; only their role differs.
  - **Approaches we will evaluate:** whole-message classification; extract the active request, then classify.
- Evidence footer, two sourced facts:
  - "In corporate email (not banking): quoted earlier text appeared in 96% of 300 real customer replies and broke a keyword classifier (Zamula & Loskutov, 2026)."
  - "Each BANKING77 instance has one intent label; active and historical spans are not annotated (Casanueva et al., 2020)."
- Small tag on the bubble: "illustrative example".

**What the slide does not claim.** That this message type is frequent in banks, or that a model was observed to fail on it. The same source reports that its LLM and encoder were not affected by quoted text; the script says so.

**Script (Shai), outline.**
1. Greeting, names, project name.
2. Read the message. Which request should support handle?
3. Two intents, one active.
4. What could go wrong: routing by the old request sends the customer to the wrong queue.
5. Evidence: old or quoted content is very common in real support mail. In that study it broke simple keyword methods, and it did not hurt the stronger models. Their task was a yes/no decision. Ours is harder: the old content is itself a valid request. Whether models handle that is what we measure.
6. Two approaches we will compare.
7. Handover to Liza.

## 3. Slide 2: Task and novelty

**Purpose.** State the task precisely and the proposed contribution against verified prior work.

**On the slide.**
- Problem statement in the guide's template form:
  > Given one English banking message that mentions two intents, exactly one of them clearly active, predict the BANKING77 label of the active request, so that support routing follows what the customer needs now.
- Scope line: "Evaluated only on messages with exactly one unambiguous active request. Two active requests, no active request, or unclear roles are out of scope and are rejected in data QA."
- Main figure: the Role-Swap pair. Two message cards, same requests, same order, colours exchanged, different label under each.

  | Version A | Version B |
  |---|---|
  | Earlier: activate card. Now: change PIN | Now: activate card. Earlier: change PIN |
  | → `change_pin` | → `activate_my_card` |

- Strip (assessment criterion 3):

  | Reused | Adapted | Proposed as new |
  |---|---|---|
  | BANKING77, DistilBERT, Qwen, LoRA, counterfactual evaluation (CAT) | LLM data generation, label QA, extract-then-classify | Single-message active/historical attribution task; Role-Swap protocol; role-aware vs composite augmentation study |

- Footer: Elazar et al. 2024 (CAT); Tack et al. 2026; Goel et al. 2023.

**Script (Liza), outline.**
1. Text classification: input, output, 77 labels.
2. Version A, then version B. Same requests, same order, roles exchanged, so the correct label changes.
3. What this buys: "Role-Swap makes purely position-based selection fail by construction. It does not by itself rule out cue-word shortcuts such as keying on 'now', so we also test styles the model never saw, including one with misleading cues."
4. What exists: CAT tests whether a model's prediction changes when part of the input is replaced with content from another example. Other work studies intent that evolves across a conversation. We reuse the counterfactual idea.
5. What we propose: a controlled Role-Swap protocol for active-versus-historical attribution inside one message, with an augmentation study on top. To our knowledge the closest work addresses other settings; the lecturer's judgement on novelty is what we are asking for.
6. Handover to Shai.

## 4. Slide 3: Pipeline (visual abstract)

**Purpose.** Show the whole system in one figure: how data is made and how a message is classified.

**On the slide.** One diagram with two lanes.
- **Data lane:** BANKING77 → split manifest → pair and role assignment (script) → composite generation (local LLM, Llama 3.1 8B proposed) → automatic checks + human review → frozen datasets.
- **Inference lane:** message → fine-tuned classifier (DistilBERT) → one of 77 labels → routing queue. A thinner branch below: message → extractor → clean classifier → label ("alternative we compare").
- Under each box, the model or tool used. No other text.

**Script (Shai), outline.**
1. Top lane, left to right in four sentences. Sources are split before anything is generated, so no test source can reach training.
2. The script decides which request is active; the LLM only writes the wording.
3. Bottom lane: at use time it is a single classifier call. The branch is the two-step alternative.
4. One sentence on model choice: DistilBERT is the controlled platform because twelve runs fit on one GPU; the two Qwen models are the off-the-shelf comparison, and Qwen with LoRA is a potential second fine-tuned family if time allows.

## 5. Slide 4: The experiment

**Purpose.** Explain the four conditions and exactly what the main comparison shows.

**On the slide.**

| Condition | Added to clean BANKING77 | Question |
|---|---|---|
| D0 Clean | nothing | Reference |
| D1 Generic | 2,000 paraphrases | Does generic augmentation help? |
| D2 Composite | 2,000 two-intent messages; each source pair keeps one active request | Does composite exposure help? |
| **D3 Role-Aware** | **2,000 two-intent messages = 1,000 reversal pairs** | **Does role-aware augmentation improve on the composite control?** |

- Box: "Matched: same 2,000 sources, same generation spec, same model, same hyperparameters, three seeds. D2 and D3 share half their messages exactly."
- Box, same size: "Not isolated: D3 also sees every source as active once and both labels per pair. We measure the whole intervention."
- Side list, "Also compared": Qwen2.5 and Qwen3 zero-shot (off-the-shelf), extract-then-classify, position and cue heuristics. One line below, in grey: "If time allows: Qwen2.5 + LoRA, RoBERTa repeat, D2b ablation."

**Script (Shai), outline.**
1. One model, four training sets.
2. D0 and D1 in a sentence each.
3. D2 and D3 slowly: same 1,000 source pairs, same style and order settings. Version 1 is the same message in both. In version 2, D2 keeps the same request active and D3 reverses the roles.
4. What the comparison shows: the effect of the complete role-aware augmentation against a size-matched composite control.
5. What it does not show: that pairing alone is the cause. D3 also covers more active wordings. We say this up front, and an extra ablation that separates the two is planned only if time allows.
6. Handover to Liza.

## 6. Slide 5: Data

**Purpose.** Provenance, what is controlled, why labels can be trusted, how leakage is prevented.

**On the slide.**
- Fact card: BANKING77 · 77 intents · 10,003 train · 3,080 test · English · CC BY 4.0. "Each instance has one intent label; active/historical spans are not annotated."
- "Why synthetic: no public data marks which of two intents is active; real bank messages are private."
- Controlled attributes as chips: active intent · historical intent · mention order · attribution style (6; 2 held out for test) · tone · length.
- Table:

  | Set | Size | Sources from | Human check |
  |---|---|---|---|
  | Augmentation D1–D3 | 2,000 messages each | official train (90% pool) | audit sample, ≥95% valid |
  | Composite validation | 300 messages | official train (10% pool) | all, one reviewer |
  | Frozen Role-Swap test | 300 pairs | official test, 600 distinct sources | all, two reviewers, label hidden |
  | Human-written challenge | 40 pairs | official test, 80 other sources | all, cross-reviewed |

- Bottom line: "Training data: automatic checks plus a human audit sample. Test data: every pair verified by both reviewers; any disagreement or ambiguity → pair discarded, never relabelled."

**Script (Liza), outline.**
1. BANKING77: what it has and what it lacks.
2. Why we generate.
3. How: we choose two requests and decide which is active; the generator writes the wording. The label comes from our assignment.
4. That does not make the label correct by itself, so: automatic checks on everything, an audit sample for training data, and for the test every pair is checked by both of us without seeing the label. Disagreement means the pair is thrown out.
5. Leakage: test sources come only from the official test split and training sources only from the training split.
6. Against generator artefacts: two styles appear only in the test, one of them with misleading cue words, plus 40 pairs written by people. That set is small; it is a supplementary check and not evidence about real bank traffic.
7. Handover to Shai.

## 7. Slide 6: Metrics and KPIs

**Purpose.** How success is measured, against what, and what result counts.

**On the slide.**
- The metric as a small figure: three example pairs with ticks and crosses.
  **Pairwise Role-Swap Accuracy = pairs with both versions correct / all pairs**
- One line: "Always pick the last-mentioned request: 50% of messages, **0% of pairs** (on balanced Role-Swap pairs)."
- Planned comparison table, empty cells:

  | System | Pairwise | Held-out styles | Clean accuracy |
  |---|---|---|---|
  | First / last mention, cue words | | | n/a |
  | Qwen2.5 zero-shot | | | |
  | Qwen3 zero-shot | | | |
  | Extract, then classify | | | |
  | D0 Clean | | | |
  | D1 Generic | | | |
  | D2 Composite | | | |
  | **D3 Role-Aware** | | | |

- Decision rule: "Supported if D3 − D2 ≥ 5 points pairwise with the 95% paired-bootstrap interval above 0, and D3 clean accuracy within 2 points of D0 on the untouched BANKING77 test. Interval above 0 but gain under 5 → small effect, not success. Interval includes 0 → inconclusive. Mean of three seeds." The full five-case table is in `05_PROTOCOLS.md` §6.1 and on backup B5.
- Small list, component checks: generation acceptance rate, reviewer agreement, extraction selection accuracy.

**Script, outline.**
Shai (0:35):
1. A pair succeeds only if both versions are right.
2. Why: the last-mention shortcut gets half the messages and none of the pairs.
3. Main test: D3 minus D2 on the 300 frozen pairs, three seeds, with a confidence interval.

Liza (0:35):
4. The rule is fixed now, before any result: at least five points with the interval above zero, and no more than two points lost on ordinary BANKING77 against D0. These are planning thresholds, not from a power analysis; with 300 pairs a five-point gain may come out inconclusive, and we would report it so.
5. If the simple baselines or zero-shot models already do well, that is a finding too.
6. Closing sentence: we do not claim to have solved this; we propose a controlled way to find out whether role-aware augmentation helps, how much, and where it fails. Thanks.

## 8. Backup slides

| # | Content | Columns / form | Used when asked |
|---|---|---|---|
| B1 | Related work | paper · task · data · evaluation · limitation for our question · our difference (table in `05_PROTOCOLS.md` §7) | "Isn't this CAT?" |
| B2 | D2 vs D3 worked example | One source pair: the shared version 1, D2's version 2, D3's version 2, with exact texts, active labels, and a note on the label-coverage difference | "What exactly differs?" |
| B3 | QA flow and split manifest | Flow: generate → automatic checks → human review → accept / reject → record provenance; plus the pool table | "How do you know labels are right?" / "What is source-disjoint?" |
| B4 | Schedule | milestone · owner · deliverable · dependency | "Can you finish?" |
| B5 | Risks | risk · trigger · response · what is preserved | "What if it does not work?" |
| B6 | Model choices | model · role · why · alternative · trade-off | "Why DistilBERT?" |

One expected failure, shown on B2 with its mechanism: "I asked about activating my card earlier today and that's sorted now. What I need is to change my PIN." A model that learned "the request near *now* is active" answers `activate_my_card`.

## 9. Script document format (`docs/script_he.md`)

- Header: presenters, language, target time.
- Per slide: presenter, time, full spoken Hebrew text in short sentences, stage cues in square brackets, the handover sentence.
- English technical terms stay in English, with a pronunciation list at the end.
- Timing table from word counts at about 130 Hebrew words per minute; corrected after rehearsal.
- "Do not say" list: that improvement is proven; that the problem is common in banks; that models are known to fail at this; that novelty is approved; that D3 vs D2 isolates pairing; that Role-Swap rules out all shortcuts.
- A shortened version (about 60%) in case the allotted time is five minutes.

## 10. Speaking balance

| Presenter | Slides | Time |
|---|---|---|
| Shai | 1, 3, 4, first half of 6 | about 4:25 |
| Liza | 2, 5, second half of 6 | about 3:35 |

If you prefer an even split, Liza takes slide 3 and the balance becomes 3:15 / 4:45 the other way; say which you want.
