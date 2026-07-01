# Message Queues And Event-Driven Design

## What It Is

A message queue lets producers send work asynchronously to consumers. It decouples services and helps absorb traffic spikes.

```text
Order Service -> Queue -> Email Worker
                      -> Invoice Worker
                      -> Analytics Worker
```

## Why Interviewers Care

Queues are used when work does not need to happen synchronously in the user request path. They improve resilience and scalability but introduce eventual consistency.

## When To Use Queues

Use queues for:

- Email and SMS.
- Push notifications.
- Video transcoding.
- Image processing.
- Analytics.
- Webhook delivery.
- Search indexing.
- Order fulfillment steps.

Avoid queues when:

- The user needs an immediate authoritative answer.
- Strong transaction semantics are required and cannot be modeled asynchronously.

## Queue vs Stream

Queue:

- Work is usually consumed by one worker.
- Good for background jobs.
- Examples: RabbitMQ, SQS.

Stream:

- Events are stored and multiple consumers can read them.
- Good for event history and fan-out.
- Example: Kafka.

## Real-Time Examples

### YouTube Upload

User uploads video. Upload service stores original file and publishes a transcoding job. Workers generate multiple resolutions asynchronously. User sees "processing" until complete.

### E-Commerce Order

Order placement should be confirmed quickly. Email, invoice, analytics, and warehouse notifications can happen through queues.

### Food Delivery

Order status events are published:

```text
OrderPlaced -> RestaurantAccepted -> DriverAssigned -> PickedUp -> Delivered
```

Different consumers update notifications, tracking, analytics, and support dashboards.

## Delivery Semantics

At-most-once:

- Message may be lost.
- No duplicates.

At-least-once:

- Message will usually be delivered.
- Duplicates are possible.

Exactly-once:

- Hard and expensive.
- Often achieved practically through idempotent consumers.

Interview phrase:

> I would assume at-least-once delivery and make consumers idempotent.

## Idempotency

Consumers should safely process the same message multiple times.

Example:

Payment event has `paymentId`. Consumer checks whether `paymentId` was already processed before updating order status.

## Dead-Letter Queue

A dead-letter queue stores messages that repeatedly fail.

Use it for:

- Poison messages.
- Invalid payloads.
- Dependency failures after max retries.

Monitor DLQ size and alert when it grows.

## Ordering

Ordering can reduce scalability.

Example:

For chat messages, ordering matters within one conversation, not globally. Partition by `conversationId`.

For order status, events for one order should be processed in order.

## Failure Modes

- Consumer lag grows during spikes.
- Poison message blocks processing.
- Duplicate messages cause repeated side effects.
- Queue outage delays async workflows.
- Bad partition key creates hot partitions.

## Interview Follow-Ups

### What if consumers are slower than producers?

Scale consumers, add backpressure, increase partitions, optimize processing, or shed low-priority work.

### How do you prevent duplicate emails?

Use idempotency keys and store send records. If `emailType + orderId` already sent, skip.

### How do you handle retries?

Use exponential backoff, max retry count, and DLQ. Do not retry poison messages forever.

## Strong Interview Phrase

> I would keep user-critical operations synchronous and move non-critical side effects to queues, assuming at-least-once delivery with idempotent consumers.
