# Research protocols (revision 2)

This file fixes the operational details that the first plan left open. It was written in response to the review by the course's Proposal Reviewer bot. Where this file and an older sentence elsewhere disagree, this file governs.

Everything here is a plan. No data has been generated and no model has been trained.

## 0. Confirmed constraints

| Item | Value | Source |
|---|---|---|
| Course | "Large Language Model for Natural Language Processing", HIT, undergraduate | `Info/LLM4NLP_Syllabus_V3.pdf` |
| Required scope | Synthetic training data; compare several LLMs, both off-the-shelf and fine-tuned | Syllabus, section 3 |
| Proposal presentation | Not graded: "for feedback and approval only" | Syllabus, section 8 |
| Grade weights | Interim 20%, final presentation 40%, GitHub repo 40% | Syllabus, section 8 |
| Milestones | Proposal, interim (about week 10 in the syllabus table), final (week 13) | Syllabus, section 7 |
| Hardware | One local NVIDIA RTX 4080, 16 GB VRAM (checked with `nvidia-smi`) | Team machine |
| API budget | None assumed. All required work runs locally | Decision |
| Talk length | Planned 8 minutes; not stated in any course document, to be confirmed with the lecturer | Open |

The graduate-only requirements (three genuinely different models, mini-paper) do not apply. The plan still includes three model families.

## 1. Data-split manifest

BANKING77 ships with 10,003 training and 3,080 test utterances. Every utterance gets a stable `source_id` (split name + row index) before anything else happens.

| Pool | Built from | Size | Used for | Never used for |
|---|---|---|---|---|
| P-train | Official train, stratified 90% | about 9,003 | Clean training in D0–D3; the only pool that feeds synthetic training data | Any evaluation |
| P-val | Official train, stratified 10% | about 1,000 | Clean validation; sources for composite validation | Training |
| P-test | Official test, all | 3,080 | Clean accuracy; sources for the two Role-Swap test sets | Training, validation, model selection, prompt tuning |

| Dataset | Sources | Rule |
|---|---|---|
| Clean training | all of P-train | Same for every condition |
| D1, D2, D3 augmentation | the **same 2,000 utterances** drawn from P-train (1,000 pairs of different intents) | `source_id` must be in P-train |
| Composite validation | 300 utterances from P-val → 150 Role-Swap pairs, 300 messages | Disjoint from training by construction |
| Frozen Role-Swap test | 600 distinct utterances from P-test → 300 pairs, 600 messages | Each source appears in exactly one pair |
| Human-written challenge | 80 other utterances from P-test → 40 pairs, 80 messages | Disjoint from the 600 above |
| Clean test | all 3,080 of P-test, unmodified | Never seen in training through any path |

Leakage checks, run by script and stored in `results/`:
1. Set intersection of `source_id` between every training set and every evaluation set must be empty.
2. Near-duplicate check across pools: normalised exact match, plus cosine similarity above 0.95 between sentence embeddings. A P-train utterance that near-duplicates a test source is removed from P-train. The count removed is reported.
3. The frozen test is generated, reviewed and hashed before any D1–D3 model is trained. The hash goes in the repo.
4. Prompts are developed only on P-train and P-val sources.

Sampling for the 680 test sources: stratified over the 77 intents (the official test has 40 per intent, so about 9 per intent are used). Pairs are formed from different intents, with half the pairs drawn from intents that are semantically close (same BANKING77 topic family, for example two card intents) and half from distant ones.

## 2. Fair-comparison protocol for D0–D3

### 2.1 What is identical across conditions
- Model: `distilbert-base-uncased`, a 77-way classification head, same initial checkpoint.
- Clean training data: all of P-train.
- Hyperparameters: fixed in advance for all conditions (learning rate 2e-5, batch 32, max length 128, up to 10 epochs). They are tuned once on D0 using P-val and then frozen.
- Checkpoint selection: best macro-F1 on the union of clean validation and composite validation. Same rule for every condition.
- Seeds: three training seeds per condition, the same three for all. 12 DistilBERT runs in total.
- Evaluation sets: identical and frozen.
- Augmentation budget: D1, D2 and D3 each add exactly 2,000 messages built from the same 2,000 P-train sources.

