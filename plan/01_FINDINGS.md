# Findings from the exploration

What was read: every file in the repo, the course project PDFs under `Info/Course Slides/0_Intro/`, and these pages on apartsin.com: the proposal guide (Hebrew), the guides index, project requirements, assessment criteria, in-class presentation prep, GitHub submission, technical presentations, course expectations, synthetic data generation, the course page, the course bots page and the past student projects list.

Not read: the remaining course lecture PDFs (about 70 topic decks, not about the project), the syllabus PDF, the midterm and final presentation guides, and the HIT syllabus portal link.

## 1. The repository today

| Item | State |
|---|---|
| `README.md` | One line: `# QuotedScope` |
| `CLAUDE.md` | The task description |
| `project-idea.md` | 1,583 lines, untracked. Full draft of the deck (Marp), the Hebrew script and a 20-part Hebrew guide |
| `Info/` | Course slides as PDF, syllabus, five link shortcuts. Ignored by git (`.gitignore` line 221). This is correct: the slides are the lecturer's material and should not be published |
| Git | Branch `main`, three commits, remote `github.com/shaico111/QuotedScope` |
| Tooling on this machine | Python 3.12, PyMuPDF, `pdftotext`. No Node, no Marp |

## 2. The project as the draft defines it

- **Problem:** a customer message can mention several intents, but only one is the active request. Example: "Last week I needed to activate my card. That issue is resolved. Today I need to change my PIN." Correct label: `change_pin`.
- **Task:** single English banking message with exactly one active intent, output one of the 77 BANKING77 labels.
- **Proposed contribution:** a Role-Swap benchmark (same two source requests, same order, roles exchanged, so the correct label flips), a controlled augmentation study, and a small human-written challenge set.
- **Core experiment:** one model, DistilBERT, under four training conditions.

| Condition | Extra training data |
|---|---|
| D0 Clean | none |
| D1 Generic | 2,000 paraphrases |
| D2 Composite-Unpaired | 2,000 composites; for each source pair the same request stays active in both versions |
| D3 Role-Aware | 1,000 role-reversal pairs, 2,000 messages, built from the same source pairs as D2 |

- **Main comparison:** D3 vs D2, on 300 frozen, source-disjoint Role-Swap pairs.
- **Main metric:** Pairwise Role-Swap Accuracy: a pair counts only if both versions are classified correctly.
- **Other comparisons:** off-the-shelf Qwen2.5 and Qwen3 zero-shot, Qwen2.5-1.5B with LoRA if the GPU allows, scope extraction followed by a clean classifier, two heuristic baselines.
- **Targets (planned, not results):** +5 points pairwise for D3 over D2, at most 2 points lost on clean BANKING77 accuracy.
- **Out of scope:** RAG, agents, UI, server, multi-turn conversations, two active intents, languages other than English.

## 3. What the course requires

### 3.1 From the course PDFs (`1022_CourseStructure`, `1023_ProjectRequirements`)

- Project scope: define a novel task or research question, generate synthetic training and evaluation data, train or fine-tune models, compare with off-the-shelf pretrained models. The slide says: a few fine-tuned models against a few off-the-shelf models.
- "Best approach to identify a novel task: a task with no data."
- Avoid a project that is an exercise, and avoid one that is infeasible in a semester. Wanted: technical depth, several components, non-trivial design decisions.
- "Focus on project novelty." The project is not a software engineering exercise and not a perfect-model exercise.
- Milestones: week 5 proposal, week 9 interim (data generation, EDA, baseline, three or more papers), week 13 final, GitHub repo two weeks after the last class.
- **Proposal template, five slides**, with the note "adapt to your project, add information, details, specifics, more slides":
  1. Motivating use case: background, why it matters, why it is hard, how it is solved today.
  2. Task description: formal problem statement, inputs and outputs, novelty.
  3. Models and methods: pipeline, model or technique per step, adjustments and fine-tuning.
  4. Data specification and generation: requirements, dataset, labeling, what synthetic data and how.
  5. Metrics and KPIs: how results are measured, overall quality and quality of each step, protocols, ground truth.
