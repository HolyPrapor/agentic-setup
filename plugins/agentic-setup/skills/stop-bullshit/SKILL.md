---
name: stop-bullshit
description: Spawn an adversarial subagent to audit the previous response for hallucinations and superficial shortcuts, then relay its critique. Use when the user distrusts the last chunk of work or invokes $stop-bullshit.
---

# /stop-bullshit

Use this skill when the user invokes the `/stop-bullshit` command (or otherwise indicates they do not trust the last chunk of work). The invocation may optionally include arguments specifying exactly what they don't trust.

## Workflow

### 1. Information Gathering & Handoff

Act as a "dumb router" to collect data for the auditor subagent. Do not explain your past reasoning, justify your choices, or summarize the text. You must gather:

- `<CONTEXT>`: The overarching task goal and the user's immediate preceding prompt prior to the suspect work.
- `<SUSPECT_TEXT>`: The raw, exact text of your **last response** to the user. Do not include raw tool calls, the subagent will evaluate workspace state itself.
- `<USER_CONCERN>`: Any arguments the user passed to the command (e.g., "did you actually check the RPC errors?").

### 2. Subagent Invocation

Spawn a read-only subagent with `spawn_agent` using the following exact prompt template, injecting the blocks you gathered:

```text
Role: You are a zero-trust auditor. Your goal is to actively uncover hallucinations, superficial shortcuts, and ignored requirements.
<CONTEXT>
{Inject CONTEXT here}
</CONTEXT>
<SUSPECT_TEXT>
{Inject SUSPECT_TEXT here}
</SUSPECT_TEXT>
<USER_CONCERN>
{Inject USER_CONCERN here}
</USER_CONCERN>
Task:
1. Identify the claims, factual assertions, code paths, or conclusions presented in the SUSPECT_TEXT.
2. Formulate a plan to verify them independently (e.g., code search, reading documentation, or checking dirty files if code was written).
3. Do not assume anything stated is true until you verify it yourself.
4. Output a continuous, narrative critique explaining what holds up and what smells, with absolute file links to exact lines sprinkled in for evidence.
5. DO NOT modify any code.
```

Then `wait_agent` for the audit to complete.

### 3. Parent Synthesis & Final Delivery

When the subagent completes its audit and sends you the narrative critique:

1. **Read & Understand**: Process the critique. Perform the necessary knowledge work to understand the gaps it found.
2. **Present & Respond**: Output a response to the user that includes the subagent's critique, followed by your *own* response to it.
   - If the critique found real hallucinations or superficial shortcuts, honestly acknowledge the fault and suggest concrete improvements.
   - If you believe the critique is wrong, respectfully explain why with ground truth evidence.
3. **DO NOT APPLY CHANGES**: You are strictly forbidden from modifying code, writing files, or applying the fixes during this turn. Stop and wait for the user to explicitly request the changes.
