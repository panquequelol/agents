# Coding Agents

Agent instructions, specialist agent prompts, code rules, and MCP setup.

## Use

Copy the files you need into an agent project. The main agent reads `AGENTS.md`.

Each file in `subagents/` is a system prompt. Install it as the subagent's instructions. A subagent cannot read these files at run time and gets no conversation history, so every request needs a self-contained brief. `AGENTS.md` tells the main agent when to invoke each subagent and what the brief must contain.

The workflows use [Oracle](./subagents/oracle.md) for advice and approval review. Oracle gives advice unless the brief asks for an approval review with a gate verdict. Use a new invocation for approval. An installed Oracle does not gain these rules from its name or a file mention. Replace conflicting agent instructions before using these workflows.

| File | Use |
| --- | --- |
| [AGENTS.md](./AGENTS.md) | Main agent rules for concise technical text, code, and agent work. |
| [ASSISTANT.md](./ASSISTANT.md) | Voice and answer-shape rules for short, direct replies. |
| [rules/STANDARDS.md](./rules/STANDARDS.md) | Code organization, TypeScript, and React rules. |
| [rules/better-result.md](./rules/better-result.md) | Error handling with `better-result`. |
| [mcp.json](./mcp.json) | MCP server configuration. |

## Commands

| File | Use |
| --- | --- |
| [commands/create-pr.md](./commands/create-pr.md) | Create a draft PR that targets `main`. |
| [commands/grill.md](./commands/grill.md) | Interview a subject until the brief has no silent assumptions. |
| [commands/planning.md](./commands/planning.md) | Write an implementation plan in vertical slices, then stop for approval. |
| [commands/research.md](./commands/research.md) | Run one or two bounded Librarian lanes, then synthesize in the main agent. |
| [commands/solve-with-oracle-sentinel.md](./commands/solve-with-oracle-sentinel.md) | Solve a task with Oracle advice and a fresh Oracle approval review. |
| [commands/loop-local-approval.md](./commands/loop-local-approval.md) | Run local review loops with Oracle as the approval gate. |
| [commands/loop-remote-approval.md](./commands/loop-remote-approval.md) | Clear in-scope Pullfrog findings on the current PR. |
| [commands/citadel.md](./commands/citadel.md) | Performance critique in the voice of a hostile audit. |
| [commands/linus.md](./commands/linus.md) | Design critique in the voice of a hostile maintainer. |
| [commands/vercel-linear.md](./commands/vercel-linear.md) | Interface critique in the voice of a hostile design audit. |

## Specialist agents

Use a specialist agent for its assigned task. Give each agent the problem, constraints, relevant files, and a required output.

| Agent | Use |
| --- | --- |
| [Oracle](./subagents/oracle.md) | Get implementation advice or a strict approval review based on the request. |
| [Librarian](./subagents/librarian.md) | Research the web, documentation, public code, and business contacts. Use `lookup` for API signatures. |

The solve command keeps its existing filename.

## MCP servers

`mcp.json` configures these servers:

- `opensrc`
- `grep-app`
- `exa`. Export `EXA_API_KEY` before you start the agent.
- `sequential-thinking`
