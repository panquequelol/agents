Answer one deep research question with one or two bounded Librarian runs. The main agent owns scope, lane selection, synthesis, and the final answer. The workspace stays unchanged.

## Scope

1. State the question and the decision it informs.
2. State criteria, time window, geography, exclusions, known facts, and reasonable assumptions.
3. Use the `deep` tier.

## Lanes

1. Use one lane by default.
2. If the question has two independent parts, use two parallel lanes.
3. For two lanes, pass lower limits of 4 rounds, 8 calls per round, 8 opened sources, and 20 minutes to each Librarian.
4. Give each Librarian its lane question, full scope, and limits.
5. Launch each lane once and wait for its result.
6. If a lane fails, mark it `blocked: task-failed` and continue with the available result.

Use at most two Librarian runs. The shared maximum is 8 rounds, 64 research calls, 16 opened sources, 20 minutes for research, and 5 minutes for parent synthesis. Stop the full pass 25 minutes after the lanes start. Do not retry a lane. Do not launch a synthesis agent.

## Synthesis

1. Merge the lane findings in the main agent.
2. Keep each claim linked to its lane sources.
3. Use two independent origins or one controlling authority for each material claim.
4. Mark weaker claims `not enough evidence`.
5. Show disagreements and missing coverage as gaps.
6. Stop after synthesis. Do not start more research.

## Final response

1. Lead with the answer.
2. Give material findings with direct sources and dates.
3. Give public business contacts in the format from `subagents/librarian.md` when requested.
4. State uncertain claims, blocked lanes, assumptions, and gaps.
5. Give total rounds, research calls, opened sources, and elapsed time.
