# Agent Workflow

A Git-based workflow for taking a change from idea to verified merge with T3 Code and Codex. It provides approval gates, dependency-aware implementation, independent reviews, stacked pull requests, and templates for resuming work.

- [AGENTS.md](AGENTS.md) is the instruction entry point.
- [WORKFLOW.md](WORKFLOW.md) describes the process.
- [Templates](templates/) cover initiatives, tickets, dependency graphs, and reviews.

## Quickstart

Clone this repository to a location the agent can read. Choose either a project or a directory of projects to opt in. Keep host-specific paths in your local instruction files, or use environment variables when sharing one instruction file across machines.

### Configure paths with environment variables

Set `AGENT_WORKFLOW_DIR` to the absolute path of the workflow checkout. For directory-wide opt-in, also set `AGENT_WORKFLOW_PROJECTS_DIR` to the absolute path of the projects directory. For example, in a POSIX shell with the checkout at `~/projects/agent-workflow`:

```sh
export AGENT_WORKFLOW_DIR="$HOME/projects/agent-workflow"
export AGENT_WORKFLOW_PROJECTS_DIR="$HOME/projects"
```

Set these variables in the environment that actually runs the agent. A shell export affects that shell and its child processes. If your GUI launcher, linked machine, or remote runner starts the agent separately, configure the variables there or substitute literal paths in its local instruction file. If you use literal paths, you can skip the exports. Open a fresh session after changing the setup.

### Enable for one project

Add this to the project's existing `AGENTS.md` or the active `AGENTS.override.md` at its root.

```markdown
For work in this project, resolve AGENT_WORKFLOW_DIR from the agent process environment. If it is set, read AGENTS.md in that directory and follow the workflow it references. If the path is unset or unreadable, report the missing setup and continue using the project's own instructions.
```

For a linked worktree, make the project's instruction file available in that checkout too. A shared relative path must resolve from the location specified in the instruction. A scratch session can opt in by naming both the target project and the workflow checkout in its prompt.

### Enable for a directory of projects

Add the following rule to the active Codex home's `AGENTS.md`. Codex uses `~/.codex` by default or the directory specified by `CODEX_HOME`. If that directory has a nonempty `AGENTS.override.md`, add the rule there. Preserve any existing instructions.

```markdown
Resolve AGENT_WORKFLOW_PROJECTS_DIR and AGENT_WORKFLOW_DIR from the agent process environment. Apply this workflow only when the scope is configured and the target project is inside it. Compare resolved paths at directory boundaries. For a linked Git worktree, use git worktree list --porcelain to identify the primary checkout and check its location. In a scratch session, use the target project named in the task. For an in-scope project, read AGENTS.md from AGENT_WORKFLOW_DIR and follow the workflow it references. If the workflow path is unset or unreadable, report the missing setup and continue using the project's own instructions.
```

To maintain an optional shared local pointer, direct the global rule to the projects directory's `AGENTS.md` and put the workflow reference there. For example, when the checkout is a child of that directory, point to `agent-workflow/AGENTS.md` relative to the local pointer. Codex searches from the Git root down, so the parent directory's file needs that explicit reference. For another agent runtime, put the equivalent rule in its project or user instruction file.

See [Codex instruction discovery](https://learn.chatgpt.com/docs/agent-configuration/agents-md) for precedence and session startup behavior.

### Verify in T3 Code

Open a fresh thread using Codex and ask it to identify the workflow entry point, the three approval gates, and when a dependent ticket can start. With directory-wide opt-in, repeat from an out-of-scope repository and an external linked worktree of an in-scope project. In a scratch thread, name an in-scope target project explicitly. The checkout and configured paths must be readable in the environment where the agent runs. Configure each linked machine or remote environment separately.

T3 Code can use its available delegation and worktree controls for independent ready tickets and reviewer assignments. Record any runtime limitations in the initiative. The workflow is driven by instructions and templates. Start a thread or task to run it.

To disable the workflow, remove the reference you added from the active instruction file.

## License

[MIT](LICENSE)
