# Project workflow

## Approval policy

The owner approves the idea, approves the grilled spec, and approves merge. Between spec approval and merge approval, agents build tickets, implement, test, review, fix findings, and open or update PRs autonomously within the approved scope. The workflow itself does not grant access to repositories or external services.

After idea approval, agents draft and grill the spec autonomously, resolving questions from available evidence and bringing only consequential unresolved product decisions to the owner. After grilled-spec approval, agents create and validate the task graph and execute it without another planning approval. They select ready work, assign agents, test, request independent reviews, fix findings, rerun checks, integrate results, and prepare PRs without asking for permission at each step.

User intervention is reserved for the three approval gates, material scope decisions, or blockers that agents cannot resolve with their available authority and tools. Batch decision requests with a recommendation and supporting evidence. Continue unrelated ready work while one branch is blocked. Report progress without turning status updates into approval requests.

Record approvals against a specific artifact revision. Silence is not approval. Material changes to intended behavior, acceptance criteria, scope, architecture commitments, or constraints return to the owner for a spec decision. Routine implementation choices do not require another approval.

## Lifecycle

| Stage | Work and evidence | Exit condition |
| --- | --- | --- |
| 1. Ideate | Define the problem, intended users, desired outcome, alternatives, constraints, and non-goals. Challenge whether the project is worth doing. | Owner approves the idea brief. |
| 2. Specify | Write behavior, user flows, acceptance criteria, boundaries, dependencies, risks, and verification strategy. Identify unknowns explicitly. | A complete spec is ready for challenge; drafting alone is not approval. |
| 3. Grill the spec | Try to break the spec: ambiguous requirements, missing failure paths, conflicting constraints, feasibility, and untestable outcomes. Resolve findings and record decisions. | No unresolved blocking findings; owner approves the exact revised spec. |
| 4. Build tickets | Autonomously decompose approved outcomes into umbrella issues, tasks, subtasks, and optional sub-subtasks. Map leaf tickets to acceptance criteria and build a validated dependency graph. | All acceptance criteria have implementation and verification coverage; dependencies and parallel execution groups are explicit; ready tickets can execute without another planning conversation. |
| 5. Implement | Dispatch independent ready leaf tickets to separate agents concurrently, within capacity. Each agent inspects the code, makes a bounded change, and updates ticket evidence. | Each implementation satisfies its ticket and is ready to verify. |
| 6. Test | Run checks appropriate to the change and repository. Exercise acceptance criteria, failure paths, and regressions. | Required checks pass; evidence names the revision tested and any limitations. |
| 7. Review and fix | Review correctness, scope, maintainability, security where relevant, and missing verification. Fix findings and rerun affected checks. | All findings have a recorded disposition; no unresolved blockers; relevant checks pass on the latest revision. |
| 8. Submit PR | Open a reviewable PR with scope, linked tickets, behavior changes, verification, and residual risks. Address remote review and CI findings through the same fix loop. | PR is ready, required checks/reviews pass, and owner approves merge for the current head. |
| 9. Merge and learn | Merge using repository policy, verify the merge result, close completed tickets, and capture process friction. | Changes are merged, required post-merge checks pass, and outcome and follow-ups are recorded. |

A failed test returns to implementation. A review finding returns to implementation and testing. A spec contradiction returns to spec grilling. New scope becomes a separate proposed ticket or a spec revision. A merge failure remains open work; do not report completion just because a PR exists.

For a larger project, repeat stages 5–9 in small, coherent PRs. Every PR needs merge approval. A merge approval expires when its head changes. Parent issues close only when their required children and their own acceptance criteria are complete.

### Pull request stacks

Stack all pull requests for the same initiative by default. PR 1 targets the integration branch. Branch the second PR from the first PR's branch and use that branch as its base. Continue the pattern for later PRs. Number the stack in the PR descriptions and link each layer to its immediate prerequisite. Use independent branches against the same base only when the owner explicitly requests unstacked PRs.

Merge the stack in order, starting with PR 1. Before merging, check each layer's base, recorded order, exact approved head, and required checks. Once a lower layer merges, retarget the next layer as needed and confirm its diff contains only that layer's work. If retargeting requires a push that changes its head, rerun affected checks and obtain approval of the new head. Stack order applies to delivery and merge. Implement independent ready tasks in parallel as described below.

## Ticket hierarchy

Use only as much depth as the work needs:

- **Umbrella:** a project outcome or major capability, linked to the approved spec.
- **Task:** a coherent deliverable within the umbrella.
- **Subtask:** a bounded implementation or verification unit.
- **Sub-subtask:** an optional split when a subtask is still too large or has independent dependencies.

Only leaf tickets are scheduled for implementation. A leaf should have a clear outcome, acceptance criteria, dependencies, verification, and an expected PR boundary. Do not create empty hierarchy levels to satisfy a template. Cross-cutting verification can have its own ticket, but each implementation ticket still carries its own verification responsibilities.

Ticket states: `draft → ready → in_progress → verifying → reviewing → ready_for_pr → awaiting_merge → done`.

`blocked` records a reason, responsible decision or dependency, and a resume condition. It can apply at any unfinished state. Failed verification or required review fixes return the ticket to `in_progress`. Record each transition and its evidence. `done` requires merge and required post-merge checks, not merely local completion.

## Roles and execution

