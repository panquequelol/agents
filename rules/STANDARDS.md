# Standards

## Principles

### Subtract Before You Add
When: Apply when sequencing an addition, refactor, or rewrite. Remove dead code, redundant validators, and stub references first, then build on the simpler base.

When evolving a system, remove complexity first, then build.

**Why:** Adding to a complex system compounds complexity. Removing first leaves less code, reveals the essential structure, and usually makes the next design obvious. Default to subtraction.

Make simplification a continual investment. Leave the design slightly simpler and more capable behind the same or smaller surface than you found it.

**The pattern:**
- Sequence removal before construction
- Cut before you polish (get to the minimum before investing in quality)
- Design for observed usage, not speculative edge cases
- No speculative validators, parsers, or guards beyond what the spec demands
- Simplify prompts (remove redundant instructions, excessive templates)
- When a reference has no novel content, delete it rather than leaving a stub

### Redesign From First Principles
When: Apply when integrating a new requirement into an existing design. Redesign as if the requirement had been a foundational assumption from day one, instead of bolting it on.

When integrating a change, don't bolt it onto the existing design. Redesign as if the requirement had been there from the start.

- Read all affected files and understand the current design
- Ask: "if we were writing this from scratch with this new requirement, what would we build?"
- Propagate the change through every reference: types, docs, examples, rationale sections
- Think about the whole redesign, then deliver it incrementally

This is the method for preserving option value when integrating changes into an existing design.

### Attack the Premise

When two or more fixes that share one premise have failed the same gate, suspect the premise, not the fixes.

**Why:** Each failure under a shared premise is evidence about the premise.

