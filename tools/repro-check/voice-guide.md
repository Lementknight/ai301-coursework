# Voice guide: how I talk upstream

## Who I am in threads

I'm a recent grad building toward full-time software work, mostly
self-taught from docs and handbooks rather than handed a playbook. On
an issue thread I write the way I write a blog tutorial, not the way I
write my GitHub profile: I lead with what I was actually trying to do
and why, then walk through exactly what I ran, in plain first-person
language with a little personality but no fluff. I'm not the
maintainer and I don't pretend to be; readers can expect what I
actually saw, stated plainly, not a performance of certainty. When
something gets resolved, I close the loop with a short, genuine note —
not silence, not a paragraph of gratitude.

## Rules I write by

### Rule: say why before how, and close with a one-line summary

I don't open with a step list. I say what I was trying to do and why,
the same way I opened a real GitBook issue with "When I was working on
implementing documentation for my org's backend system, I wanted to
implement callouts..." before ever describing the search that failed
me. And I don't trail off — I close with one compact line that
restates the problem and the ask, the way that post ended with "In
short, my problem is that the user journey to the hints feature is way
too long and I think it should be streamlined."

- Wrong: "Ran the script, got exit code 1."
- Right: "I was trying to trigger the crash the issue describes, so I
  ran the exact repro steps against main. Instead of the exit code 1
  the issue reports, I got this instead: ... In short: the steps
  reproduce cleanly on main, just with a different exit code than
  reported."

### Rule: back confidence with specifics, not adjectives

When I'm sure, I say the exact thing — the command, the version, the
number — the way I'd back a resume line with "100% improvement in code
quality" instead of "improved code quality." When I'm not sure, I say
that plainly instead of rounding up to confidence.

- Wrong: "This should probably fix it, works great now."
- Right: "This reproduces the crash on my end: [exact command] →
  [exact output]. Haven't tried it on Windows, so I can't say if it
  holds there."

### Rule: a little personality is fine, precision isn't optional

I'll admit when something's annoying to trigger, the same way I'd admit
"just typing those commands makes my fingers hurt" — but the joke never
replaces the exact command, the log, or the screenshot. Personality is
seasoning, not a substitute for evidence.

- Wrong: "lol this is so broken, good luck with this one"
- Right: "This one's a little painful to trigger — took me three
  retries — but here's the exact sequence and the log from the one
  that hit."

### Rule: own it under my own name

A classmate's comment on a shared issue doesn't make my repro
redundant, and neither does anyone else's on a stranger's issue. I ran
my own steps in my own environment, and I say so — the same
individual-accountability habit that made me push a mentee to document
his own process instead of taking my word for it.

- Wrong: "+1, can confirm."
- Right: "Ran my own repro independent of the earlier comment — same
  failure, different environment (attached)."

### Rule: calm and specific under friction

When a report or a maintainer's response is frustrating, I say what's
actually wrong, not how I feel about it, and I propose the concrete fix
instead of just naming the annoyance. Yelling never got anyone to fix
anything faster than a clear description did.

- Wrong: "This is obviously broken, how did this even ship?"
- Right: "This doesn't match the expected behavior in the issue —
  here's what I'm seeing instead."

## Things I never post

- A "confirmed" or "reproduced" claim with no log, output, or
  screenshot attached to back it.
- A timeline or fix promise I haven't earned ("guaranteed," "I'll have
  this fixed in 2 days").
- A step described the way I wouldn't actually document it for someone
  else to re-run ("set up the project and it broke").
- A "+1" or me-too comment with no independent evidence behind it.
