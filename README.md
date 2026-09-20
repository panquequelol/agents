# Coding Agents

Agent instructions, specialist agent prompts, code rules, and MCP setup.

## Use

Copy the files you need into an agent project. The main agent reads `AGENTS.md`.

Each file in `subagents/` is a system prompt. Install it as the subagent's instructions. A subagent cannot read these files at run time and gets no conversation history, so every request needs a self-contained brief. `AGENTS.md` tells the main agent when to invoke each subagent and what the brief must contain.

Oracle gives advice unless the brief asks for an approval review with a gate verdict. Use a new invocation for approval. An installed Oracle does not gain these rules from its name or a file mention. Replace conflicting agent instructions before you use it.

| File | Use |
| --- | --- |
| [AGENTS.md](./AGENTS.md) | Main agent rules for concise technical text, code, and agent work. |
| [rules/PRINCIPLES.md](./rules/PRINCIPLES.md) | Design, workflow, TypeScript, and React rules. |
| [rules/BETTER-RESULT.md](./rules/BETTER-RESULT.md) | Error handling with `better-result` v3. |
| [mcp.json](./mcp.json) | MCP server configuration. |

## Commands

| File | Use |
| --- | --- |
| [commands/create-pr.md](./commands/create-pr.md) | Create a draft PR that targets `main`. |
| [commands/research.md](./commands/research.md) | Run one or two bounded Librarian lanes, then synthesize in the main agent. |
| [commands/citadel.md](./commands/citadel.md) | Performance critique in the voice of a hostile audit. |
| [commands/linus.md](./commands/linus.md) | Design critique in the voice of a hostile maintainer. |
| [commands/vercel.md](./commands/vercel.md) | Interface critique of screen and behavior, in the voice of a hostile design audit. |

## Specialist agents

Use a specialist agent for its assigned task. Give each agent the problem, constraints, relevant files, and a required output.

| Agent | Use |
| --- | --- |
| [Oracle](./subagents/oracle.md) | Get implementation advice or a strict approval review based on the request. |
| [Librarian](./subagents/librarian.md) | Research the workspace, the web, documentation, public code, and business contacts. Use `lookup` for API signatures. |

## MCP servers

`mcp.json` configures these servers:

- `opensrc`
- `grep-app`
- `exa`. Export `EXA_API_KEY` before you start the agent.