- Repo: meaningful name; folders for slides (PPT and PDF), code, data, results, visuals; README with motivation, problem statement, visual abstract, datasets, augmentation and generation methods, input/output examples, models and pipelines, training, metrics, results, repository structure, team members.
- Common pitfalls listed: focusing only on accuracy, ignoring formal requirements (slides, repo formats, dates), low presentation quality, leaving coding to the end.

### 3.2 From Sasha's proposal guide (six parts, each with "present / requirements / avoid")

| Part | Requirement that matters most for us |
|---|---|
| Motivating use case | Specific and **evidence-based**. At least one of: real-system examples, user scenarios, scientific or professional sources, existing products, representative input/output examples, statistics. Avoid claiming no solution exists without surveying |
| Task definition | Input, output, processing task, target users, operating conditions. Template sentence: "Given [input], build a system that produces [output] to support [use case], while meeting [constraints]" |
| Innovation | Based on a survey of prior work. For each claim: what exists, what is reused, what limitation remains, what the project adds. A new evaluation protocol, a new dataset, and a comparison of methods not compared before are all listed as valid innovation. A new architecture is not required |
| Models and methods | Whole pipeline, not one model call. Why each model fits, which alternatives were considered. **At least one trained or fine-tuned model**, not only zero-shot or few-shot |
| Data | Source, counts, what a sample contains, labels, train/validation/test split. For synthetic data: why real data is insufficient, which properties are controlled, how diversity is created, how correctness is checked, how synthetic data stays separate from test. Name the risks: label noise, leakage, duplicates, imbalance. Avoid evaluating only on synthetic data and treating generated labels as absolute truth |
| Metrics | Metrics with justification, ground truth, test set construction, compared systems, what counts as success. At least one baseline, component-level and end-to-end evaluation. Avoid choosing metrics after seeing results |
| Extra section | Show **concrete examples**: representative data, pipeline stages, expected input and output, and **errors you expect to see**, each with a cause |

The page ends with a ten-item submission checklist. It will be copied into the guide as the final pre-presentation check.

One detail worth using: among the guide's own "model error examples" is a spam false positive caused by a marketing phrase **inside a quoted reply**. The lecturer's own example of a model error is an instance of our problem. It can be mentioned in one sentence on slide 1.

### 3.3 Assessment criteria (six, no weights given)

1. Problem-domain understanding beyond common sense, backed by sources.
2. Depth of solution-space exploration: alternatives compared, trade-offs explained.
3. Novelty and effective reuse: **clearly separate reused, adapted and new components**.
4. Correct and rigorous methodology: splits, leakage, baselines, error analysis, reproducibility.
5. Present and defend clearly. Three minimum visual elements: **a visual abstract (problem → method → result), concrete input/output datapoints, a results comparison against a baseline**.
6. Reasonable effort toward good performance.

### 3.4 Other guides

- **Course expectations:** the target is scientific innovation (a novel element in problem, method or result). Product value is the floor: a plausible real user. Engineering serves the science. Our project fits: benchmark plus controlled study, with support routing as the plausible use.
- **Technical presentations, ten tips:** tell a story; justify the real choices and skip the obvious ones; rehearse and record; one idea per slide; show only what the audience should read; precise terms; pronounce names correctly; deliver real technical content; spend time where the information is; show it with figures.
- **In-class prep:** public GitHub repo registered in the course registry before presenting; self-evaluate with the course bot over several rounds; rehearse with a timer; after the talk apply feedback and upload slides as PPT and PDF.
- **Synthetic data:** decide labels and variety attributes before prompting; keep the label separate from the variety attributes; do not let one attribute correlate with the label; small batch, inspect, then scale; save one row per sample with label, prompt, model and output; never trust labels you have not verified.
- **GitHub submission:** recommended tree `slides/`, `src/`, `notebooks/`, `data/`, `configs/`, `results/`, `visuals/`, `tests/`; README sections as in 3.1 plus reproducibility, team and contributions; at least four captioned figures by the final submission.
- **Requirements page:** thirteen core requirements; six more for graduate students (three genuinely different models, ablations, repeated runs, mini-paper).
- **Course bots:** a Proposal Reviewer bot for the LLM course exists as a custom GPT. It needs a ChatGPT login.

