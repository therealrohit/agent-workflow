# Agent Workflow

A Git-based workflow for taking a change from idea to verified merge with T3 Code and Codex. It provides approval gates, dependency-aware implementation, independent reviews, stacked pull requests, and templates for resuming work.

- [AGENTS.md](AGENTS.md) is the instruction entry point.
- [WORKFLOW.md](WORKFLOW.md) describes the process.
- [Templates](templates/) cover initiatives, tickets, dependency graphs, and reviews.

## Quickstart

Clone this repository to a location the agent can read. Choose either a project or a directory of projects to opt in. Replace the placeholders below with paths for that installation. Keep machine-specific instructions in local configuration, or use a relative path in a shared project when every contributor uses the same checkout layout.

### Enable for one project

Add this to the project's existing `AGENTS.md` or the active `AGENTS.override.md` at its root.

```markdown
For work in this project, read <workflow-checkout>/AGENTS.md and follow the workflow it references.
```

For a linked worktree, make the project's instruction file available in that checkout too. A shared relative path must resolve from the location specified in the instruction. A scratch session can opt in by naming both the target project and the workflow checkout in its prompt.

### Enable for a directory of projects

Add the following rule to the active Codex home's `AGENTS.md`. Codex uses `~/.codex` by default or the directory specified by `CODEX_HOME`. If that directory has a nonempty `AGENTS.override.md`, add the rule there. Preserve any existing instructions.

```markdown
When the target project is inside <projects-directory>, read <workflow-checkout>/AGENTS.md and follow the workflow it references. Match directory boundaries when checking scope. For a linked Git worktree, use git worktree list --porcelain to identify the primary checkout and check its location. In a scratch session, use the target project named in the task.
```

For example, you can use `~/projects` as the scope and `~/projects/agent-workflow` as the checkout. To maintain an optional shared local pointer, direct the global rule to the projects directory's `AGENTS.md` and put the workflow reference there. Codex searches from the Git root down, so the parent directory's file needs that explicit reference.

See [Codex instruction discovery](https://learn.chatgpt.com/docs/agent-configuration/agents-md) for precedence and session startup behavior.

### Verify in T3 Code

Open a fresh thread using Codex and ask it to identify the workflow entry point, the three approval gates, and when a dependent ticket can start. With directory-wide opt-in, repeat from an out-of-scope repository and an external linked worktree of an in-scope project. In a scratch thread, name an in-scope target project explicitly. The checkout and configured paths must be readable in the environment where the agent runs. Configure each linked machine or remote environment separately.

T3 Code can use its available delegation and worktree controls for independent ready tickets and reviewer assignments. Record any runtime limitations in the initiative. The workflow is driven by instructions and templates. Start a thread or task to run it.

To disable the workflow, remove the reference you added from the active instruction file.

## License

[MIT](LICENSE)
