---
name: babs-poc
description: Writes proof-of-concept tests and formats the final report for a smart contract security finding that has already passed babs-triage. Use when the user invokes /babs-poc or asks to write a PoC, a Foundry test, a mainnet fork test, a testnet exploit script, or format a finding into a Cantina, Sherlock, or HackenProof report. Input is one triaged finding, the target test type (local, mainnet fork, or testnet script), the target platform, and repo access. Re-verifies deployment evidence before a fork/testnet PoC and re-verifies the unprivileged-actor and harm/profit claims against real running code before writing anything. Does not hunt for bugs, dedupe findings, or judge validity/severity — those are babs-hunt, babs-dedup, and babs-triage.
---

# babs-poc

Read `references/poc-agent.md` in full before writing anything — it has the
exact workflow for each test type and the exact report template for each
platform. Do not improvise a template or a workflow step here.

## Required inputs before starting

Per STEP 0 in the reference file, refuse to start and ask for whichever is
missing:

- The triaged finding report, in whatever format it arrives (ideally
  babs-triage's own output, but not required to be).
- The target test type: local test, mainnet fork test, or testnet script.
- The target platform report format: Cantina, Sherlock, or HackenProof.
- Read access to the actual target repository — needed to reuse existing
  test infrastructure (setUp, fixtures, helpers) rather than building
  everything from scratch.

## Procedure

1. Confirm the finding actually has what a PoC needs: a specific broken
   value, a specific consuming call, and a specific differential outcome.
   If the finding you were handed is missing any of these, it hasn't
   cleared babs-triage's Gate 4 — send it back rather than trying to
   invent a scenario to write a test around. Gates A and B in the reference
   file are separate, later checks — they can still fail here even after
   this initial check passes, since they're testing the claim against
   actual running code and actual deployment evidence, not just checking
   the finding has the right shape on paper.
2. If a mainnet fork or testnet script was requested, run Gate A first —
   confirm real, already-deployed addresses actually exist for every
   contract the PoC needs. If they don't, stop and say so; offer a local
   test instead rather than guessing an address.
3. Run Gate B for every finding regardless of test type — while
   constructing the attack, confirm the attacker's role stays fully
   unprivileged and the real numbers actually produce the claimed harm. If
   either breaks down while writing the PoC, stop and say so, and send the
   finding back to babs-triage rather than writing a PoC that softens or
   misrepresents what actually happened.
4. Run the workflow matching the requested test type (STEP 1, 2, or 3 in
   the reference file). Reuse the target repo's existing test
   infrastructure wherever it exists; write only the new attack-specific
   code.
5. Actually run what you wrote and capture the real output — a local test
   always runs (no external dependency); a fork test or testnet script
   runs if outbound RPC access is available, otherwise hand off the
   file plus the exact commands needed to run it.
6. Format the finding and the PoC into the requested platform's exact
   template from the reference file. Do not add sections the template
   doesn't have, and do not drop sections it does have — HackenProof's
   format has no Recommendation field (fold the fix into the end of
   Vulnerability Details instead); Sherlock's Proof of Concept field is
   always "No Response" regardless of whether a PoC was written, per
   this program's own convention for that platform.

## Out of scope for this skill

- Finding new bugs (babs-hunt)
- Merging/deduping findings from multiple sources (babs-dedup)
- Judging validity, exploitability, or severity (babs-triage) — this
  skill assumes the finding handed to it already survived that gate, and
  its own Gates A/B re-check that survival against real code rather than
  re-deciding severity or scope from scratch