## 4. Past projects on Sasha's site (54 Language AI projects listed)

Closest to ours:

| Project | What it is | How ours differs |
|---|---|---|
| LazyspondAI (2026) | Intent classification of noisy Hebrew and "Heblish" Instagram messages | Their difficulty is noise and language mix. Ours is which of two clean intents is active |
| Patient Portal Triage (2026) | DistilBERT routing of noisy patient-portal messages, local-LLM synthetic data | Same model family and routing use. No counterfactual pairs, no role attribution |
| AnySys Triage (2025) | Support-ticket triage that adapts to any product | Domain adaptation, single-intent tickets |
| QUCKASSIST (2025) | LLM insights for customer support | Support domain, different task |
| RegulAItion (2025) | Decision support for banking clerks | Banking domain only |
| Semantic API Diagnosis (2026), Adversarial Agreement Benchmark (2025) | Controlled benchmarks | Precedent: "a controlled benchmark" is an accepted project type in this course |

None of the 54 deals with active versus historical intent, quoted content, or role-swap evaluation. Intent classification and support triage are, however, well-trodden here. The pitch has to lead with the Role-Swap benchmark and the D2/D3 study, and not with "intent classification for banking".

## 5. Gaps in the current draft

### 5.1 Slides are too dense
Slides 3, 4 and 5 of the draft each carry a pipeline or list, a table, and four to six bold bullet lines, at 20 to 25 px. This contradicts tips 4, 5 and 10 and would likely overflow when rendered. The draft itself notes the rendered deck was never checked.

### 5.2 The three required visuals are missing
No visual abstract, no figure of a datapoint, no comparison layout. At proposal stage there are no results, so the third becomes an empty "planned comparison" table with the baselines named and the cells blank.

### 5.3 No evidence on slide 1
The draft footer says "Illustrative example. Related evidence: Zamula & Loskutov (2026)". The guide wants explicit evidence. After the citation check in 5.9, the honest options are: Zamula & Loskutov for prevalence only (quoted text in 96% of 300 real customer replies; it broke a keyword classifier but not the LLM or the encoder); the BANKING77 fact that every message carries exactly one intent (so composite messages are outside what standard benchmarks test); a small pilot (see 5.10).

### 5.4 Problem statement not in the template form
All the pieces exist in the draft; they are just not written as the one sentence the guide asks for.

### 5.5 Reused / adapted / new is only in the guide
It is an assessment criterion, so it belongs on slide 2 as a three-column strip.

### 5.6 No concrete data examples beyond the one use case, and no expected errors
The D2 vs D3 difference is the heart of the project and the draft explains it only in words. Needed: one source pair shown as its two D2 versions and its two D3 versions. Also needed: three expected error types with causes (recency bias: picks the last-mentioned intent; cue overfitting: keys on "now" or "today" and fails on unseen styles; intent confusion between near-duplicate BANKING77 labels, unrelated to roles).

### 5.7 Synthetic data section incomplete
Missing: why real data is insufficient (no public dataset marks active versus historical intent; real bank messages are private), the list of controlled attributes as a table (active intent, historical intent, mention order, attribution style, tone/length), and the name of the generator model. Sasha's guide also warns about one attribute correlating with the label; in our case that is **mention order and cue words**, and the design already balances order. This should be stated on slide 4 because it shows the guide was read.

### 5.8 Off-the-shelf comparison is marked optional in places
The draft says Qwen LoRA is "subject to GPU verification", which is fine: DistilBERT already satisfies the fine-tuning requirement. But the course scope also requires comparison with off-the-shelf models, so Qwen zero-shot must be stated as a firm part of the plan, not as optional.

### 5.9 Citations: checked on 2026-11-14, all exist, one cuts against the draft's claim
Each link in the draft was opened in the browser and its title, authors and abstract read. I read abstracts only, not the full papers.

