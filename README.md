# readykeyper

A readiness check for the keypers of Shutter's Permanent Private Voting suite ([shutter-network/shutter-governance-protocol](https://github.com/shutter-network/shutter-governance-protocol)). It finds what `/health` misses (a lost key share, a wrong coordinator identity, clock skew, a bad restore) before a keyper starts, restarts or rejoins a set that secures live votes.

> **Status: in development.** The work is split into the milestones below. Each one is defined in its own issue, and each ends with a demo.

## Why

I set up the suite's keypers on my own machine, following its docs. I found that a keyper can answer `/health` with `200` and still break a vote:

- **A keyper restarts without its key share.** It still reports healthy, and the vote count is abandoned later.
- **One keyper of three loses its share.** The vote still counts on the other two, and nobody notices until a second one goes.
- **The coordinator identity is wrong.** Nothing complains until key generation fails.
- **The clock is about five minutes off.** The keyper never finishes bootstrapping.

An operator finds out when a vote fails. readykeyper is meant to catch these before that.

## What it checks

**Default mode** needs no secrets. It checks:
- the keyper's own status;
- its announced key and address, against Shutter's public API;
- its clock;
- the elections it holds.

**Deep mode** runs inside the keyper's container. It also checks the coordinator identity, the stored state, and the key share for each election.

**Exit codes:**
- `0` ready;
- `1` not ready;
- `2` needs attention.

**A restore test** proves that a restored keyper still holds the share the vote count needs.

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
