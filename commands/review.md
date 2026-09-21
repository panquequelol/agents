---
description: "Run a three-axis code review across Defects, Standards, and Complexity."
argument-hint: "[fixed-point] [spec-path]"
---

Review changes against a fixed point with three parallel Oracle subagents. The main agent handles verification, lane dispatch, synthesis, and the final gate verdict.

## 1. Initial verification

1. Identify the fixed point reference. When unspecified, use `HEAD~1` on `main`, or `main` on other branches.
2. Verify the reference with `git rev-parse <fixed-point>`. If the reference is invalid, stop immediately.
3. Find the merge base commit:
   `BASE=$(git merge-base <fixed-point> HEAD 2>/dev/null || echo <fixed-point>)`
4. Capture all changes between the merge base and the working tree:
   - Unified diff: `git diff $BASE`.
   - Commit log: `git log $BASE..HEAD --oneline`.
   - Untracked files: `git status --porcelain`.
5. Locate the specification target:
   - If provided, check the `[spec-path]` argument.
   - Check the commit log for issue references in the format `#123`.
   - Search for matching files in `docs/` or `specs/`.
   - If found, store the path or reference in `SPEC_TARGET`. If not found, set `SPEC_TARGET` to `none`.
6. If the diff and untracked files are empty, stop. Do not launch subagents for empty changes.
7. Run repository test, lint, and type checks. Record command names, exit codes, and output text as check evidence.

## 2. Parallel review lanes

Launch three parallel Oracle subagents in background mode: Defects, Standards, and Complexity. Subagents remain read-only. Do not allow file edits or state mutations during review. Collect completion reports before synthesis. Do not poll.

Use the installed Oracle review checklist. Assign performance, database access, and telemetry to Defects. Assign compliance and search visibility to Standards. Assign safe removal of unused code and cleanup to Complexity. Preserve the other relevant checks.

In each brief, instruct the subagent to remain strictly read-only, designate `$BASE` as the comparison base, and provide the unified diff, commit log, untracked files, check evidence, and `SPEC_TARGET` when active. Require an approval review with a gate verdict (`Changes requested`, `Cannot approve`, or `Approved`) and structured findings:
- Priority: P0 (data loss, security hole, crash), P1 (bug, regression, unmet requirement), P2 (local defect, standard violation), P3 (nit).
- Location: `file:line`.
- Issue: What fails and under what condition.
- Realistic failure case: Concrete scenario that triggers the defect.
- Impact: Concrete operational or developer effect.
- Evidence: Supporting lines, contracts, or test outputs.
- Fix: Smallest concrete code change.
- Type: Introduced, pre-existing, or unmet task requirement.

### Lane 1: Defects
- Focus: Runtime bugs, logic flaws, and edge cases.
- Rules: Assume invalid inputs, concurrency, and failures. When `SPEC_TARGET` is active, verify diff against specification requirements.
- Checks:
  - Off-by-one errors, boundary conditions, and empty or null collections.
  - Unhandled async faults, floating promises, and race conditions.
  - Non-atomic partial updates that corrupt state on failure.
  - Behavioral regressions, missing requirements, or contradictions of `SPEC_TARGET`.

### Lane 2: Standards
- Focus: Repository standards, domain modeling, and naming.
- Rules: Check against `rules/STANDARDS.md` and `rules/BETTER-RESULT.md`. Documented rules override general smells. Skip items that tooling already enforces.
- Code smell heuristics:
  - Primitive Obsession: Loose strings or numbers used instead of domain models or tagged errors.
  - Data Clumps: Three or more parameters passed together instead of a typed object.
  - Repeated Switches: Branching on type strings across files instead of discriminated unions.
  - Mysterious Name: Ambiguous names that hide intent.
  - Divergent Change: One module modified for multiple unrelated responsibilities.
  - Feature Envy: Functions that inspect external module state instead of calling pure domain operations.
- Checks: Banned `useEffect`, untyped errors, missing `better-result` TaggedError, loose `Record<string, unknown>`, and missing domain boundaries.

### Lane 3: Complexity
- Focus: Structural minimalism, code deletion, and control flow simplicity.
- Rules: Delete unnecessary complexity. Do not rearrange complex code. Gate blocks only on introduced complexity. Report pre-existing complexity under Caveats.
- Code smell heuristics:
  - Speculative Generality: Unused parameters, premature abstractions, or dead extension points.
  - Middle Man: Functions that pass calls through without adding behavior.
  - Duplicated Code: Copied logic that must be consolidated or deleted.
  - Shotgun Surgery: Tightly coupled changes spread across many files.
- Checks:
  - Flag high cyclomatic complexity: Report functions with more than 10 decision paths or nested decision trees (such as nested loops, switch cases, and chained boolean operators). Recommend early returns, lookup tables, or smaller functions to simplify control flow.
  - Find opportunities where redesign deletes branches, helper functions, or layers.
  - Reject unstructured branching: flag nested `if` statements and single-use boolean flags added to existing functions.
  - Reject pass-through functions, intermediate helpers, and unused abstractions.
  - Flag introduced call chains deeper than 3 layers.

## 3. Synthesis and action plan

1. Parse all lane reports.
2. Resolve conflicts with this precedence hierarchy:
   - `Defects` overrides `Standards` and `Complexity`. Do not delete safety guards, atomic rollbacks, or error handling to satisfy style rules or shorten code.
   - `Standards` overrides `Complexity`. Do not delete required types or domain schemas to shorten code.
   - Apply `Complexity` reductions after you satisfy defect and standards requirements.
3. Deduplicate findings across lanes. If multiple lanes flag the same root issue, merge them into one item under the higher-precedence axis and cite secondary axes.
4. Consolidate the gate verdict:
   - If any active lane returns `Verdict: Changes requested`, the overall verdict is `Verdict: Changes requested`.
   - If no lane requested changes, but any active lane fails to return a report, times out, produces unconfirmed output, or returns `Verdict: Cannot approve`, the overall verdict is `Verdict: Cannot approve`.
   - If all active lanes return `Verdict: Approved`, the overall verdict is `Verdict: Approved`.
5. Output the result in this exact order:
   - Standalone `Verdict: <verdict>`.
   - `## Summary`: At most three bullets summarizing key results.
   - `## Remediation Plan`: A sequenced markdown task list ordered from P0 to P3 for the main agent or developer to address. Write `None` when there are no findings. Each item specifies file, line, reason, and concrete fix.
   - `## Findings by Axis`: Deduplicated findings grouped under `### Defects`, `### Standards`, and `### Complexity`. Write `None` under an axis when it has no findings.
   - `## Caveats`: Consolidated deduplicated caveats, pre-existing complexity, and verification gaps across all lanes. Write `None` when there are no caveats.