### 2.2 How D2 and D3 are built from the same specification
For each of the 1,000 source pairs (A, B), a generation spec is drawn once: mention order, attribution style, tone, length band, generator, prompt version.

| | Version 1 | Version 2 |
|---|---|---|
| D2 Composite | spec, A active | same spec, **A active again**, new generation seed |
| D3 Role-Aware | spec, A active (**the identical message as D2 version 1**) | same spec, **B active** |

So D2 and D3 share 1,000 messages exactly. They differ in the other 1,000, and the only field of the spec that differs there is which request is active.

Balancing applied to both conditions:
- Mention order: the active request comes first in 50% of messages.
- Active-label distribution: which side of each pair is "A" is chosen so that the per-intent count of active labels in D2 is as close as possible to D3's. The remaining per-intent difference is reported in a table.
- Attribution style: same distribution, training styles only.

### 2.3 What still differs, stated openly
Even with the matching above, D3 differs from D2 in three linked ways:
1. D3 contains explicit role reversals (the intended factor).
2. In D3 every one of the 2,000 sources is active once. In D2, 1,000 sources are active twice and 1,000 are never active. D3 therefore sees more distinct active wordings.
3. In D3 each source pair appears with both labels. In D2 each pair appears with one.

**Claim we will make:** D3 versus D2 measures the effect of the complete role-aware augmentation intervention against a size-matched, source-matched composite control.
**Claim we will not make:** that the difference is caused by pairing alone, or by reversal alone.

### 2.4 Optional ablation, only if time allows
**D2b Reshuffled:** the same 2,000 sources, each active exactly once and historical exactly once, but with partners reshuffled so that no two messages form a reversal pair. D2b has the same label and wording coverage as D3 and lacks only the minimal-pair structure. D3 versus D2b would separate factor 1 from factors 2 and 3. It costs 1,000 extra generated messages and three extra training runs. It is not promised in the proposal.

## 3. Synthetic data generation

### 3.1 Why synthetic
BANKING77 gives one intent label per utterance and has no annotation of active or historical spans. We found no public dataset that marks which of two mentioned intents is the active one, and real bank messages are private.

### 3.2 Generator configuration (proposed default; the team confirms at setup)
| Role | Proposed model | Why |
|---|---|---|
| Generator | Llama 3.1 8B Instruct, run locally through Ollama | Free, fits 16 GB, and from a different model family than the Qwen models we evaluate, so Qwen is not tested on text it wrote |
| Automatic judge | Mistral 7B Instruct, local | Different family from the generator, so it does not simply agree with its own output |
| Fallback | A hosted model through the course's OpenRouter budget | Trigger: the pilot batch fails the audit twice after prompt revision |

Sampling: temperature 0.8, top-p 0.95, one message per call, seed logged. Exact model tags are recorded in the manifest when they are pulled.

### 3.3 Controlled attributes
The label (which request is active) is assigned by script, never by the generator. Variety attributes are sampled independently of the label:

| Attribute | Values |
|---|---|
| Mention order | active first / active second, balanced 50/50 |
| Attribution style | 6 styles: resolved past issue; quoted earlier request; earlier support interaction; dismissed request; **indirect temporal reference (test only)**; **misleading cue (test only)** |
| Tone | neutral / frustrated / brief |
| Length band | short / medium |
| Intent distance | close / distant |

The "misleading cue" style puts a recency word inside the historical clause, for example: "I asked about activating my card earlier today and that's sorted now. What I need is to change my PIN." A model that keys on "now" or "today" picks the wrong request.

