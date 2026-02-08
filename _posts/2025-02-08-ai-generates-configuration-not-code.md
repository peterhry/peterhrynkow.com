---
layout: post
title: "AI Generates Configuration, Not Code: How I Use LLMs to Build Product Capabilities"
date: 2025-02-08 00:00:00
categories: [ai, architecture]
---

There's a wave of "app generators" promising that anyone -- not just developers -- can describe what they want and get a working application. The pitch is compelling, but the reality is that these tools generate application code with few guardrails. The output is deployed and you hope the tests catch the problems -- assuming tests even exist.

My concern isn't with developers using AI to write code. That's a productivity tool with a human in the loop who understands what's being produced. The problem is when code generation is the *product*, aimed at end users who can't review what the AI wrote and have no way to verify it's correct.

I took a different approach. In the platform I've been building, **AI generates validated configuration -- not executable code.** The configuration is mechanically validated against strict schemas before it ever touches production. If the AI produces something invalid, it's a schema error, not a production incident.

This distinction -- configuration vs. code -- is the difference between AI as a reliable product feature and AI as a liability.

## The Setup: Why Configuration Generation Works

In [Part 1]({% post_url 2025-02-01-schema-driven-platforms %}), I described building a schema-driven platform where every domain model, form, and settings page is defined by JSON schemas. Adding a new capability to the platform means authoring a bundle of JSON files that conform to a meta-schema.

The interesting property of this architecture is that **the output space is fully constrained.** An LLM generating a capability bundle can only produce:
- JSON Schema objects with specific property types
- UI layout definitions using a fixed vocabulary of layout elements
- Form definitions with a known set of field types and options
- Settings with declared defaults and enumerations

There's no arbitrary code. No function definitions. No control flow. Just structured data that either passes validation or doesn't.

## The Generation Pipeline

Here's what actually happens when a new business onboards:

**Step 1: Natural Language Requirements.** The user describes their business in plain English. "I'm a drywall contractor. I need to track job sites, manage material orders, and let customers request quotes online."

**Step 2: Structured Extraction.** The LLM (Claude) extracts structured requirements: industry vertical, core entities, relationships, booking/allocation needs, form intents. This is a constrained extraction task -- the model maps free text to a known taxonomy of platform capabilities.

**Step 3: Bundle Generation.** Using the extracted requirements and the platform's meta-schema as context, the LLM generates a complete capability bundle. This includes:
- Resource type schemas (the data models)
- UI layouts for create/edit forms
- List configurations for table views
- Public form schemas with field validation
- Parameter bindings that map form fields to backend operations
- Settings schemas with sensible defaults

**Step 4: Mechanical Validation.** The generated bundle goes through the exact same validation pipeline as human-authored bundles. AJV validates against the meta-schema. Every sub-schema is checked. Invalid bundles are rejected with specific error messages.

**Step 5: Storage and Activation.** Valid bundles are stored as versioned, immutable records. Each business owns its bundle. The bundle is activated, and the generic UI immediately renders the new capability.

The entire pipeline runs in about 30 seconds. From "I run a yoga studio" to a live system with class scheduling, student management, and booking forms.

## What the Meta-Schema Actually Constrains

The power of this approach lives in the meta-schema. It's a 1,600+ line JSON Schema document that defines:

**Resource types must declare traits.** A resource is `allocatable` (can be booked), `temporal` (has time-based availability), `quantified` (has inventory), etc. These traits determine which platform behaviors apply. The AI can't invent new platform behaviors -- it can only compose existing ones.

**Forms must declare intent.** Every form has an `intent`: `create_allocation`, `cancel_allocation`, `modify_allocation`, or `create_inquiry`. The intent determines which backend operation runs on submission. The AI doesn't generate backend logic -- it maps form fields to existing operations through a parameter binding DSL.

**Parameter bindings use a fixed expression language.** Form fields map to RPC parameters through a small set of expressions: field references, literal values, runtime context lookups, time arithmetic, conditional switches. This is expressive enough for real-world forms but constrained enough that every expression is mechanically evaluable.

**Settings schemas must use known types and enumerations.** The AI can't introduce arbitrary behavior flags. Settings must conform to what the platform can actually configure.

## Why This Is Better Than Code Generation

I've seen teams try to use LLMs to generate React components, API endpoints, or database queries. The fundamental problem is verification. How do you know the generated code is correct? You need tests, code review, and runtime monitoring -- the same pipeline you use for human-written code, except now you're doing it for output you didn't write and may not fully understand.

Configuration generation sidesteps this entirely:

**Validation is mechanical.** Either the bundle conforms to the meta-schema or it doesn't. There are no subtle runtime bugs.

**The blast radius is bounded.** A bad configuration means one business sees incorrect field labels or a missing form section. It doesn't mean a SQL injection or a broken API for all customers.

**Iteration is cheap.** If the generated bundle isn't quite right, regenerate with adjusted requirements. Each generation produces a new immutable version. There's no merge conflict, no code review, no deployment.

**The AI gets better at the right task.** When you use an LLM for structured generation against a schema, you can measure accuracy in hard terms: what percentage of generations pass validation on the first attempt? What percentage require human correction? These metrics are actionable. "Did the generated code work?" is much harder to quantify.

## The Feedback Loop: Validation Errors as Training Signal

When a generated bundle fails validation, the error messages are specific and actionable: "Resource type 'yoga_class' declares trait 'allocatable' but schema includes property 'qty_available' -- platform-managed fields must not appear in resource schemas."

These errors serve two purposes:
1. **LLM self-correction.** The errors are fed back to the LLM for a retry. In practice, first-attempt validation pass rates are high, and the retry loop catches most remaining issues.
2. **Meta-schema refinement.** Repeated generation failures in the same area signal that the meta-schema is ambiguous or that the LLM needs better prompting. This feedback loop has driven several meta-schema revisions that improved both human and AI authoring.

## The Staff Engineering Insight

The decision to use AI for configuration generation wasn't primarily an AI decision. It was an *architecture* decision. The schema-driven platform had to exist first. The meta-schemas had to be rigorous. The validation pipeline had to be airtight. The generic UI had to be complete.

AI was the *last* piece, not the first. And it only worked because every preceding layer was designed with constraints that make the AI's job tractable.

This is the pattern I'd advocate for any team looking to integrate generative AI into their product:

1. **Define the output space formally.** Use schemas, grammars, type systems -- whatever makes the valid output mechanically verifiable.
2. **Build the validation pipeline first.** If you can't mechanically verify the output, you don't have an AI feature. You have an AI experiment.
3. **Make the AI generate data, not behavior.** Configuration, schemas, mappings, templates -- things that are declarative and inspectable. Not code that needs to be understood to be trusted.
4. **Version and isolate.** Every AI generation should produce an immutable, versioned artifact scoped to a single tenant. No shared mutable state.

The goal isn't to use AI to write your platform. It's to design your platform so that AI can configure it safely.

## What's Next

The current system generates complete capability bundles from scratch. The next frontier is *incremental modification*: a business owner saying "I want to add a 'dietary restrictions' field to my booking form" and having the AI produce a v2 bundle with that change, validated against the same meta-schemas, without touching anything else.

This is harder than initial generation because it requires understanding the existing bundle, making a targeted change, and preserving everything else. But the architecture supports it -- bundles are versioned, immutable, and mechanically diffable.

---

*This is Part 2 of a three-part series. [Part 1]({% post_url 2025-02-01-schema-driven-platforms %}) covered schema-driven platform architecture. [Part 3]({% post_url 2025-02-15-i-built-an-event-router-then-deleted-it %}) covers what I learned from building a custom event-driven architecture, then deleting it.*
