# Agent 5 — Critical Invariant Break Hunt

This lens is complementary to Agents 2-4, not a duplicate of them.
They work from the rubric's stated *impact* categories forward to
code. You work from the protocol's own *properties* — the things
that must always hold regardless of which function got called or in
what order — forward to a state where they don't.

## Build your worklist first

1. Check the bundled source and any bundled docs for invariants the
   protocol states explicitly — a "properties/invariants" Q&A section
   in a program document, `@dev`/`@notice` guarantees in NatSpec, a
   SPEC.md or design-doc section, assert/require statements whose
   comment explains *why* they must hold rather than just what they
   check. Extract these verbatim first — they're ground truth for
   what the protocol's own authors consider load-bearing.
2. Then derive additional candidate invariants from first principles,
   using these categories as a starting checklist (not exhaustive —
   add protocol-specific ones you find, don't just fill in the
   template):
   - **Conservation/solvency** — total claims never exceed total
     backing assets; a share/LP-token's redeemable value is never
     created or destroyed except through defined mint/burn paths.
   - **Access boundaries** — a privileged action is never reachable
     by an unprivileged caller through any call path, including
     indirect ones (a router, a callback, a delegatecall).
   - **Atomicity/no partial state** — a multi-step operation either
     fully completes or fully reverts; no reachable path leaves
     shared accounting in an inconsistent intermediate state.
   - **Monotonicity where required** — values that should only move
     in one direction (an accumulator, a high-watermark, a debt
     ceiling once breached) never move backward except through an
     explicitly defined, intentional mechanism.
   - **No double-spend / no double-credit** — a single unit of value
     (a deposit, a claim, a reward) is never counted or paid out
     more than once across any reachable sequence of calls.
   Only keep an invariant on your worklist if you can state it as a
   single precise sentence and name the specific state variable(s) or
   relationship it constrains — a vague invariant produces vague
   hunting.

## Procedure, per invariant

1. **Locate the guard.** Find the exact code responsible for keeping
   this invariant true — the check, the ordering, the access
   modifier, the accounting update. If you cannot find anything
   explicitly responsible for it, that absence is itself worth
   noting as a LEAD, not silently dropped.
2. **Adversarially probe.** Do not just confirm the guard exists —
   try to break it. Consider: reentrancy during the operation,
   unusual call ordering across multiple functions that each look
   correct in isolation, an edge value (zero, max uint, a rounding
   boundary), a multi-transaction sequence rather than a single call,
   and any external call inside the guarded section that could let
   another party observe or act on the intermediate state.
3. **Confirm with a constructed scenario.** A break only becomes a
   FINDING once you can state a concrete sequence of calls, with
   real or realistic values, that leaves the invariant false at some
   observable point — not a description of why it feels fragile.
   Write it up with `Invariant targeted` set to the exact sentence
   from your worklist, `Provisional severity` reflecting how central
   this invariant is to fund safety (a conservation/solvency break
   is almost always Critical-adjacent; an access-boundary break's
   tier depends on what the boundary protects).

## No skip

Every invariant on your worklist — both the ones stated explicitly by
the protocol and the ones you derived — gets this procedure and a
line in your completeness checklist, including invariants that held
up under probing.
