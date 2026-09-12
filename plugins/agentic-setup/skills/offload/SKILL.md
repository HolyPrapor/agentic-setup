---
name: offload
description: Spawn N independent subagents with identical neutral inputs to research, verify, or prototype without bias, then synthesize. Use when the user invokes $offload [N] or asks for independent verification.
---

# /offload

Use when the user invokes `/offload [N] [task]` to spawn `N` (default: 1) independent subagents.

## Core Directives

1. **Unbiased Perspective ("Do Not Disable Thinking")**: Provide necessary context, paths, and constraints, but zero leading opinions, favorite solutions, or speculative framing. Subagents must reason independently from ground truth.
2. **Same Inputs by Default**: If `N > 1`, run workers with identical inputs for independent verification. Decompose only when asked to shard: split into `N` disjoint, uncoordinated slices, state the split, and dispatch.
3. **Prompt Reuse & Strict Session Isolation**: Write the XML task specification once to a unique temp file. **NEVER** reference or leak your own session, transcript, or scratch directory — subagents reading parent session files destroys independence.

## Task XML Schema

Write to a unique temp file `T`:

```xml
<offload>
<objective>
[Neutral statement of goal, hypothesis, or evaluation question]
</objective>
<context>
[Factual background, key files, and repo paths. No opinions.]
</context>
<constraints>
[Invariants, requirements, and constraints]
</constraints>
<deliverables>
[Expected output: findings, trade-offs, proof, or code]
</deliverables>
</offload>
```

## Workflow

1. **Write Prompt**: Save the `<offload>` XML to a unique temp file.
2. **Dispatch Concurrently**: Spawn all `N` subagents in parallel with `spawn_agent`, each with an identical prompt that points at the temp file:
   - *Read-Only*: "Read and execute `T` independently."
   - *Code Modifications*: "Read and execute `T`. Work exclusively inside dedicated workspace: `<ws_name>`."
   - *Sharded*: "Read and execute `T`, restricted to your slice: `<slice_i>`."
3. **Await & Synthesize**: `wait_agent` on all ids until they report. Compare results:
   - **Consensus**: Points of agreement.
   - **Divergence**: Conflicting conclusions or alternative approaches.
   - **Unique Insights**: Novel findings from individual workers.

   Synthesize for the user and continue. Close the workers with `close_agent` once synthesis is done.

   For sharded runs, concatenate slices and verify all reported. Consensus and divergence apply only to identical-input runs.
