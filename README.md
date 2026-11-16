# QuotedScope

**Active Intent Classification in Single-Message Banking Support**

Undergraduate HIT course project on large language models for natural language processing. Authors: Liza and Shai.

## Problem

A banking support message can mention a request that needs action now alongside a previous request. For example, a customer might mention activating a card last week and ask to change their PIN today. Both are valid intent labels, but only one determines the support queue.

Given an English message containing two distinct banking intents, one clearly active and one historical, predict the active intent as one of the 77 BANKING77 labels.

## Proposed hypothesis

Role-aware synthetic training examples may improve a classifier's ability to follow changes in which request is active while preserving accuracy on ordinary single-intent messages.

## Research question

Does role-aware augmentation improve DistilBERT's pairwise accuracy compared with composite augmentation that uses the same source utterances and the same number of added messages?

In a Role-Swap pair, both versions mention the same source requests in the same order. Reversing their active and historical roles changes the correct label.

## Data

[BANKING77](https://huggingface.co/datasets/PolyAI/banking77) provides 77 intent labels, 10,003 official training utterances and 3,080 official test utterances. Each original utterance carries one intent label. The project will generate two-intent messages from these utterances to obtain examples with controlled roles.

## Planned dataset creation pipeline

1. Stratify the official training split into 90% training and 10% development before generation. Reserve the official test split for evaluation and audit source IDs and near-duplicates across pools.
2. Select source pairs with different intents. Specify the active request before generation and derive its label from the source utterance. Control mention order, attribution style, tone, length and intent similarity.
3. Run automated checks on all generated messages, audit a stratified human sample from each training augmentation condition, and review all composite development messages.
4. Target 300 frozen Role-Swap pairs using 600 distinct official test sources. Both team members review each candidate pair independently with labels hidden. Freeze accepted pairs before training the augmented conditions and report the actual accepted count.
5. Reserve two attribution styles for test only. Build a separate challenge set targeting 40 human-written pairs from 80 other test sources. Evaluate clean accuracy on all 3,080 original test utterances.

## Planned experimental comparison

| Condition | DistilBERT training data |
| --- | --- |
| D0 — Clean | Original single-intent training utterances |
| D1 — Paraphrase | D0 plus 2,000 single-intent paraphrases |
| D2 — Composite | D0 plus 2,000 two-intent messages. Both versions of each source pair keep the same request active. |
| D3 — Role-Aware | D0 plus 2,000 two-intent messages. The active request switches between versions of each source pair. |

Use the same DistilBERT checkpoint, clean data, training settings and three seeds across conditions. D1–D3 use the same set of 2,000 source utterances. D2 and D3 share the first version of each source pair. Their comparison measures the complete role-aware augmentation procedure, including changes to which source requests appear as active. Planned baselines include two zero-shot Qwen models, first/last-mention and cue-word heuristics, and a Qwen extract-then-classify pipeline.

The primary metric is **pairwise Role-Swap accuracy**, the share of pairs where both labels are predicted correctly. The proposed success rule requires a mean D3–D2 gain of at least **5 percentage points** over three seeds, a paired-bootstrap **95% confidence interval** with a lower bound above zero, and a D3 clean accuracy drop of **at most 2 percentage points** versus D0. Secondary results include per-message accuracy, macro-F1, and breakdowns by mention order, attribution style and intent similarity.

## Scope

The study covers single-message classification with two distinct intents and one clearly active request. Conclusions apply to the controlled benchmark and the clean BANKING77 test. Deployment and multi-turn conversations are outside the study scope.

## Status

Proposal stage. See the [proposal slides](slides/proposal/Proposal.pptx), [dated tasks](TASKS.md) and [work plan](WORK_PLAN.md). Dataset generation, training and results are pending.
