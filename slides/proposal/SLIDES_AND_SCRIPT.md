# QuotedScope — proposal deck: build specification and Hebrew script

This one file contains everything needed to produce the proposal presentation.

- **Part A** is the build specification for the PowerPoint file. It is written so that a model or a person can build the deck without knowing the project. All on-slide text is final and in English.
- **Part B** is the spoken script in Hebrew for Shai and Liza. It goes into the speaker notes of the matching slide.

Status of the content: this is a project **proposal**. No data has been generated and no model has been trained. Every number is a planned quantity, not a result.

---

# PART A — BUILD SPECIFICATION

## A0. Instructions to whoever builds the file

1. Produce one file: `QuotedScope_Proposal.pptx`, widescreen 16:9 (13.333 in × 7.5 in). Use `python-pptx` or PowerPoint directly.
2. Build **12 slides in this order**: core slides 1–6, then one divider slide "Backup", then backup slides B1–B6. (The divider is not counted in the 12; total is 13 with it.)
3. Use only native shapes, text boxes and tables. No external images, no clip art, no icons from the internet, no stock photos.
4. **Copy the on-slide text exactly as written here.** Do not add bullets, do not rephrase, do not add numbers, results, percentages or citations that are not in this file. Do not translate. If something does not fit, reduce spacing or shape size first; only then reduce the font, never below the minimum sizes in A1.
5. Put the Hebrew text from Part B into the speaker notes of the slide with the same number. Backup slides have no notes.
6. Every core slide has, in the top-right corner, a small grey tag with the course template part (given per slide), and a footer with the sources (given per slide).
7. After building, export to PDF and look at every page. Fix any text that overflows its shape, overlaps another element, or runs off the slide.
8. Text shown below in a code box is on-slide text. Text after "Layout:" or "Build note:" is an instruction and must not appear on the slide.

## A1. Visual style

| Item | Value |
|---|---|
| Slide size | 13.333 in × 7.5 in |
| Background | White `#FFFFFF` |
| Font | Calibri for everything (fallback: Arial) |
| Slide title | 30 pt bold, colour `#12345A`, left-aligned, at top-left (x 0.6 in, y 0.4 in, width 10.8 in) |
| Body text | 20 pt, colour `#1F2937`. Minimum allowed: 18 pt |
| Table text | 16 pt; header row bold on fill `#E5ECF4`. Minimum allowed: 14 pt |
| Footer (sources) | 12 pt, colour `#6B7280`, bottom-left (x 0.6 in, y 6.95 in, width 12 in) |
| Template tag | 12 pt, colour `#6B7280`, top-right (x 11.2 in, y 0.45 in), right-aligned |
| Slide number | 12 pt, colour `#6B7280`, bottom-right |
| **Active** colour | Teal `#0F766E` (text on it: white). Light version for fills: `#D5F0EC` |
| **Historical** colour | Grey `#8A94A3` (text on it: white). Light version for fills: `#E9ECF0` |
| Error / warning colour | Red `#C0392B` |
| Panel fill | `#F2F5F9`, no outline, rounded corners |
| Emphasis box outline | 2 pt, `#12345A` |

Colour rule used on every slide: anything about the **active** request is teal; anything about the **historical** request is grey. Never swap them.

Margins: keep all content inside x 0.6–12.7 in and y 1.3–6.8 in.

---

## A2. Slide 1

**Title:**
```
Two requests in one message. Only one needs action.
```
**Template tag:** `1 · Motivating use case`

**Layout:** two columns. Left column x 0.6–6.4 in. Right column x 6.9–12.7 in.

**Left column, top (y 1.4–3.9 in): chat bubble.** A rounded rectangle with panel fill. Above it a small label, 12 pt grey: `Illustrative customer message`. Inside, 22 pt text. The phrase "activate my card" is bold in the historical grey; the phrase "change my PIN" is bold in the active teal:
```
"Last week I needed to activate my card. That issue is resolved. Today I need to change my PIN."
```
Under the bubble, two small pill shapes side by side:
```
Historical: activate_my_card
```
(grey fill, white text)
```
Active: change_pin
```
(teal fill, white text)

**Left column, bottom (y 4.4–6.6 in): three lines, 20 pt.** The first words of each line are bold.
```
Who: support teams that route messages by intent.
Why hard: both intents are stated clearly; only their role differs.
Approaches we will evaluate: whole-message classification · extract the active request, then classify.
```

**Right column (y 1.4–5.2 in): routing diagram.** Three levels, connected by arrows.
- Top box, panel fill: `Customer message`
- Middle box, outline `#12345A`: `Intent classifier`
- Bottom, two boxes side by side:
  - Left box, light grey fill: `Queue: Card activation`
  - Right box, light teal fill: `Queue: PIN change`
