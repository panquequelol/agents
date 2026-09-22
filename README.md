# Coding Agents

Agent kit for dud.cl. It holds the main agent rules, specialist prompts, code rules, skills, and command prompts.

Follow [INSTALL.md](./INSTALL.md) to install the kit.

## Contents

- [AGENTS.md](./AGENTS.md): main agent rules
- [commands/](./commands/): command prompts
- [mcp.json](./mcp.json): MCP servers
- [rules/](./rules/): design principles, code standards, and `better-result` patterns. Install writes them to `~/.dud/rules/`.
- [skills/](./skills/): nuke, research, to-pr, to-trello, and verify
- [subagents/](./subagents/): Oracle and Librarian system prompts

Lint with oxlint. Format with oxfmt. Check AI code patterns with anti-slop.
