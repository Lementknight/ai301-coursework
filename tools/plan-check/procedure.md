# Procedure: how this skill grades a plan package

## Read order

1. Read the repro evidence block first, through its Expected and Actual lines. Write down in one line each: what behavior it pins down, and every control run (a step that changes one thing and keeps the rest the same) with its result. The cause check is graded against these notes, so they come before any claim about cause.
2. Read the issue and thread highlights. Note any explicit direction from an owner, maintainer, or collaborator, any prior-art PR, and any commenter's claimed cause. A claimed cause is a claim only, not evidence.
3. Read the repo-facts block. Note the bug-report asks, the contribution policy, and any AI-use or AI-disclosure rule.
4. Read the candidate plan in order: diagnosis, scope, changes or approach, test plan, risks and unknowns. Note the files or areas it names and what it says it will not touch.
5. Read the candidate plan comment last, so it is read against everything above.

## Evidence gathering

1. cause-fits-repro: take the plan's stated cause. List each repro observation and control from step 1 of Read order. For each, write whether the stated cause predicts that result. Record the first one that does not.
2. bounded-scope: list every change the plan makes and every area it names. Compare that list with the issue title and the repro's Actual line. Record any item the issue never asked for, and quote the plan's "won't touch" line.
3. executable: record the files, functions, or layers named, the concrete change at each, and every place the plan leaves a choice open or defers a decision to build time.
4. decisive-test: record the test's steps and its expected result. Compare them with the repro's numbered steps and Expected line. Record whether the outcome would differ before and after the fix.
5. thread-and-policy-fit: record the maintainer direction or prior work from the thread, and whether the comment responds to it. Record the repo's AI-use or disclosure rule, and quote the comment's disclosure sentence, or note that there is none.
6. honest-unknowns: record the unknowns the plan states and any claim of certainty. Compare each claim with what the repro shows.

## Check execution

1. Run the checks in the order of the rubric's table. Grade each from the evidence recorded in the Evidence gathering stage. Don't re-read the whole package unless a note is missing or unclear.
2. Grade each check pass, fail, or unclear, and write one line naming the fact or quote that decided it. Never grade a check without a quote or fact.
3. A check is a pass only if the evidence meets the pass condition in the rubric. A confident tone, a polished plan, or an authoritative thread comment is never a pass.
4. If the evidence the check needs is missing from the package, grade by the rubric: a section the plan lacks (no test plan, no scope) is a fail. Unclear is only for repro evidence that can't confirm or rule out the cause, or for a policy that is too ambiguous to apply.
5. If the procedure doesn't say how to handle something, note the gap in the summary. Don't invent a step.

## Verdict assembly

1. Apply the rubric's verdict rule. Any required check graded fail gives reject. Any required check graded unclear also gives reject. All required checks passing gives accept. Preferred checks never change the result.
2. In the summary, quote the deciding evidence for each failed or unclear required check. For an accept, quote the evidence for the checks closest to failing.
3. Emit the JSON block last, with every check, its grade, a one-line evidence quote, and the verdict.