- Arrow from the classifier to the right box: solid, teal, 2.5 pt.
- Arrow from the classifier to the left box: **dashed**, red, 2 pt, with a small red label next to it: `potential error`

**Right column, bottom (y 5.4–6.6 in): evidence panel**, panel fill, 16 pt:
```
Corporate email (not banking): quoted earlier text occurred in 96% of 300 real customer replies; a keyword classifier failed on it.
BANKING77: one intent label per instance; no active/historical annotation.
```

**Footer:**
```
Sources: Zamula & Loskutov (2026), Int. J. of Open Information Technologies · Casanueva et al. (2020), BANKING77.     Project proposal · Shai & Liza · LLMs for NLP, HIT
```

---

## A3. Slide 2

**Title:**
```
Same requests, swapped roles, different label
```
**Template tag:** `2 · Task and novelty`

**Top (y 1.3–2.4 in): problem statement panel**, full width, panel fill, 18 pt. The words "Given", "predict" and "so that" are bold.
```
Given one English banking message that mentions two intents, exactly one of them clearly active, predict the BANKING77 label of the active request, so that support routing follows what the customer needs now.
```
Directly under it, 14 pt grey italic, one line:
```
Scope: only messages with exactly one unambiguous active request. Two active requests, none, or unclear roles are rejected in data QA.
```

**Middle (y 2.8–5.1 in): the Role-Swap pair.** Two cards side by side with a double-headed arrow between them labelled `roles swapped`. Each card: white fill, 1.5 pt outline `#12345A`, a header strip, the message in 18 pt, and a label pill at the bottom.

Left card, header `Version A`:
```
"Last week I needed to activate my card. That issue is resolved. Today I need to change my PIN."
```
Colouring: "activate my card" grey bold (historical), "change my PIN" teal bold (active). Pill, teal: `→ change_pin`

Right card, header `Version B`:
```
"Today I need to activate my card. Last week I needed to change my PIN; that issue is resolved."
```
Colouring: "activate my card" teal bold (active), "change my PIN" grey bold (historical). Pill, teal: `→ activate_my_card`

Under both cards, centred, 16 pt:
```
Same two requests · same order of mention · only the roles change
```

**Bottom (y 5.4–6.7 in): three-column strip**, a table with one header row and one body row, 14 pt.

| Reused | Adapted | Proposed as new |
|---|---|---|
| BANKING77 · DistilBERT · Qwen · counterfactual evaluation (CAT) | Synthetic-data generation and QA · extract-then-classify | Role-Swap benchmark for active vs. historical intent · matched D3 vs. D2 augmentation study |

Build note: give the third column header a teal fill with white text; the other two headers use the normal header fill.

**Footer:**
```
Prior work: Elazar et al. (2024), Findings of EMNLP (CAT) · Tack, Laban & Neville (2026), arXiv:2607.20734 · Goel et al. (2023), EMNLP (PRESTO). Novelty is a proposal, pending instructor review.
```

---

## A4. Slide 3

**Title:**
```
From BANKING77 to a routing decision
```
**Template tag:** `3 · Models and methods (a)`

**Layout:** one diagram with two horizontal lanes. Each lane has a lane label at the left (bold, 16 pt, `#12345A`) and a row of boxes joined by arrows. Each box has a main line (16 pt bold) and a second line (13 pt, grey) naming the tool or model.

**Lane 1 (y 1.6–3.6 in), label:** `Building the data`

Six boxes, left to right:

| # | Main line | Second line |
|---|---|---|
| 1 | BANKING77 | 77 intents, public |
| 2 | Split manifest | sources split before generation |
| 3 | Pair + role assignment | script decides which request is active |
| 4 | Composite generation | local LLM (Llama 3.1 8B, proposed) writes the wording |
| 5 | Checks + review | automatic checks, human review |
| 6 | Versioned datasets | train · validation · frozen test |

Build note: box 3 has a teal outline, because it is where the label is fixed.

**Lane 2 (y 4.2–5.3 in), label:** `Classifying a message`

Four boxes, left to right:

| # | Main line | Second line |
|---|---|---|
| 1 | Customer message | two intents, one active |
| 2 | Fine-tuned classifier | DistilBERT |
| 3 | 1 of 77 labels | active request |
| 4 | Routing queue | |

**Lane 2b (y 5.6–6.5 in), thinner and all in grey, label:** `Alternative we compare`

Three boxes: `Customer message` → `Extract active request (Qwen2.5-1.5B)` → `Clean classifier → label`

**Footer:**
```
Planned pipeline. Generator model is a proposed default. Models: distilbert-base-uncased · Qwen/Qwen2.5-1.5B-Instruct · Qwen/Qwen3-4B-Instruct-2507.
```

---

## A5. Slide 4

