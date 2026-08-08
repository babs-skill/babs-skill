# babs-skill

Reusable Codex and Claude skill for smart-contract audit workflows:
- Bug hunting
- Findings deduplication
- Contest and bug bounty triage
- Testnet, mainnet fork and local test writing and execution
- Report formatting in Cantina, sherlock, hackenProof and immunefi style

## Install

Clone this repository into your Codex skills directory:

    ~/.codex/skills/babs-skill/

Clone this repository into your Claude skills directory:

    ~/.claude/skills/babs-skill/

## Commands
```md
run /babs-hunt in the in scope codebase using [platform name or custom rubric impacts criteria]
run /babs-dedup on the fundings
run /babs-triage against the fundings using [platform name guidelines or custom rubric impacts criteria]
run /babs-poc on the findings using [test name: local test/mainnet fork test/testnet] in [platform name format]
```

## Skills

| Skill | Description |
| --- | --- |
| [babs-hunt](babs-hunt) | Runs 5 parallel agents: verifies known-issue fixes actually hold and checks if the fix itself introduces a new bug, hunts for sibling functions missing that same fix, hunts each Critical/High/Medium impact bullet from the program's own rubric for matching code, and hunts for breaks in the protocol's highest-stakes invariants |
| [babs-dedup](babs-dedup) | Merges findings and leads from multiple sources (babs-hunt, solidity-auditor, 0xSimao AI, manual review) into one clean set, grouping duplicates by root cause and fix path so nothing gets triaged or PoC'd twice |
| [babs-triage](babs-triage) | Runs every finding through a strict adversarial gate sequence — already accepted or known, privileged actor unless the protocol names it untrusted, mechanism confirmed real against the actual code, exploitable without a skipped documented safeguard, and a concrete broken-value-to-harmed-party chain — before it gets a severity |
| [babs-poc](babs-poc) | Writes the proof of concept as a local Foundry test, a mainnet/L2 fork test, or a live testnet exploit script, then formats the finding into a Cantina, Sherlock, or HackenProof report |

| Skill | Description |
| :-- | :--- |
| [babs-hunt](https://github.com/babs-skill/babs-skill/tree/main/babs-hunt) | Checks known-issues fix and try to bypass it, checks if the fix introduces new bug, hunt for siblings issues, impact driven and critical invariants breaks hunt |
| [babs-dedup](https://github.com/babs-skill/babs-skill/tree/main/babs-dedup) | |
| [babs-triage](https://github.com/babs-skill/babs-skill/tree/main/babs-triage) | |
| [babs-poc](https://github.com/babs-skill/babs-skill/tree/main/babs-poc) | |