### 3.4 Record schema (one row per generated message)
`message_id, pair_id, version, split, source_a_id, source_b_id, source_a_text, source_b_text, active_source, active_label, historical_label, mention_order, style, tone, length_band, intent_distance, generator_model, prompt_version, seed, text, auto_check_status, auto_check_reasons, review_1, review_2, review_status, rejection_reason`

### 3.5 Scale-up order
1. Hand-test the prompt on 10 pairs.
2. Pilot batch of 50 pairs across all training styles; both team members read all 100 messages.
3. Revise the prompt; record `prompt_version`.
4. Generate the frozen test first, then validation, then training.

## 4. QA protocol

### 4.1 Acceptance checklist (the same five checks everywhere)
A message is valid only if all hold:
1. **Meaning preserved:** both source requests are still recognisable.
2. **Unique active request:** exactly one request needs action now.
3. **Role consistent:** the active request is the one the script assigned.
4. **No third request** was introduced.
5. **Readable** as a customer message; label names do not appear verbatim.

A message that fails any check is rejected. A label is never changed to fit the text.

### 4.2 Automatic checks (every generated message)
- Length bounds, English only, no verbatim label names.
- Near-duplicate filter between messages of **different** pairs, and across splits. Two messages of the same pair are related on purpose and are exempt from it: D3's two versions are a minimal pair, and D2's two versions are two wordings of the same assignment. For D2 a separate rule applies inside the pair: version 2 must not be a copy of version 1 (exact match after normalisation, or word-level Jaccard overlap above 0.8, is rejected and regenerated), so D2 adds real wording variety and not repeated rows.
- Judge check: the judge model is asked which request needs action now, with the options "first", "second", "both", "neither", "unclear". Its answer must match the assigned role.

### 4.3 Human review, by split

| Split | Who reviews | How much | Acceptance rule |
|---|---|---|---|
| Training (D1–D3) | One team member | Audit sample of 120 messages per condition, stratified by style | Batch accepted if at least 95% of the sample is valid. Otherwise the failing style is fixed and regenerated |
| Composite validation | One team member | All 300 messages | Invalid messages are replaced |
| Frozen Role-Swap test | **Both** team members, independently | All 300 pairs, 600 messages | A pair is kept only if both reviewers pass both versions |
| Human-written challenge | Each member writes 20 pairs; the **other** member reviews them with the label hidden | All 40 pairs | A pair is kept only if the non-author reviewer picks the intended request in both versions and passes the checklist. One blind reviewer per pair, not two, because the author knows the label. Rejected pairs are rewritten until 40 are accepted |