**Title:**
```
One model, four training sets: does role-aware augmentation help?
```
**Template tag:** `3 · Models and methods (b)`

**Top (y 1.4–4.2 in): table**, full width, 16 pt. The D3 row is bold with a light teal fill.

| Condition | Added to clean BANKING77 | Question |
|---|---|---|
| D0 Clean | nothing | Reference |
| D1 Generic | 2,000 paraphrases | Does generic augmentation help? |
| D2 Composite | 2,000 two-intent messages; each source pair keeps one active request | Does composite exposure help? |
| D3 Role-Aware | 2,000 two-intent messages = 1,000 reversal pairs | Does role-aware augmentation improve on the composite control? |

**Middle (y 4.4–5.8 in): two boxes of equal size, side by side.**

Left box, panel fill, heading bold `Matched`, 16 pt:
```
Same 2,000 source requests · same generation settings · same model and hyperparameters · 3 seeds. D2 and D3 share half their messages exactly.
```

Right box, white fill with emphasis outline, heading bold `Not isolated`, 16 pt:
```
D3 also sees every source as active once, and both labels per pair. We measure the whole intervention, not pairing alone.
```

**Bottom (y 6.0–6.7 in): one line, 16 pt**, the first two words bold:
```
Also compared: Qwen2.5 and Qwen3 zero-shot (off-the-shelf) · extract-then-classify · position and cue-word heuristics
```
Below it, 13 pt grey:
```
If time allows: Qwen2.5 + LoRA · RoBERTa repeat · D2b ablation
```

**Footer:**
```
Main comparison: D3 vs. D2. All quantities are planned.
```

---

## A6. Slide 5

**Title:**
```
Built from BANKING77, controlled by design, checked by people
```
**Template tag:** `4 · Data specification and generation`

**Layout:** left column x 0.6–4.6 in; right column x 5.0–12.7 in.

**Left column, top (y 1.4–3.3 in): fact card**, panel fill. Heading bold 20 pt `BANKING77`, then 16 pt:
```
77 intents · 10,003 train · 3,080 test
English · CC BY 4.0
One intent label per instance. Active / historical spans are not annotated.
```

**Left column, middle (y 3.5–4.6 in), 16 pt**, first two words bold:
```
Why synthetic: we did not find a public dataset that marks active vs. historical intents within one message; real bank messages are not publicly available.
```

**Left column, bottom (y 4.8–6.6 in):** heading bold 16 pt `What we control`, then six small pill shapes (light teal fill, 14 pt), wrapped onto two or three rows:
```
active intent
historical intent
mention order
attribution style (6; 2 test-only)
tone
length
```

**Right column, top (y 1.4–5.0 in): table**, 14 pt. The "Frozen Role-Swap test" row is bold with a light teal fill.

| Set | Size | Sources from | Human check |
|---|---|---|---|
| Augmentation D1–D3 | 2,000 messages each | official train (90% pool) | audit sample, ≥ 95% valid |
| Composite validation | 300 messages | official train (10% pool) | all, one reviewer |
| Frozen Role-Swap test | 300 pairs | official test, 600 distinct sources | all, two reviewers, label hidden |
| Human-written challenge | 40 pairs | official test, 80 other sources | all, cross-reviewed, label hidden |

**Right column, bottom (y 5.2–6.6 in): emphasis box**, white fill with emphasis outline, 16 pt:
```
The label is assigned by our script, never by the generator.
Test pairs: any disagreement or ambiguity → pair discarded, never relabelled.
```

**Footer:**
```
Source: Casanueva et al. (2020), PolyAI/banking77. Label-noise caution: Ying & Thomas (2022). All counts are planned.
```

---

## A7. Slide 6

**Title:**
```
A pair counts only if both versions are right
```
**Template tag:** `5 · Metrics and KPIs`

**Layout:** left column x 0.6–5.6 in; right column x 6.0–12.7 in.

**Left column, top (y 1.4–3.6 in): metric figure.** Three rows, each row is one pair. Each row has: a label (`Pair 1`, `Pair 2`, `Pair 3`), two small squares for Version A and Version B, and a result at the right. Above the squares, small column headings `A` and `B`.

| Row | Square A | Square B | Result text |
|---|---|---|---|
| Pair 1 | green ✓ | green ✓ | `counts` (teal, bold) |
| Pair 2 | green ✓ | red ✗ | `does not count` (red) |
| Pair 3 | red ✗ | green ✓ | `does not count` (red) |

Build note: green is the active teal `#0F766E`; red is `#C0392B`.

**Left column, middle (y 3.8–4.6 in): formula panel**, panel fill, 18 pt bold, centred:
```
Pairwise Role-Swap Accuracy = pairs with both versions correct ÷ all pairs
```

