---
name: handover
description: Summarize the work so far, crucial user context, and next steps as a compact XML block for a fresh conversation. Use when the user asks for a handover or invokes $handover.
---

# Handover Skill

You have been asked to provide a handover summary. The goal of this summary is to seamlessly transition the current task to a fresh agent in a new conversation, avoiding context bloating.

## Instructions

When you are invoked to provide a handover, your objective is to produce a dense, noise-free summary of the current session so it can be seamlessly passed to a fresh agent.

1. **Information Extraction**:
   - **Objective**: What is the ultimate goal?
   - **State**: What is the current status? What is completed and what is broken/pending?
   - **Crucial Context**: Include exact file paths, constraints, specific tool configurations, known bugs, or design decisions. What *must* the new agent know so the user doesn't have to repeat it?
   - **Next Steps**: What exact actions remain to be taken?
2. **Formatting Rules**:
   - Do NOT use emojis.
   - Do NOT use a rigid bulleted template. Instead, use clear XML tags (`<objective>`, `<state>`, `<context>`, `<next_steps>`) when formatting the context. This is highly token-efficient and perfectly parsed by LLMs in the next session.
   - Omit any section that isn't applicable to the current context.
   - **Zero fluff**: Strip out all conversational filler, intros, and trailing explanations. Do not explain your thought process or reasoning (e.g., remove "We tried this because..."). Frame everything as concrete facts ("Constraint: X must be Y").
3. **Output**: Combine the sections inside a `<handover>` block and wrap the entire output in a markdown code block so the user can easily copy and paste it into a new conversation.

## Example Format

Use the tags flexibly as needed. Your output should look something like this:

```xml
<handover>
<objective>
Replace the legacy diff renderer with the unified diff representation.
</objective>
<state>
- Refactoring of `diff_utils.py` is complete.
- Currently blocked by a failing test related to trailing whitespace.
</state>
<context>
- Key files: `path/to/diff_utils.py`, `path/to/diff_utils_test.py`
- Constraint: The renderer must not choke on CRLF input from Windows clients.
</context>
<next_steps>
Fix the failing `test_trailing_whitespace_handling` in `diff_utils_test.py`.
</next_steps>
</handover>
```
