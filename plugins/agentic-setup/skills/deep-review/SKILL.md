---
name: deep-review
description: Review a change for necessity (was this asked for?) and consequence (given it exists, what breaks?). Use when asked to review code or to self-review. Do not use for style, formatting, lint, naming, or readability review — those have dedicated tooling.
---

# Deep Review

Evaluate necessity before consequence: if code should not exist, flag it for
deletion rather than reviewing its internal correctness.

## 0. Intent

Identify in 2-3 bullets what the change achieves for an external user or caller.

- Treat bug descriptions or user requirements as primary.
- The implementation plan is not authority:
  an author's intent to add a toggle or abstraction does not make it required.
- If the diff contradicts the description (e.g. promises a flag not in code),
  note it as a stale description, not a code defect.
- If no requirement is documented, infer the minimum user outcome, not a
  summary of the diff. Ask only if the change is large and the goal unclear.

## 1. Necessity

Assume every addition is unnecessary until proven required by an active caller.
If deleting the addition and hardcoding the current behavior breaks nothing, flag it.

Actively hunt:

- **Speculative flags & config**: Options where only one value is used or needed.
- **Unread schema & API surface**: Added proto fields, types, or parameters that
  no active code path reads or branches on. Pass-through forwarding is not a use.
- **Premature abstractions**: Interfaces, factories, or indirection with only one
  concrete implementation.
- **Dead branches**: Handling for hypothetical inputs no current caller sends.

## 2. Consequence

Trace what breaks given the code exists:

- **Constraint shifts**: Relaxing a precondition breaks downstream assumptions;
  tightening one breaks existing callers or persisted data. Check callers inside
  the diff, then immediate external consumers.
- **Serialization & symmetry**: When writing or encoding state, trace the inverse
  read/decode. Verify distinct inputs cannot collide on the same key, and written
  fields are restored without silent data loss.
- **Sequential control flow**: Trace branching strictly top-down (first match wins).
  Verify that preceding conditions evaluate to false before asserting execution
  reaches a subsequent branch or fallback.
- **Sentinels & defaults**: Verify zero/empty/null sentinels cannot be confused
  with valid domain values.
- **Failure paths & state**: Verify error and cancel paths perform cleanup and
  status propagation matching success paths.
- **Deletions**: Verify removed code leaves no dangling callers, dead tests, or
  deleted TODOs for unresolved bugs.

## 3. Corroborate

Actively attempt to falsify candidate findings before reporting:

- **Falsification check**: State what would make the finding false (e.g. an earlier
  matching guard, an active caller), then search specifically for it.
- **Scoped search**: Scope searches to immediate packages and callers. Avoid
  unbounded repository-wide greps.
- **Conditionals**: If a finding relies on uninspectable external state, label it
  `[Conditional: assumes <X>]`. Discard findings disproved by code search.
- **Counter-examples**: Cite a site in the codebase handling it correctly to
  demonstrate internal inconsistency.

## 4. Suppress

Skip preexisting flaws in untouched code, moved or renamed logic, linter/format
warnings, and naming or comment preferences. Never suppress a defect because
adjacent code makes the same mistake; repeated defects are systemic.

## Output

Structure into `## Intent`, `## Necessity`, `## Consequence` (ordered by severity; if none, state "No findings."), and conclude with `## Comments to Leave`.

### Detailed Findings

Per finding include: Severity, Location (`path:symbol` only; never line numbers), Quoted Code, Problem, Falsification Check Run, and Minimal Fix.

### Comments to Leave

Conclude with a copy-pasteable list of review comments, grouped by files. Each comment MUST include:

- **Title**: Short summary with severity.
- **Code Link**: Clickable file links with lines, where needed.
- **Target Symbol / Code**: Quoted snippet of the exact code anchor.
- **Comment Text**: Concise, professional review comment ready to post directly, clearly explaining the problem, consequence, and suggested fix without fluff.