| Reference in the draft | Result |
|---|---|
| Casanueva et al. 2020, BANKING77 | Dataset card exists at `PolyAI/banking77`, license `cc-by-4.0`, arXiv 2003.04807. The 10,003 training count is confirmed by the Ying & Thomas abstract. The 3,080 test count was not on the part of the page I read; confirm when the data is downloaded |
| Ying & Thomas 2022, "Label Errors in BANKING77" | Exists. Abstract: over 1,400 (14%) of the 10,003 training utterances "may have been incorrectly labelled", found by two automated approaches. The draft's careful wording ("suspected") is right |
| Elazar et al. 2024, CAT (Findings of EMNLP) | Exists. CAT replaces **part of the input with its counterpart from a different example** and expects an attentive model to change its prediction. Tested on ten datasets across four tasks such as NLI and reading comprehension |
| Goel et al. 2023, PRESTO (EMNLP main) | Exists. Over 550K multilingual human–assistant conversations with disfluencies, code-switching and revisions |
| Tack, Laban & Neville 2026, arXiv 2607.20734 | Exists. Turns static single-turn tasks into multi-turn conversations in which the user's intent evolves, and studies how well LLMs track it |
| Zamula & Loskutov 2026, injoit.org | Exists; full text is in Russian. **See the warning below** |
| `Qwen/Qwen2.5-1.5B-Instruct`, `Qwen/Qwen3-4B-Instruct-2507` | Both model pages exist on Hugging Face |

**Warning on Zamula & Loskutov.** The draft cites it as evidence that quoted content harms intent classification. The abstract says something narrower. On 300 real customer replies, quoted text was present in 96% of them and it broke the **keyword** baseline. But "both the LLM and the encoder are insensitive to quoted text (p ≥ 0.75)". So the paper supports two statements and contradicts a third:

- Supports: quoted or historical content is very common in real support traffic (96% of replies).
- Supports: shallow, keyword-style methods are destroyed by it.
- Contradicts: that fine-tuned encoders or LLMs are harmed by it. In their setting they were not.

What this means for us:
1. Slide 1 may cite it for prevalence only. Citing it as evidence that models fail would be wrong, and Sasha could check.
2. It makes "wouldn't a modern model simply get this right?" the most likely hard question. The honest answer: in their binary task (continue the conversation or not) the quoted text did not carry a competing label. In ours the historical request is a valid intent from the same 77-label set, which is a harder case, and whether models fail at it is exactly what D0 and the zero-shot baseline will measure. A possible outcome is that D0 already does well; the project must say so up front.
3. It raises the value of the optional pilot in 5.10, since we have no evidence yet that the failure exists.

**Note on CAT.** Its mechanism, swapping a part of the input and expecting the prediction to change, is closer to Role-Swap than the draft's one-line description suggests. The difference to state: CAT swaps in content from another example to test whether the model attends to that part at all; Role-Swap keeps both requests and exchanges only their roles, and we also use the pairs for training. The novelty answer in the guide will be written against this, not against a vague "counterfactual testing".

### 5.10 Optional strengthening: a tiny pilot
Twenty hand-written Role-Swap pairs run through one off-the-shelf zero-shot model would give a real number for slide 1 ("in a 20-pair pilot, pairwise accuracy was X%"). It answers the question "do models actually fail at this?" with data. It needs about an hour and a model to call. It is not required for a proposal, so it is listed as optional; say if you want it.

### 5.11 Small inconsistencies in the draft
- Interim milestone: the guide table says week 9; Sasha's website says week 8.
- The name "QuotedScope" suggests quoted text, while the task is active versus historical intent, where quoting is one of six styles. One sentence in the guide should explain the name.
- A useful fact the draft never states outright: a classifier that always picks the first-mentioned or the last-mentioned request scores **0% pairwise** by construction, because mention order is identical in both versions of a pair. This is the clearest argument for the metric and belongs on slide 5.

## 6. What is already good and stays

- Scope discipline: no RAG, agent, UI.
- The D0 to D3 design with matched budgets and shared source pairs.
- Honest wording: no invented results, novelty stated as a proposal, the D3 vs D2 confound admitted.
- Leakage controls: source-disjoint splits, frozen test, held-out styles, human-written challenge.
- The 20 prepared questions, the glossary, and the division of speaking roles.
