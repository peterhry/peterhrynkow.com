---
layout: post
title: "Structured output was my LLM aha moment"
date: 2024-06-11 00:00:00
categories: [ai, llm]
---

When I started building with LLM agents, my first instinct was to control behavior through the system prompt. It worked—until the prompt got long enough that small edits had unpredictable side effects. Tests were brittle, and every new requirement made the prompt harder to maintain. The breakthrough came when I stopped engineering prompts and started engineering **structured output**.

### The brittle prompt phase

The natural starting point was a system message packed with rules—voice, steps, edge cases, fallback copy. A typical prompt looked something like this:

```
You are a task management assistant. When the user asks you to
create a task, respond with the task name on the first line,
followed by a priority (high, medium, or low) on the second line.
If the user wants to edit a task, respond with "EDIT:" followed
by the task id, then the updated fields. If the user wants to
delete a task, respond with "DELETE:" followed by the task id.
Always be concise. Never include extra commentary...
```

Then downstream code had to parse this free text:

```ts
function parseResponse(text: string) {
  if (text.startsWith("EDIT:")) {
    const parts = text.slice(5).trim().split("\n");
    const id = parts[0];
    // hope the model formatted this correctly...
    return { action: "edit", id, fields: parseFields(parts.slice(1)) };
  }

  if (text.startsWith("DELETE:")) {
    return { action: "delete", id: text.slice(7).trim() };
  }

  // must be a new task... probably
  const lines = text.split("\n");
  return {
    action: "create",
    name: lines[0],
    priority: lines[1]?.toLowerCase().trim(),
  };
}
```

This code is a house of cards. The model sometimes added preamble before the action keyword. It sometimes spelled out "High" instead of "high." It occasionally invented new response formats I hadn't anticipated. Every new feature meant more parsing branches, more edge cases, more ways for the integration to silently break.

Asserting correctness was equally painful. Tests ended up checking substrings and regex patterns against the raw LLM output—brittle checks that broke whenever I adjusted the prompt, even if the underlying behavior was correct.

### Discovering schemas as contracts

The turning point was treating the model's output like a function return type instead of prose. I stopped asking the model to "respond with text in this format" and started declaring a schema.

Here's a simplified version using [Zod](https://zod.dev/). Most LLM frameworks—LangChain, n8n, Vercel AI SDK—support Zod schemas as a way to define structured output:

```ts
import { z } from "zod";

const CreateAction = z.object({
  action: z.literal("create"),
  name: z.string(),
  priority: z.enum(["high", "medium", "low"]),
  due: z.string().date().nullable(),
});

const EditAction = z.object({
  action: z.literal("edit"),
  taskId: z.string(),
  fields: z.object({
    name: z.string().optional(),
    priority: z.enum(["high", "medium", "low"]).optional(),
    due: z.string().date().nullable().optional(),
  }),
});

const DeleteAction = z.object({
  action: z.literal("delete"),
  taskId: z.string(),
});

const AgentResponse = z.discriminatedUnion("action", [
  CreateAction,
  EditAction,
  DeleteAction,
]);
```

The first thing I noticed: the schema replaced half the system prompt. I no longer needed to explain the response format in prose—the schema *was* the format. The system prompt shrank to a few sentences about tone and domain context.

The second thing: the downstream code became trivial. No more regex, no more substring checks, no more guessing:

```ts
const result = AgentResponse.parse(response);

switch (result.action) {
  case "create":
    await createTask(result.name, result.priority, result.due);
    break;
  case "edit":
    await updateTask(result.taskId, result.fields);
    break;
  case "delete":
    await deleteTask(result.taskId);
    break;
}
```

TypeScript narrows the type inside each case branch. If I access `result.name` in the `"create"` case, the compiler knows it exists. If I try to access it in the `"delete"` case, it's a type error. The schema gave me guardrails at the model boundary *and* type safety through the rest of the codebase.

### Discriminated unions are the key insight

The most useful pattern I found was the discriminated union—a single `action` field that determines the shape of the rest of the response. This is what turned the model from a text generator into something closer to a function dispatcher.

Before schemas, I was implicitly asking the model to pick an action and then format its response according to rules scattered across a long prompt. With a discriminated union, the model's job is explicit: pick an action and populate its fields. The allowed actions are enumerated. The required fields for each action are defined. There's no ambiguity.

