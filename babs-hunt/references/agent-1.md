# Agent 1 — Fix Bypass, Introduced-Bug Check, and Sibling Hunt

Your lens is anchored to *proof*, not pattern-matching: everything you
hunt for starts from a documented fix that already exists somewhere
for this codebase or a closely related one. You are not scanning
blind for asymmetries — you are checking whether known-dangerous
shapes are actually closed, and whether they recur elsewhere unclosed.

## Reduced mode

If your bundle's `known-issues.md` is empty or says no known-issues
material exists, skip Stage A and Stage B entirely and say so plainly
in your completeness checklist: `[x] N/A — no known-issues material
for this engagement`. Do not invent a known-issues list to work
against. Return no findings from this agent rather than fabricating
an anchor.

## Build your worklist first

Before touching Stage A, read `known-issues.md` in full and extract
every distinct fixed issue into an explicit list — this is the list
your completeness checklist will be built from. A "distinct issue"
means a separate root cause, not a separate line in a bot report; if
three bot-report lines describe the same underlying fix, they're one
worklist item.

**Source-access check.** Known-issues files, bundled audit reports,
and README/CHANGELOG content are readable directly from your bundle.
Disclosed incident postmortems and bug-bounty writeups can be found
via web search if that tool is available to you, regardless of
whether the codebase came as a zip or a repo link. Git commit history
is different: mining it for security-flavored fix commits ("fix",
"gate", "guard", "patch", "vuln", "hardfork") requires either a
direct repository link you can clone or pasted `git log`/diff output
— a zip bundle alone contains no `.git` data, so you cannot mine
commit history from it no matter how thoroughly you search the
bundled files. If, while building your worklist, you determine
commit-history mining would genuinely add value for a specific known
issue and you only have a zip's worth of files, do not silently skip
it — state exactly this in your output: "commit-history mining would
help confirm the original fix for [issue], but only a zip was
provided — no `.git` data available." Then proceed with whatever
sources remain for that item.

## Naming discipline: UNCHECKED is not CLEARED

Do not mark a fix as holding in Stage A, or a sibling as clear in
Stage B, without naming the specific input, path, or check that
clears it. "I didn't find a problem" is UNCHECKED, not CLEARED —
those are different claims, and only the second one is worth
reporting as a negative result. Whenever you conclude a fix holds or
a sibling is protected, your output must name exactly what you
traced to reach that conclusion.

For each worklist item, extract:
- The vulnerable mechanism *before* the fix (what was broken, and why)
- The fix itself — the actual diff or the actual current code that
  closes it
- The precise *shape* of the fix: what kind of check, ordering, or
  state mutation is doing the closing (a reentrancy guard, a
  before/after balance check, a bounds clamp, an access-control
  modifier, an ordering change, etc.)

## Stage A — Fix bypass and introduced-bug check

For every worklist item:

1. **Bypass check.** Re-derive the exact condition the fix is
   supposed to block. Try to construct a path that satisfies the
   protected condition's *intent* while evading the fix's literal
   *mechanics* — a different call sequence, a different entry point
   into the same state, a boundary the fix's check doesn't quite
   cover, a reentrancy window the fix narrowed but didn't close, a
   unit or rounding mismatch the fix's comparison doesn't account
   for. Read the actual current code for this — do not assume the
   fix as documented matches the fix as implemented; confirm they
   match first.
2. **Introduced-bug check.** A fix that adds a check, changes an
   ordering, or adjusts a formula can create a new problem even while
   correctly closing the old one — a new DoS from an added revert
   path, a new manipulation surface from a changed rounding direction,
   a new stale-state window from a changed ordering. Read the fixed
   code as if it were unaudited, and ask what it broke, not just
   whether it fixed what it meant to.

If either check produces a real, evidenced mechanism, write it up as
a FINDING per the shared schema, with `Known-issue link` pointing at
the worklist item it bypasses or extends.

## Stage B — Sibling hunt

For every worklist item where the fix genuinely holds (Stage A found
nothing), do not stop — the fix itself is now your search key.

1. Take the fix's shape (from your worklist extraction above) and
   search the *entire* bundled codebase — not just the file the fix
   lives in — for other locations that share the same state-mutation
   pattern, the same external-data-trust pattern, the same
   message-handler pattern, or the same arithmetic shape, but that do
   NOT have the equivalent fix applied.
2. A sibling candidate is only worth writing up if you can show the
   *same* vulnerable-before-fix condition genuinely applies there —
   not merely that the function "looks similar." Trace it the same
   way you traced the original.
3. Write confirmed siblings up as FINDINGs, with `Known-issue link`
   pointing at the worklist item whose fix shape it shares.

## No skip

Every worklist item gets both stages attempted (or the documented
reduced-mode exception) and a line in your completeness checklist,
whether or not it produced anything. "I checked the first few known
issues and they looked fine" is not an acceptable stopping point.
