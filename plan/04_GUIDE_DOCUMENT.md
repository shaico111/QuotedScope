# Plan for the explainer guide (`docs/guide_he.md`)

Reader: Shai or Liza, the evening before the presentation, needing to understand the whole project well enough to present any slide and answer questions about any part. Assume the reader has not seen the project before. Language: Hebrew with English technical terms (decision C).

The existing 20-part draft guide is the base. It is thorough but reads as a reference list of short fragments. The rebuild reorders it so it can be read front to back once, and adds what is missing.

## 1. Table of contents

| Part | Title | Source | Length |
|---|---|---|---|
| 0 | How to use this guide: what to read if you have 10 minutes, 1 hour, or an evening | new | half a page |
| 1 | The project in 30 seconds, and in 3 minutes | draft part 20, extended | 1 page |
| 2 | The story: how the idea evolved | draft part 1, rewritten as narrative | 2 pages |
| 3 | The task, exactly: input, output, what is excluded, rejection rules | draft part 2 | 1 page |
| 4 | Role-Swap explained with one worked example | draft part 3 | 1 page |
| 5 | What is new and what is not; reused / adapted / new; related work | draft part 4, references verified | 2 pages |
| 6 | Data: BANKING77, splits, leakage, quantities | draft part 5 | 2 pages |
| 7 | The four training conditions, and what D3 vs D2 does and does not show | draft part 6, plus worked example | 2 pages |
| 8 | Synthetic data generation and QA | draft parts 7 and 8, plus Sasha's synthetic-data rules | 2 pages |
| 9 | Models and baselines | draft part 9 | 2 pages |
| 10 | Metrics and statistics, with a worked calculation | draft part 10, plus the 0% argument | 2 pages |
| 11 | **Slide by slide**: content, purpose, script, what to point at, likely questions | draft part 11, rebuilt | 5 pages |
| 12 | **Questions and answers** | draft part 12, extended to about 30 | 5 pages |
| 13 | How the project maps to Sasha's requirements and assessment criteria | new | 1 page |
| 14 | Work plan, milestones, responsibilities | draft parts 13 to 15 | 1 page |
| 15 | Glossary and pronunciation | draft part 16 | 1 page |
| 16 | Things not to mix up, and things not to say | draft part 17 | half a page |
| 17 | Checklist before presenting (Sasha's ten items plus ours) | draft part 18, replaced by his official list | half a page |
| 18 | Sources | draft part 19, verified | half a page |

## 2. Part 11, slide by slide: the fixed format

For each of the six core slides (see `03_DECK_AND_SCRIPT.md`, revision 2), the same six blocks, so the reader always knows where to look:

1. **What is on the slide**: a thumbnail image of the slide and a list of each element.
2. **Why this slide exists**: which of Sasha's template questions and which assessment criterion it serves.
3. **The one sentence to remember**: if the presenter forgets everything else.
4. **The script**: the full Hebrew text for that slide, same as in `script_he.md`.
5. **Behind the script**: the reasoning the presenter does not say but must know. For example, on slide 3, why D2 uses the same source pairs as D3.
6. **Questions this slide tends to trigger**, with links to the answers in part 12.

The backup slides get a short version: what it shows and which question brings it up.

## 3. Part 12, the Q&A bank

About 30 questions. Each entry: the question in English (as Sasha may ask it) and in Hebrew, a short answer of two or three sentences to say aloud, a longer explanation for understanding, who answers first, and which backup slide to show.

Grouped by theme:

**Novelty and prior work**
- What exactly is novel?
- Isn't this already covered by counterfactual testing work such as CAT?
- How is this different from past course projects: LazyspondAI, Patient Portal Triage, AnySys Triage? *(new)*
- Is this scientific innovation or only an engineering exercise? *(new, from the course-expectations guide)*
- Why the name QuotedScope? *(new)*

**Motivation**
- How do you know real customers write like this?
- Wouldn't a modern LLM simply get this right? Zamula & Loskutov found their LLM and encoder insensitive to quoted text. *(new, and the most likely hard question; see findings 5.9. The answer: their task was binary and the quoted text carried no competing label; ours puts a second valid intent in the message. Whether that is enough to break models is what D0 and the zero-shot baseline measure, and "it is not" is a possible result)*
- Who is the user of this system? *(new)*

**Task and scope**
- Why only one active intent? What about two active requests?
- Why a single message and not a conversation?
- Why BANKING77?
- Why English only? *(new)*

**Experiment design**
- How do you isolate the role-swap effect?
- Why do you need D1? Why D2?
- Why not a D4 that pairs without reversing? *(new)*
- Why DistilBERT and not a larger model? *(new)*
- Why fine-tune Qwen as well?
- What if Qwen outputs a sentence and not a label?
- Why not simply extract the active request?

**Data**
- Why synthetic data? Why is real data not enough? *(new)*
- Which model generates the data, and doesn't it bias the test? *(new)*
- How do you validate synthetic labels?
- Is BANKING77 ground truth reliable?
- How do you prevent leakage?
- Can the model solve the benchmark by spotting "now" or "previously"?
- Why is the human-written set so small?

**Metrics**
- Why pairwise accuracy and not plain accuracy?
- Are the 300 pairs statistically independent? What does the confidence interval mean?
- Where does the 5-point target come from? *(new; the honest answer is that it is a planning threshold we chose)*
- Does pairwise accuracy prove the model understands?

**Feasibility and outcome**
- Can you finish in a semester? What is the fallback if there is no GPU?
- What if role-aware training loses?
- What would you do with more time? *(new)*

Rule for every answer: say what is known, say what is not, and never claim a result. Where the honest answer is "we don't know yet, that is what the experiment is for", the entry says so.

## 4. Part 2, the narrative

Purpose: let a presenter tell the project as a story with a through-line, which is the first of Sasha's presentation tips, and understand why each design choice was made.

Structure, as a sequence of "we wanted X, hit problem Y, so we decided Z":

1. **The plain task.** Intent classification is old and largely solved on clean benchmarks.
2. **The observation.** Real messages carry history: resolved issues, quoted earlier requests. A message can name two intents while asking for one.
3. **The gap in the data.** BANKING77 has one intent per message and no notion of role. There is no public data for the question. This is the course's own recipe for novelty: "a task with no data".
4. **First idea: generate composite messages.** Then the worry: a model could do well on them by shortcut, such as always picking the last request.
5. **The Role-Swap idea.** Keep the requests and their order, swap the roles. Shortcuts based on position now fail by construction.
6. **From benchmark to experiment.** A benchmark alone measures. The course requires training. So: does training on role reversals help? That needs controls, which gives D0 to D3.
7. **The tightening.** Review rounds that sharpened the claims: novelty stated as protocol plus study and not as a new method; the admission that D3 vs D2 does not isolate pairing; source-disjoint test pairs for the statistics; a human-written set against generator artefacts.
8. **The cuts.** What was left out on purpose and why: RAG, agents, UI, conversations, multiple active intents.
9. **Where it stands.** A proposal: design fixed, nothing run, novelty to be confirmed by the lecturer.
10. **What happens next.** Milestones to interim and final.

**Limit I want to be clear about:** steps 1 to 6 are reconstructed from the logic of `project-idea.md`; step 7 from its references to earlier versions. I do not know how the idea actually came about. If you give me the real origin in a few sentences (what you first considered, what feedback changed it), the narrative will use that. Otherwise it will be labelled in the guide as "the logic of the project" and not as its history.

A one-page standalone version goes to `docs/narrative_he.md`.

## 5. Part 13, requirements map

A table the presenters can glance at to see that nothing required is missing:

| Requirement (source) | Where we meet it |
|---|---|
| Novel task, "task with no data" (course slides) | Slide 2; guide part 5 |
| Synthetic training and test data (course slides) | Slide 5 |
| At least one fine-tuned model (proposal guide) | Slide 4: DistilBERT D0 to D3 |
| Comparison with off-the-shelf models (course slides) | Slides 4 and 6: Qwen zero-shot |
| Evidence-based use case (proposal guide) | Slide 1 footer |
| Novelty based on prior-work survey (proposal guide) | Slide 2, backup B1 |
| Reused / adapted / new (assessment criterion 3) | Slide 2 strip |
| Full pipeline, not one model call (proposal guide) | Slide 3 diagram |
| Data source, counts, splits, risks (proposal guide) | Slide 5, backup B3 |
| Baseline and success criteria (proposal guide) | Slide 6 |
| Component and end-to-end evaluation (proposal guide) | Slide 6 |
| Concrete examples and expected errors (proposal guide) | Slides 1 and 2, backup B2 |
| Visual abstract, datapoints, comparison (assessment criterion 5) | Slides 3, 2, 6 |
| Public repo, registered (prep guide) | Your action, decision H |

Followed by Sasha's ten-item proposal checklist with our status per item.

## 6. Writing rules for the guide

- Full sentences and short paragraphs. The draft's one-line fragments are fine as a reference but hard to read through.
- Every number identical to the deck. A single source of truth: the numbers table in part 6.
- Each technical term explained at first use in one plain sentence, then used consistently.
- Planned things are written as planned. No sentence may read as if an experiment has been run.
- Examples marked as illustrative when they are not actual BANKING77 rows.
- No claim about prior work without a verified reference.

## 7. Quality checks before handing over

1. Numbers match across deck, script, guide and README (scripted check on the key figures: 77, 10,003, 3,080, 2,000, 1,000, 300, 600, 40, 5 points, 2 points, 95%).
2. Script text in the guide is identical to `script_he.md`.
3. Every slide thumbnail in the guide is from the final rendered PDF.
4. Every reference opens and says what we claim it says.
5. Read-through as a newcomer: can the 30-second and 3-minute summaries be understood without the rest?
6. Hebrew rendering check: mixed Hebrew and English lines display in the right order in the Markdown preview.
