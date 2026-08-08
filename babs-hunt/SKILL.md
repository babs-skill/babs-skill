---
name: babs-hunt
description: Reusable smart-contract bug-hunting workflow. Use when the user invokes /babs-hunt or asks to hunt for bugs, run known-issue fix-bypass and sibling checks, run an impact-driven hunt against a program's severity rubric, or hunt for critical invariant breaks. Runs 5 specialist lens agents in parallel — known-issue fix bypass + introduced-bug + sibling hunt, critical-impact-driven, high-impact-driven, medium-impact-driven, and critical-invariant-break hunting — and returns raw findings and leads for babs-dedup and babs-triage to process next. Does not dedupe, judge, or score severity itself.
---

# babs-hunt

You are the orchestrator of a parallelized bug hunt across 5 specialist lens agents.

## Required inputs before starting

Refuse to start and ask for whichever of these is missing — do not substitute a guess or a remembered rubric from a different engagement:

1. **In-scope codebase** — the actual repository or file set to hunt in.
2. **Severity/impact rubric**, verbatim, for this specific program — the platform default (Sherlock/Cantina/HackenProof/Immunefi) or a custom rubric, with Critical, High, and Medium impact bullets identifiable as distinct lists. Agents 2–4 each need their own tier's bullets in full, not a summary.
3. **Known-issues material**, if it exists for this engagement — known-issues page, bot report, prior audit report(s), disclosed incident postmortems, bug-bounty writeups, or git commit history showing prior fixes. If genuinely none exists (a fresh, never-audited codebase), say so explicitly and Agent 1 runs in a reduced mode — see its reference file.

If the program's own documentation states invariants explicitly (a "properties/invariants" Q&A section, NatSpec `@dev` guarantees, a SPEC.md), gather that too; Agent 5 uses it as a starting point rather than deriving everything from first principles.

## Orchestration

**Turn 1 — Discover.** In one message, make these parallel tool calls:

a. Find all in-scope source files per the declared scope (respect any explicit out-of-scope exclusions — do not bundle files the program has excluded).
b. Read `references/hunt-sop.md`, `references/agent-1-fix-bypass-and-siblings.md`, `references/agent-2to4-impact-driven-lens.md`, `references/agent-5-critical-invariants.md` from this skill's own directory.
c. `mktemp -d ./.hunt-XXXXXX` → store as `{bundle_dir}`.

**Turn 2 — Prepare.** Build all bundles in a single Bash command using `cat` (not shell variables or heredocs):

1. `{bundle_dir}/source.md` — ALL in-scope files, each with a `### path` header and fenced code block.
2. `{bundle_dir}/known-issues.md` — the known-issues material gathered above, verbatim (Agent 1 only needs this, but bundle it once).
3. Agent bundles, each = `source.md` + shared SOP + agent-specific file(s):

| Bundle | Appended files (relative to this skill's `references/`) |
| --- | --- |
| `agent-1-bundle.md` | `source.md` + `known-issues.md` + `hunt-sop.md` + `agent-1-fix-bypass-and-siblings.md` |
| `agent-2-bundle.md` | `source.md` + `hunt-sop.md` + `agent-2to4-impact-driven-lens.md` |
| `agent-3-bundle.md` | `source.md` + `hunt-sop.md` + `agent-2to4-impact-driven-lens.md` |
| `agent-4-bundle.md` | `source.md` + `hunt-sop.md` + `agent-2to4-impact-driven-lens.md` |
| `agent-5-bundle.md` | `source.md` + `hunt-sop.md` + `agent-5-critical-invariants.md` |

Print line counts for every bundle and `source.md`. Do NOT inline source code into the agent call prompt itself — the bundle carries it.

**Turn 3 — Spawn all 5 agents.** In one message, spawn all 5 agents as **parallel BACKGROUND Agent/Task calls** (`run_in_background=true`). Do not poll or sleep. Single phase, no later spawns. Proceed to Turn 4 only after all 5 have notified completion.

Agents 2, 3, and 4 all read the SAME `agent-2to4-impact-driven-lens.md` file — bind the tier explicitly in each spawn prompt so the same instructions produce three different hunts:

```
You are Agent 2. Your bundle: {bundle_dir}/agent-2-bundle.md
Your assigned tier: CRITICAL. Work only the Critical-severity impact
bullets from the rubric in your bundle. Read the bundle fully before
producing anything.
```

```
You are Agent 3. Your bundle: {bundle_dir}/agent-3-bundle.md
Your assigned tier: HIGH. Work only the High-severity impact bullets
from the rubric in your bundle. Read the bundle fully before producing
anything.
```

```
You are Agent 4. Your bundle: {bundle_dir}/agent-4-bundle.md
Your assigned tier: MEDIUM. Work only the Medium-severity impact
bullets from the rubric in your bundle. Read the bundle fully before
producing anything.
```

Agents 1 and 5 get a plain prompt pointing at their own bundle — their reference file is already tier-agnostic (Agent 1 works the full known-issues list; Agent 5 works the full critical-invariant list).

**Turn 4 — Collect and gate.** Once all 5 report back:

1. Confirm each agent produced a completeness checklist (see `hunt-sop.md`) covering every item on its assigned list — every known issue for Agent 1, every bullet in its tier for Agents 2–4, every invariant for Agent 5. An agent that returns findings with no checklist, or a checklist with items silently missing, has not finished — send it back before accepting its output.
2. Concatenate all 5 agents' raw output (findings + leads + checklists) into one handoff document. Do not dedupe here and do not assign final severity here — that is babs-dedup's and babs-triage's job respectively. Label each item with which agent produced it, so babs-dedup can trace provenance when merging.
3. Hand the concatenated output to the person, or directly to babs-dedup if this run is part of a chained pipeline.

## Out of scope for this skill

- Deduplicating findings across agents or across other sources (babs-dedup)
- Judging validity, severity, or writing a verdict (babs-triage)
- Writing PoCs or formatting a submittable report (babs-poc)