**Left column, bottom (y 4.8–5.6 in), 16 pt:**
```
"Always pick the last-mentioned request": 50% of messages, 0% of pairs (on balanced Role-Swap pairs).
```
Build note: make "0% of pairs" bold red.

**Left column, lowest (y 5.8–6.6 in), 14 pt grey:**
```
Component checks: generation acceptance rate · reviewer agreement · extraction selection accuracy
```

**Right column, top (y 1.4–4.9 in): planned comparison table**, 14 pt. The result cells are **left empty on purpose**. The D3 row is bold with a light teal fill. Table caption above it, 14 pt grey italic: `Planned comparison — no results yet`

| System | Pairwise | Held-out styles | Clean accuracy |
|---|---|---|---|
| First / last mention, cue words | | | n/a |
| Qwen2.5 zero-shot | | | |
| Qwen3 zero-shot | | | |
| Extract, then classify | | | |
| D0 Clean | | | |
| D1 Generic | | | |
| D2 Composite | | | |
| D3 Role-Aware | | | |

**Right column, bottom (y 5.1–6.6 in): decision-rule box**, white fill with emphasis outline, 14 pt. The first line is bold. (pp = percentage points; the full five-case rule is on backup slide B5.)
```
Decision rule, fixed before any experiment
Success: D3 − D2 ≥ 5 pp pairwise · paired 95% CI lower bound > 0 · D3 clean-accuracy loss ≤ 2 pp vs. D0.
Positive gain below 5 pp: small effect. CI includes 0: inconclusive. Clean loss > 2 pp: trade-off, not success.
```

**Footer:**
```
Mean of 3 seeds · 300 frozen pairs · clean accuracy on the untouched BANKING77 test set. Thresholds are planning choices, not from a power analysis.
```

---

## A8. Divider slide

Centred, 36 pt bold, `#12345A`:
```
Backup
```
Under it, 18 pt grey:
```
Shown only if a question calls for it
```

---

## A9. Backup slide B1

**Title:**
```
B1 · Related work and how this project differs
```
**Table**, full width, three columns, 14 pt.

| Paper | What it studies | Our difference |
|---|---|---|
| Elazar et al. (2024), CAT | Input replacement to test attentiveness | We swap intent roles and study augmentation |
| Tack, Laban & Neville (2026) | Intent that changes across conversation turns | One-message attribution |
| Goel et al. (2023), PRESTO | Semantic parsing of realistic dialogs | Controlled Role-Swap classification |
| Zamula & Loskutov (2026) | Quoted text in corporate email; stronger models unaffected | A competing, valid historical intent |
| Casanueva et al. (2020), BANKING77 | Single-intent classification | Two-intent composites with assigned roles |

**Footer:**
```
Checked from the published abstracts. Full texts of CAT and Tack et al. are read before the final novelty claim.
```

## A10. Backup slide B2

**Title:**
```
B2 · D2 vs. D3 on one source pair
```
**Top (y 1.4–2.3 in), 16 pt**, two pills then a note:
```
Source 1: "I need help activating my card."  → activate_my_card
Source 2: "I need to change my PIN."  → change_pin
```
```
Illustrative sources, not actual BANKING77 rows.
```

**Middle (y 2.5–5.3 in): table**, 14 pt.

| | Message | Label |
|---|---|---|
| Version 1 — identical in D2 and D3 | "Last week I needed to activate my card, and that's been sorted. Today I need to change my PIN." | change_pin |
| Version 2 in D2 — same role, new wording | "My card activation issue from last week is resolved. What I need now is a PIN change." | change_pin |
| Version 2 in D3 — roles reversed | "I still need to activate my card. The PIN change I asked about last week is already done." | activate_my_card |

Build note: the third row has a light teal fill.

**Bottom left (y 5.5–6.6 in), panel, 14 pt:**
```
Coverage difference: across this pair D2 trains on one label, D3 on both.
```
**Bottom right (y 5.5–6.6 in), emphasis box, 14 pt**, heading bold `Expected failure`:
```
"I asked about activating my card earlier today and that's sorted now. What I need is to change my PIN." A model that learned "the request near now is active" answers activate_my_card. Correct: change_pin.
```

## A11. Backup slide B3

**Title:**
```
B3 · Data QA flow and split manifest
```
**Top (y 1.4–2.4 in): flow of five boxes with arrows**, 14 pt:
```
Generate → Automatic checks → Human review → Accept / reject → Record provenance
```

**Left (y 2.7–6.6 in, x 0.6–6.3 in):** heading bold 16 pt `A message is valid only if all five hold`, then 14 pt numbered list:
```
1. Meaning preserved: both source requests are recognisable.
2. Unique active request: exactly one needs action now.
3. Role consistent: the active one is the one the script assigned.
4. No third request was introduced.
5. Readable; label names do not appear verbatim.
```
Under it, 14 pt:
```
Failing any check → rejected. A label is never changed to fit the text.
Test review: both team members, label hidden. Reviewers are the authors; this limitation is reported.
```