Test review procedure:
- The reviewer sees only the message text and the two source requests in random order. The assigned label is hidden.
- The reviewer answers: which request is active (first / second / both / neither / unclear), and whether each of the checklist items holds.
- A pair is accepted only when both reviewers independently choose the assigned request in both versions and pass the checklist.
- Disagreements are not arbitrated. The pair is discarded.
- We generate about 400 candidate pairs to end with exactly 300 accepted. The 300 are fixed by stratified selection from the accepted pool, before any model sees them.
- Reported: first-pass agreement (Cohen's kappa on the active-request choice), rejection rate, and rejection reasons.

Stated limitation: the reviewers are the authors. They are blind to the label but not to the project. We will say so, and we will not call the review independent of the team.

## 5. Models

### 5.1 Why each model

| Model | Role | Why chosen | Alternative considered | Trade-off |
|---|---|---|---|---|
| DistilBERT, fine-tuned | Controlled platform for D0–D3 | Small enough for 12 runs with seeds on one GPU; a standard encoder classifier | RoBERTa-base, DeBERTa-v3-base | Potentially stronger; higher compute requirements. If time allows, D2 and D3 are repeated on RoBERTa-base to check the effect is not specific to DistilBERT |
| Qwen2.5-1.5B-Instruct, zero-shot | Off-the-shelf generative baseline | Runs locally, open weights | Larger hosted LLMs | Hosted models cost money and change over time |
| Qwen3-4B-Instruct-2507, zero-shot | Stronger off-the-shelf baseline | Newer and larger, still fits 16 GB | Same | Slower; run on the full test once |
| Qwen2.5-1.5B-Instruct + LoRA (if time allows) | Second fine-tuned family | Tests whether the D2/D3 finding carries to a generative model | Full fine-tuning | LoRA fits in memory. Trained on D2 and D3 data only, one seed |
| Extract, then classify | Pipeline alternative | The obvious engineering solution; it must be beaten or explained | none | Errors in extraction propagate |
| Heuristics: first mention, last mention, cue words | Shortcut detectors | Show how much of the benchmark shallow rules solve | none | none |

Generative models are constrained to the 77 labels: the label list is in the prompt, decoding is greedy, the output is normalised by a fixed rule, and anything that is not a valid label counts as wrong. The invalid-output rate is reported.

### 5.2 Two pipelines
- **Data pipeline:** BANKING77 → split manifest → pair and role assignment (script) → generation (LLM) → automatic checks → human review → frozen datasets.
- **Inference pipeline:** message → tokenizer → fine-tuned classifier → 77-way label → routing queue.
- **Alternative inference pipeline:** message → extractor (Qwen2.5-1.5B, prompted to copy the request that needs action now) → clean D0 classifier → label.

### 5.3 Scoring the extraction step without span annotation
We know both source requests of every composite. The extracted text is scored as correct when it is closer (sentence-embedding cosine) to the active source request than to the historical one. This "active-request selection accuracy" is validated by hand on 60 test messages. No span-level ground truth is claimed.

## 6. Evaluation and reporting

| Measurement | Definition |
|---|---|
| Primary outcome | Pairwise Role-Swap Accuracy on the 300 frozen pairs: share of pairs with both versions correct |
| Primary comparison | D3 minus D2, in percentage points, mean over three seeds |
| Uncertainty | 95% confidence interval from a paired bootstrap that resamples whole pairs (10,000 resamples), using each pair's score averaged over seeds. Seed-to-seed standard deviation reported next to it |
| Secondary | Per-message accuracy and macro-F1 on the Role-Swap test |
| Clean retention | Accuracy on all 3,080 unmodified test utterances; D3 compared with **D0**, same seeds |
| Robustness | Pairwise accuracy by attribution style (training styles against the two held-out styles), by mention order, by intent distance; and on the 40 human-written pairs |
| Shortcut probes | The three heuristics on every subset; cue-word frequency by role in the training data |
| Component checks | Generation acceptance rate, reviewer agreement, judge-versus-human agreement, extraction selection accuracy |

### 6.1 Decision rule, fixed before any experiment

| Result on D3 − D2 (pairwise) | Conclusion we will state |
|---|---|
| Point estimate ≥ 5 points **and** CI lower bound > 0 **and** D3 clean accuracy within 2 points of D0 | Hypothesis supported |
| CI lower bound > 0 but point estimate below 5 points, clean accuracy within 2 points | A real but small effect, below our practical threshold. Reported as such, not as success |
| CI includes 0 | Inconclusive at this sample size |
| CI entirely below 0 | Hypothesis not supported: role-aware augmentation did worse than the composite control |
| Any pairwise gain, but D3 clean accuracy more than 2 points below D0 | A trade-off, not a success: the gain is reported together with its cost |

The five rows cover every outcome; the pairwise rows are read first and the clean-accuracy guardrail is applied to whichever one holds.

The 5-point threshold and the 300-pair size are planning choices. They do not come from a power analysis. A rough calculation shows the limit: if D2 and D3 disagree on 20% of pairs, the standard error of the difference is about 2.6 points, so the interval is about ±5 points. A true gain of 5 points may therefore come out as inconclusive, and we will report it that way.

### 6.2 What pairwise accuracy does and does not show
A model that always picks the first-mentioned or the last-mentioned request gets 50% of messages and 0% of pairs on balanced Role-Swap pairs, because mention order is the same in both versions. So Role-Swap removes position shortcuts by construction.

It does **not** remove cue-word shortcuts: a model that learns "the request next to *now* is active" can still pass. That is why the held-out styles include one with misleading cues, why a cue-word heuristic is one of the baselines, and why results are broken down by style. High pairwise accuracy is evidence of correct behaviour on this test, not proof of understanding.

## 7. Related work (checked on 2026-11-14 from the published abstracts)

| Paper | Task | Data | Evaluation | What it leaves open for our question | Our difference |
|---|---|---|---|---|---|
| Elazar et al., 2024, Findings of EMNLP: Counterfactual Attentiveness Test (CAT) | Tests whether models attend to each part of a paired input | Ten datasets, four tasks such as NLI and reading comprehension | Replace one part of the input with the counterpart from another example; an attentive model should change its prediction | Not about intents inside one message; the swap brings in content from a different example; evaluation only | We keep both requests and exchange only their roles, in single-message intent classification, and we also train on such pairs |
| Tack, Laban & Neville, 2026, arXiv 2607.20734 | How well LLMs track user intent that evolves across turns | Static tasks transformed into multi-turn conversations | LLM performance as intent is revised over the conversation | Multi-turn, generative agents; no classifier training study | One message, a fixed label set, controlled minimal pairs, fine-tuned classifiers |
| Goel et al., 2023, EMNLP: PRESTO | Parsing realistic task-oriented dialogs | Over 550K multilingual human–assistant conversations with revisions, disfluencies, code-switching | Semantic parsing | User revisions within a dialog, parsed into structures; no active-versus-historical role-swap test | Intent classification with an explicit role-reversal protocol |
| Zamula & Loskutov, 2026, Int. J. Open Information Technologies | Triage of customer replies to corporate email | 300 real replies; quoted text present in 96% | Keyword classifier, few-shot LLM, fine-tuned XLM-RoBERTa | Binary decision; the quoted text carries no competing label. Their LLM and encoder were **not** sensitive to quoted text | We test the harder case where the historical content is itself a valid intent from the same label set |
| Casanueva et al., 2020: BANKING77 | Single-intent banking classification | 13,083 utterances, 77 intents | Accuracy | One intent label per utterance; no role annotation | We build two-intent messages with assigned roles on top of it |
| Ying & Thomas, 2022, Insights workshop: Label Errors in BANKING77 | Label-noise analysis | BANKING77 train | Automated error detection: over 1,400 (14%) training utterances may be mislabelled | n/a | We screen the selected source utterances |

Honest status: these were verified from abstracts. The full texts of CAT and Tack et al. are read before the deck is final. The novelty sentence is therefore worded as a proposal:

> **Proposed contribution:** a controlled Role-Swap evaluation protocol for active-versus-historical intent attribution within a single message, together with an augmentation study that compares role-reversal training with matched composite training. To our knowledge, the closest prior work tests counterfactual attentiveness or evolving intent in other settings, not this one.

An important consequence of the Zamula & Loskutov result: it is possible that modern classifiers already handle this. Our D0 and zero-shot baselines measure that first. If they score high on pairwise accuracy, the finding is that the problem is smaller than assumed, and the D2/D3 study reports whatever headroom remains.

## 8. Feasibility

### 8.1 Compute estimate (to be confirmed by timing the first run)
| Job | Count | Expected cost on one RTX 4080 |
|---|---|---|
| DistilBERT fine-tuning, about 11,000 short texts | 12 runs | minutes each |
| Generation with an 8B local model | about 6,000 messages including rejects and D1 | a few hours in total |
| Qwen zero-shot on 600 + 80 + 3,080 texts | 2 models | under an hour each |
| Qwen2.5-1.5B LoRA | 2 runs | about an hour each |
| Human review | 600 test messages × 2 reviewers, plus audits | about 3 hours per person |

### 8.2 Schedule by milestone

| By | Deliverable | Owner | Depends on |
|---|---|---|---|
| Proposal + 1 week | Split manifest and leakage script; prompt hand-tested; pilot batch of 50 pairs reviewed | Liza (data), Shai (scripts) | Lecturer's approval |
| Proposal + 2 weeks | D0 trained with three seeds; zero-shot Qwen2.5 on a 50-pair pilot test; heuristics | Shai | Manifest |
| Interim | Frozen 300-pair test reviewed and hashed; validation set; at least 300 training pairs; EDA; baselines table (D0, heuristics, zero-shot); three papers reviewed in full | Both | Pilot accepted |
| Interim + 2 weeks | Full D1–D3 data; 12 DistilBERT runs; primary comparison | Shai (training), Liza (data, audits) | Frozen test |
| Final − 1 week | Extraction baseline; Qwen3 zero-shot; LoRA; human-written set; error analysis | Both | Primary comparison |
| Final | Results, figures, deck; then the repo | Both | |

### 8.3 What definitely runs and what is conditional
- **Guaranteed minimum:** split manifest, generation and QA, frozen test, human-written set, and this model comparison:
  - fine-tuned: DistilBERT in four conditions (D0–D3), three seeds each;
  - off-the-shelf: **both** Qwen2.5-1.5B-Instruct and Qwen3-4B-Instruct-2507, zero-shot (inference only, under an hour each on the local GPU);
  - non-neural and pipeline baselines: three heuristics, extract-then-classify.
- **Planned, dropped first if time runs out (in this order):** RoBERTa repeat, D2b ablation, Qwen2.5 LoRA.

On the slides, the guaranteed items appear without qualification and the three conditional ones are marked "if time allows".

### 8.4 Risks with triggers

| Risk | Trigger | Response | What is preserved |
|---|---|---|---|
| Generator output not valid enough | Pilot audit below 95% after two prompt revisions | Switch to the hosted fallback model for the test set; simplify styles | Benchmark and D0–D3 |
| Reviewers disagree often | First-pass kappa below 0.8 | Tighten the style definitions; drop the weakest style | A smaller but clean test; size reported |
| Benchmark solvable by cue words | Cue heuristic above 80% pairwise on training styles | Report it; make the held-out styles the headline result | A finding about shortcut vulnerability |
| No problem to solve | D0 or zero-shot above 90% pairwise | Report it as the main finding; focus analysis on the hard subsets (close intents, misleading cues) | Benchmark and negative result |
| Unstable comparison | Seed standard deviation larger than the D3 − D2 gap | Add seeds up to five; report as inconclusive if it persists | Honest result |
| Time | Interim deliverables late | Drop conditional items in the order above | D0–D3 and the frozen test |

## 9. Corrections to the reviewer bot's report

The review was useful and most of it is adopted above. Three of its statements were checked and are not right as written:

| Bot statement | What we found |
|---|---|
| "Verified citations: No" and "references remain unverified" | Every reference and both model IDs were opened and checked on 2026-11-14 (`01_FINDINGS.md`, section 5.9). What remains is reading two papers in full |
| Timeline and feasibility cited to "Project Requirements — Requirement 10" | Requirement 10 on the course page is repository quality and reproducibility. Progress through milestones is Requirement 12 |
| The slide says "every accepted message is reviewed by a person" | The plan's wording was "a generated message is kept only if a reviewer agrees", next to a table that said training gets a human sample. The bot's reading is fair and the contradiction was real; it is fixed in section 4.3 |

One point where we follow the bot with a caveat: it recommends matching class marginals between D2 and D3. That is done (section 2.2), but it cannot remove the coverage difference described in 2.3, which is inherent in the design. Only the optional D2b ablation addresses it.
