# System Design And Patterns Interview Guide

This guide is built for interview preparation, not textbook reading. The goal is to help you explain concepts clearly, apply them to real systems, discuss tradeoffs, and handle follow-up questions.

## How To Study

Use this order if you have limited time:

1. Start with `system-design/00-interview-approach.md`.
2. Study one system design building block at a time.
3. Revise the design patterns before low-level design rounds.
4. Revise SOLID before object-oriented design and refactoring questions.
5. Use the revision folder for quick checks before interviews.

## 7-Day Revision Plan

Day 1: Interview approach, requirements clarification, API design, capacity thinking.

Day 2: Load balancing, API gateways, rate limiting, circuit breakers.

Day 3: Caching, CDN, database scaling, consistency and availability.

Day 4: Queues, async workflows, observability, security.

Day 5: Creational and structural design patterns.

Day 6: Behavioral patterns and SOLID principles.

Day 7: Mock prompts, follow-up questions, and cheatsheet revision.

## Interview Answering Structure

For system design, use this sequence:

1. Clarify functional and non-functional requirements.
2. Estimate scale only enough to justify design choices.
3. Define APIs and data model.
4. Draw a high-level architecture in words.
5. Deep dive into bottlenecks, scaling, consistency, and failures.
6. Close with observability, security, and tradeoffs.

For design patterns and SOLID, use this sequence:

1. State the problem.
2. Name the pattern or principle.
3. Explain why it fits.
4. Show a small example.
5. Mention one tradeoff or common mistake.

## Contents

### System Design

- `system-design/00-interview-approach.md`
- `system-design/01-load-balancer.md`
- `system-design/02-api-gateway.md`
- `system-design/03-caching.md`
- `system-design/04-rate-limiting.md`
- `system-design/05-circuit-breaker.md`
- `system-design/06-message-queues.md`
- `system-design/07-database-scaling.md`
- `system-design/08-consistency-availability.md`
- `system-design/09-observability.md`
- `system-design/10-security.md`
- `system-design/11-common-system-design-patterns.md`

### Design Patterns

- `design-patterns/creational-patterns.md`
- `design-patterns/structural-patterns.md`
- `design-patterns/behavioral-patterns.md`

### SOLID

- `solid-principles/solid-with-examples.md`

### Revision

- `revision/interview-cheatsheet.md`
- `revision/common-follow-up-questions.md`
- `revision/mock-system-design-prompts.md`

## Quick Interview Checklist

Before answering, always know:

- Is the system read-heavy, write-heavy, or balanced?
- What consistency does the user experience require?
- What is the failure mode if a dependency is down?
- Where can the system become hot: user, key, shard, service, queue, or region?
- What can be cached safely?
- Which operations must be idempotent?
- What metrics prove the design is working?