The owner supplies product decisions and the three approvals. A coordinator maintains phase, artifacts, dependencies, and handoff state. An implementer performs bounded ticket work. A reviewer challenges the spec or implementation and records findings with evidence.

Parallel execution by separate agents is a required capability of the eventual orchestrator. The coordinator dispatches multiple independent ready tickets whenever agent capacity is available. Each implementer receives an explicit scope, file ownership, prerequisite artifacts, and verification requirements. Code review uses a separate reviewer agent, with fixes returned to the implementer and rechecked by the reviewer. Spec grilling also uses an independent review pass. If the runtime cannot supply separate agents, record the capability limitation and any serial or self-review fallback explicitly; never present it as fulfilling the parallel or independent-review requirement.

## Dependency graph and parallel scheduling

The ticket hierarchy describes containment; a separate directed acyclic graph describes execution prerequisites. A parent-child relationship alone does not make siblings depend on each other. Every executable leaf has explicit incoming dependency edges, including an empty list for independent roots. Each edge names the prerequisite ticket, the required output, and the condition that releases the dependent ticket. Resolve dependencies on a parent into explicit leaf prerequisites or a defined aggregate completion condition.

Before dispatch, validate that ticket IDs are unique, every dependency exists, the graph has no cycles, all approved acceptance criteria are covered, and prerequisite contracts are clear. Repair planning defects autonomously within the spec. Do not introduce artificial dependencies merely to force serial execution.

Scheduling rules:

1. Compute the ready set: approved-scope leaf tickets with satisfied prerequisites, adequate context, defined verification, no active claim, and no unresolved blocker.
2. Assign independent ready tickets to separate agents concurrently, up to configured capacity. Claim each ticket once and persist its agent assignment before dispatch to prevent duplicate work. Queue excess ready work automatically.
3. Give concurrent implementers isolated working copies through the selected runtime and define ownership for shared files, interfaces, and mutable resources. Coordinate interface contracts first; serialize conflicting integration steps or add a real prerequisite where shared work cannot be isolated safely.
4. On completion, verify the output and record its immutable revision or artifact, tests, and review evidence. An agent saying it has finished is not sufficient to release a dependency.
5. Recompute the ready set whenever prerequisites complete, work fails, capacity becomes available, or a blocker is resolved. Dispatch newly ready tasks without waiting for unrelated work to finish. Use completion events or runtime handoffs, not repeated status polling.
6. Integrate compatible outputs, resolve routine conflicts, and run checks against the combined revision. Review the integrated result as well as individual changes. A parent is not complete just because its children passed in isolation.

Example: T1 and T2 have no prerequisites; T3 requires verified outputs from both. Dispatch T1 to agent A and T2 to agent B at the same time. T3 remains blocked if either prerequisite is unfinished or fails verification. Once both outputs pass their required tests and reviews and are available together on T3's working base, dispatch T3. If capacity is full, T3 enters the ready queue until a slot opens.

A prerequisite need not be merged before its dependent starts unless its edge explicitly requires merge. By default, its output must pass required tests and review and be incorporated into the dependent's base. Track this output readiness separately from ticket `done`, which still requires merge and post-merge checks. This allows T1, T2, and T3 to be delivered in one integrated PR without an intermediate human merge gate. If a prerequisite output changes or is invalidated, mark consumers stale, pause affected dispatches, and reverify or rework affected dependents against the new revision.

Failures block only affected graph branches. Agents attempt evidence-driven fixes autonomously within a configured retry or resource budget; repeated identical failures trigger diagnosis rather than endless retries. Escalate with the attempted fixes, exact blocker, and recommended decision when outside authority or unable to progress. Continue independent branches. Persist graph state, claims, output revisions, and dependency satisfaction so recovery can reconcile actual running agents and completed artifacts before restarting work.

## Records and resuming

Store one initiative directory in the target repository, for example `work/checkout-flow/`, with:

- `initiative.md`: phase, idea, approvals, approved spec revision, grilling findings, decisions, and handoff.
- `tickets/`: one file per ticket, or links to equivalent issues in a selected tracker.
- `task-graph.md`: dependency edges, ready/blocked sets, active claims, capacity, and prerequisite output evidence, using the supplied template.
- `reviews/`: review findings and verification evidence for each PR or spec revision.

Use the templates in this folder. Local files are the initial artifact format; no issue tracker, CI provider, or agent runtime is selected yet. If a tracker is introduced, choose one authoritative location and link it instead of maintaining competing status copies.

At each handoff, record the current phase, active ticket, changed revision, checks run, outstanding findings, blockers, and next action. Resume from this record and inspect the actual repository and PR state before proceeding. Preserve existing user changes.

Before requesting merge approval, prepare the full PR and required evidence. After approval, confirm that its head and required checks still match, then merge. Approval does not override branch protections or repository policy. Never deploy or publish as an implied part of merge; use the project's explicit release scope.

## Improving the loop

Record phase entry/exit times, approval wait, blocked time, review rounds, rework caused by unclear specs, and regressions discovered after merge. Separate active work from waiting so faster execution does not hide slow decisions. Start with lightweight observations; do not build a metrics system before the process is useful.

After each merged increment, capture the biggest source of friction, one proposed process change, and how to judge whether it helped. Apply small operational improvements within the approved workflow. Changes to approval gates or authority need the owner's agreement. Speed is useful when acceptance criteria remain satisfied and regression risk stays controlled.
