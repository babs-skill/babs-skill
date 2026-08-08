---
name: babs-dedup
description: Merges findings and leads from multiple independent hunting sources into one clean, deduplicated set before triage. Use when the user invokes /babs-dedup or asks to merge, deduplicate, or cluster findings that came from more than one source — babs-hunt's 5 agents, solidity-auditor, 0xSimao AI, and/or manual review notes. Does not judge validity, exploitability, or severity, and does not decide fix quality — those stay babs-triage's job. Input is the raw findings/leads output from any combination of those sources; output is a grouped, provenance-tagged, completeness-checked merge ready to hand to babs-triage.
---

# babs-dedup

Read `references/dedup-agent.md` in full before clustering anything — it has
the exact duplicate definition, the intake/ID scheme, and the required
completeness tally. Do not improvise the clustering criteria here.

## Required inputs before starting

- Findings and/or leads from at least one source; this skill is most useful
  with two or more (its whole purpose is merging across sources, so a
  single-source run should just confirm there's nothing to merge and pass
  everything through as Unique).
- Whatever native format each source produced its output in — do not
  pre-normalize before handing it to this skill; STEP 0 in the reference
  file is the normalization step, and doing it twice risks losing the
  provenance trail.

Repo access is not required for this skill — it works from the write-ups
it's given, not from re-deriving anything against source code. If a
borderline case genuinely can't be resolved from the write-ups alone, say
so in the Borderline section rather than guessing or fetching the repo
yourself.

## Procedure

1. Run STEP 0 (intake and normalization) from the reference file across
   every item in every source before clustering anything — every finding
   and every lead gets a cross-source ID and a recorded provenance before
   comparison starts.
2. Cluster and compare per the duplicate definition — same location, same
   mechanism, same underlying impact, same fix path (judged by whether
   fixing one closes the other, not by whether the two write-ups used the
   same words to describe the fix).
3. Within each cluster, pick Primary by evidence quality (most concrete
   citations, clearest traced mechanism, a Finding over a Lead), and
   splice in any detail a non-primary duplicate had that the Primary is
   missing before setting the duplicate aside.
4. Leave genuine uncertainty as Unique by default, or Borderline when it's
   specifically worth flagging for a human or babs-triage to look at —
   never force a merge you're not confident about; an incorrect merge
   deletes a possibly-real, distinct bug.
5. Produce the completeness tally before returning anything. Every
   cross-source ID from step 1 must appear exactly once in the final
   output.

## Out of scope for this skill

- Finding new bugs (babs-hunt)
- Judging validity, exploitability, or severity of any item (babs-triage)
- Writing PoCs or formatting the final report (babs-poc)
