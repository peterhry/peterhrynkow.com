---
layout: post
title: "I Built a Custom Event Router, Then Deleted It: Lessons in Knowing When to Stop Building"
date: 2025-02-15 00:00:00
categories: [architecture]
---

Every booking on my platform kicks off a chain of work -- confirmation emails, calendar events, reminders -- all handled by n8n, a visual workflow tool. It worked fine. But in late January, I decided I could do better.

I committed a custom event processing system: an event emitter, a task scheduler, an email sender -- over a dozen modules, documented across five markdown files totaling 4,000+ lines.

Six days later, I committed `back to n8n!!!` with three exclamation marks. Two days after that, I deleted all of it. Over 3,000 lines of application code, gone.

This is what I learned about the difference between good architecture and appropriate architecture.

## What I Was Trying to Solve

The platform processes inbound work: form submissions, bookings, confirmation emails, calendar events, reminders. Before the event system, all of this orchestration lived in n8n. Workflows were easy to build, execution history was visible, and integrations with Gmail and Google Calendar were pre-built.

But n8n was a separate system with its own database, its own deployment, its own failure modes. Every workflow was a black box from the platform's perspective. I couldn't query "what happened after this booking was created?" from my application database. The event-driven purist in me wanted a first-class event system: emit events, route them through handlers, track attempts, support replay.

So I built one.

## What I Built

The system had five major components:

**Event emission.** Typed events (`allocation_created`, `allocation_modified`, `form_submitted`) stored in a database table with idempotency via deduplication keys.

**Event processing.** An edge function that resolved which handlers should run based on event type and business configuration, executing them in sequence. Each execution was recorded as an `event_attempt` with status tracking.

**Task scheduling.** Events triggered scheduled tasks -- pickup reminders, return reminders, status transitions -- with edge case handling for past dates.

**Email sending.** A template resolution engine that pulled templates from capability settings, resolved variables against event data, and sent through Gmail via OAuth.

**Calendar integration.** Created and updated Google Calendar events when allocations were created or modified.

Events were immutable and replayable. Handlers were isolated and testable. Attempt tracking gave complete observability. Deduplication prevented double-processing. It was, by any technical measure, well-designed.

I was proud of it.

## Why I Deleted It

Within a week of running the system end-to-end, three things became clear:

**1. Debugging was miserable.** When a confirmation email didn't send, I had to trace through: Was the event emitted? Check the event table. Was it processed? Check the event_attempt table. Did the handler succeed? Read the error JSON. Was the template correct? Check the capability settings. Was the OAuth token valid? Check the integration table.

With n8n, I opened the execution history, saw the green and red nodes, clicked the failed one, and read the error. Done.

The irony was brutal. I'd built the event system partly for *observability*. But observability isn't debuggability. A complete audit trail in database rows doesn't help when you need to understand *why* something failed. Visual execution history -- data flowing through nodes, with errors highlighted inline -- is genuinely superior for diagnosing integration failures.

**2. Edge cases multiplied.** The custom system handled the happy path well. But real-world orchestration is mostly edge cases. What happens when a calendar event needs updating but the OAuth token expired? What about partial failures where the email sent but the calendar update didn't? What about timezone conversions for scheduled tasks?

Each edge case required code. In n8n, each required dragging a node and configuring it. The velocity difference was enormous.

**3. I was rebuilding n8n, badly.** The moment I caught myself writing retry logic with exponential backoff for failed email sends, I stopped. I was building a workflow engine. n8n *is* a workflow engine, maintained by a team dedicated to exactly this problem. My version would always be worse: fewer integrations, worse error handling, no visual builder, no execution history UI.

Every hour spent on orchestration infrastructure was an hour not spent on the product. For an early-stage platform with one developer, that's a fatal misallocation.

## The Commit That Changed Everything

```
102b67b  back to n8n!!!
```