**Right (y 2.7–6.6 in, x 6.7–12.7 in): table**, 13 pt.

| Pool | Built from | Used for | Never used for |
|---|---|---|---|
| P-train | official train, 90% | clean training; all synthetic training sources | any evaluation |
| P-val | official train, 10% | clean and composite validation | training |
| P-test | official test, all 3,080 | clean accuracy; sources for both Role-Swap test sets | training, validation, model selection, prompt tuning |

Under the table, 13 pt:
```
Scripted check: no source ID is shared between any training set and any evaluation set. The test set is frozen and hashed before D1–D3 are trained.
```

## A12. Backup slide B4

**Title:**
```
B4 · Schedule and owners
```
**Table**, full width, 14 pt.

| By | Deliverable | Owner |
|---|---|---|
| Proposal + 1 week | Split manifest and leakage script; prompt tested; pilot batch of 50 pairs reviewed | Liza (data), Shai (scripts) |
| Proposal + 2 weeks | D0 with 3 seeds; Qwen2.5 zero-shot on a pilot test; heuristics | Shai |
| Interim | Frozen 300-pair test reviewed and hashed; validation set; 300+ training pairs; EDA; baseline table; 3 papers reviewed | Both |
| Interim + 2 weeks | Full D1–D3 data; 12 DistilBERT runs; primary comparison | Shai (training), Liza (data, audits) |
| Final − 1 week | Extraction baseline; Qwen3 zero-shot; human-written set; error analysis | Both |
| Final | Results, figures, deck; then the GitHub repository | Both |

**Under the table, 14 pt:**
```
Compute: one local RTX 4080 (16 GB). No paid API is required. Runtimes are estimates until the first run is timed.
Guaranteed: D0–D3 × 3 seeds, two Qwen zero-shot baselines, heuristics, extract-then-classify. If time allows: Qwen2.5 + LoRA, RoBERTa repeat, D2b.
```

## A13. Backup slide B5

**Title:**
```
B5 · Risks, triggers and responses
```
**Table**, full width, 13 pt.

| Risk | Trigger | Response | What is preserved |
|---|---|---|---|
| Generator output not valid enough | Pilot audit below 95% after two prompt revisions | Hosted fallback model for the test set; simpler styles | Benchmark and D0–D3 |
| Reviewers disagree often | First-pass kappa below 0.8 | Tighten style definitions; drop the weakest style | Smaller, clean test; size reported |
| Solvable by cue words | Cue heuristic above 80% pairwise on training styles | Report it; held-out styles become the headline | A finding on shortcut vulnerability |
| No problem to solve | D0 or zero-shot above 90% pairwise | Report as the main finding; analyse hard subsets | Benchmark and negative result |
| Unstable comparison | Seed standard deviation larger than the D3 − D2 gap | Up to five seeds; report as inconclusive | Honest result |
| Time | Interim deliverables late | Drop conditional items in order | D0–D3 and the frozen test |

**Under the table, 13 pt**, heading bold `All five outcomes of the decision rule`:
```
≥ 5 points and interval above 0 → supported · interval above 0, gain under 5 → small effect · interval includes 0 → inconclusive · interval below 0 → not supported · clean accuracy more than 2 points below D0 → trade-off, not success
```

## A14. Backup slide B6

**Title:**
```
B6 · Why these models
```
**Table**, full width, 13 pt.

| Model | Role | Why chosen | Alternative considered |
|---|---|---|---|
| DistilBERT, fine-tuned | Controlled platform for D0–D3 | Small enough for 12 runs with seeds on one GPU | RoBERTa-base, DeBERTa-v3-base: potentially stronger; higher compute requirements |
| Qwen2.5-1.5B-Instruct, zero-shot | Off-the-shelf baseline | Open weights, runs locally | Hosted LLMs: cost money, change over time |
| Qwen3-4B-Instruct-2507, zero-shot | Stronger off-the-shelf baseline | Newer and larger, still fits 16 GB | Same |
| Qwen2.5-1.5B + LoRA (if time allows) | Potential second fine-tuned family | Does the D2/D3 finding carry to a generative model? | Full fine-tuning: does not fit as comfortably |
| Extract, then classify | Pipeline alternative | The obvious engineering solution; must be beaten or explained | — |
| First / last mention, cue words | Shortcut detectors | Show what shallow rules already solve | — |

**Under the table, 13 pt:**
```
Generative models are constrained to the 77 labels; any output that is not a valid label counts as wrong, and the invalid-output rate is reported.
```

---

## A15. Consistency checklist for the builder

