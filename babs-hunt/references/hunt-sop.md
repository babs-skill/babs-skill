# Shared hunt SOP — read by every lens agent

These rules apply regardless of which lens you are running. Your own
agent file adds the specific procedure on top of this.

## Ground rules

- Search the actual bundled source for every claim. Never assume a
  function name, a modifier, a storage layout, or a control-flow path
  — quote nothing you have not personally read in this bundle.
- Do not guess at severity beyond a rough provisional tag (see schema
  below). Final severity is decided later by babs-triage against the
  program's own rubric text, gate by gate — your job is to surface a
  real, well-evidenced mechanism, not to argue its payout tier.
- Prefer a small number of well-evidenced findings over a long list of
  speculative ones. A finding with no concrete file:line citation and
  no concrete broken-value-plus-consuming-call pair is a LEAD, not a
  finding — label it as such rather than dressing it up.
- Stay inside the bundled in-scope source. Do not report on excluded
  files even if you notice something there in passing — note it as an
  aside in your completeness checklist instead, don't write it up as
  a finding.
- No generic security advice, no restating the rubric category name as
  if that were evidence, no "this could/may/enables" language in the
  mechanism description — describe what the code actually does.

## NO SKIP — completeness is mandatory, not best-effort

Every lens agent works from an explicit, enumerable list: known
issues (Agent 1), rubric bullets in your tier (Agents 2–4), or
identified critical invariants (Agent 5). You must address every
single item on that list before returning — not a representative
sample, not "the ones that looked promising first." An agent that
silently drops items partway through has not completed its job, even
if the items it did cover were handled well.

Before returning anything, produce a completeness checklist with one
line per list item, in the exact form:

```
[x] <item> — <FINDING #N | LEAD #N | checked, nothing found>
```

Every item gets a line. "Checked, nothing found" is a fully
acceptable, honest outcome — silence about an item is not.

## Output schema

Use this exact structure for every finding or lead you produce.

### FINDING #N
**Title:** [a few words]
**Lens/Agent:** [your agent number and name]
**Provisional severity:** [Critical | High | Medium | Low — your own
estimate, clearly marked provisional]
**File/Function:** [exact path and function/line]
**Mechanism:** [2-4 sentences — the exact broken behavior, in your
own words, grounded in what you read]
**Evidence:** [file:line citations; short exact excerpts where they
clarify the claim]
**Known-issue link (Agent 1 only):** [which known issue this bypasses
or extends, or "N/A — new, no known-issue anchor" if Agent 1 is
running in reduced mode]
**Rubric bullet targeted (Agents 2-4 only):** [the exact bullet text
you were hunting for when this surfaced]
**Invariant targeted (Agent 5 only):** [the invariant statement,
verbatim from source docs if one exists, or as you derived it]

### LEAD #N
**Observation:** [what looked suspicious]
**Why it's suspicious:** [the specific reason, not a vibe]
**What's missing to confirm:** [the exact gap — a trace you didn't
finish, a precondition you couldn't verify, a value you couldn't
pin down]

### Completeness checklist
[as specified above, one line per assigned list item]
