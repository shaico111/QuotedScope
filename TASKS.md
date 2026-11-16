# QuotedScope - Dated Tasks

Updated 2026-11-16. QS IDs are planning references. Dates are proposed internal targets in Asia/Jerusalem. Owners and official course dates are to be confirmed by the team.

**Ready to submit: 2027-01-17 (internal target).** See [WORK_PLAN.md](WORK_PLAN.md) for milestones, dependencies and scope decisions.

| Task | Planned start | Internal target | Depends on |
| --- | --- | --- | --- |
| QS-01 — Proposal follow-up and feedback | 2026-11-16 | 2026-11-18 | Existing proposal deck |
| QS-02 — BANKING77 inventory, source audit, splits and leakage checks | 2026-11-16 | 2026-11-22 | None |
| QS-03 — Related work and evaluation protocol | 2026-11-16 | 2026-12-06 | QS-01 for final protocol review |
| QS-04 — Pilot generation and reviewer calibration | 2026-11-23 | 2026-11-29 | QS-02 |
| QS-05 — Lock generation/review rules and build composite development set | 2026-11-30 | 2026-12-06 | QS-04 |
| QS-06 — Build, review and freeze the Role-Swap test | 2026-12-07 | 2026-12-10 | QS-05; start review scheduling during the pilot |
| QS-07 — Evaluation harness, D0 and interim baselines | 2026-11-23 | 2026-12-10 | QS-02; final development scores after QS-05 |
| QS-08 — Pilot training augmentation and interim EDA | 2026-11-30 | 2026-12-10 | QS-04 |
| QS-09 — Interim presentation | 2026-12-07 | 2026-12-13 | QS-03; final deck includes QS-05–QS-08 outputs |
| QS-10 — Complete D1–D3 generation, QA and matching audit | 2026-12-11 | 2026-12-20 | QS-05, QS-08 |
| QS-11 — Run matched DistilBERT experiments for D0–D3 | 2026-12-21 | 2026-12-27 | QS-03, QS-05–QS-07, QS-10 |
| QS-12 — Build and review the human-written challenge set | 2026-11-30 | 2026-12-20 | QS-02, QS-04; disjoint test-source IDs |
| QS-13 — Complete Qwen, heuristic and extract-then-classify baselines | 2026-12-11 | 2026-12-27 | QS-06, QS-07; locked QS-12 for challenge scores |
| QS-14 — Compute confidence intervals, analyze errors and write conclusions | 2026-12-28 | 2027-01-03 | QS-11–QS-13 |
| QS-15 — Final presentation, figures and rehearsal | 2027-01-04 | 2027-01-07 | QS-14 |
| QS-16 — Clean-environment run and submission-ready repository | 2027-01-08 | 2027-01-17 | QS-14, QS-15; incorporate presentation feedback when received |

If a target slips, defer optional experiments first. Preserve the frozen test, the matched D3–D2 comparison and the repository readiness target.
