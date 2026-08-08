# babs-skill

Reusable Codex and Claude skill for smart-contract audit workflows, finding verification, PoC writing, fuzzing, report formatting, duplicate grouping, and bounty triage.

## Install

Clone this repository into your Codex skills directory:

    ~/.codex/skills/babs-skill/

Clone this repository into your Claude skills directory:

    ~/.claude/skills/babs-skill/

Commands

    ~run /babs-hunt in the in scope codebase using [platform name or custom rubric impacts criteria]
run /babs-dedup on the fundings
run /babs-triage against the fundings using [platform name guidelines or custom rubric impacts criteria]
run /babs-poc on the findings using [test name: local test/mainnet fork test/testnet] in [platform name format]

## Triggers

- `/instruction-01`: Architecture, invariants, and attack surfaces
- `/instruction-02-attack-surface`: Attack-surface bug hunt
- `/instruction-02-invariant`: Invariant break hunt
- `/instruction-03-adversarial`: Adversarial verification
- `/instruction-03-poc`: Foundry PoC writing
- `/instruction-04-fuzzing`: Foundry fuzzing test writer
- `/instruction-05-cantina`: Cantina report formatting
- `/instruction-05-sherlock`: Sherlock report formatting
- `/instruction-06-duplicates`: Duplicate finding triage
- `/instruction-07-triage`: Bounty finding triage

## Structure

    babs-skill-v2/
      SKILL.md
      agents/
        openai.yaml
      references/
        instruction-01.md
        instruction-02-attack-surface.md
        instruction-02-invariant.md
        instruction-03-adversarial.md
        instruction-03-poc.md
        instruction-04-fuzzing.md
        instruction-05-cantina.md
        instruction-05-sherlock.md
        instruction-06-duplicates.md
        instruction-07-triage.md
