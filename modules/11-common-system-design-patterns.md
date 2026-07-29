# Module 11 — Common System Design Patterns

## TL;DR

Production patterns: backpressure, retries with backoff, timeouts, bulkheads, pagination, CDN, object storage, webhooks, leader election, distributed locks, and idempotency.

## Concept

### Daily-life analogy

Like safety equipment on a ship: lifeboats (fallbacks), watertight doors (bulkheads), and emergency drills (circuit breakers).

This file covers high-value concepts that often appear as follow-up questions.

## Backpressure

Backpressure tells upstream callers to slow down when the system is overloaded.

Examples:

- Return `429 Too Many Requests`.
- Limit queue size.
- Reject low-priority work.
- Slow producers when consumer lag grows.

Real example:

If video transcoding workers are overloaded, the upload service can still accept the file but show "processing delayed" instead of letting queues grow infinitely.

Interview phrase:

> I would use backpressure so overload is controlled instead of silently becoming an outage.

## Retries

Retries handle transient failures.

Use with:

- Timeouts.
- Exponential backoff.
- Jitter.
- Idempotency.
- Max retry count.

Bad retry behavior:

Every service retries immediately and multiplies traffic to an already failing dependency.

Real example:

Retry payment provider timeout only if the operation uses an idempotency key.

## Timeouts

Every network call should have a timeout.

Without timeout:

- Threads wait forever.
- Connection pools fill.
- User requests hang.
- Cascading failures become more likely.

Real example:

Recommendation Service should not delay checkout. If it does not respond in 100 ms, skip recommendations.

## Bulkheads

Bulkheads isolate resources so one failing area does not sink the whole system.

Examples:

- Separate thread pools for payment and recommendation calls.
- Separate database connection pools.
- Separate queues for high-priority and low-priority jobs.

Real example:

If analytics processing is slow, it should not consume worker capacity needed for order confirmation.

## Pagination

Pagination prevents returning huge result sets.

Offset pagination:

```http
GET /orders?limit=20&offset=100
```

Simple but slow and inconsistent for large changing datasets.

Cursor pagination:

```http
GET /orders?limit=20&cursor=lastSeenCreatedAt
```

Better for large feeds and timelines.

Real example:

Instagram-style feeds should use cursor pagination because new posts arrive while users scroll.

## CDN

A CDN caches public content close to users.

Use for:

- Images.
- Videos.
- Static assets.
- Public downloads.

Real example:

An e-commerce site serves product images from CDN so the origin service is not overloaded.

Failure modes:

- Private data cached publicly.
- Stale content after deploy.
- Cache purge delays.
- Origin overload during cache miss storm.

## Object Storage

Object storage stores large files such as images, videos, invoices, backups, and logs.

Examples:

- S3-like storage.
- Blob storage.

Real example:

A chat app stores images in object storage and keeps only metadata in the database.

## Webhooks

Webhooks let one system notify another asynchronously over HTTP.

Real example:

Payment provider sends:

```http
POST /webhooks/payment
```

when a payment succeeds or fails.

Important details:

- Verify signatures.
- Make webhook handling idempotent.
- Return quickly.
- Process heavy work asynchronously.
- Store received event IDs.

## Leader Election

Leader election chooses one node to coordinate work.

Use for:

- Scheduled jobs.
- Cluster coordination.
- One active processor for a partition.

Real example:

Only one scheduler instance should send daily billing reminders.

## Distributed Locks

Distributed locks coordinate access across machines.

Use carefully. Many problems are better solved with database constraints, idempotency, or queues.

Real example:

Prevent two workers from processing the same expensive report generation job.

Failure concerns:

- Lock timeout too short.
- Lock holder dies.
- Clock assumptions are wrong.
- Work continues after lock expires.

## Idempotency

Idempotency means repeating the same operation has the same effect.

Use for:

- Payments.
- Order creation.
- Webhook processing.
- Queue consumers.
- Retries.

Real example:

`POST /payments` accepts `Idempotency-Key`. If the client retries, the system returns the original result instead of charging again.

## Interview Q&A

> For production readiness, I would add timeouts, retries with backoff, circuit breakers, backpressure, idempotency, and observability around every critical dependency.

## Further Reading

- Practice with `cheatsheets/mock-system-design-prompts.md`
- Quick reference: `overview-concepts/`
