---
layout: post
title: "Schema-Driven Platforms: Why JSON Schema Is the Most Underrated Tool in Your Stack"
date: 2025-02-01 00:00:00
categories: [ai, architecture]
---

I've spent the last several months building a multi-tenant SaaS platform from scratch. The kind where every customer needs slightly different data models, different forms, different workflows. The kind that, done wrong, turns into an unmaintainable mess of `if customer === 'acme'` branches.

Early on, I made a decision that shaped everything that followed: **JSON Schema would be the single source of truth for validation, UI generation, and runtime behavior.** Not just for API validation. For *everything*.

That decision turned out to be the best architectural call I've made in years.

## The Problem: Vertical Fields Are a Trap

Every multi-tenant platform eventually faces the same temptation. A customer needs a "color" field. Another needs "square footage." Another needs "session duration." The easy path is to add columns to your database tables, write custom UI for each, and move on.

This works for two customers. By the fifth, you're drowning. Every new customer type requires migrations, new form components, new list columns, new validation logic. Your "platform" is really just a collection of bespoke applications sharing a login page.

I'd seen this pattern destroy codebases at previous companies. So I drew a hard line: **no vertical fields in core tables.** Domain-specific data lives in JSONB columns, typed entirely by schemas.

## The Architecture: One Schema, Three Jobs

Here's the core insight that makes this work. A single JSON Schema definition does triple duty:

**1. Validation.** AJV validates data at every boundary -- form submissions, API calls, database writes. The schema *is* the contract.

**2. UI Generation.** The same schema drives form rendering. A `string` with an `enum` becomes a dropdown. A `number` with `minimum` and `maximum` gets range validation. A `boolean` renders as a toggle. Combined with a UI layout schema (using JSONForms conventions), you get full CRUD interfaces without writing a single component.

**3. List Configuration.** A companion `list` schema defines table columns, search paths, sort defaults, and cell formatting. The same generic list component renders any entity type.

The result: adding a new entity type to the platform means authoring four JSON files (schema, UI layout, list config, metadata). No migrations. No new components. No deployments.

## What I Actually Built

The platform has a generic resource system. Every domain object -- whether it's an equipment item, a class session, a service offering -- is stored in the same table with a JSONB `data` column. The resource's `type_key` points to its schema definition.

The frontend has exactly three resource views: list, form, and detail. They're completely generic. They accept schemas as props and render accordingly.

```
ResourceListView  + list.json   = any table
ResourceFormView  + schema.json + ui.json = any form
ResourceDetailView + ui.json    = any detail page
```

Settings work the same way. Each capability declares a settings schema, and the settings UI is auto-generated. No settings page is hand-coded.

Public-facing forms work the same way. A form schema defines fields, validation, and dynamic options. The same rendering pipeline produces customer-facing booking forms, inquiry forms, cancellation flows -- all from JSON.

## The Hard Parts Nobody Talks About

This isn't a free lunch. Here's what was genuinely difficult:

**Schema design is API design.** When your schema drives UI, every property name, every `title`, every `description` matters. You're designing a user interface in JSON. It requires the same care as designing a REST API, but the feedback loop is different -- you only see problems when you render the form.

**UI layout schemas are necessary and annoying.** Pure JSON Schema doesn't encode layout. You need a separate UI schema to specify: which fields go in which section, what order, what hints to give the renderer. I use a `Categorization > Category > Control` hierarchy. It works, but it's another artifact to maintain per entity type.

**Dynamic behavior requires schema extensions.** Standard JSON Schema doesn't handle "show this field only if that setting is enabled" or "populate this dropdown from a database query." I ended up defining custom extensions: `x-options` for dynamic dropdowns (resolved server-side), `x-condition` for conditional visibility, `x-hidden` for fields present in data but invisible in forms. These extensions are processed both server-side (before delivery) and client-side (during rendering).

**Generic list views need explicit formatting.** A schema tells you a field is a `number`, but not that it's currency. List configurations need a `kind` property (`text`, `currency`, `date`, `boolean`) to format cells correctly. This is domain knowledge that JSON Schema alone can't express.

## Where AI Fits In

Here's where it gets interesting. Once your entire domain model is expressed as schemas, **AI can generate new domain models**. A language model that understands your meta-schema can produce valid capability definitions from a natural language description.

"I run a yoga studio with class bookings and student management" becomes a complete set of resource types, forms, and settings -- validated against the same meta-schemas, rendered by the same generic UI.

This only works *because* the target is structured data, not code. The AI generates configuration that's mechanically validated before it touches production. The blast radius of a bad generation is a validation error, not a runtime crash.

I'll write more about this in a follow-up post.

## Lessons for Staff+ Engineers

If you're evaluating this pattern for your own platform:

**Start with the meta-schema.** Before writing any application code, define the schema that validates your schemas. This forces you to think about what abstractions your platform actually needs. My meta-schema went through multiple major revisions before it stabilized, and each revision clarified the platform's actual capabilities.

**Invest in server-side schema processing.** Don't send raw schemas to the client and let the frontend figure out conditional visibility and dynamic options. Process schemas on the server, resolve options against real data, filter invisible fields, and deliver a "ready to render" package. This keeps the frontend simple and prevents data leaks.

**Accept that you'll need extensions.** Vanilla JSON Schema won't be enough. Define your extensions explicitly, document them, and process them in a single pipeline. Don't scatter `x-` handling across your codebase.

**Generic views are an investment, not a shortcut.** Building truly generic list/form/detail views takes longer upfront than building one bespoke page. The payoff comes at entity type three or four, and then it compounds. By entity type ten, you're adding new domain models in an afternoon.

**JSON Schema + JSONB + generic UI = a platform that scales by configuration, not code.** That's the real unlock. Not any single technology, but the discipline of keeping domain meaning out of your core and expressing it entirely in validated, renderable schemas.

---

*This is Part 1 of a three-part series on building AI-enabled SaaS platforms. Next up: [using generative AI to produce validated product configuration at onboarding time]({% post_url 2025-02-08-ai-generates-configuration-not-code %}).*
