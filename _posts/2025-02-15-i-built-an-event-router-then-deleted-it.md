---
layout: post
title: "I Built a Custom Event Router, Then Deleted It: Lessons in Knowing When to Stop Building"
date: 2025-02-15 00:00:00
categories: [architecture]
---

I'm building a rental platform. When a booking is created, a lot needs to happen: confirmation emails, calendar events, reminders. All of that orchestration was handled by n8n, a visual workflow tool. It worked fine.

In late January, I decided I could do better and committed a custom event processing system. An event emitter, a task scheduler, an email sender -- over a dozen modules for routing and orchestrating everything that happens after a booking is created. I documented all of it across five markdown files totaling over 4,000 lines.

Six days later, I committed "back to n8n!!!" with three exclamation marks. Two days after that, I deleted all of it. Over 3,000 lines of application code, gone.

This is the story of what I built, why I deleted it, and what I learned about the difference between good architecture and appropriate architecture.

## What I Was Trying to Solve

The platform processes inbound work: form submissions come in, bookings get created, customers need confirmation emails, calendar events need scheduling, reminders need to fire before pickup dates.

Before the event system, this orchestration lived in n8n, a visual workflow automation tool. n8n worked fine. Workflows were easy to build, the execution history was visible, and integrations with Gmail and Google Calendar were pre-built.

But I had a nagging feeling. n8n was a separate system with its own database, its own deployment, its own failure modes. Every workflow was a black box from the platform's perspective. I couldn't query "what happened after this booking was created?" from my application database. The event-driven purist in me wanted a first-class event system: emit events, route them through handlers, track attempts, support replay.

So I built one.

## What I Built

The system had five major components:

**Event emission.** Any operation could emit a typed event: `allocation_created`, `allocation_modified`, `allocation_cancelled`, `form_submitted`. Events were stored in a database table with idempotency via deduplication keys. The deduplication logic computed keys per event type -- allocation events keyed on allocation ID, form events keyed on submission key.

**Event processing.** An edge function that accepted an event ID, looked up the event, resolved which handlers should run based on the event type and business configuration, and executed them in sequence. Each handler execution was recorded as an `event_attempt` with status tracking (started, succeeded, failed, skipped).

**Task scheduling.** Events like `allocation_created` triggered scheduled tasks: "send pickup reminder 1 day before start date," "send return reminder 1 day before end date," "mark allocation as started when start date arrives." The scheduler handled edge cases like allocations with past start dates (emit immediately instead of scheduling).

**Email sending.** A template resolution engine that pulled email templates from capability settings, resolved variables against event data, and sent through Gmail via OAuth tokens.

**Calendar integration.** Created and updated Google Calendar events when allocations were created or modified.

It was, objectively, a well-designed system. Events were immutable and replayable. Handlers were isolated and independently testable. The attempt tracking gave complete observability. The deduplication prevented double-processing.

I was proud of it.

## Why I Deleted It

Within a week of having the system running end-to-end, three things became clear:

**1. Debugging was miserable.** When a confirmation email didn't send, I had to trace through: Was the event emitted? Check the event table. Was it processed? Check the event_attempt table. Did the handler succeed? Read the error JSON. Was the email template correct? Check the capability settings. Was the OAuth token valid? Check the integration table.

With n8n, I opened the workflow execution history, saw the green and red nodes, clicked the failed one, and read the error. Done.

The irony was brutal. I'd built the event system partly for *observability* -- a complete audit trail of every event and every handler attempt. But observability isn't the same as debuggability. Having all the data in database rows doesn't help when you need to understand *why* something failed. Visual workflow execution history, where you can see data flowing through nodes, is genuinely superior for diagnosing integration failures.

**2. Edge cases multiplied.** The custom event system handled the happy path well. But real-world orchestration is mostly edge cases. What happens when a booking is modified and the calendar event needs updating but the OAuth token expired? What about retry timing? What about partial failures where the email sent but the calendar update failed? What about timezone conversions for scheduled tasks?

Each edge case required code. In n8n, each edge case required dragging a node and configuring it. The development velocity difference was enormous.

**3. I was rebuilding n8n, badly.** The moment I caught myself writing a retry mechanism with exponential backoff for failed email sends, I stopped. I was building a workflow engine. n8n *is* a workflow engine, built by a team dedicated to exactly this problem. My custom version would always be worse: fewer integrations, worse error handling, no visual builder, no execution history UI.

I was spending engineering time on orchestration infrastructure instead of on the product. For an early-stage platform, that's a fatal misallocation.

## The Commit That Changed Everything

