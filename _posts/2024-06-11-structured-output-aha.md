---
layout: post
title: "Structured output was my LLM aha moment"
date: 2024-06-11 00:00:00
categories: [ai, llm]
---

For months I tried to tame large language models with elaborate system prompts. The result was fragile: tiny edits would change behavior, tests were hard to write, and every new requirement ballooned the prompt. The aha moment came when I flipped the problem around and asked the model to **produce structured output** instead of free text.

### The brittle prompt phase

My early attempts relied on giant system messages packed with rules—voice, steps, edge cases, fallback copy. Any change risked unexpected responses, and asserting correctness meant brittle substring checks. Even worse, the model’s “shape” was fuzzy, so downstream code had to guess what came back.

### Discovering schemas as contracts

Structured output forced me to declare the schema first: fields, enums, optional data, and validation rules. Tool-call style responses or JSON schemas gave the model guardrails and let me steer execution by naming the allowed actions. Suddenly I could:

- Describe every requirement once in the schema, not scattered across prose prompts.
- Add or deprecate fields without rewriting the entire system message.
- Validate deterministically, so tests checked structure and values instead of vibes.

### Production benefits

Schemas made the integration boring—in a good way. With predictable shapes, I could diff versions of the schema to track behavior changes, run contract tests, and log mismatches. Observability improved because errors pointed to a specific field or enum instead of “prompt drift.”

### How I iterate now

1. **Define the contract** first: inputs, outputs, allowed actions, and defaults.
2. **Keep prompts short**, focused on how to populate the schema, not storytelling.
3. **Validate everything** and fail loudly when the contract is broken.
4. **Version schemas** so changes are deliberate and testable.

Structured output turned prompting from art into engineering. The model still writes words, but the codebase now treats those words like data—and that shift made LLM work maintainable.
