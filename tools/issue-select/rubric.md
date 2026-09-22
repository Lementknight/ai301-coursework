# Rubric: is this a good first issue?


## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Does the contribution policy contain an outright ban on AI-assisted contributions? | `CONTRIBUTING.md` (repo root or `.github/`) and any dedicated AI-policy files it links to; in eval mode, the "contribution policy" line under Repo facts | Pass unless the policy explicitly bans AI-generated/AI-assisted contributions. Conditions (disclosure rules, allowed tools, required testing, human-review expectations) are terms to follow, not a ban, and pass. Silence — no contributor doc, or a doc that says nothing about AI/tooling — also passes. Only an explicit ban fails. | required |
| Does the contribution policy set out conditions for AI/tooling use? | Same evidence as above | The policy states specific terms for AI-assisted contributions (disclosure rules, allowed tools, required testing, human-review expectations). Silence, or a doc that doesn't mention AI/tooling → fail. This never blocks acceptance; it tells you what your workflow must look like on an accepted issue. | preferred |
| Are there labels on the issue? | The candidate issue's labels as shown in the issue sidebar; in eval mode, the "labels:" field for the issue under Repo facts | The candidate issue has at least one label applied (e.g. `good first issue`, `bug`, `help wanted`). No labels on the issue → fail. | required |
| Is the issue unclaimed? | Assignees, linked PRs, and the comment thread; in eval mode, "this issue: assignees:", "linked PRs:" (with state), and the Comments section under Repo facts | Pass if there is no assignee, no open linked PR, and no live claim comment ("I'll take this", "working on this") standing unresolved. A closed, unmerged linked PR is an abandoned attempt and does not fail this check. An assignee, an open linked PR, or an unresolved active claim → fail. | required |
| Is the issue well-scoped for a newcomer? | The issue body and comment thread | Pass unless: the issue is explicitly an umbrella/tracking issue meant to be split into sub-items; the thread shows the design is still being actively debated with no maintainer decision; a maintainer states the fix touches core internals; or the issue is a pure usage/support question rather than a concrete change. A terse body or missing repro steps does not by itself fail this check — grade the size of the work being asked for, not the polish of the writeup. | required |
| Is the repo actively maintained? | Recent default-branch commits and last push to any branch; in eval mode, the same fields under Repo facts | At least one default-branch commit or push within the last 90 days (eval mode: within 90 days of the bundle's capture date). Nothing in that window → fail: a repo this quiet won't review your PR. | required |
| Have many issues been recently resolved? | The repo's closed-issues list sorted by most recently closed; in eval mode, the "recently closed issues" stat under Repo facts | At least 5 issues closed in the last 14 days (eval mode: within 14 days of the bundle's capture date). This is a strong-health signal, not a minimum bar for aliveness — fewer than 5 does not mean the repo is unhealthy, just less exceptionally active. Fewer than 5 → fail. | preferred |

## Verdict rule

Accept if and only if all `required` checks pass. `preferred` checks never
change the verdict; they only rank accepted issues and tell you what the
workflow will look like. Unclear counts as fail on a required check: a
first issue whose contributor docs, labels, or maintainer activity cannot
be verified is not a first issue to take. Unclear on a preferred check is
just a fail on that check, with no effect on the verdict.
