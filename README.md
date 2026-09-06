# Coding Agents

Agent instructions, specialist agent prompts, code rules, and MCP setup.

## Use

Copy the files you need into an agent project. The main agent reads `AGENTS.md`.

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
| [commands/planning.md](./commands/planning.md) | Write an implementation plan in vertical slices, then stop for approval. |
| [commands/research.md](./commands/research.md) | Answer a research question with Librarian lanes, a coverage check, and one synthesis. |
| [commands/solve-with-oracle-sentinel.md](./commands/solve-with-oracle-sentinel.md) | Solve a task with one Oracle plan and a Sentinel approval gate. |
| [commands/loop-local-approval.md](./commands/loop-local-approval.md) | Run local review loops with Sentinel as the approval gate. |
| [commands/loop-remote-approval.md](./commands/loop-remote-approval.md) | Clear in-scope Pullfrog findings on the current PR. |
| [commands/citadel.md](./commands/citadel.md) | Performance critique in the voice of a hostile audit. |
| [commands/linus.md](./commands/linus.md) | Design critique in the voice of a hostile maintainer. |
| [commands/vercel-linear.md](./commands/vercel-linear.md) | Interface critique in the voice of a hostile design audit. |

## Specialist agents

Use a specialist agent for its assigned task. Give each agent the problem, constraints, relevant files, and a required output.

| Agent | Use |
| --- | --- |
| [Oracle](./subagents/oracle.md) | Get a second opinion for plans, debugging, specifications, and refactor choices. |
| [Sentinel](./subagents/sentinel.md) | Review changes before handoff or commit. Do not treat work as complete without its approval. |
| [Librarian](./subagents/librarian.md) | Research markets, companies, people, and current docs on the web with a depth tier and a written ledger. |

## MCP servers

`mcp.json` configures these servers:

- `opensrc`
- `grep-app`
- `deepwiki`
- `exa`. Export `EXA_API_KEY` before you start the agent.
- `sequential-thinking`
