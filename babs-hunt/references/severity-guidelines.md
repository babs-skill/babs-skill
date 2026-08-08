# Platform Severity Guidelines

Reference file for instruction-03-adversarial and instruction-07-triage.
Load this file when a platform is declared and apply the matching section exclusively.
Do not mix criteria across platforms.

---

## SHERLOCK
Link: https://docs.sherlock.xyz/audits/judging/guidelines

### Valid Severity Tiers: High, Medium only. Low and Informational are not rewarded.

### HIGH
Direct loss of funds without extensive external conditions. Loss must be significant:
- Users lose more than 1% AND more than $10 of their principal or yield
- Protocol loses more than 1% AND more than $10 of fees
- DoS: funds locked more than one week AND impacts a time-sensitive function (both must apply)

### MEDIUM
- Loss of funds requiring specific external conditions or constrained states. Loss must be relevant:
  - Users lose more than 0.01% AND more than $10 of principal or yield
  - Protocol loses more than 0.01% AND more than $10 of fees
- Breaks core contract functionality, rendering it useless or leading to relevant loss
- DoS: funds locked more than one week OR impacts a time-sensitive function (at least one must apply)
- Note: likelihood is NOT considered when assigning severity. Only impact counts.
- Note: if a single attack causes <0.01% loss but is infinitely replayable, treat it as 100% loss

### NOT VALID (hard invalids — stop immediately if any apply)
- Gas optimizations
- Incorrect event values
- Zero address checks
- User input validation unless it causes major protocol malfunction or significant loss to others
- Admin input/call validation: admin incorrect call order = invalid; admin action breaking assumptions = invalid
- Contract/admin address blacklisting affecting protocol = invalid (unless attacker weaponizes it against protocol)
- Front-running initializers with no irreversible damage
- User experience issues: temporarily inaccessible funds recoverable by admin = invalid
- User self-blacklisting (harms only themselves)
- Accidental direct token transfers harming only the sender
- Loss of airdrops or rewards not part of original protocol design
- Incorrect view function values (invalid unless used in a larger function causing fund loss)
- Stale Chainlink prices / round completeness (invalid unless protocol uses pull-based oracle without freshness check)
- Chain re-org and network liveness issues
- ERC721 unsafe mint
- Future issues from unmentioned integrations
- Non-standard token behavior unless explicitly in README
- Sequencer downtime or misbehavior
- Issues from previous contests marked wont-fix or acknowledged

### ADMIN TRUST RULES
- Internal protocol roles are trusted by default
- Admin acting maliciously alone = invalid
- Admin unknowingly causing harm during honest operation = potentially valid
- Admin restrictions explicitly defined in README can make bypass issues valid
- External admin breaking assumptions = invalid unless README defines restrictions

### DOS SEVERITY RULE
- Single occurrence <1 week + not time-sensitive = invalid
- Single occurrence <1 week + time-sensitive = medium
- Single occurrence >1 week = medium (at minimum)
- Both criteria (>1 week AND time-sensitive) = high
- Repeatable short DOS: if 2-3 iterations reach 7 days, may qualify as medium

---

## CANTINA
Link: https://docs.cantina.xyz/evaluations-and-standards/severity-classifications/competition-finding-severity

### Valid Severity Tiers: High, Medium, Low, Informational

### Severity Matrix (Impact × Likelihood)
| Severity | Impact: High | Impact: Medium | Impact: Low |
|---|---|---|--|
| Likelihood: High | High | High | Medium |
| Likelihood: Medium | High | Medium | Low |
| Likelihood: Low | Medium | Low | Informational |

- High impact = funds can be lost
- High likelihood = any participant can trigger the bug

### HARD RULES
- Admin-required issues: at most Low severity, unless the protocol was explicitly designed to be resilient against such actions
- User errors manageable by frontend: at most Informational
- Dust/rounding losses: at most Low
- ERC20 approval race conditions: invalid
- Weird token behavior (non-standard ERC20): at most Low unless README explicitly allows them
- Previously acknowledged findings: invalid
- AI-generated findings submitted without validation: disqualification risk

### SEVERITY DECISION HEURISTIC
Ask: what happens if this bug is never fixed?
- Catastrophic and triggerable by anyone or occurs naturally → High
- Protocol can function without the fix → Low
- Fix goes against the protocol's design philosophy → Informational at most

### PoC RULE (do not enforce during verification — only flag)
High and Medium submissions require a coded PoC. Exception: missing functions, or reputation score ≥ 80.

---

## HACKENPROOF
Link: https://docs.hackenproof.com/bug-bounty/vulnerability-classification/smart-contracts

### Valid Severity Tiers: Critical, High, Medium, Low

### CRITICAL
- Direct theft of funds or NFTs
- Permanent freezing of funds or NFTs
- Governance result manipulation (vote hijacking, quorum bypass)
- Protocol insolvency (under-collateralization, unbacked tokens, critical mispricing)
- Unauthorized minting or burning of tokens / direct manipulation of token supply

### HIGH
- Temporary freezing of funds or NFTs
- Theft of unclaimed funds (yield, royalties)
- Permanent freezing of unclaimed funds
- Oracle manipulation: influencing on-chain price feeds or data sources

### MEDIUM
- Theft of gas (unbounded loops, expensive operations exploitable by attackers)
- Gas limit / Out-of-Gas vulnerabilities (poor gas handling → transaction failure, loss of funds, halted functionality)
- Denial of Service: gas exhaustion, block stuffing, or malicious state manipulation that disrupts contract availability
- No-profit attacks (Griefing): damage to protocol or users without financial gain for the attacker

### LOW
- Failure to deliver promised returns (e.g. staking pool advertises fixed APY but underperforms due to bug)
- Uninitialized storage variables (can lead to privilege escalation but often low-risk)
- Any issue not matching above tiers → assess using CVSS score

### PRIVILEGED ACTION RULE
Any privileged action may be grounds for severity downgrade or disqualification.

---

## IMMUNEFI
Link: https://immunefi.com/immunefi-vulnerability-severity-classification-system-v2-3/

### Valid Severity Tiers: Critical, High, Medium, Low

### CRITICAL (Level 4)
- Manipulation of governance voting result deviating from voted outcome with direct change from intended effect
- Direct theft of any user funds (at-rest or in-motion), other than unclaimed yield
- Direct theft of any user NFTs (at-rest or in-motion), other than unclaimed royalties
- Permanent freezing of funds
- Permanent freezing of NFTs
- Unauthorized minting of NFTs
- Predictable or manipulable RNG resulting in abuse of principal or NFT
- Unintended alteration of what the NFT represents (token URI, payload, artistic content)
- Protocol insolvency

### HIGH (Level 3)
- Theft of unclaimed yield
- Theft of unclaimed royalties
- Permanent freezing of unclaimed yield
- Permanent freezing of unclaimed royalties
- Temporary freezing of funds
- Temporary freezing of NFTs

### MEDIUM (Level 2)
- Smart contract unable to operate due to lack of token funds
- Block stuffing
- Griefing (no profit motive for attacker, but damage to users or protocol)
- Theft of gas
- Unbounded gas consumption

### LOW (Level 1)
- Contract fails to deliver promised returns but does not lose value
