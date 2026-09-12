# Engineering Directives

Standing directives. These apply to all work unless the user says otherwise.

## 1. Core Engineering & Architecture (YAGNI & Simplicity)

- **The Best Code is Code Never Written**: Deletion over addition; boring over clever (clever is what gets decoded at 3 AM).
- **No Backward Compatibility Shims**: Do not preserve obsolete paths. Remove deprecated code, schemas, and endpoints cleanly rather than introducing compatibility layers, fallbacks, or migration wrappers.
- **Simplest Working Implementation**: Implement the minimum solution that fully satisfies current requirements. Avoid speculative abstractions, configuration, and indirection.
- **Concrete Anti-Patterns to Avoid**:
  - *Single-implementation abstractions*: Never create an interface, protocol, or abstract base class for a single concrete implementation "just in case".
  - *Speculative configurability*: Never introduce flags, settings, or environment variables for values that have no active requirement to change (hardcode constants directly).
  - *Future scaffolding*: Never write boilerplate, hooks, or parameters "for later" - let later scaffold for itself.
- **Grow in Layers**: Start from the smallest version that works end-to-end; add each new capability on top of an already functional product. Never trade a working product for unfinished complexity.
- **Modularity**: Keep components modular and concerns clearly separated.
- **Reuse Before Writing**: Check existing dependencies, the standard library, and existing utility packages in the codebase before implementing custom solutions. Inspect documentation and types before assuming a capability is missing.
- **Long-Term Architectural Decisions**: Do not accept stopgaps meant to be replaced later.

## 2. Testing & Verification Philosophy

- **Contracts Over Features**: Test contracts, invariants, business logic, and boundary conditions - never write tautological tests that merely mirror implementation details or confirm that a function/symbol exists.
- **Respect the Compiler/Typechecker**: Do not write tests to verify types, nullability, or schema properties already guaranteed by the compiler or static type analysis.
- **Decouple from External Provider Shapes**: Do not write tests that tightly mock or simulate the internal payload shape of external APIs (e.g. LLM providers, 3rd-party services) that can change independently. Focus tests on internal adapter boundary contracts, transformations, and error handling.
- **Cull Superficial UI/Registration Tests**: Do not write tests that merely verify a UI element rendered or that a slash command registered without testing actual state transitions.

## 3. Execution-First Communication

Communication exists to advance decision and execution.

- **Action & Payload First (BLUF)**: Begin line 1 with the direct answer, verdict, runnable command, or code location. Place rationale and context after the payload.
- **Positive Assertions**: Describe systems, designs, and answers by what they are, contain, and do. Define specifications by positive capabilities rather than absences.
- **Decision Architecture**: Structure questions and options around decisions: state the recommended choice first, followed by at most 2-3 ranked alternatives with one-line trade-offs.
- **Bounded Actionability**: Structure multi-step work as bounded, numbered steps. End open work with exactly one immediate, runnable next action.
- **Plain Technical Delivery**: State outcomes, verified facts, and test diagnostics (location, expected, and actual) plainly and directly.
- **Code References**: Designate locations using clickable paths with line-range fragments; quote minimal exact snippets.
