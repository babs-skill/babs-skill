---
name: babs-triage
description: Adversarial verification and severity triage for smart contract security findings. Trigger on "/babs-triage" or a request to triage, verify, judge, or assign severity to one or more findings, whether from babs-hunt, babs-dedup, solidity-auditor, 0xSimao AI, or manual review. Input is one or more findings (claim + code reference, and the PoC/report text if one exists), the program's platform and severity rubric, and — where available — deployment configs/registries showing what's actually live in the reference deployment (or, for a pre-launch engagement, what the deploy scripts/config designate as the intended configuration). Does not hunt for new bugs, does not merge/dedupe findings, and does not write PoCs — those are babs-hunt, babs-dedup, and babs-poc.
---

# babs-triage

Read `references/triage-agent.md` in full before evaluating anything — it is
the complete gate sequence and the only source of truth for how a verdict is
reached. This file only orchestrates the run; do not improvise gate logic
here.

## Required inputs before starting

- The finding(s) to triage: the claim, the affected file/function, and the
  actual submitted report/PoC text if one exists (read it directly per the
  SILENT WORK section — never triage from a paraphrase or summary of a
  finding).
- The program's platform and its severity rubric, verbatim. If the person
  hasn't supplied the rubric text for this program yet, ask for it before
  scoring any gate — do not fall back to a generic or remembered rubric from
  a different program.
- Read access to the actual codebase and the actual program document (scope
  list, known issues, Q&A/design-rationale prose) for this specific
  engagement. Gate 0 requires reading these directly, not from memory of a
  prior engagement.
- First establish whether this is a live engagement (a bug bounty on a
  deployed protocol, or a contest auditing an already-deployed system) or a
  pre-launch contest with nothing deployed yet — the program document
  usually states this directly. Then gather accordingly:
  - **Live engagement**: deployment configs, registries, address lists, or
    deploy scripts showing what's actually wired up today. If none exist at
    all, say so explicitly before triaging — every finding will resolve
    Gate 0.7 as UNCONFIRMED rather than CONFIRMED-LIVE, capping every
    otherwise-valid finding's severity at Medium. That's a systemic effect
    on the whole batch, not a per-finding footnote — flag it up front so it
    isn't mistaken for a run of unusually weak findings.
  - **Pre-launch engagement**: the codebase's own deploy scripts, config
    files, or architecture/design docs, if they designate a specific
    configuration among whatever multiple options the code technically
    supports. A pre-launch engagement with no deployment at all is normal
    and does NOT by itself cap severity — Gate 0.7 only caps a pre-launch
    finding when even the intended design leaves genuine ambiguity about
    which supported path will actually be used.

## Procedure

1. For each finding, work through `references/triage-agent.md` gate by gate,
   in order, stopping at the first FAIL/INVALID exactly as specified.
2. Do the silent work (reading source, tracing paths, reading the full
   program document, searching for existing safeguards, checking deployment
   evidence for Gate 0.7) before answering any gate — never answer a gate
   from the finding's own framing alone.
3. Produce only the OUTPUT ONLY block specified in the reference file for
   each finding. No commentary, no gate-by-gate narration, no meta-discussion
   of the process — that belongs in SILENT WORK, not the reply.
4. When triaging a batch, work highest-claimed-severity-first if the source
   tool provided one, so the highest-value candidates are judged before time
   runs out on a large batch.
5. If a finding is a LEAD (real signal, no PoC, insufficient proof) rather
   than a full claim, say so plainly instead of forcing it through the full
   gate sequence — a lead that hasn't produced a concrete mechanism yet
   belongs back with the hunter, not through Gate 2's mechanism-verification
   test as if it were a finished claim.
6. If explicitly asked, produce the reporter-facing verdict table from the
   end of the reference file, for the whole triaged batch, after every
   finding's own OUTPUT ONLY block already exists. Never produce this table
   unprompted.

## Out of scope for this skill

- Finding new bugs (babs-hunt)
- Merging/deduping findings from multiple sources before they arrive here
  (babs-dedup) — this skill assumes it's being handed one already-distinct
  finding at a time
- Writing PoCs or formatting the final report (babs-poc)
