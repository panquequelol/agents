Solve the user task with one Oracle advice plan, at most one follow-up advice request, and a strict Oracle review gate. The main agent owns all edits and checks. Oracle does not edit files.

Use [Oracle v3](../subagents/oracle_v3.md) for every request. Load the definition and confirm that Oracle's active instructions use it before work starts. Stop if installed legacy instructions conflict. Keep the existing finding IDs, routing states, and failure codes for compatibility.

## Explore

1. Read the code that controls the reported behavior.
2. Trace direct callers, tests, contracts, and canonical helpers.
3. Record the original problem, constraints, non-goals, baseline `HEAD`, pre-existing worktree changes, relevant files, required checks, and one small hypothesis that cites code evidence.
4. Mark each unverified claim as an assumption.
5. Do not edit files before Oracle returns its plan.

Use the recorded baseline and pre-existing-change evidence to identify task changes. Stop if the comparison cannot separate task changes from earlier work.

## Ask Oracle for advice

1. Ask one new Oracle instance with no prior context for an implementation plan.
2. Give Oracle a complete brief with the task, constraints, non-goals, relevant files, key code, evidence, assumptions, and hypothesis.
3. Ask Oracle to test the hypothesis and return one specific in-scope solution.
4. Require an ordered implementation plan with affected files and functions, behavior to preserve, checks, risks, and stop conditions.
5. If Oracle fails or does not return the complete plan, stop with `Verdict: Cannot approve`. Use the failure report with zero review cycles and state that the follow-up advice request was not used. Do not implement without the plan.
6. Use the original task as the authority when it conflicts with the Oracle plan.

## Implement

1. Implement Oracle's in-scope solution.
2. Record each necessary deviation from the plan and its reason.
3. Read the complete changed target.
4. Run the required targeted checks.
5. Do not run a build or start a development server unless the user asked for it.

## Oracle review loop

1. Set the cycle count to zero.
2. Set the follow-up advice budget to one request.
3. Before each review request, add one to the cycle count.
4. Never make more than four review requests.
5. Snapshot `HEAD`, the current diff, untracked task files, and check results.
6. Ask a new Oracle instance with no prior context to review the snapshot.
7. Give the reviewer a complete brief with the original problem, constraints, non-goals, Oracle plan, recorded comparison base and pre-existing-change evidence, current target files, current diff, related callers and tests, check results, and finding dispositions.
8. Do not give the reviewer a previous verdict.
9. Require the reviewer to review the complete current target, not only prior findings.
10. Treat a review response as complete only when it has exactly one standalone `Verdict:` line, one `Summary:` section with at most three bullets, one `Findings:` section with `None` or valid finding entries, and one `Caveats:` section. Treat a response with a missing or duplicate required part, or a verdict outside the v3 definition, as malformed.
11. For each non-approval finding, require the reviewer to return the v3 finding fields and:
    - `Finding ID: S<cycle>-F<sequence>`
    - `Root cause: <file:line and symbol> | unknown`
    - `Behavior authority: <task clause, test, contract, or file:line> | missing | conflict:<citations>`
    - `Minimal fix candidates:` followed by one or more `<files and symbols> | <observable behavior effect> | <owning module>` entries, or `unknown`
    - `Preservation conflict: none | <Oracle plan step, test, caller, or contract citation>`
12. Require the reviewer to combine syntax-only alternatives into one fix candidate. Do not ask it for a complexity label, a routing recommendation, or a confidence score.
13. Count a failed, incomplete, or malformed review request as one cycle. For cycles one through three, record it as `blocked: sentinel-request-failed` or `blocked: sentinel-response-malformed`, then stop without a follow-up advice request. Do not infer missing routing fields.
14. Accept approval only when a complete review response has the exact standalone line `Verdict: Approved`.
15. Treat every other result as non-approval.
16. Accept approval only when the target matches the snapshot and all required checks pass.

### Finding routing

For cycles one through three, assign each valid non-approval finding one state:

