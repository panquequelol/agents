# Coding Agents

Agent instructions, specialist agent prompts, code rules, and MCP setup.

## Use

Copy the files you need into an agent project. The main agent reads `AGENTS.md`.

Each file in `subagents/` is a system prompt. Install it as the subagent's instructions. A subagent cannot read these files at run time and gets no conversation history, so every request needs a self-contained brief. `AGENTS.md` tells the main agent when to invoke each subagent and what the brief must contain.

Oracle gives advice unless the brief asks for an approval review with a gate verdict. Use a new invocation for approval. An installed Oracle does not gain these rules from its name or a file mention. Replace conflicting agent instructions before you use it.

| File | Use |
| --- | --- |
| [AGENTS.md](./AGENTS.md) | Main agent rules for concise technical text, code, and agent work. |
| [rules/STANDARDS.md](./rules/STANDARDS.md) | Design, workflow, TypeScript, and React rules. |
| [rules/BETTER-RESULT.md](./rules/BETTER-RESULT.md) | Error handling with `better-result` v3. |
| [mcp.json](./mcp.json) | MCP server configuration. |

## Commands

| File | Use |
| --- | --- |
| [commands/create-pr.md](./commands/create-pr.md) | Create a draft PR that targets `main`. |
| [commands/research.md](./commands/research.md) | Run one or two bounded Librarian lanes, then synthesize in the main agent. |
| [commands/review.md](./commands/review.md) | Run a three-axis review across Defects, Standards, and Complexity. |
| [commands/scan.md](./commands/scan.md) | Scan the codebase for one known issue pattern and report findings with evidence. |
| [commands/vercel.md](./commands/vercel.md) | Interface critique of screen and behavior, in the voice of a hostile design audit. |

## Specialist agents

Use a specialist agent for its assigned task. Give each agent the problem, constraints, relevant files, and a required output.

| Agent | Use |
| --- | --- |
| [Oracle](./subagents/oracle.md) | Get implementation advice or a strict approval review based on the request. |
| [Librarian](./subagents/librarian.md) | Research the workspace, the web, documentation, public code, and business contacts. Use `lookup` for API signatures. |

## Skills

