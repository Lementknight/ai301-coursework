# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

**Where it lives.** Eval bundle: the plan's `### Diagnosis` (or a `Cause:` line) holds the stated cause. The `## Repro evidence` block holds what that cause must explain: the numbered steps with their timings or outputs, any control run (a step that changes one thing, such as a flag, an input, or a path), and the `Expected` / `Actual` lines. The `## Thread highlights` section holds commenters' claimed causes. Live: the plan's Diagnosis section, and your own posted repro comment on the issue thread (or the house repro pack quoted in the drafts).

**What good looks like.** The stated cause predicts every step and control in the repro evidence, and the changed component is the one the repro isolates. A cause that only says "as identified in this thread" doesn't count as grounding unless the repro also shows it. A control that contradicts the cause (the bug survives with the suspected part removed, or the suspected part works in a control) rules it out.

## Scope

**Where it lives.** Eval bundle: the plan's `### Scope` section or an `In:` / `Out:` line, and the `### Changes` or `### Approach` list. Live: the same sections of `plan.md`.

**What good looks like.** One bounded change at the site the repro isolates, with a "won't touch" statement that doesn't include the broken thing. A fix with one named, deferred follow-up is bounded. A drive-by refactor, migration, new option, UI rework, or a second symptom bundled in is not, however polished the plan is.

## Executability

**Where it lives.** Eval bundle: the plan's `### Changes`, `### Approach`, or equivalent numbered steps, plus the files named in Scope. Live: the same sections of `plan.md`.

**What good looks like.** A stranger could start without asking: a named file, function, or layer, the concrete change there, and the order of work. Choices are made in the plan. Phrases like "somewhere", "whichever is easier", "maybe also check", or "investigate first" with no chosen approach mean the plan isn't executable.

## Test plan

**Where it lives.** Eval bundle: `### Test plan` or a `Test:` line in the plan, read against the repro's numbered steps and its `Expected` line. Live: the plan's test plan against your unit 2 repro steps.

**What good looks like.** It re-runs the repro steps (or encodes the failing case as a test) and states an observable result that differs before and after the fix, matching the repro's Expected line. "Run the full test suite", "nothing should regress", and "should feel fast" name no outcome for the fix itself.

## Honesty

**Where it lives.** Eval bundle: the plan's risks and unknowns, and the tone of the plan comment (what it claims is verified). Live: the plan's risks and unknowns section, the `## Deviations` heading at the end of `plan.md`, and the comment draft.

**What good looks like.** What the plan hasn't checked (other platforms, other cases, untested assumptions) is said so, and certainty is claimed only for what the repro shows. A terse plan with no stated unknowns that claims nothing unproven is honest. A deviation from the posted plan is recorded under Deviations.

## Comms

**Where it lives.** Eval bundle: the `## Candidate plan comment`, read against `## Thread highlights` (maintainer or collaborator direction, prior PRs) and `## Repo facts` (bug-report asks, contribution policy, AI-use policy). Live: the draft in `comment.md`, the issue thread, and the repo's CONTRIBUTING and AI-policy files.

**What good looks like.** The comment answers what the maintainer already said (follows their direction, or says why it differs) and names prior PRs it relates to. Where the repo's AI policy requires disclosure, the comment says in plain words that AI was used and that the author reviewed it. Where a policy says comments must be human-written, the comment says it is. Boilerplate that could be pasted on any issue isn't engagement.
