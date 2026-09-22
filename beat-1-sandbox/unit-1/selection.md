# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

[#61 issue link](https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61)

**Verdict output**

[Your skill's live-mode output for this issue, pasted verbatim and ending with the
fenced JSON verdict block. A summary does not satisfy this field.]

**The verdict must record `accept` for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run — retries are unlimited and a
partial re-run costs about $0.20 — or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

```
Scope check: codepath/pathreview-ai301-fa26-s3 matches the scoped source in scope.md.
No house rules invoked (no assignee, no linked PR, no comments on this issue).

Checks:
- Does the contribution policy contain an outright ban on AI-assisted contributions? — pass
  docs/CONTRIBUTING.md exists, covers workflow/commits/CI, never mentions AI or
  AI-assisted tooling — no ban stated.
- Does the contribution policy set out conditions for AI/tooling use? (preferred) — fail
  docs/CONTRIBUTING.md contains no AI/tooling-specific terms.
- Are there labels on the issue? — pass
  labels: bug, good first issue, api, tier-1.
- Is the issue unclaimed? — pass
  no assignee, no linked PR, no comments at all.
- Is the issue well-scoped for a newcomer? — pass
  single root cause in api/routes/health.py — the DB probe passes a raw SQL string,
  which SQLAlchemy 2.x requires wrapped in sqlalchemy.text(); reproduction steps
  given in the issue body.
- Is the repo actively maintained? — pass
  last commit 2026-09-16T21:42:18Z, repo pushed_at 2026-09-16T21:50:20Z, within
  90 days of today.
- Have many issues been recently resolved? (preferred) — fail
  only 2 issues closed in the last 14 days, #52 and #43.

Verdict: accept. Both failing checks are tagged preferred and never gate the
verdict; every required check passed.

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61",
  "checks": [
    {"name": "Does the contribution policy contain an outright ban on AI-assisted contributions?", "grade": "pass", "evidence": "docs/CONTRIBUTING.md exists, covers workflow/commits/CI, never mentions AI or AI-assisted tooling — no ban stated"},
    {"name": "Does the contribution policy set out conditions for AI/tooling use?", "grade": "fail", "evidence": "docs/CONTRIBUTING.md contains no AI/tooling-specific terms (preferred, does not block)"},
    {"name": "Are there labels on the issue?", "grade": "pass", "evidence": "labels: bug, good first issue, api, tier-1"},
    {"name": "Is the issue unclaimed?", "grade": "pass", "evidence": "no assignee, no linked PR, no comments at all"},
    {"name": "Is the issue well-scoped for a newcomer?", "grade": "pass", "evidence": "single root cause in api/routes/health.py — raw SQL string needs sqlalchemy.text(); reproduction steps given"},
    {"name": "Is the repo actively maintained?", "grade": "pass", "evidence": "last commit 2026-09-16T21:42:18Z, repo pushed_at 2026-09-16T21:50:20Z, within 90 days of today"},
    {"name": "Have many issues been recently resolved?", "grade": "fail", "evidence": "only 2 issues closed in the last 14 days, #52 and #43 (preferred, does not block)"}
  ],
  "verdict": "accept"
}
```
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

Run 1 (original rubric: 3 required checks, `accept iff all three pass`, including one
dense check requiring a contributor doc to *explicitly authorize* AI/tooling use, with
silence counting as fail):

    agreement: 12/20 scored items  (bar: 18/20: below the bar; category floor unmet: no match in clear-accept)

This result reproduced identically across five separate full runs on the same rubric —
not run-to-run noise, a structural cap. Every one of the eight gold-`accept` issues
failed on the same two checks every time (the AI/tooling-authorization check and the
"≥5 issues closed in 14 days" check), which is what pointed at the rubric rather than
the model.

Run 2 (split the dense AI/tooling check into a required "no outright ban" check plus a
preferred "states conditions" check; loosened the activity gate to a required "active
within 90 days" check plus a preferred "≥5 closed in 14 days" check):

    categories: claimed 0/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 1/4
    agreement: 13/20 scored items  (bar: 18/20: below the bar; category floor unmet: no match in claimed)

`clear-accept` fixed (0/8 → 8/8), but `claimed` and `scope` collapsed. The original
3-check rubric had been passing those categories (4/4 each) by accident — it rejected
almost everything, so it never had to actually detect a claimed or out-of-scope issue.
Loosening the gate exposed that the rubric had no check for either.

Run 3 (added two more required checks, "Is the issue unclaimed?" and "Is the issue
well-scoped for a newcomer?"):

    categories: claimed 4/4  clear-accept 7/8  dead-repo 3/3  policy 1/1  scope 3/4
    agreement: 18/20 scored items  (bar: 18/20: PASS)

Run 4 (final, committed run against `tools/issue-select/rubric.md`, written by
`--save-run` to `eval-run.txt`):

    categories: claimed 4/4  clear-accept 7/8  dead-repo 3/3  policy 1/1  scope 3/4
    agreement: 18/20 scored items  (bar: 18/20: PASS)

**Issue analysis**

`issue-19`, gold `accept`, my rubric's verdict `reject`:

    issue-19  accept  reject   NO     failed: Does the contribution policy set out conditions for AI/tooling use? (preferred), Is the issue well-scoped for a newcomer?, Have many issues been recently resolved? (preferred)

Two of the three failed checks are tagged `(preferred)`, so by the harness's own rule
they cannot have caused the reject — the required "Is the issue well-scoped for a
newcomer?" check is the one that actually sank it. My rubric read the issue as
unbounded (or mid-debate, or a support question rather than a concrete change) where
the gold label disagreed. That is a real disagreement about scope judgment, not a bug
in the check's mechanics: the check is doing its job (gating on scope), it is just
calibrated a notch stricter than the gold label on this particular issue.

**Check rationale**

    | Does the contribution policy contain an outright ban on AI-assisted contributions? | `CONTRIBUTING.md` (repo root or `.github/`) and any dedicated AI-policy files it links to; in eval mode, the "contribution policy" line under Repo facts | Pass unless the policy explicitly bans AI-generated/AI-assisted contributions. Conditions (disclosure rules, allowed tools, required testing, human-review expectations) are terms to follow, not a ban, and pass. Silence — no contributor doc, or a doc that says nothing about AI/tooling — also passes. Only an explicit ban fails. | required |

This replaced my original check, which required a contributor doc to *explicitly
authorize* AI/tooling use and failed on silence. That single check was dense enough
that it was quietly doing two jobs at once — "is there a ban to avoid" and "are there
conditions to plan around" — and it was too strict either way: it alone caused every
gold-`accept` issue to fail, every run. The skill's own `evidence-guide.md` settles
what the bar should actually be ("Silence passes... Conditions are not bans... An
outright ban is a fail"), so I rewrote the check to fail only on an explicit ban, and
moved the "does it spell out conditions" question into its own `preferred` check that
informs the workflow without gating the verdict.

**Trade-offs**

Before the split, this check alone caused every one of the eight gold-`accept` issues
(issue-01, 04, 06, 09, 11, 14, 16, 19) to fail and get rejected regardless of their
other qualities — that's the issue set whose result the split changes; after the split,
`clear-accept` went from 0/8 to 7-8/8 across runs 2-4. The trade-off: the check now
trusts silence as if it were permission. If a repo's real stance is "quietly hostile to
AI-assisted contributions but never writes it down," this check will not catch that —
only the `preferred` "states conditions" check can still surface that nuance, and by
design a `preferred` check never blocks acceptance. I'm accepting that miss: a policy
nobody wrote down is not something a newcomer can be expected to discover before
opening a PR, so treating silence as a block would reject repos for a document that
doesn't exist.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

[Answer all three:

1. The issue's fit to your interests and to the time available.
    As a data engineer this issue is something that I have already done in the past thus I think it's an easy win for me to get started for this course.
2. What the verdict identified correctly, and what you weighed that the rubric could
   not.
    I think that the making the writing about AI usage and tooling to be a good signal for the rubric, but it is clear that it is not a common practice among the repos that I have looked at so far. As for my other checks, I believe that they offered a good lay of the land and the summaries were really helpful.
3. The anticipated difficulty in claiming it.]
    I don't anticipate much difficulty in fulfilling this task.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