Before handing the file over, confirm each line:

- [ ] 6 core slides, 1 divider, 6 backup slides, in order.
- [ ] Titles match this file word for word.
- [ ] Active is teal and historical is grey on slides 1, 2 and B2.
- [ ] Slide 1: the dashed arrow is labelled "potential error".
- [ ] Slide 2: Version A ends in `change_pin`, Version B in `activate_my_card`.
- [ ] Slide 6: the comparison table has no numbers in the result cells.
- [ ] These numbers appear and are unchanged: 77 · 10,003 · 3,080 · 2,000 · 1,000 · 300 · 600 · 40 · 80 · 96% · 95% · 5 points · 2 points · 3 seeds.
- [ ] No project experiment is presented as completed. External published findings (96%), theoretical examples (50% / 0%), planned quantities and success thresholds are clearly distinguished. The words "we found", "we show", "results show" do not appear.
- [ ] Every core slide has its template tag, footer and slide number.
- [ ] Speaker notes of slides 1–6 contain the Hebrew text from Part B.
- [ ] Exported PDF checked: no overflow, no overlap, nothing under 12 pt; table text on B1 at least 14 pt.

---

# PART B — HEBREW SCRIPT (תסריט הדיבור)

**מציגים:** שי וליזה · **שפת השקופיות:** אנגלית · **שפת הדיבור:** עברית · **משך יעד:** כ־8 דקות · **שלב:** הצעת פרויקט

הערות לשימוש:
- טקסט בסוגריים מרובעים הוא הוראת במה ולא נאמר בקול.
- מונחים טכניים נשארים באנגלית.
- הזמנים הם הערכה לפי אורך הטקסט. יש לתקן אותם אחרי חזרה בקול עם שעון.

| שקופית | מציג/ה | זמן |
|---|---|---|
| 1 — Use case | שי | 1:10 |
| 2 — Task and novelty | ליזה | 1:30 |
| 3 — Pipeline | שי | 1:10 |
| 4 — The experiment | שי | 1:30 |
| 5 — Data | ליזה | 1:30 |
| 6 — Metrics | שי, ואז ליזה | 1:10 |
| **סה"כ** | | **8:00** |

---

## שקופית 1 — שי (כ־1:10)

שלום לכולם. אנחנו שי וליזה, והפרויקט שלנו נקרא QuotedScope.

נתחיל מהודעה אחת שלקוח שולח לבנק.

[להצביע על בועת ההודעה]

"בשבוע שעבר הייתי צריך להפעיל את הכרטיס. זה כבר טופל. היום אני צריך לשנות את ה־PIN."

איזו בקשה צריך לנתב לטיפול?

יש כאן שתי כוונות ברורות: הפעלת כרטיס ושינוי PIN. אבל רק אחת מהן פעילה. השנייה היא היסטוריה.

[להצביע על החץ המקווקו]

מערכת שמנתבת פניות לפי Intent עלולה לבחור בבקשה הישנה, ואז הלקוח מגיע לתור הלא נכון. זו טעות אפשרית, לא טעות שכבר מדדנו.

מה אנחנו כן יודעים? במחקר על דוא"ל עסקי, לא על בנקאות, טקסט מצוטט מהודעות קודמות הופיע ב־96 אחוז מתשובות הלקוחות. הוא שבר מסווג שמבוסס על מילות מפתח, אבל לא פגע במודלים החזקים יותר.

שם ההחלטה הייתה כן או לא. אצלנו המקרה קשה יותר: התוכן הישן הוא בעצמו בקשה תקפה מאותה רשימת תוויות. האם מודלים מתמודדים עם זה? את זה בדיוק אנחנו מציעים למדוד.

נשווה שתי גישות: סיווג של ההודעה כולה, וחילוץ של הבקשה הפעילה ורק אחר כך סיווג.

**מעבר:** ליזה תגדיר עכשיו את המשימה במדויק.

---

## שקופית 2 — ליזה (כ־1:30)

מבחינה טכנית זו משימת Text Classification.

הקלט הוא הודעה אחת באנגלית שמזכירה שתי כוונות, ורק אחת מהן פעילה באופן ברור. הפלט הוא תווית אחת מתוך 77 התוויות של BANKING77: התווית של הבקשה הפעילה.

הודעות עם שתי בקשות פעילות, או בלי בקשה פעילה ברורה, נמצאות מחוץ לתחום. אנחנו פוסלים אותן בבקרת האיכות.

[להצביע על Version A]

הרעיון המרכזי נקרא Role-Swap. בגרסה A הפעלת הכרטיס היא העבר, ושינוי ה־PIN הוא הבקשה הפעילה.

[להצביע על Version B]

בגרסה B מופיעות אותן שתי בקשות, באותו סדר, אבל התפקידים התחלפו. ולכן גם התווית הנכונה מתחלפת.

