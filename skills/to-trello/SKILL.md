---
name: to-trello
description: Turn a conversation or rough idea into short Trello cards with native checklists.
disable-model-invocation: true
---

## 1. Read the context

1. Extract the outcome, agreed requirements, constraints, and open questions from the request and conversation.
2. Read the target repository's applicable `AGENTS.md` files and linked project instructions. Inspect code only when it can answer a material question.
3. Keep only task-relevant information. Exclude credentials and unnecessary personal or customer data.
4. Leave implementation planning for the later local session.

## 2. Find the destination

1. Use the board and list from the explicit request. Otherwise, use the repository instructions.
2. Discover the available Trello tools and their current schemas. Prefer a Trello MCP server, then a Trello connector. If authentication is required, give the connection instructions and pause.
3. Fetch the board and its lists. Verify their IDs, that the list belongs to the board, and that neither is archived. If the destination is missing or ambiguous, ask the user. Never guess a list from its name.

If card or native checklist tools are unavailable, offer a complete draft for manual creation. State that nothing was created.

## 3. Clarify the scope

1. Ask only unanswered questions that change the outcome, scope, or acceptance checks. Use the question tool when available. Batch related questions and give concrete choices where useful.
2. Offer to capture the idea with open questions. If the user chooses this, mark the description `Needs clarification` and put deferred decisions in a native `To clarify` checklist. Otherwise, resolve material gaps before proceeding.
3. Default to one card per outcome. Propose separate cards only for work that needs independent priority or delivery. Put steps within the same outcome in checklists. Include any proposed split in the final preview.

For "message customers when their report is done", clarify the channel, recipients, completion event, and message contents, including a report link or attachment. Reuse answers already in the conversation. Exact wording can remain an open question if the user wants to defer it.

Proceed when the outcome is clear and each material question is answered or explicitly deferred. Do not mark it ready for implementation while material questions remain.

## 4. Draft the card

Keep each card within these writing limits:

- Title: at most 100 characters, with a concrete action and outcome.
- Description: at most 2,000 characters, including Markdown, whitespace, and URLs.
- Checklist item: one short action, decision, or observable acceptance check.

Honor any smaller limit reported by the active tool or service. Keep essential context in the description so the card makes sense without this conversation.

Use this shape for the description. Omit optional lines and replace all placeholders:

```text
<Outcome and reason, in one or two sentences.>

Scope: <Agreed behavior and constraints.>
Status: Needs clarification.
Out of scope: <Relevant exclusions.>
References: <Existing links to supporting material.>
```

Include the status line only when questions remain. Link existing documents for longer details. If essential content exceeds the budget, ask about a smaller scope or a linked reference. Never silently truncate requirements or use comments as overflow storage.

Use native checklists:

- `To do`: known task-level actions in a useful order.
- `Done when`: observable acceptance checks, when they add information beyond `To do`.
- `To clarify`: decisions the user explicitly deferred.

Omit empty or redundant checklists. Keep all new items unchecked. Use Markdown checkboxes only in the preview.

## 5. Approve and create

1. Count the final title and description characters with a tool. Revise any excess without losing requirements, then count again.
2. Show the exact board, list, title, description, and grouped checklist items for every proposed card. Include the description character count.
3. Get approval for this preview before any Trello write. If the destination, scope, or content changes, get approval for the revised preview.
4. Create each approved card in the verified list.
5. Create its native checklists and items with the IDs returned by Trello.
6. Read back each card and its checklists. Verify the destination, title, description, checklist names, item text, order, and unchecked state against the approved preview.

Limit writes to the approved cards and their checklists. Leave repository files unchanged. Set labels, members, dates, or other metadata only when requested and included in the approved preview. Use existing boards and lists. Do not create boards, lists, or labels, or modify pre-existing cards.

## 6. Report the result

Return each verified card's URL and any unresolved decisions. Report success only when the approved card and all its checklist items are present.

If creation fails or times out:

1. Inspect the existing state before retrying. Use returned IDs when available. If a write returned no ID, look for the exact approved card, checklist, or item in its destination.
2. Retry only writes confirmed to be missing. If the result remains uncertain or a match might predate this run, stop and report the uncertainty. An inconclusive search does not prove that a write failed.
3. Preserve completed writes. Report their URLs, what remains missing, and any unknown write result. Never recreate the whole card or delete partial work to hide a failure.

If Trello rejects a size or object limit, ask how to reduce the remaining content before another write. Keep any card already created.
