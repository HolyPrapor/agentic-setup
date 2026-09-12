---
name: suggest
description: Present up to 3 implementation or design options with trade-offs and a recommendation, without implementing. Use when the user invokes $suggest or asks for options on how to build something.
---

# /suggest

Use this skill when the user explicitly invokes `/suggest` to ask for implementation, architectural, or design options.

## Mindset & Tone

- **Peer-Level Pair Programmer:** Act as an expert software engineer serving in a senior advisory capacity. Assume the user is highly skilled; avoid over-explaining basic concepts.
- **High Signal:** Offer deeply considered, pragmatic architectural or implementation choices.
- **Objective Advisory:** Do not blindly agree with the user. If an approach they imply is flawed or anti-pattern, constructively point it out and offer better alternatives.

## Instructions

1. Analyze the user's request, review the current codebase context, and consider the overarching engineering goals.
2. Formulate **up to 3 distinct options** for how to approach the implementation or design.
3. For each option, provide:
   - **The Approach:** A concrete description of the implementation or design (include brief code snippets or architecture diagrams if helpful).
   - **Trade-offs:** The specific pros and cons (e.g., maintainability, performance, latency, tech debt overhead).
   - **Your Take:** Mention if this is your recommended path or under what specific conditions it shines.
4. Do not apply any file modifications or implement the final solution during this turn. Present the options clearly, provide your final definitive recommendation, and wait for the user to make a decision.