מה זה נותן? מודל שבוחר תמיד את הבקשה הראשונה, או תמיד את האחרונה, חייב לטעות באחת משתי הגרסאות. כלומר Role-Swap מכשיל קיצורי דרך של מיקום, מעצם הבנייה.

הוא לא מכשיל לבדו קיצורי דרך של מילות רמז, למשל להיצמד למילה now. לכן נבדוק גם סגנונות ניסוח שהמודל לא ראה באימון, כולל סגנון עם רמזים מטעים.

מה כבר קיים? CAT בודק אם התחזית של מודל משתנה כשמחליפים חלק מהקלט בתוכן מדוגמה אחרת. מחקרים אחרים בוחנים כוונה שמשתנה לאורך שיחה. את הרעיון ה־Counterfactual אנחנו ממחזרים, לא ממציאים.

[להצביע על העמודה Proposed as new]

מה שאנחנו מציעים הוא פרוטוקול Role-Swap מבוקר לזיהוי בקשה פעילה מול היסטורית בתוך הודעה אחת, ומעליו מחקר Augmentation. למיטב ידיעתנו העבודות הקרובות עוסקות במצבים אחרים, ואת שאלת החדשנות נשמח להעמיד לשיפוט שלך.

**מעבר:** שי יציג את ה־Pipeline.

---

## שקופית 3 — שי (כ־1:10)

[להצביע על המסלול העליון]

זה ה־Pipeline כולו. המסלול העליון הוא בניית הנתונים.

מתחילים מ־BANKING77, ומחלקים את פניות המקור לפני שיוצרים משהו. כך פנייה ששימשה למבחן לא יכולה להגיע לאימון.

אחר כך סקריפט בוחר שתי פניות עם כוונות שונות וקובע מי מהן פעילה. ה־LLM רק מנסח את ההודעה המשולבת. הוא לא מחליט על התווית.

כל הודעה עוברת בדיקות אוטומטיות. בנתוני האימון אנחנו בודקים מדגם אנושי, ובנתוני המבחן כל זוג עובר בדיקה נפרדת של שנינו. רק נתונים שעומדים בכללי האיכות נכנסים לסט המתאים.

[להצביע על המסלול התחתון]

בזמן שימוש זה פשוט: הודעה נכנסת ל־DistilBERT שעבר Fine-Tuning, ויוצאת תווית אחת שקובעת את התור.

הענף האפור הוא החלופה שנשווה אליה: קודם לחלץ את הבקשה הפעילה, ואז לסווג אותה.

למה DistilBERT? כי הוא קטן מספיק כדי להריץ שתים־עשרה ריצות אימון על GPU אחד. שני מודלי Qwen הם ההשוואה למודלים מוכנים, Off-the-Shelf. Qwen עם LoRA הוא משפחה שנייה אפשרית ל־Fine-Tuning, אם יישאר זמן.

---

## שקופית 4 — שי (כ־1:30)

זה לב הפרויקט: מודל אחד, ארבעה סטים של אימון.

D0 מאומן על BANKING77 נקי בלבד. זו נקודת הייחוס.

D1 מוסיף אלפיים פרפראזות רגילות. הוא בודק אם סתם עוד נתונים עוזרים.

[להצביע על השורות D2 ו־D3]

עכשיו ההשוואה החשובה. D2 ו־D3 בנויים מאותם אלף זוגות של פניות מקור, עם אותן הגדרות של סגנון וסדר. לכל זוג יש שתי גרסאות.

הגרסה הראשונה זהה לגמרי בשני התנאים.

בגרסה השנייה, D2 משאיר את אותה בקשה פעילה, רק בניסוח אחר. D3 הופך את התפקידים.

אותה כמות הודעות, אותם מקורות, אותו מודל, אותם היפר־פרמטרים, ושלושה Seeds לכל תנאי.

[להצביע על התיבה Not isolated]

מה ההשוואה מראה? את ההשפעה של Augmentation מודע־תפקיד בשלמותו, מול ביקורת של הודעות מורכבות באותו גודל.

מה היא לא מראה? שהסיבה היא הזיווג לבדו. ב־D3 כל פנייה מופיעה פעם אחת כפעילה, ולכן הוא רואה גם יותר ניסוחים פעילים שונים. אנחנו אומרים את זה מראש. ניסוי נוסף שמפריד בין שני הגורמים מתוכנן רק אם יישאר זמן.

**מעבר:** ליזה תסביר איך בונים את הנתונים ואיך בודקים אותם.

---

## שקופית 5 — ליזה (כ־1:30)

[להצביע על כרטיס BANKING77]

הבסיס הוא BANKING77: 77 כוונות, קצת יותר מעשרת אלפים פניות אימון ו־3,080 פניות מבחן.