This matters more than it sounds. When you add a new capability—say, a `"reorder"` action—you add a new branch to the union:

```ts
const ReorderAction = z.object({
  action: z.literal("reorder"),
  taskIds: z.array(z.string()),
});

const AgentResponse = z.discriminatedUnion("action", [
  CreateAction,
  EditAction,
  DeleteAction,
  ReorderAction,
]);
```

One schema change. One new `case` in the switch. The system prompt doesn't need to explain the new response format because the schema already does. Compare that to the prose prompt approach, where adding a new action means writing new formatting rules, new parsing code, and hoping the model doesn't confuse the new format with existing ones.

### Nested schemas for richer output

Once I got comfortable with flat schemas, I started nesting them for more complex responses. A useful pattern is having the model return both an action *and* a user-facing message:

```ts
const AgentOutput = z.object({
  reasoning: z.string(),
  response: z.discriminatedUnion("action", [
    CreateAction,
    EditAction,
    DeleteAction,
    NoAction,
  ]),
  message: z.string(),
});
```

The `reasoning` field is a lightweight chain-of-thought—it gives the model space to "think" before committing to an action. The `message` field is what the user sees. The `response` field is what the code acts on.

This separation was important. Early on, I tried to extract both the action and the user message from the same blob of text. That meant the model's conversational reply and its machine-readable action were tangled together. Splitting them into distinct fields made each one more reliable.

### Testing goes from vibes to assertions

This was the payoff I didn't fully appreciate until I experienced it. With free-text responses, my tests looked like this:

```ts
it("creates a task", async () => {
  const response = await agent.run("remind me to buy groceries tomorrow");
  expect(response).toContain("buy groceries");
  expect(response.toLowerCase()).toMatch(/high|medium|low/);
});
```

These tests passed when they shouldn't have and failed when they shouldn't have. They were checking vibes.

With structured output, the same test becomes:

```ts
it("creates a task", async () => {
  const response = await agent.run("remind me to buy groceries tomorrow");
  const result = AgentResponse.parse(response);

  expect(result.action).toBe("create");
  expect(result.name).toBe("Buy groceries");
  expect(result.priority).toBe("medium");
  expect(result.due).not.toBeNull();
});
```

The assertion is against parsed, validated data. If the model returns an unexpected action, the test fails with a clear message—"`expected 'create', received 'edit'`"—instead of a confusing substring mismatch. If it returns an invalid priority value, Zod throws a validation error before the assertion even runs.

I also started writing schema-level tests that don't invoke the model at all:

```ts
it("rejects an invalid action", () => {
  expect(() =>
    AgentResponse.parse({ action: "archive", taskId: "123" })
  ).toThrow();
});

it("requires taskId for delete", () => {
  expect(() =>
    AgentResponse.parse({ action: "delete" })
  ).toThrow();
});
```

These run instantly, never flake, and catch regressions when the schema evolves. They're the contract tests I wished I'd had from the start.

### How I iterate now

My workflow has settled into a pattern:

1. **Define the schema first.** Before writing a single line of prompt, I declare the output shape. The schema *is* the spec. If I can't express a requirement as a field or enum value, I rethink the requirement.
2. **Keep prompts short.** The system prompt explains context and tone—who the user is, what domain we're in. It doesn't explain response formatting. That's the schema's job.
3. **Validate at the boundary.** Every model response gets parsed through the schema immediately. Invalid output is caught before it touches application logic.
4. **Fail loudly.** When validation fails, I log the raw response alongside the Zod error. This makes debugging straightforward: you see exactly what the model returned and exactly which field violated the contract.
5. **Version schemas deliberately.** Adding a field is a conscious decision. Removing one is a migration. Diffs to the schema file show up in code review and tell a clear story about how the agent's behavior is changing.

### The shift

Structured output turned prompting from art into engineering. The model still writes words, but the codebase treats those words like data—typed, validated, and testable. The schema is the contract between the model and the application, and once that contract exists, everything downstream gets simpler: parsing, testing, error handling, observability.

If you're fighting with elaborate prompts and fragile string parsing, try flipping the problem. Define the shape you want, hand it to the model, and let the schema do the heavy lifting.
