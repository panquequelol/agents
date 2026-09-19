# Coding Standards

- **Single responsibility**: Each source file should have a clear, focused scope/purpose
- **Split large files**: Break files when they become large or handle too many concerns
- **Type separation**: Always separate types and interfaces into `types.ts` or `types/*.ts`
- **Constants extraction**: Move constants to a dedicated `constants.ts` file

## Runtime Environment

- **Prefer isomorphic code**: Write runtime-agnostic code that works in Node, browser, and workers whenever possible
- **Clear runtime indicators**: When code is environment-specific, add a comment at the top of the file:

```ts
// @env node
// @env browser
```

## Explicitness

Favor explicit, traceable code over implicit "magic". A reader (human or agent) should be able to follow where every name comes from without running tooling.

## Comments

- **Avoid unnecessary comments**: Code should be self-explanatory
- **Explain "why" not "how"**: Comments should describe the reasoning or intent, not what the code does

# TypeScript

TypeScript is infested by landmines, from `Promise` to try-catch blocks. 

All steps in problem-solving must be explicit, pure and composable.

## TypeScript Patterns

- Bias for functinal programming.
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

# Error Handling

- USE errors as values over throwing exceptions for expected failure paths
- USE `better-result` (`dmmulroy/better-result`) for fallible operations
- USE tagged/structured error types over untyped error strings
- Reserve thrown exceptions for truly exceptional, unrecoverable, or framework-boundary cases
- Propagate errors explicitly; do not swallow them or replace them with success-shaped fallbacks

## Error Message Design

- Write error messages to help the reader understand and recover: say what happened, why it happened if known, what the impact is, and what to do next
- Prefer specific, concrete wording over vague or generic messages
- If the cause is unknown, say that plainly; do not invent false precision
- State what is still true or preserved, especially whether data, prior work, or system state remain intact
- Include the most useful recovery action or next diagnostic step
- Match detail to audience: user-facing errors should be plain and actionable; internal errors should include precise operational context needed for debugging
- Internal errors should name the failing operation, relevant identifiers, expected vs actual state when useful, and the most likely remediation path

# BANNED: useEffect

Direct usage of `useEffect` is BANNED in this codebase. Most `useEffect` usage compensates for something React already provides better primitives for. Banning the hook forces logic to be declarative, predictable, and event-driven, preventing race conditions, infinite loops, and dependency hell. Use a query library (e.g., React Query) for data fetching.

Banning `useEffect` forces cleaner tree design:
- Parents own orchestration and lifecycle boundaries.
- Children can assume preconditions are already met.
- Each unit does one job, and coordination happens at clear boundaries.
