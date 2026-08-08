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
| :-- | :--- |
| [babs-hunt](https://github.com/babs-skill/babs-skill/tree/main/babs-hunt) | Checks known-issues fix and try to bypass it, checks if the fix introduces new bug, hunt for siblings issues, impact driven and critical invariants breaks hunt |
| [babs-dedup](https://github.com/babs-skill/babs-skill/tree/main/babs-dedup) | |
| [babs-triage](https://github.com/babs-skill/babs-skill/tree/main/babs-triage) | |
| [babs-poc](https://github.com/babs-skill/babs-skill/tree/main/babs-poc) | |