**Pattern:**
- **Write the premise down.** The premise is the one sentence that every failed fix assumed.
- **Take a census before the next fix.** Count the imbalance per actor. The census shows which actors hold the imbalance, not how large it is. Write the census as a rerunnable script.
- **Read the skew.** If the same few actors hold most of the imbalance on every run, something assigns them that role. Find what assigns the role. That assignment is the next "why" to trace to its root cause.
- **Remove the asymmetry instead of compensating for it**, per the [Laziness Protocol](#laziness-protocol). Rotate the role between actors, randomize the assignment, or move the role, so that no actor holds it on every run. A return path, a shared pool, a batched hand-off, or a periodic rebalance leaves the assignment in place and adds work on every run.

**Stop:**
- Do not start the next fix before the premise is written down and the census exists.
- If the census is even across actors, the premise is not the cause. Look for the cause elsewhere and keep the census as evidence.

This principle is distinct from [Redesign From First Principles](#redesign-from-first-principles), which rebuilds a design around a new requirement. It questions a fact the current design assumes.

### Laziness Protocol
When: Apply when refactoring, evaluating diff size, or tempted to add abstractions, layers, or signal threading. Bias toward deletion and the smallest change that solves the problem.

Aim for the most result with the least code and complexity.

- **Prefer deletion.** When asked to refactor or improve, look for removals before additions.
- **Maintain a flat call hierarchy.** Avoid deep call chains. A rich interface that hides substantial work is not a deep call chain. If answering a question requires tracing through more than 3 files or layers, flatten it.
- **Consolidate decisions.** Do not repeat the same choice in several places. Put it behind one source of truth and pass the result as a simple flag.
- **Minimize the diff.** Make the smallest change that solves the problem. Fewer lines beat "elegant" boilerplate.
- **Question the threading.** If a task asks you to pass a new signal through types, schemas, pipelines, or similar layers, stop and look for a more direct path.
- **Sweat the small leaks.** Remove tiny pass-throughs, representation leaks, and duplicated choices before they spread. Small leaks compound into permanent coordination costs.

**The test:** If a human developer would find the code exhausting to maintain, it is a bad solution.

### Model the Domain
When: Apply when writing stateful logic, or when code branches a lot or repeats a shape assumption across files. Encode the domain in a structure instead of scattered conditionals.

Encode the real domain in a data structure instead of scattering it across conditionals.

**Why:** Scattered booleans, repeated shape assumptions, and branching spread across files are accidental complexity. A structure that matches the domain makes invalid states unrepresentable and deletes branches. Choosing it at write time is cheap. Recovering it later reads as a refactor and gets deferred.

**Reach for structures like these:**

- A state machine instead of scattered booleans, phases, or lifecycle checks.
- A typed object/model instead of loose parameters or repeated shape assumptions.
- A map, registry, lookup table, or discriminated union instead of branching spread across files.
- A reducer or command/event model instead of ad hoc state mutations.
- A module organized around one body of domain knowledge instead of a sequence such as load, validate, transform, and save. Execution order is not ownership.
- A small module boundary that gathers repeated behavior, ownership, or invariants.
- A queue, cache, index, graph/tree, or normalized collection where the data access pattern calls for it.
- Any other structure that fits. When none fits, work out what the code must never allow and how the data gets read, then find the structure that encodes exactly that.

Do not force an abstraction. Prefer boring code if the current shape is already clear, local, and unlikely to grow. Be skeptical of an abstraction that adds indirection without removing branches, duplicated rules, invalid states, or lifecycle risk.

The sign that you skipped this is a new feature that grows an existing if/else chain by one more branch, or a second boolean that must stay in sync with the first. Temporal decomposition is another sign. Phase-named modules repeat the same domain rules across steps.

### Sequence Work Into Verifiable Units
When: Apply to multi-step work (sweeps, migrations, runs of similar edits) and to how you stack commits and PRs. Break work into small units that each end in a verifiable state, check each before the next, and order delivery so the sequence proves itself to a reviewer.

Order work as a sequence of small units, each ending in a state you can check, and don't advance until the current one is green.

**Why:** A break caught at the unit that caused it is cheap to localize. A break caught after a batch is buried, and you have already built further on a broken base. Sequencing those same units into a delivery a reviewer can replay turns "trust me" into "watch it go red, then green."

**Execution.** In a sweep, migration, or any run of similar edits, verify each change before starting the next. Each unit is a before/after bracket: known-good state, one change, run the check, then proceed. Rebase onto clean trunk first so every check measures against the real baseline. When a lever does the edits, the per-unit check is nearly free. Run it anyway.

**Delivery.** Stack commits and PRs in the order that proves the work. The canonical shape is the failing test first, then the fix on top. Other story orders are a subtraction before the reshape, a baseline capture before the treatment, the scaffold before the feature. Each commit lands on its own and the sequence reads as an argument.

**Pattern:**
- Pick the smallest unit that ends in a check: an edit plus its test, or a commit that stands alone.
- Verify before advancing. Red to green per unit, never deferred to a final batch.
- Order the units so the sequence builds confidence on its own, for you while executing and for a reviewer reading the stack.

Keep each check against the real artifact, and make the per-unit check cheap enough to run every time.

### Boundary Discipline

Place validation, type narrowing, and error handling at system boundaries. Trust internal code unconditionally. Business logic lives in pure functions. The shell is thin and mechanical.

**Why:** Scattered validation is noisy, redundant, and gives a false sense of safety. Keep logic out of framework wiring so it can be tested without the framework.

**The pattern:**
- **At boundaries** (CLI args, config files, external APIs, network protocols): validate, return errors, handle defensively.
- **Inside the system:** typed data, error propagation, no re-validation. Trust the types.
- **Across the boundary.** Expose domain concepts, not the boundary's private representation. Keep general-purpose mechanism inside and special-purpose policy at the edge.

**Applications:**

Validation and error handling:
- Validate config at parse time (the boundary), not inside business logic
- Parse raw data into domain types at the boundary
- Do not re-export transport, storage, framework, or wire types through the public surface
- No redundant nil checks deep in call chains if the boundary already validated

Code organization:
- Business logic in pure functions with no framework dependencies
- Parse functions: pure transforms from raw bytes to typed state
- Prompt construction: structured state in, string out
- Scoring and assessment: pure transforms from state to results

**The tests:**
- "Is this data crossing a system boundary right now?" If not, validation is redundant.
- "Can this be a pure function that the shell just calls?" If yes, extract it.

### Encode Lessons in Structure

Encode recurring fixes in mechanisms (tools, code, metadata, automation) instead of textual instructions. Every error, human correction, and unexpected outcome is a learning signal. Capture it, route it, and close the loop.

**Why:** Textual instructions are easy to miss. They require the reader to notice, remember, and comply. Structural mechanisms (lint rules, metadata flags, runtime checks, automation scripts) enforce the rule without cooperation.

**Pattern:**
When you catch yourself writing the same instruction a second time:
1. Ask: can this be a lint rule, a metadata flag, a runtime check, or a script?
2. If yes, encode it. Delete the instruction
3. If no (requires judgment), make the instruction more prominent and add an example of the failure mode

**Pick the strongest mechanism.** When more than one mechanism would work, choose the strongest the situation allows (an unrepresentable state that cannot compile, then a lint or banned API that fails CI, then a canonical helper, then a runtime check), because agents copy whatever the surrounding code already does and a weaker guard becomes the next template.

**Corollary:** If the fix is structural, only use the structural fix. The instruction is the symptom.

**Feedback loop:**
- **Capture every correction.** When the human intervenes or tests fail, decide if it's a one-off or a pattern.
- **Route to the right layer.** One-off -> session note. Recurring fix -> rule or lint. Systemic issue -> principle.
- **Close the loop.** Don't just record. Apply now or create a concrete todo.

**Anti-patterns:**
- Acknowledging without recording ("I'll keep that in mind" does not persist)
- Recording without routing (a note about a lint rule that should exist is wasted unless the lint rule gets implemented)
- Fixing without generalizing (fixing one instance while leaving the recurring pattern intact)

### Never Block on the Human

The human supervises asynchronously. Agents must stay unblocked. Make reasonable decisions, proceed, and let the human course-correct after the fact.

**Why:** Every permission pause stalls the pipeline and makes the human the bottleneck. Since code changes are reversible and reviewable, a wrong decision usually costs less than blocking.

**Pattern:**
- **Proceed, then present.** Do the work, show the result. Don't ask "should I do X?" Do X, explain why.
- **Reserve questions for genuine ambiguity.** Ask only when you cannot infer intent from context.
- **Make the system self-healing.** When you notice a problem, log it and fix it in the next round.
- **Supervision is async.** Design workflows for review-after-the-fact.

**Boundaries:**
- **Irreversible actions** (force-push, delete production data, send external messages) still require confirmation.
- **Reversible actions** (write code, edit notes, split tasks) should proceed without blocking.
- **Product direction** comes from the human. *Execution* should not block.

## Code Organization

- **Single responsibility**: Each source file should have a clear, focused scope/purpose
- **Split large files**: Break files when they become large or handle too many concerns
- **Type separation**: Always separate types and interfaces into `types.ts` or `types/*.ts`
- **Constants extraction**: Move constants to a dedicated `constants.ts` file

### Runtime Environment

- **Prefer isomorphic code**: Write runtime-agnostic code that works in Node, browser, and workers whenever possible
- **Clear runtime indicators**: When code is environment-specific, add a comment at the top of the file:

```ts
// @env node
// @env browser
```

### Explicitness

Favor explicit, traceable code over implicit "magic". A reader (human or agent) should be able to follow where every name comes from without running tooling.

### Comments

- **Avoid unnecessary comments**: Code should be self-explanatory
- **Explain "why" not "how"**: Comments should describe the reasoning or intent, not what the code does

## TypeScript

TypeScript is infested by landmines, from `Promise` to try-catch blocks.

All steps in problem-solving must be explicit, pure and composable.

### TypeScript Patterns

- Bias for functional programming.
- Colocate code that changes often close together, code that changes together belongs together.
- Compose a program via multiple isolated functions, features are about piping data into the right shape.
- Constructive type modeling: Build the shape so the illegal value can't be constructed.
- Discriminated unions: Model variants with a kind literal discriminant so impossible states can't be represented. No optional-field bags.
- Schema-derived types: Reach for `Pick`/`Omit`/`Parameters`/`ReturnType`/`Awaited`/`typeof` before declaring a new interface.
- Object args: Pass objects, not positional, so argument order is self-documenting. Skip on hot paths (per-frame render, tokenizers, parsers).
- `Type` > `interface`
- `Function` > Arrow functions (`() => {...}`). Arrow functions only used in callbacks. Composing a feature? `Function`, Piping a map? `.map((...) => {...})`
- Prefer small, cohesive modules organized around one primary domain type or concept
- In TypeScript, when a module is centered on a primary type, prefer an OCaml-style namespaced module pattern: `export type X = ...` plus `export const X = { ... } as const` for constructors, parsers, combinators, and other domain operations
- Prefer attaching domain logic to the module for its primary type rather than scattering it across generic utility files
- When a module starts accumulating substantial logic for other types or domains, split those concerns into their own sibling modules
- Prefer specific domain modules over catch-all `utils` files
- `Record<string, unknown>` considered harmful slop. It’s slop likely indicative of widening or erasing types and or not parsing types into stronger domain types at IO boundaries.

## Error Handling

- USE errors as values over throwing exceptions for expected failure paths
- USE `better-result` (`dmmulroy/better-result`) for fallible operations
- USE tagged/structured error types over untyped error strings
- Reserve thrown exceptions for truly exceptional, unrecoverable, or framework-boundary cases
- Propagate errors explicitly; do not swallow them or replace them with success-shaped fallbacks

If the project uses `better-result`, read `~/.dud/rules/BETTER-RESULT.md` for patterns.

### Error Message Design

- Write error messages to help the reader understand and recover: say what happened, why it happened if known, what the impact is, and what to do next
- Prefer specific, concrete wording over vague or generic messages
- If the cause is unknown, say that plainly; do not invent false precision
- State what is still true or preserved, especially whether data, prior work, or system state remain intact
- Include the most useful recovery action or next diagnostic step
- Match detail to audience: user-facing errors should be plain and actionable; internal errors should include precise operational context needed for debugging
- Internal errors should name the failing operation, relevant identifiers, expected vs actual state when useful, and the most likely remediation path

## HTTP APIs

Without an error schema, a caller must guess what failed. A structured error lets the caller fix the input and retry with intent.

- Each 4xx and 5xx response has a schema. 429 and 503 responses give `Retry-After` data. 400 responses identify invalid fields.
- All errors use one schema.
- Each error has a machine-readable code. Each schema has a human-readable message.
- Examples do not expose internal details.

Predictable names let callers infer paths safely. Inconsistent names force callers to memorize exceptions and can cause bugs.

- Resource paths use nouns, such as `/users` and `/orders`. Collection paths use plural names.
- All paths use one case. Prefer kebab-case.
- Use HTTP methods, not action verbs in URLs.

## React

**BANNED: `useEffect`**

Direct usage of `useEffect` is BANNED in this codebase. Most `useEffect` usage compensates for something React already provides better primitives for. Banning the hook forces logic to be declarative, predictable, and event-driven, preventing race conditions, infinite loops, and dependency hell. Use a query library (e.g., React Query) for data fetching.

Banning `useEffect` forces cleaner tree design:
- Parents own orchestration and lifecycle boundaries.
- Children can assume preconditions are already met.
- Each unit does one job, and coordination happens at clear boundaries.
