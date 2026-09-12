---
name: preserve
description: Enrich project context documentation with durable, high-level knowledge discovered during the session, previewed before it is written. Use when the user invokes $preserve.
---

# /preserve

Enriches project documentation with durable, high-level knowledge discovered during the conversation. Focus strictly on forward-looking orientation and cut easily discoverable implementation details.

## The Durability Filter

Before preserving any knowledge, ensure it is:

- **Durable**: Remains true across quarters; immune to routine code drift.
- **Orientation-focused**: Clarifies project goals, system boundaries, and non-obvious cruxes.
- **Zero implementation fluff**: Omit commands, source file manifests, internal function names, and ephemeral debugging steps.

## Workflow

1. **Analyze**: Identify new goals, architectural shifts, or canonical pointers from the session.
2. **Structure**: Update existing context docs, start a new document, or open a dedicated project subfolder for deeper topic breakdowns. Keep top-level orientation docs short (~15-25 lines) and link deeper docs.
3. **Align**: When in doubt about whether an insight is durable or how to structure it, use the **`gm`** skill to interview the user.
4. **Stage**: Present a preview diff of proposed additions or modifications; mutate only upon user confirmation.
