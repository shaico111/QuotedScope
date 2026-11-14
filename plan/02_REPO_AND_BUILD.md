# Repository and build plan

## 1. Target structure

Follows the tree recommended in Sasha's GitHub submission guide. At proposal stage only the marked parts get real content; the rest are placeholders so the structure is in place for the interim milestone.

```
QuotedScope/
├── README.md                      [content now]
├── CLAUDE.md                      [exists]
├── LICENSE                        [decision below]
├── requirements.txt               [content now: python-pptx, pymupdf, matplotlib]
├── .gitignore                     [exists; keeps Info/ out]
├── slides/
│   └── proposal/
│       ├── QuotedScope_Proposal.pptx   [content now]
│       ├── QuotedScope_Proposal.pdf    [content now]
│       └── build_deck.py               [content now: generates the PPTX]
├── docs/
│   ├── script_he.md               [content now]
│   ├── guide_he.md                [content now]
│   ├── narrative_he.md            [content now]
│   ├── references.md              [content now]
│   └── archive/project-idea.md    [moved here, decision G]
├── visuals/
│   ├── visual_abstract.png        [content now]
│   ├── pipeline.png               [content now]
│   ├── role_swap_example.png      [content now]
│   └── build_figures.py           [content now]
├── src/                           [placeholder README: data/, models/, training/, evaluation/]
├── notebooks/                     [placeholder README]
├── data/                          [placeholder README: raw/, processed/, samples/]
├── configs/                       [placeholder README]
├── results/                       [placeholder README]
├── plan/                          [these five files; removed or kept after approval, your call]
└── Info/                          [local only, git-ignored]
```

Notes:
- `Info/` stays ignored. The course slides are the lecturer's material.
- No placeholder code. Empty `src/` modules that do nothing would only look like work that was not done. Each placeholder folder gets a three-line README saying what will go there and at which milestone.
- `plan/` is working material. Suggestion: delete it from the branch before merging, since the guide replaces it.

## 2. README (proposal-stage version)

Sections follow the course list, with honest "planned" markers where nothing exists yet:

1. Title, one-line description, status badge in text: "Stage: proposal (week 5). No experiments have been run."
2. Motivation
3. Problem statement, with the input/output example
4. Visual abstract (figure)
5. Planned datasets: BANKING77 source, license, counts; planned synthetic sets with quantities
6. Planned data generation and QA
7. Planned models and pipeline (figure)
8. Planned metrics and success criteria
9. Results: "none yet"
10. Repository structure
11. Team members and responsibilities
12. References

The repo description on GitHub (one sentence) should be set too: "Role-Swap benchmark for active vs. historical intent attribution in banking support messages." Setting it changes the public repo page, so I will ask before doing it.

## 3. How the deck is built

Recommended: **`python-pptx` script → native PPTX → PDF**.

Why this and not Marp:
- The course asks for PPT and PDF in the repo. A native PPTX is editable in PowerPoint, so you can adjust a word the night before without touching code.
- Speaker notes can hold the Hebrew script per slide, so presenter view shows it.
- Python is already installed. Marp needs Node, which is not.
- The script keeps the deck reproducible: change a number in one place, rebuild.

Steps:
1. `visuals/build_figures.py` draws the three figures (matplotlib, vector-clean, consistent palette).
2. `slides/proposal/build_deck.py` assembles slides: 16:9, one typeface, minimum body size 20 pt, a footer line for sources.
3. PDF export. First choice: PowerPoint on this machine through COM automation if it is installed. Second: LibreOffice headless if installed. If neither exists, I will tell you and you export with "Save as PDF", which takes ten seconds.
4. **Visual check:** render each PDF page to PNG with PyMuPDF and look at every slide for overflow, overlap, text size and contrast. The draft skipped this step, and it is the one that catches a table falling off the slide.

Design rules for the deck:
- One idea per slide; a headline that states the point, not the topic ("Same words, opposite roles, different label", not "Task definition").
- No more than about 40 words of body text on a core slide. Tables only where the table is the content (D0 to D3).
- Colour carries meaning consistently: one colour for active, one muted colour for historical, across every figure and slide.
- Sources in a small footer on the slide that uses them.
- English only on slides. Hebrew only in notes.

## 4. Git

- Work on a branch `proposal-kit`, not on `main`.
- Commits in logical pieces: scaffold, figures, deck, script, guide.
- Nothing is committed or pushed until you ask. A PR to `main` afterwards if you want review.
- `.gitignore` is the generic Python template plus `/Info`. I will check that `*.pptx`, `*.pdf` and `*.png` are not ignored before the first commit.

## 5. License decision

The GitHub guide lists a LICENSE file. BANKING77 is CC BY 4.0, which constrains redistributed data, not our code. Suggestion: MIT for code, and a note in the README that derived data will carry CC BY 4.0 with attribution to PolyAI. This can wait until data exists; say if you prefer to add it now.

## 6. What I will not do without asking

- Push, open a PR, change the repo description or visibility.
- Register the team in the course registry (yours to do on Moodle).
- Use the Proposal Reviewer bot (needs your ChatGPT login).
- Download BANKING77 or run any model, unless you approve the optional pilot in findings 5.10.
