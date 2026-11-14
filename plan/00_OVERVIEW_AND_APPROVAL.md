# QuotedScope proposal kit — plan overview and approval sheet

Status: **waiting for approval. Nothing has been built yet.** The only files created are the five plan files in this folder.

Read in this order:

| File | What it covers |
|---|---|
| `00_OVERVIEW_AND_APPROVAL.md` (this file) | What will be built, in what order, and the decisions I need from you |
| [01_FINDINGS.md](01_FINDINGS.md) | What I found in the repo, the course slides and Sasha's website, and where the current draft falls short |
| [02_REPO_AND_BUILD.md](02_REPO_AND_BUILD.md) | Repository structure, file list, how the deck is built, git steps |
| [03_DECK_AND_SCRIPT.md](03_DECK_AND_SCRIPT.md) | Slide-by-slide plan for the English deck and the Hebrew script |
| [04_GUIDE_DOCUMENT.md](04_GUIDE_DOCUMENT.md) | Plan for the explainer document for Shai and Liza (overview, per-slide explanation, Q&A, narrative) |
| [05_PROTOCOLS.md](05_PROTOCOLS.md) | **Revision 2.** Split manifest, D2/D3 fair-comparison protocol, generation and QA protocol, decision rule, related-work table, schedule, risks. Written in response to the Proposal Reviewer bot; it governs where older text disagrees |

