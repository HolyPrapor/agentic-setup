---
name: loop
description: Iterate on the current work N times, judging each round with a fresh read-only critic subagent. Use when the user invokes $loop [N] or asks for repeated critique rounds.
---

# /loop

Use when the user invokes `/loop [N] [critic]` to iterate on the current work `N` (default: 3) times.

You do the work. A fresh subagent judges it. You act on the judgment. Repeat.

The critic may be a skill, fan-out, or plain instruction. The workpiece is whatever is currently being worked on.

## Each Round

1. **Critique**: Spawn a fresh critic with `spawn_agent`, give it the workpiece and goal, then `wait_agent` for it. Request concrete findings, not prose. **Never reuse a prior subagent** — an agent prompted again with `send_input` defends past verdicts rather than re-evaluating.
2. **Act**: Apply findings yourself. Maintain a private log of decisions (applied or rejected, with reason). **Never show this log to a critic** — primed critics stop looking. A recurring rejected finding is likely valid, not noise. Don't debate it on judgment alone: verify against ground truth or ask the user. If a round proposes reversing an accepted change, stop and surface the conflict.
3. **Report**: One line on what changed. Stop early if a round yields nothing new and nothing previously rejected. Close critics with `close_agent` when the loop ends.

## Rules

- **Read-only critics**: Critics must not write files. Perform all side effects yourself after loop exit.
- **Two layers only**: You spawn critics; critics do not spawn (state this in their prompt). If a critic skill spawns subagents, lift its critic prompt directly; parent-side prohibitions do not apply inside a loop.
- **Fan-out**: If running multiple critics per round, write a fresh task spec each round. Synthesis feeds Act, not the user.
- **Prefer real oracles**: If a tool, check, or source of truth can decide it, use that instead of a subagent.
- **Workpiece on disk**: If not already a file, write it to a scratch temp file that is not inside any agent session directory before round 1. Edit in place; only findings enter context.
- **Halt if undefined**: If you cannot define what "done" looks like, stop before round 1.