- `pending-direct`: The finding has one known root cause, one explicit behavior authority, one concrete minimal fix candidate, and no preservation conflict.
- `pending-oracle`: The finding needs advice because it cites conflicting behavior authorities, more than one behaviorally different valid fix candidate, or a preservation conflict.
- `fixed`: The main agent changed the target, read it, and reran all required checks.
- `rejected-invalid`: Code or check evidence disproves the finding or its stated impact.
- `rejected-out-of-scope`: Evidence shows that the finding is pre-existing, not task-caused, or requires a change outside the frozen goal.
- `blocked`: The root cause, behavior authority, or concrete fix is missing, or the finding cannot progress because input is missing, a follow-up advice request failed, the advice budget is exhausted, or a required fix conflicts with the frozen goal.

Record material verification gaps as `blocked`, even when `Findings:` is `None`. Optional caveats are not findings.

Do not route by priority, file count, line count, estimated effort, or terms such as complex, subtle, risky, or architectural.

### Oracle finding advice

1. After classification, stop without a follow-up advice request if any finding is `blocked`.
2. If no finding is `blocked` and one or more findings are `pending-oracle`, ask one new Oracle instance for advice before changing the reviewed snapshot.
3. Batch all `pending-oracle` findings from that review cycle into the same request.
4. Give Oracle the original task, frozen goal, constraints, non-goals, initial Oracle plan, plan deviations, current snapshot, complete target, diff, related callers and tests, check results, complete review response, finding dispositions, and each escalated finding with all routing fields.
5. Ask Oracle to test each finding. Do not ask it to approve the implementation or re-plan the complete task.
6. Require one result for each finding with `Finding ID`, `Assessment: supported | unsupported | out-of-scope | needs-input`, `Evidence`, `Recommended candidate`, `Smallest action`, `Behavior to preserve`, and `Required checks`.
7. Do not count the advice request as a review cycle and do not reset the review cycle count.
8. Never retry the advice request. Never make more than one follow-up advice request.
9. If the advice request fails, is incomplete, or is malformed, move every batched finding to `blocked: oracle-request-failed` and stop.
10. After a complete Oracle response, verify its evidence and map each assessment:
    - Map verified `supported` with one concrete candidate and no remaining conflict to `pending-direct`.
    - Map verified `unsupported` to `rejected-invalid`.
    - Map verified `out-of-scope` to `rejected-out-of-scope`.
    - Map `needs-input`, invalid evidence, no concrete candidate, or remaining ambiguity to `blocked`.
11. Oracle advice does not replace the next approval review. The original task remains the authority.

### Resolution and stop rules

1. Resolve each `pending-direct` finding as `fixed`, `rejected-invalid`, `rejected-out-of-scope`, or `blocked`.
2. Fix each actionable finding that the task caused and that stays within the frozen goal.
3. Do not change unrelated code to get approval.
4. After each fix, read the changed target and rerun all required checks.
5. Give each new reviewer the finding disposition ledger without a previous verdict.
6. Start the next cycle only when no finding is `pending-direct` or `pending-oracle`.
7. Stop before cycle four only when the reviewer returns `Verdict: Approved` or a finding becomes `blocked`.
8. If a new finding needs advice after the follow-up advice budget is used, mark it `blocked: oracle-budget-exhausted` and stop.
9. Do not request advice after cycle four because no review cycle remains to verify a resulting fix.
10. If cycle four fails, is malformed, or does not return `Verdict: Approved`, record the raw result and failure as a finding, then use `Verdict: Cannot approve` as the terminal verdict.
11. If the loop stops without approval, do not start another review. Return the failure report.

## Final response

For an approved implementation, report the implementation, check results, cycle count, and Oracle review verdict.

If the initial Oracle fails or the loop stops without approval, output only these sections:

## Original problem
State the problem in one sentence.

## Implementation
State the solution and check results in one short paragraph. If implementation did not start, state that.

## Oracle review findings
List the latest findings, their states, and the stop reason in one-line bullets. For an initial Oracle failure, list the Oracle blocker. Write `None` when there are no findings.

## Oracle review verdict
Use `Verdict: Cannot approve`. State the latest raw review verdict or `not requested`, the review cycle count, and whether the follow-up advice request was used.
