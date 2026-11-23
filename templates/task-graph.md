# Task graph: <initiative>

Approved spec revision:
Graph revision:
Coordinator:
Agent runtime:
Maximum concurrent agents (including reviewers):
Retry / resource budget:
Last reconciled at:

## Nodes and claims

List executable leaves. Hierarchy parents remain in the ticket map unless represented by an explicit aggregate dependency condition.

| Ticket | Prerequisite IDs (empty = root) | State | Assigned agent / run claim | Working copy / base | Output revision | Verification / review evidence |
| --- | --- | --- | --- | --- | --- | --- |
| T1 | | ready | | | | |
| T2 | | ready | | | | |
| T3 | T1, T2 | blocked | | | | |

## Dependency edges

Each row means the consumer waits for the prerequisite's named output and condition. Use immutable revisions for completed outputs. Parent containment is not a dependency edge.

| Prerequisite | Consumer | Required output / interface | Release condition | Consumed revision | Satisfied? / evidence |
| --- | --- | --- | --- | --- | --- |
| T1 | T3 | <define output> | Tests and review pass; output available on consumer base | | No |
| T2 | T3 | <define output> | Tests and review pass; output available on consumer base | | No |

## Validation and dispatch

- [ ] Unique IDs; all dependencies exist; no cycles.
- [ ] Approved acceptance criteria covered by implementation and verification.
- [ ] Edge outputs and release conditions are explicit.
- [ ] Shared interfaces and resource ownership are defined.
- [ ] Independent roots are eligible for separate concurrent agents.
- [ ] Claims are persisted before dispatch; recovery reconciles active runs.

Current ready set:
Active claims:
Queued for capacity:
Blocked branches and resume conditions:
Stale outputs / affected consumers:
Next dispatch or integration action:

T1 and T2 run concurrently on separate agents when capacity permits. T3 starts only after both edge conditions are satisfied. Verified outputs can release T3 before merge; ticket completion still requires merge. Integrate and check the combined revision before requesting merge approval.

## Events and recovery

| Time | Event / ticket | Claim or output revision | Dependency impact | Next action |
| --- | --- | --- | --- | --- |

Record starts, failures, verified completions, invalidations, integration checks, and reconciliations. A failed branch does not stop independent work. A changed output invalidates affected dependency evidence and triggers revalidation.
