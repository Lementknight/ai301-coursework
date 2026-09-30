# Evidence guide: where proof lives in a reproduction package

## Environment

Where it lives: in an eval bundle, the repro report's environment/setup
lines (OS or platform, tool version, relevant config), usually near
the top of the report section — read against the repo-facts block's
stated target version and the issue context's own environment, if the
reporter gave one. In live mode, the draft repro report's environment
lines, read against the issue thread's original post and any
maintainer follow-up that names the target version or branch.

What good looks like: platform and the tool's exact version (or
commit/branch) stated plainly, not "latest." If the environment
differs from what the issue targets — an older version, a different
OS — the report says so explicitly instead of presenting the attempt
as if it were on the same target.

## Steps

Where it lives: in an eval bundle, the repro report's steps section,
usually numbered or a command block. In live mode, the draft's steps,
read against the issue's own "steps to reproduce" (or thread
description) for the starting state and trigger the bug actually
needs.

What good looks like: every step is an exact command, input, or UI
action, not a description of one ("set up the project" is not a
step). A starting state is named (repo at a given ref, a specific
input file, a specific flag) so a stranger isn't guessing setup. The
step that actually triggers the behavior is present, not skipped or
paraphrased away.

## Behavior shown

Where it lives: in an eval bundle, the repro report's artifact block
(pasted output, log, error trace, or described screenshot), read
directly against the issue context's stated symptom — the exact error
message, exit condition, or visual/functional defect. In live mode,
the draft's pasted output or log, read against the issue thread's
original bug description and any maintainer confirmation of the exact
symptom.

What good looks like: the artifact contains the same symptom the issue
names — same error text or class, same exit behavior, same defect —
not a related-but-different failure produced by a different input or
code path. Where useful, a control run (the same steps against a known
working case) that isolates what actually triggers the difference,
rather than a control that's just pasted in for show.

## Honesty

Where it lives: the claim comment's and repro report's concluding
language — what it says happened — read next to the behavior-shown
artifact from the section above. Same locations in live mode, just the
draft's own closing lines next to its own pasted artifacts.

What good looks like: the stated conclusion never exceeds what the
artifact shows. "Reproduces" is used only when the artifact matches
the issue's behavior; "could not reproduce" is a fine, honest outcome
when it names the real attempt made and what differed from the
issue's setup. Confidence language ("verified," "guaranteed,"
"definitely") appears only when backed by a shown mechanism in the
artifact, never asserted from inference alone.

## Comms

Where it lives: in an eval bundle, the claim comment's own text, read
against the repo-facts block's stated issue-template asks,
contribution policy, and AI-use policy (if any). In live mode, the
draft claim.md/repro.md text, read against the repo's CONTRIBUTING
docs, issue template, and any stated AI-disclosure policy; `scope.md`'s
house rules layer on top in live mode only.

What good looks like: the comment names something specific to this
issue and this attempt — a comment generic enough to paste unchanged
onto any issue, or a "+1"/me-too with no independent content, is
boilerplate even if a solid repro report sits next to it. If the
repo's stated policy requires disclosing AI assistance, the comment
discloses it plainly; if the policy is silent or permissive, no
disclosure is required to pass. No promises beyond what's actually
been done — no fix timeline, no self-assignment claimed as settled.
Tone stays descriptive of the problem, not combative toward
maintainers or other reporters.