```
102b67b  back to n8n!!!
```

I brought n8n back the same day I had the realization. Added the Docker Compose setup, restored the database schema, and started migrating handlers back to visual workflows.

Two days later:

```
33c458c  move more logic back to n8n
```

And then a week of cleanup commits where I deleted every event processing file, every handler, every scheduler, and every documentation file. Over 4,000 lines of markdown documentation. Over 3,000 lines of TypeScript. All gone.

```
5a50506  cleanup, wip
```

That commit removed: `event-emitter.ts`, `event-processor.ts`, `event-types.ts`, `email-sender.ts`, `convention-engine.ts`, and every file in the `event-process/` directory: `index.ts`, `invoke-operation.ts`, `resolve-actions.ts`, `schedule-tasks.ts`, `send-calendar-event.ts`. Plus `event-emit/`, `event-list/`, `event-process-queued/`, `event-replay/`, and `task-scheduler/`.

It felt great.

## What I Kept

Not everything was thrown away. The core operations layer -- the simple, synchronous, request-in/response-out handlers for creating requests, managing allocations, and handling tasks -- stayed. These are pure functions: accept parameters, validate against a schema, call a database RPC, return a result. No events, no async processing, no orchestration.

The database tables for events still exist in the migration history. I didn't write a migration to drop them. They're empty and unused. A reminder.

The lesson crystallized into a principle: **operations should be synchronous and deterministic. Orchestration should be delegated to a purpose-built tool.**

## The Hybrid Architecture

What I ended up with is a hybrid that plays to each tool's strength:

**Edge functions handle direct operations.** Form submissions, allocation creation, resource CRUD. These are synchronous, validated, and return immediately. They're TypeScript running on Deno -- fast, typed, easy to test.

**n8n handles asynchronous orchestration.** After a booking is created, n8n picks up the event and orchestrates: send confirmation email, create calendar event, schedule reminders. It handles retries, OAuth token refresh, and integration-specific error handling with pre-built nodes.

**The platform doesn't need to know about orchestration internals.** It fires an operation, gets a result. What happens afterward -- emails, calendar events, reminders -- is n8n's concern. The boundary is clean.

## Lessons for Staff+ Engineers

**1. Observability and debuggability are not the same thing.** A complete event log in your database is great for auditing. But when something breaks at 2 AM, you want a visual execution trace with the actual data at each step, not a `SELECT * FROM event_attempt WHERE status = 'failed'` query. Know which one you actually need.

**2. "I could build this" is not a reason to build it.** I *could* build an event processing system. I'm a competent engineer. But the question isn't capability -- it's opportunity cost. Every hour spent on orchestration infrastructure was an hour not spent on the product's actual differentiator.

**3. Deleting code is an engineering skill.** That cleanup commit -- removing 3,000+ lines of working code and 4,000+ lines of documentation -- was one of the hardest and best things I did. The codebase got simpler, the deployment got simpler, and the debugging story got dramatically better. Sunk cost is real, and overcoming it is a skill.

**4. Match your abstraction level to your stage.** A custom event-driven architecture makes sense for a platform at scale, with a dedicated infrastructure team, where the performance and customization benefits justify the maintenance cost. For an early-stage product with one developer, it's over-engineering of the highest order. I should have known this. I did know this. I built it anyway because it was intellectually satisfying. The three exclamation marks in that commit message were aimed at myself.

**5. AI-assisted development makes this mistake easier.** I'll be honest: I used AI coding assistants extensively during this period. They made building the event system *fast*. The event emitter, the processor, the scheduler -- all produced and iterated quickly with AI help. The speed was intoxicating. But AI assistants optimize for the task you give them, not for whether the task should exist. The easier it is to build complex systems, the more discipline you need to ask "should I?"

## The Meta-Lesson

The best architecture isn't the most technically elegant one. It's the one that lets you ship the right thing at the right time. I spent a week building infrastructure I didn't need, then spent another week deleting it. The product didn't advance during either week.

The platform is better now than it was with the custom event system. Not because the event system was bad -- it was well-designed. But because the platform is *simpler*, and simplicity compounds. Every feature I build now has fewer moving parts, fewer failure modes, and faster debugging. That's worth more than architectural purity.

Sometimes the best engineering decision is `git rm -r`.

---

*This is Part 3 of a three-part series. [Part 1]({% post_url 2025-02-01-schema-driven-platforms %}) covered schema-driven architecture. [Part 2]({% post_url 2025-02-08-ai-generates-configuration-not-code %}) covered using AI for configuration generation. This post covered knowing when to delete your own work.*
