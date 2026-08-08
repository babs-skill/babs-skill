# Agents 2-4 — Impact-Driven Lens (parameterized by tier)

You were spawned with an assigned tier — CRITICAL, HIGH, or MEDIUM.
Everything below applies only to your assigned tier's bullets. Ignore
the other two tiers entirely; another agent is already covering each
of them in parallel, and duplicating their work wastes this run.

This lens works backward from *outcome* to *code*, not forward from
code to outcome. You are not scanning for bug patterns in general —
you are starting from the program's own declared payout criteria and
asking what code, if broken, would produce exactly that.

## Build your worklist first

Extract every distinct impact bullet belonging to your assigned tier
from the rubric text in your bundle, verbatim. Each bullet is one
worklist item — do not merge similar-sounding bullets, and do not
skip a bullet because it looks hard to satisfy or already covered by
another one.

## Procedure, per bullet

1. **Hypothesize before reading.** Before you open the relevant code,
   write down: what specific kind of broken value, missing check, or
   wrong ordering would produce exactly this bullet's stated outcome
   — not a category of bug, a concrete shape (e.g. "a share-price
   calculation using stale reserves after an external call" rather
   than "an oracle bug").
2. **Search for that shape.** Now search the bundled source for code
   matching the hypothesis. If nothing matches, revise the hypothesis
   once or twice against what the codebase actually does — don't
   force a match that isn't there, and don't spend unlimited time on
   one bullet at the expense of the rest of your list.
3. **Confirm or refute with evidence.** If you find a real candidate,
   trace it fully: read the actual function, confirm the actual
   control flow, and check whether it genuinely produces the
   bullet's outcome in a constructed scenario with real or realistic
   numbers — not just that it "could." If it doesn't hold up under
   that trace, it's a LEAD at best, not a finding.
4. Write confirmed mechanisms up as FINDINGs per the shared schema,
   with `Rubric bullet targeted` set to the exact bullet text you
   started from, and `Provisional severity` set to your assigned
   tier unless the actual mechanism you found clearly belongs to a
   different tier (say so if that happens — it's a signal for
   babs-triage, not an error on your part).

## No skip

Every bullet in your assigned tier gets this procedure and a line in
your completeness checklist — including bullets where step 2 or 3
turned up nothing. A tier with five bullets needs five checklist
lines, not two findings and silence about the rest.
