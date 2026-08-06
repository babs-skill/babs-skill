# STEP 1 — LOCAL TEST POC
## Write a Foundry PoC that demonstrates the confirmed impact.

### WORKFLOW:
1. Inspect existing test files for setUp(), helpers, fixtures, token setup
2. Reuse everything reusable - write only new attack code
3. Keep it short, readable, and direct with no much comments and avoid using bullet dashes at all.

### OUTPUT:
- Full `.t.sol` file
- Exact command to run it

**Before finalizing, verify:**
- Assertion measures the actual bug, not a weaker proxy
- Test fails if bug is fixed
- Attack amount is 30-60% of available liquidity unless finding requires otherwise
- One-two console.log to demonstrate the impact.


# STEP 2 — MAINNET FORK POC
## Write a Foundry PoC as a mainnet/L2 fork test against the real live deployment.

### WORKFLOW:
1. Pull real, already-deployed contract addresses from the program's own scope/deployment documentation, never guess or reuse addresses from memory.
2. Declare minimal, self-contained interfaces for only the functions the PoC calls, skip importing the project's own source tree entirely, this avoids remapping and dependency issues since the PoC only needs the ABI, not the implementation.
3. Fork with vm.createSelectFork(vm.envString("<RPC_ENV_VAR>")), reading the RPC endpoint from an environment variable rather than hardcoding it.
4. Simulate the trigger condition deterministically with cheatcodes (vm.mockCall, vm.warp, vm.deal, deal) rather than waiting for it to occur naturally, and use vm.snapshot/vm.revertTo to show a clean before/after contrast on the same state.
5. Keep it short, readable, and direct with no much comments and avoid using bullet dashes at all.

### OUTPUT:
- Full `.t.sol` file
- foundry.toml content
- Exact setup and run commands, from forge install through the final `forge test` call

**Before finalizing, verify:**
- Assertion measures the actual bug, not a weaker proxy
- Test fails if bug is fixed
- Attack amount is 30-60% of available liquidity unless finding requires otherwise
- One-two console.log to demonstrate the impact


# STEP 3 — TESTNET SCRIPT (.s.sol)
## Write a Foundry Script that executes the exploit as real, broadcast transactions on a public testnet.

### WORKFLOW:
1. Check whether the program has an official testnet deployment of the target contracts, if yes reuse those exact addresses, if no the script must first deploy the relevant contracts, or a minimal faithful reproduction of the vulnerable logic, itself before running the exploit sequence.
2. Write a contract inheriting forge-std's Script, with a run() function wrapped in vm.startBroadcast()/vm.stopBroadcast() so every call inside becomes a real transaction rather than a simulated one.
3. Keep it short, readable, and direct with no much comments and avoid using bullet dashes at all.
4. Attempt to actually run the script if the environment currently allows outbound RPC access, otherwise hand off the script and commands for the user to run.

### OUTPUT:
- Full `.s.sol` file
- foundry.toml content
- Exact setup and run commands, including the `forge script ... --rpc-url <TESTNET_RPC> --broadcast --private-key <KEY>` invocation

**Before finalizing, verify:**
- Assertion/log measures the actual bug, not a weaker proxy
- Attack amount is 30-60% of available liquidity unless finding requires otherwise
- Script prints the resulting transaction hash(es) or a block explorer link so the outcome is independently verifiable
