# readykeyper

Readiness checks for the keypers of Shutter's Permanent Private Voting suite ([shutter-network/shutter-governance-protocol](https://github.com/shutter-network/shutter-governance-protocol)).

> **Status: in development.** The work is split into the milestones below, each defined in its own issue.

## The problem

A keyper's `/health` endpoint says whether the process is up. It does not say whether the keyper can do its part in a vote. Several failures pass `/health` and only show up when key generation or the vote count fails:

- **A keyper restarts without its key share.** It keeps answering `/health` with `200`, and the vote count is abandoned later.
- **One keyper of three loses its share.** The vote still completes on the remaining shares, so the lost redundancy goes unnoticed until another keyper fails.
- **The coordinator identity is wrong.** Nothing reports it until key generation fails.
- **The clock is about five minutes off.** The keyper does not finish bootstrapping.

Operators learn about these when a vote fails.

## The solution

readykeyper checks the conditions a vote depends on before a keyper starts, restarts or rejoins a set. It reports each condition with a clear result, and gives an exit code that scripts and monitoring can act on.

## Features

- **Default mode, no secrets needed.** Run it from outside the keyper. It checks:
  - the keyper's status;
  - its announced key and address, against the deployment's public API;
  - its clock;
  - the elections it holds.
- **Deep mode.** Run it inside the keyper's container with `--deep`. It also checks the coordinator identity, the stored state, and the key share for each election.
- **Restore test.** Proves that a restored keyper still holds the share the vote count needs, and that a bad restore is caught: an empty folder, a backup from before key generation, or a missing secrets file.
- **Read-only.** It writes nothing, prints no key, share or token, and connects only to the keyper and the public API.
- **Exit codes:**
  - `0` ready;
  - `1` not ready;
  - `2` needs attention.

## Milestones

| | Milestone | Issue |
|---|---|---|
| M1 | Reproduce the gaps on a local three-keyper set | #1 |
| M2 | The default check, no secrets needed | #2 |
| M3 | The deep check, inside the keyper container | #3 |
| M4 | The restore test | #4 |
| M5 | Install, docs and an operator checklist | #5 |
| M6 | Feedback from operators | #6 |
| M7 | Optional: a pointer from the suite's own operator docs | #7 |

## Licence

[AGPL-3.0](LICENSE), the same licence as Shutter's voting suite.