**Revision 2 changes (after the reviewer bot's first report):** six core slides instead of five (template slide 3 split into pipeline and experiment); D2/D3 claim narrowed to "the complete role-aware intervention against a matched composite control"; explicit split manifest; per-split QA rules with two blind reviewers on every test pair; a misleading-cue held-out style against cue-word shortcuts; a pre-fixed decision rule with three seeds; generator model proposed; course level and hardware confirmed (undergraduate HIT; one RTX 4080 16 GB; the proposal presentation is ungraded, "for feedback and approval only").

## 1. What you asked for

From `CLAUDE.md`: a repo holding a project proposal **presentation (English)**, a **script (Hebrew)** for Shai and Liza, and a **document that explains the project** to someone who has to present it without knowing it: general overview, each slide's content, purpose and script, likely questions with suggested answers, and a narrative of how the project evolved.

## 2. Starting point

- `project-idea.md` already holds a full draft of all three items (a Marp deck, a Hebrew script, a 20-part Hebrew guide). It was written in a chat session and says so itself: no files were produced and the rendered deck was never checked.
- The research design in that draft is sound and I plan to keep it: four DistilBERT conditions (D0 to D3), D3 vs D2 as the main comparison, 300 frozen Role-Swap pairs, no RAG, agent or UI.
- So this is a **rebuild and upgrade**, not a new design. The work is: check it against Sasha's actual guides, fix the gaps, verify every citation, and turn it into real files (PPTX, PDF, Markdown, repo structure).

## 3. Deliverables

| # | Deliverable | Language | Format | Location |
|---|---|---|---|---|
| 1 | Proposal deck: 5 core slides + backup slides | English | PPTX + PDF | `slides/proposal/` |
| 2 | Presentation script, split between Shai and Liza, with timings and cues | Hebrew | Markdown (also pasted into the PPTX speaker notes) | `docs/script_he.md` |
| 3 | Explainer guide for Shai and Liza | Hebrew, English technical terms | Markdown | `docs/guide_he.md` |
| 4 | Project narrative: how the idea evolved | Hebrew | Section of the guide, plus a one-page standalone | `docs/narrative_he.md` |
| 5 | Q&A bank: about 30 questions with answers and who answers | Hebrew, questions also in English | Section of the guide | `docs/guide_he.md` |
| 6 | README matching Sasha's GitHub guide, proposal-stage version | English | Markdown | `README.md` |
| 7 | Repo skeleton for the later stages (empty folders with short READMEs) | English | folders | see file 02 |
| 8 | Figures: visual abstract, pipeline, Role-Swap example | English | PNG + source | `visuals/` |
| 9 | Verified reference list | English | Markdown | `docs/references.md` |

## 4. The main changes compared to the current draft

Details and reasons are in [01_FINDINGS.md](01_FINDINGS.md), section 5.

1. **Lighter slides.** The draft slides are mostly tables and bullet walls. Sasha's presentation guide asks for one idea per slide and figures over text. Dense material moves to backup slides and to the script.
2. **Three required visuals.** His assessment criteria name a visual abstract, concrete input/output datapoints, and a comparison against a baseline. The draft has none of them as figures.
3. **Evidence on slide 1.** His proposal guide requires at least one piece of evidence for the use case. The draft says only "illustrative example".
4. **Problem statement in his template form** ("Given [input], build a system that produces [output] to support [use case], under [constraints]").
5. **Reused / adapted / new** shown on the novelty slide. This is an explicit grading criterion.
6. **Concrete data examples and expected errors.** The guide asks for them at proposal stage. I will add a real D2 vs D3 example and three expected failure types.
7. **Synthetic-data justification**: why real data is not enough, which attributes are controlled, how validity is checked. The generator model is not named anywhere in the draft; it needs to be.
8. **Citation check, done.** All references and both Qwen model IDs exist (findings 5.9). One problem: the paper the draft uses as evidence on slide 1 (Zamula & Loskutov) reports that quoted text is very common but that its LLM and fine-tuned encoder were **not** affected by it. Slide 1 will cite it for prevalence only, and the deck will say openly that whether models fail on our harder case is what the experiment measures.
9. **Differentiation from past course projects.** Sasha's site lists four past projects close to this area. The guide gets a prepared answer for each.

## 5. Order of work after approval

1. Write `docs/references.md` from the verified list (verification is done; reading the full papers behind the novelty claim is still to do).
2. Scaffold the repo (folders, README, requirements, LICENSE decision).
3. Build figures.
4. Build the deck with a script (`slides/proposal/build_deck.py`), render to PDF, and look at every slide image to check overflow and readability.
5. Write the Hebrew script; time it by word count against the target per slide.
6. Write the guide, the narrative and the Q&A bank.
7. Consistency pass: numbers, terms and claims identical across deck, script, guide and README.
8. Hand over to you for a pass through Sasha's Proposal Reviewer bot (it needs a ChatGPT login, so that step is yours), then apply its feedback.
9. Commit on a branch and open a PR, only when you say so.

## 6. Decisions I need from you

Recommended option first in each row. If you just say "approved", I go with the recommendations.

| # | Decision | Recommendation | Alternative |
|---|---|---|---|
| A | Slide count | **Changed in revision 2:** 6 core slides (template slide 3 split in two) plus 6 backup slides | 5 core slides exactly as the template, with a denser slide 3 |
| B | Deck tool | Native PPTX generated by a Python script (`python-pptx`), editable in PowerPoint, Hebrew script in speaker notes. Python 3.12 is installed; Node and Marp are not | Marp Markdown, which requires installing Node |
| C | Language of the guide | Hebrew with English technical terms, like the existing draft | English |
| D | Talk length | Plan for 8 minutes. No time limit appears in any guide I read | Give me the limit from Moodle or from Sasha |
| E | Course level | **Resolved:** the syllabus in `Info/` is the undergraduate HIT course "Large Language Model for Natural Language Processing". Graduate-only requirements do not apply | Tell me if that is wrong |
| F | Synthetic data generator | **Changed in revision 2:** Llama 3.1 8B Instruct locally through Ollama, with Mistral 7B as the automatic judge. A non-Qwen generator, so the Qwen baselines are not tested on text Qwen wrote. Hosted fallback through the course's OpenRouter budget | You pick another |
| G | `project-idea.md` | Keep it in the repo under `docs/archive/` as the record of the draft | Leave it untracked, or delete it |
| H | Repo visibility and registry | Sasha's prep guide requires a **public** GitHub repo, registered with team members in the course registry spreadsheet on Moodle, before the presentation. I do not know whether `shaico111/QuotedScope` is public or registered | You confirm; registration is yours to do |
| I | Liza's full name and GitHub user | Needed for the title slide and the README team section | Leave first names only |

## 7. Things I could not find out

- The presentation date and the allotted time.
- The real history of the idea before `project-idea.md`. The narrative can be reconstructed from that file (it mentions earlier versions and a "latest review" that fixed novelty, D2/D3 and the statistics), but anything earlier I would be inventing. Two or three sentences from you on how you got to this idea would make the narrative true instead of plausible.
- Interim presentation week: the course PDF says week 9, the website says week 8. The guide will say "week 8 or 9, confirm on Moodle".
- Whether the instructor has already heard the idea. The draft says novelty approval "is not guaranteed".
