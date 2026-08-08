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

## Filter before finalizing the worklist

A candidate only stays on your worklist if it clears all three:

1. **If broken, the consequence is severe** — direct fund theft,
   direct fund loss, permanent protocol damage, funds getting frozen
   or made permanently unwithdrawable, or a denial-of-service that
   blocks a core function indefinitely. An invariant whose break just
   causes a minor UX inconvenience or a cosmetic inconsistency does
   not belong on this list — Agents 2-4 already cover the full
   severity spectrum from their own angle; this lens exists
   specifically for the highest-stakes properties.
2. **Non-obvious** — not enforced by a single, visible modifier or
   check that any reviewer would spot on a first read. If the
   protection is a one-line guard sitting directly on the function,
   it's not what this lens is for.
3. **Not a standard, already-correct library pattern** — exclude
   invariants that reduce to "does `nonReentrant` hold," "does
   `Ownable` gate this correctly," "does `Pausable` work as
   documented," or an equivalent standard OpenZeppelin pattern used
   as intended. Assume these are correctly wired unless something you
   already found elsewhere gives specific, concrete reason to doubt
   it — don't spend this lens's effort re-verifying well-trodden
   library behavior.

Drop anything that fails any of the three. There is no numeric cap on
how many invariants remain after this filter — carry every one that
clears it, however many that turns out to be. The filter is what
keeps the list tight, not an arbitrary count.

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
