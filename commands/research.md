Answer a research question with Librarian lanes, a coverage check, and one synthesis. The main agent owns decomposition, gate checks, and the final answer. Librarian owns the searching and writes the ledgers.

## Scope

1. Restate the question. State the decision it informs, criteria, time window, geography, exclusions, and known facts.
2. Choose the tier in this order. If the user asks for a fast check, use `quick`. Else if the user asks for depth, the answer drives a decision with real cost, or the subject is a market, company, or person, use `deep`. Else use `standard`.
3. Set `run-id` to `<yyyy-mm-dd>-<hhmmss>-<slug>`. If `tmp/research/<run-id>/` exists, append `-2`, then `-3`, until the name is free. Create the directory.

## Lanes

1. For `deep`, split the question into three to five lanes. A lane is independent when its probes run without another lane's result.
2. For `standard` and `quick`, use one lane.
3. For each lane, launch one Librarian task in the background. Give it the lane question, the decision, criteria, time window, geography, exclusions, known facts, the tier, and the ledger path `tmp/research/<run-id>/<lane-id>.md`. One lane writes one ledger.
4. Write `tmp/research/<run-id>/manifest.md` with one row per lane: lane id, task id, ledger path, `resumes: 0`, `status: open`.
5. Wait for every lane.
6. If a lane task fails or its ledger file is missing, launch it once more with the same brief and update its task id. If it fails again, set its manifest row to `status: blocked`, `reason: task-failed`.

## Coverage check

1. For each lane with manifest `status: open`, recalculate from the probe and source rows: probes, opened sources, source classes, zero-delta streak, and `failed` source classes per subquestion. Compare them with the tier row in `subagents/librarian.md`.
2. A lane passes when both hold. Every subquestion is `saturated`, or `blocked` with `failed` probes in three distinct source classes. Every `saturated` subquestion meets the saturation condition of its tier row.
3. If a lane passes, set its manifest row to `status: saturated`.
4. If a lane fails and `resumes` is two, set its manifest row to `status: blocked`, `reason: coverage`.
5. If a lane fails and `resumes` is below two, resume its task with the exact failing subquestion and gate, add one to `resumes`, wait for the task, then return to step 1.
6. Continue when no manifest row has `status: open`.

## Synthesis

1. For one lane, use its ledger as the answer source. If the lane has no ledger, give the lane status and reason as the answer.
2. For several lanes with at least one ledger, launch one Librarian in synthesis mode. Give it the full brief from Scope step 1, the tier, the manifest path, every existing ledger path, and the output path `tmp/research/<run-id>/synthesis.md`.
3. For several lanes without any ledger, give the lane statuses and reasons as the answer.
4. If the synthesis task fails or the synthesis file is missing, launch it once more. If it fails again, answer from the ledgers and state that synthesis failed.
5. Check each material claim against its `<lane-id>:<source-id>` entries. A claim needs two independent origins or one controlling authority. Mark a claim that fails as `not enough evidence` and list it under uncertain.

## Final response

1. Answer first. Then known, uncertain, and disputed.
2. Sources with dates.
3. Gaps, and lanes with `status: blocked` and their reason.
4. Ledger paths. For several lanes, the synthesis path or the synthesis failure.
