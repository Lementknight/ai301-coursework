# Rubric: is this reproduction package ready to post?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| environment-recorded | The repro report's environment record (OS/platform, tool version, relevant config), read against the issue's stated target version or environment. | Pass if the environment needed to place the attempt is stated (at minimum: platform and the tool's version), and any delta from the issue's target version/environment is explicitly acknowledged rather than silently substituted. Fail if no environment is recorded, or a different one is used with no acknowledgment. | required |
| steps-rerunnable | The repro report's steps, read as a sequence a stranger with no access to the reporter's setup would execute. | Pass if the commands or actions that trigger and surface the reported behavior are given exactly, and any setup step names precisely what's needed to build that starting state, even if not pasted as a literal file dump. Fail if a step is genuinely vague ("set up the project"), assumes access nobody else has, or the step that actually triggers the behavior is paraphrased or missing. | required |
| behavior-matches-issue | The artifact (output excerpt, log, screenshot) in the repro report, read against the specific behavior the issue describes. | Pass if the artifact shows the same behavior the issue reports — same error, symptom, or exit condition — not an adjacent one produced by a different input or a different failure path. An honest, evidenced cannot-reproduce also passes this check: a genuine attempt at the issue's exact trigger, with the artifact from that attempt shown, that plainly states the reported behavior did not occur. Fail if the artifact shows a different symptom, a gracefully-handled case narrated as the issue's crash (or vice versa), a cannot-reproduce with no real attempt shown, or no artifact at all. | required |
| honest-outcome | The claim comment's and repro report's stated conclusion, read against what the shown artifacts actually demonstrate. | Pass if every claim is backed by the artifact shown, including an honest "could not reproduce" that names what was tried and what differed from the issue's setup. Fail if the report claims more certainty than the artifact supports (a guarantee, a "verified" root cause with no shown mechanism), or narrates a mismatched artifact as if it confirmed the issue. | required |
| disclosure-compliant | The repo-facts block's stated AI-use policy, read against the claim and repro comment text. | Pass if the repo states no AI-disclosure requirement, or if it does and the comment discloses AI assistance as required. Fail only if the policy requires disclosure and the comment does not disclose. | required |
| specific-not-boilerplate | The claim comment's own language. | Pass if the comment states what was actually tried or found for this issue, in the commenter's own words — generic enough to paste onto any issue unchanged, or a "+1"/me-too with no independent content, fails this even if a repro report exists elsewhere in the package. Also fails if the comment promises a timeline or outcome the commenter has not earned (a fix guarantee, a delivery date). | required |
| motivation-stated | The claim comment's opening lines. | Pass if the comment briefly says what the commenter was trying to do or verify before describing steps or results. This is a clarity nicety, not a proof requirement. | preferred |
| calm-tone | The claim and repro comment text. | Pass if the comment describes what's wrong without hostile or dismissive language toward maintainers or other contributors. | preferred |

## Verdict rule

Accept only if every `required` check grades `pass`. Any `required`
check graded `fail` or `unclear` returns `reject`. `preferred` checks
are informational only and never change the verdict.