I brought n8n back the same day. Docker Compose setup, database schema restored, handlers migrated back to visual workflows. Two days later, `move more logic back to n8n`. Then a week of cleanup commits: every event processing file, every handler, every scheduler, every documentation file. The emitter, the processor, the scheduler, the email sender -- all of it. Over 3,000 lines of TypeScript and 4,000 lines of documentation, gone.

It felt great.

## What I Kept

Not everything was thrown away. The core operations layer stayed: synchronous, request-in/response-out handlers for creating requests, managing allocations, and handling tasks. Pure functions that accept parameters, validate against a schema, call a database RPC, and return a result. No events, no async processing, no orchestration.

The database tables for events still exist in the migration history. I didn't write a migration to drop them. They sit there empty -- a reminder.

The lesson crystallized into a principle: **operations should be synchronous and deterministic. Orchestration should be delegated to a purpose-built tool.**

## The Hybrid Architecture

What I ended up with is a hybrid that plays to each tool's strength:

**Edge functions handle direct operations.** Form submissions, allocation creation, resource CRUD. Synchronous, validated, returning immediately. TypeScript on Deno -- fast, typed, easy to test.

**n8n handles asynchronous orchestration.** After a booking is created, n8n orchestrates: confirmation email, calendar event, reminders. It handles retries, OAuth refresh, and integration-specific errors with pre-built nodes.

**The platform doesn't know about orchestration internals.** It fires an operation and gets a result. What happens afterward is n8n's concern. The boundary is clean.

## Lessons for Staff+ Engineers

**1. Observability and debuggability are not the same thing.** A complete event log is great for auditing. But when something breaks at 2 AM, you want a visual execution trace with actual data at each step, not `SELECT * FROM event_attempt WHERE status = 'failed'`. Know which one you actually need.

**2. "I could build this" is not a reason to build it.** The question isn't capability -- it's opportunity cost. Every hour spent on orchestration infrastructure is an hour not spent on the product's actual differentiator.

**3. Deleting code is an engineering skill.** Removing 3,000+ lines of working code was one of the hardest and best things I did. The codebase got simpler, the deployment got simpler, and debugging got dramatically better. Sunk cost is real, and overcoming it is a skill.

**4. Match your abstraction level to your stage.** A custom event-driven architecture makes sense at scale, with a dedicated infrastructure team. For an early-stage product with one developer, it's over-engineering of the highest order. I knew this. I built it anyway because it was intellectually satisfying. The three exclamation marks in that commit message were aimed at myself.

**5. AI coding tools are most dangerous in senior hands, not junior ones.** The conventional wisdom about AI coding assistants is that they're risky for junior engineers who can't evaluate the output. My experience suggests the opposite. I used AI extensively to build this event system, and it produced genuinely good code -- well-typed, well-structured, idiomatic. That was the problem. A junior engineer generating messy code with AI would have hit friction quickly: things wouldn't compose, they'd get stuck, they'd ask for help. I generated a clean, well-architected system that *worked perfectly* and shouldn't have existed. AI didn't undermine my engineering judgment -- it outran it. The code was so well-designed that my sunk cost instinct kicked in harder than it would have for code I'd struggled to write by hand. The industry worries about AI generating bad code. The more insidious risk is AI generating *good* code so fast that experienced engineers can't tell when to stop.

## The Meta-Lesson

The best architecture isn't the most technically elegant one. It's the one that lets you ship the right thing at the right time. I spent a week building infrastructure I didn't need, then another week deleting it. The product didn't advance during either week.

The platform is better now -- not because the event system was bad, but because the platform is *simpler*, and simplicity compounds. Fewer moving parts, fewer failure modes, faster debugging. That's worth more than architectural purity.

Sometimes the best engineering decision is `git rm -r`.

---

*This is Part 3 of a three-part series. [Part 1]({% post_url 2025-02-01-schema-driven-platforms %}) covered schema-driven architecture. [Part 2]({% post_url 2025-02-08-ai-generates-configuration-not-code %}) covered using AI for configuration generation. This post covered knowing when to delete your own work.*