לכל פנייה יש תווית Intent אחת. אין בו סימון של מה פעיל ומה היסטורי.

לכן אנחנו מייצרים נתונים. לא מצאנו מאגר ציבורי שמסמן איזו משתי כוונות היא הפעילה, והודעות אמיתיות של בנקים הן פרטיות.

[להצביע על התגיות What we control]

אנחנו קובעים מראש את המאפיינים: מי פעילה, מי היסטורית, סדר האזכור, סגנון הייחוס, טון ואורך. התווית נקבעת על ידי הסקריפט שלנו, לא על ידי המחולל.

זה עדיין לא מבטיח שהתווית נכונה, כי המחולל יכול לשנות משמעות או ליצור הודעה עמומה.

[להצביע על הטבלה]

לכן יש בדיקות אוטומטיות על כל ההודעות. בנתוני האימון יש מדגם ביקורת אנושי, עם סף של 95 אחוז תקינות. במבחן, כל אחד מ־300 הזוגות נבדק על ידי שנינו, בנפרד ובלי לראות את התווית. אם אחד מאיתנו לא מסכים, הזוג נזרק. לא מתקנים תוויות.

נגד דליפת מידע: מקורות האימון מגיעים רק מה־Train הרשמי, ומקורות המבחן רק מה־Test הרשמי.

ונגד תלות במחולל: שני סגנונות מופיעים רק במבחן, אחד מהם עם מילות רמז מטעות, ובנוסף ארבעים זוגות שנכתוב בעצמנו. זה סט קטן. הוא בדיקה משלימה, ולא ראיה לגבי פניות בנק אמיתיות.

**מעבר:** ולסיום, איך נמדוד הצלחה.

---

## שקופית 6 — שי, ואז ליזה (כ־1:10)

**שי (כ־0:35):**

[להצביע על איור הזוגות]

המדד המרכזי הוא Pairwise Role-Swap Accuracy. זוג נחשב הצלחה רק אם המודל צדק בשתי הגרסאות.

למה לא Accuracy רגיל? מודל שבוחר תמיד את הבקשה האחרונה יצדק בחצי מההודעות, ובאפס אחוז מהזוגות.

המבחן הראשי הוא D3 פחות D2 על 300 הזוגות הקפואים, בממוצע של שלושה Seeds, עם רווח סמך של 95 אחוז.

**ליזה (כ־0:35):**

[להצביע על תיבת Decision rule]

כלל ההחלטה נקבע מראש, לפני כל תוצאה: הצלחה היא שיפור של חמש נקודות אחוז לפחות, עם רווח סמך שכולו מעל אפס, וירידה של עד שתי נקודות ב־BANKING77 הנקי מול D0.

אלה ספי תכנון, לא תוצאות. עם 300 זוגות, שיפור קטן עלול לצאת לא חד־משמעי, וכך נדווח עליו.

גם הצלחה של מודל Zero-Shot, או היעדר שיפור, יהיו ממצאים.

אנחנו לא טוענים שפתרנו את הבעיה. המטרה היא לבדוק מתי Augmentation מודע־תפקיד עוזר, ומתי לא.

תודה רבה.

---

## מה לא אומרים

- שהשיפור כבר הוכח, או שיש תוצאות כלשהן.
- שהבעיה נפוצה בבנקים, או שידוע שמודלים נכשלים בה.
- שהחדשנות אושרה.
- ש־D3 מול D2 מבודד את הזיווג לבדו.
- ש־Role-Swap מונע כל קיצור דרך.
- שהבדיקה האנושית בלתי תלויה בצוות. הבודקים הם אנחנו.

## הגייה

BANKING77: "בנקינג שבעים ושבע" · DistilBERT: "דיסטיל־ברט" · Qwen: "צ'וון" · LoRA: "לורה" · Role-Swap: "רול סוואפ" · Counterfactual: "קאונטר־פקצ'ואל" · Augmentation: "אוגמנטיישן" · Pairwise: "פייר־וייז" · Seeds: "סידס"

## גרסה מקוצרת (אם מוקצות 5 דקות)

- שקופית 1: לדלג על הפסקה "שם ההחלטה הייתה כן או לא".
- שקופית 2: לדלג על הפסקה "מה כבר קיים" ולהשאיר רק את המשפט על ההצעה.
- שקופית 3: לומר רק את שלושת המשפטים הראשונים ואת המשפט על DistilBERT.
- שקופית 4: לדלג על D0 ו־D1 במשפט אחד: "D0 ו־D1 הם ביקורות בסיס".
- שקופית 5: לדלג על הפסקה "נגד תלות במחולל".
- שקופית 6: ליזה אומרת רק את כלל ההחלטה ואת משפט הסיום.
