# QuotedScope - Work Plan

Undergraduate HIT course project on large language models for natural language processing. Authors: Liza and Shai.

Repository: [shaico111/QuotedScope](https://github.com/shaico111/QuotedScope). The plan draws on the proposal slides and the team's revised experimental protocol.

## 1. Project summary

QuotedScope studies how to route an English banking-support message when it mentions an active request alongside a historical one. The proposed contribution combines a controlled Role-Swap benchmark with a comparison of role-aware and matched composite training augmentation. See [README.md](README.md) for the full experiment.

## 2. Course milestones and requirements

| Milestone | Course weight per proposal materials | Evidence to prepare |
| --- | --- | --- |
| Proposal presentation | Feedback and approval | Use case, formal task, related work, data and models, evaluation and feasible schedule |
| Interim presentation | 20% | Refined problem, comparison of at least three papers, pilot data and EDA, benchmark, working baseline and preliminary metrics, risks and completion plan |
| Final presentation | 40% | Reproducible methodology, fine-tuning and baseline results, success criteria, error analysis and limitations |
| GitHub submission | 40% | README, slides, permitted data samples, access instructions, code, configurations, dependencies and reproducible results |

### 2A. Dated schedule and finish line

Updated 2026-11-16. All dates use Asia/Jerusalem. These are proposed QuotedScope targets. Confirm presentation slots, the submission cutoff and required model scope through the lecturer or Moodle.

| Milestone | Internal target | Output |
| --- | --- | --- |
| Proposal follow-up | 2026-11-18 | Deck and lecturer feedback, if available (QS-01) |
| Split and leakage checks | 2026-11-22 | Dataset inventory, source manifest and overlap report (QS-02) |
| Generation pilot | 2026-11-29 | Pilot examples, rejection log, reviewer calibration and GPU timing (QS-04) |
| Development set and protocol | 2026-12-06 | Locked rules, reviewed development set, paper comparison and metrics (QS-03, QS-05) |
| Interim evidence | 2026-12-10 | Frozen and hashed Role-Swap test, pilot EDA and development baselines (QS-06–QS-08) |
| Interim deck ready | 2026-12-13 | Slides, evidence and rehearsal (QS-09) |
| Full augmentation and challenge set | 2026-12-20 | Matched generated arms, QA and reviewed human-written pairs (QS-10, QS-12) |
| Matched runs and baselines | 2026-12-27 | Three seeds per DistilBERT condition and final baseline predictions (QS-11, QS-13) |
| Analysis complete | 2027-01-03 | Primary comparison, confidence interval, clean retention and error analysis (QS-14) |
| Final deck ready | 2027-01-07 | Results, figures and rehearsal (QS-15) |
| Repository ready | 2027-01-17 | Reproducible release candidate and submission review (QS-16) |

[**TASKS.md**](TASKS.md) lists task start dates and dependencies. Split sources before generation. Lock test prompts and review rules on development material and freeze the reviewed test before training the augmented conditions.

If the plan slips, defer a RoBERTa repeat, D2b ablation and Qwen LoRA in that order, subject to the lecturer's model requirements. Preserve the paired test, clean-test comparison and reproducibility. Use the pilot to measure review throughput and report actual accepted sample counts.

## 3. Phase plan

### Phase 0 - Proposal preparation

- [x] Add the editable proposal presentation to `slides/proposal/`.
- [ ] Record lecturer feedback and any changes to the protocol, course dates or model scope (QS-01).

### Phase 1 - Dataset inventory and split

- [ ] Verify BANKING77 terms, count utterances per label and inspect source-label quality (QS-02).
- [ ] Freeze the stratified training/development split and reserve the official test. Log exact and near-duplicate checks and assign disjoint source IDs to the Role-Swap and human-written challenge sets (QS-02).
- [ ] Compare at least three related papers on intent, revisions or counterfactual evaluation (QS-03).

### Phase 2 - Generation design and review calibration

- [ ] Pilot prompts and structured records on development sources. Validate schema, label provenance, role clarity and duplicate checks (QS-04).
- [ ] Time the proposed local generator, Llama 3.1 8B Instruct. Compare automatic checks with human review. Lock prompts, acceptance rules and generation settings before constructing the test (QS-04, QS-05).

### Phase 3 - Synthetic data generation and QA

- [ ] Have one reviewer check all composite development messages. Target 300 frozen test pairs. Both reviewers independently check each candidate pair with labels hidden. Reserve two attribution styles for test only (QS-05, QS-06).
- [ ] Generate D1–D3 training data. Run automatic checks on each message and require at least 95% validity in each arm's stratified audit of 120 messages. Log rejection reasons, active-label balance and D2/D3 wording overlap (QS-08, QS-10).
- [ ] Target 40 human-written pairs. Have the team member who did not write each pair review it with labels hidden (QS-12).

### Phase 4 - Models and training

- [ ] Build the evaluation harness. Run D0, heuristic and initial Qwen zero-shot baselines on development data for the interim (QS-07).
- [ ] Train DistilBERT in D0–D3 using the same checkpoint, clean data, configuration, validation selection rule and three seeds (QS-11).
- [ ] Evaluate Qwen2.5-1.5B-Instruct, Qwen3-4B-Instruct-2507, mention-order and cue-word heuristics, and the Qwen extract-then-classify baseline with fixed configurations (QS-13).

### Phase 5 - Evaluation and analysis

- [ ] Compute mean D3–D2 pairwise accuracy over three seeds. Bootstrap whole pairs in both arms to obtain the 95% interval and report seed variability separately (QS-14).
- [ ] Apply the success rule from [README.md](README.md). Report per-message accuracy, macro-F1, clean retention, shortcut and held-out style results, human-written challenge scores and representative errors (QS-14).

### Phase 6 - Milestone deliverables

- [ ] Prepare the interim deck with related work, EDA, baseline results and remaining work (QS-09).
- [ ] Prepare the final presentation around the measured comparison, trade-offs and limitations (QS-15).
- [ ] Package slides, code, configurations, dependencies, permitted samples, split/test manifests and machine-readable results. Verify the documented commands in a clean environment (QS-16).

## 4. Methodological guardrails

- Keep training, development and test source pools disjoint. Use development data for prompt and model decisions. Record the frozen test hash and the cross-pool near-duplicate audit.
- Match D2 and D3 on source pairs, augmentation count and generation settings. Share the first message of each pair, balance mention order, and report remaining active-label differences plus the effect of the D2 rewording filter.
- Interpret D3–D2 as a comparison of full augmentation procedures. D3 also changes the diversity of active wordings and exposes both labels per source pair. D2b is an optional follow-up on the effect of pairing.
- Keep source IDs, pair IDs, labels, prompt/model versions and QA decisions. Report reviewer agreement, exclusions, actual sample sizes and inconclusive results.

## 5. Open questions

- Confirm the interim and final presentation slots and the official submission cutoff.
- Confirm whether the course requires a second fine-tuned model family alongside DistilBERT across four data conditions.
- Check generation quality, compute time and review throughput in the local pilot. Verify redistribution terms before publishing data artifacts.

## 6. Sources

- Team proposal: [Proposal.pptx](slides/proposal/Proposal.pptx).
- Dataset: [BANKING77](https://huggingface.co/datasets/PolyAI/banking77).
- Course references: [Project proposal presentation guide](https://www.apartsin.com/teaching/guides/project-proposal-presentation.html) and [course guides](https://www.apartsin.com/teaching/guides/index.html#section-about-genai-llm-courses).
