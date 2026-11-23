# Shared project workflow

When this workflow is enabled for a project, read [WORKFLOW.md](WORKFLOW.md) before starting project work. Use the [templates](templates/) as needed. Resolve these paths from the directory containing this file.

## Required behavior

- Follow ideation → specification → spec grilling → ticket graph → implementation → testing → review and fixes → PR → merge and learning.
- Stack PRs for the same initiative by default and merge them from the bottom up. Open independent PRs against the same base only when the owner explicitly requests unstacked PRs. Use the central workflow for initial bases, retargeting, and approval checks.
- Obtain owner approval for the idea, the grilled spec, and the exact PR head to merge. Reuse recorded approvals for the current scope; resume existing initiatives at their actual stage rather than restarting ideation for every ticket or conversation. Never infer approval from silence.
- After idea approval, draft and grill the spec autonomously. After grilled-spec approval, autonomously create tickets and their dependency graph, execute, test, review, fix findings, integrate, and prepare PRs. Ask only at the agreed gates, for material scope decisions, or for blockers that cannot be resolved within available authority and tools.
- Assign independent ready tasks to separate agents concurrently, within runtime capacity. Dependent tasks wait for the prerequisite outputs and verification specified by their edges. Use isolated working copies and explicit ownership, and test the integrated result. Use separate reviewer agents. Record a runtime limitation if these capabilities are unavailable; do not claim serial execution or self-review meets the parallel or independent-review requirement.
- Keep initiative state, approvals, tickets, graph edges, agent claims, immutable output revisions, verification evidence, findings, and next actions in the project's `work/<initiative>/` records or its chosen authoritative tracker. Reconcile these records with the actual code and PR state on resume. Do not invent approvals, test results, or completion.
- Continue independent work when one graph branch is blocked. Address review findings and rerun affected checks before preparing the merge approval request. Merge only after approval and required repository checks, then verify the result and record a process improvement.
- Preserve existing user changes. Repository-specific instructions supply setup, commands, and conventions. Follow explicit user directions for exceptions to this default workflow.

For questions or read-only exploration, answer directly without creating an initiative unnecessarily. When maintaining the workflow itself, treat process edits as process edits; do not start a recursive initiative for each template or instruction adjustment.
