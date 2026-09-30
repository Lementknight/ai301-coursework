# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

Lementknight

---

## Posted upstream

**Claim comment**

[Link to the comment where you claimed the issue. Use the comment's own permalink, not the
issue page on its own. **Then paste the text of that comment underneath the link** — the
pasted text is what this field is graded on, so copy across what you actually posted.]

**Reproduction comment**

[Link to the comment where you posted your reproduction. It must record the environment
(OS, relevant versions, code state), steps a stranger could follow, and what you observed.
**Then paste the text of that comment underneath the link** — the pasted text is what this
field is graded on, so copy across what you actually posted.]

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke run (`--limit 3`, pkg-01/pkg-02/pkg-03): 3/3 agreement.
2. Full run: 17/20 agreement — below the 18/20 bar. All three misses
   (pkg-05, pkg-09, pkg-10) were gold `accept` packages my rubric
   rejected.
3. Canary run (`--only pkg-05,pkg-09,pkg-10,pkg-20,pkg-06,pkg-18,pkg-08,pkg-11`)
   after revising two checks: 8/8 agreement, including the three
   previous misses and five canaries from the categories the revision
   could have touched.
4. Confirming full run: 20/20 agreement — bar: PASS. This is the run
   committed as `eval-run.txt`.

**Package analysis**

`pkg-09` (sharkdp/fd#2033): gold verdict `accept`, my rubric's final
verdict `accept` — agree. The claim/repro pair is an honest
cannot-reproduce: the reporter ran the exact scenario-2 trigger
(argument-size-limit batch reordering), showed the log from five real
attempts, and stated plainly that no reordering occurred, naming what
likely differed (uniform file-name lengths vs. the varied lengths the
bug may need). My rubric reads this as `accept` because
`behavior-matches-issue` explicitly passes a genuine, evidenced
cannot-reproduce, and `honest-outcome` passes it too since the stated
conclusion never claims more than the artifact shows. Before a
revision (see Check rationale), my rubric rejected this same package:
`behavior-matches-issue` had no cannot-reproduce exception, so an
honest "it didn't happen" always failed a check that demanded the
artifact match the issue's behavior — which a cannot-reproduce report
can never do by definition.

**Check rationale**

`behavior-matches-issue`, as it now reads in `tools/repro-check/rubric.md`:

> Pass if the artifact shows the same behavior the issue reports — same
> error, symptom, or exit condition — not an adjacent one produced by a
> different input or a different failure path. An honest, evidenced
> cannot-reproduce also passes this check: a genuine attempt at the
> issue's exact trigger, with the artifact from that attempt shown,
> that plainly states the reported behavior did not occur. Fail if the
> artifact shows a different symptom, a gracefully-handled case
> narrated as the issue's crash (or vice versa), a cannot-reproduce
> with no real attempt shown, or no artifact at all.

It reads this way because the first full run showed this check
structurally conflicting with `honest-outcome` on `pkg-09` and
`pkg-10`: both are real, evidenced cannot-reproduce reports, and
`honest-outcome` correctly passed them, but the original
`behavior-matches-issue` had no exception for a cannot-reproduce
outcome, so it failed them anyway — and since verdict requires every
`required` check to pass, an honest cannot-reproduce could never
accept under the original wording, which contradicts the proof family
the lecture named ("an evidenced cannot-reproduce is a pass, a
confident wrong-target is not"). I added the explicit carve-out rather
than weakening the check generally, so a report that merely lacks any
artifact, or narrates a mismatched artifact as a match, still fails.

**Trade-offs**

The same revision also loosened `steps-rerunnable`, from requiring
every step to be a literal executable command to requiring only that
the commands which trigger and surface the behavior are exact, with
setup steps allowed to name precisely what's needed rather than
pasting a literal file dump (this is what let `pkg-05`'s prose-described
`env.yml` setup pass alongside its two exact commands). The trade-off:
a looser steps check risks accepting a report whose real setup is
under-specified as long as the two headline commands look exact. I
checked this against the packages built to catch exactly that failure
mode — `pkg-06` (no environment recorded at all, steps omit the
Windows driver) and `pkg-18` (repro lives in an unshared private
monorepo) — by re-running them with `--only` as canaries alongside the
fix. Both still correctly reject after the change, so the looser
wording didn't buy back the packages it was never meant to pass.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