Install agent skills from [skills.sh](https://skills.sh/) with `npx skills add`.

### Install selected skills

Use `--skill` to install only the selected skills from multi-skill packages:

```sh
npx skills add mattpocock/skills --skill domain-modeling grill-me grill-with-docs grilling handoff wizard writing-for-agents
npx skills add vercel/ai@ai-sdk
npx skills add vercel-labs/agent-skills --skill writing-guidelines
npx skills add cloudflare/skills --skill cloudflare workers-best-practices
npx skills add raphaelsalaja/userinterface-wiki@userinterface-wiki
npx skills add superdesigndev/treg --skill treg
npx skills add Jakubantalik/transitions.dev --skill transitions-dev
npx skills add cursor/plugins --skill thermo-nuclear-code-quality-review deslop
npx skills add pedronauck/skills --skill motion-react
npx skills add emilkowalski/skills --skill find-animation-opportunities
npx skills add haowjy/creative-writing-skills --skill creative-writing-craft
npx skills add dmmulroy/skills --skill bro
npx skills add jakubkrehel/skills --skill better-writing better-interface
```

### Skill list

| Skill | Source | Install Command |
| --- | --- | --- |
| `ai-sdk` | [vercel/ai](https://skills.sh/vercel/ai/ai-sdk) | `npx skills add vercel/ai@ai-sdk` |
| `better-interface` | [jakubkrehel/skills](https://skills.sh/jakubkrehel/skills/better-interface) | `npx skills add jakubkrehel/skills@better-interface` |
| `better-writing` | [jakubkrehel/skills](https://skills.sh/jakubkrehel/skills/better-writing) | `npx skills add jakubkrehel/skills@better-writing` |
| `bro` | [dmmulroy/skills](https://skills.sh/dmmulroy/skills/bro) | `npx skills add dmmulroy/skills@bro` |
| `cloudflare` | [cloudflare/skills](https://skills.sh/cloudflare/skills/cloudflare) | `npx skills add cloudflare/skills@cloudflare` |
| `creative-writing-craft` | [haowjy/creative-writing-skills](https://skills.sh/haowjy/creative-writing-skills/creative-writing-craft) | `npx skills add haowjy/creative-writing-skills@creative-writing-craft` |
| `deslop` | [cursor/plugins](https://skills.sh/cursor/plugins/deslop) | `npx skills add cursor/plugins@deslop` |
| `domain-modeling` | [mattpocock/skills](https://skills.sh/mattpocock/skills/domain-modeling) | `npx skills add mattpocock/skills@domain-modeling` |
| `find-animation-opportunities` | [emilkowalski/skills](https://skills.sh/emilkowalski/skills/find-animation-opportunities) | `npx skills add emilkowalski/skills@find-animation-opportunities` |
| `grill-me` | [mattpocock/skills](https://skills.sh/mattpocock/skills/grill-me) | `npx skills add mattpocock/skills@grill-me` |
| `grill-with-docs` | [mattpocock/skills](https://skills.sh/mattpocock/skills/grill-with-docs) | `npx skills add mattpocock/skills@grill-with-docs` |
| `grilling` | [mattpocock/skills](https://skills.sh/mattpocock/skills/grilling) | `npx skills add mattpocock/skills@grilling` |
| `handoff` | [mattpocock/skills](https://skills.sh/mattpocock/skills/handoff) | `npx skills add mattpocock/skills@handoff` |
| `motion-react` | [pedronauck/skills](https://skills.sh/pedronauck/skills/motion-react) | `npx skills add pedronauck/skills@motion-react` |
| `thermo-nuclear-code-quality-review` | [cursor/plugins](https://skills.sh/cursor/plugins/thermo-nuclear-code-quality-review) | `npx skills add cursor/plugins@thermo-nuclear-code-quality-review` |
| `transitions-dev` | [Jakubantalik/transitions.dev](https://skills.sh/Jakubantalik/transitions.dev/transitions-dev) | `npx skills add Jakubantalik/transitions.dev@transitions-dev` |
| `treg` | [superdesigndev/treg](https://skills.sh/superdesigndev/treg/treg) | `npx skills add superdesigndev/treg@treg` |
| `userinterface-wiki` | [raphaelsalaja/userinterface-wiki](https://skills.sh/raphaelsalaja/userinterface-wiki/userinterface-wiki) | `npx skills add raphaelsalaja/userinterface-wiki@userinterface-wiki` |
| `wizard` | [mattpocock/skills](https://skills.sh/mattpocock/skills/wizard) | `npx skills add mattpocock/skills@wizard` |
| `workers-best-practices` | [cloudflare/skills](https://skills.sh/cloudflare/skills/workers-best-practices) | `npx skills add cloudflare/skills@workers-best-practices` |
| `writing-for-agents` | [mattpocock/skills](https://skills.sh/mattpocock/skills/writing-for-agents) | `npx skills add mattpocock/skills@writing-for-agents` |
| `writing-guidelines` | [vercel-labs/agent-skills](https://skills.sh/vercel-labs/agent-skills/writing-guidelines) | `npx skills add vercel-labs/agent-skills@writing-guidelines` |

### When to grill

| Situation | Skill |
| --- | --- |
| No repo, or the idea is not about this codebase | `grill-me` |
| A change in a repo, or a repo with no domain docs | `grill-with-docs` |

`grill-me` writes no files. `grill-with-docs` writes terms to `CONTEXT.md`.

## Local notes

Put plans, research logs, and other AI exploration in `docs/`. Do not commit this folder.

Push `CONTEXT.md` and other shared domain files. `docs/` holds personal noise from the agent.

## Tooling

Always use `oxlint` and `oxfmt` over other alternatives.

- Run [`oxlint`](https://oxc.rs/docs/guide/usage/linter/rules/eslint/complexity) for linting and complexity checks.
- Run `oxfmt` for code formatting.
- Run [`anti-slop`](https://github.com/dmmulroy/anti-slop) to detect and remove AI code patterns.

## MCP servers

`mcp.json` configures these servers:

- `opensrc`
- `grep-app`
- `exa`. Export `EXA_API_KEY` before you start the agent.
