# Mock System Design Prompts

Use these prompts for practice. For each one, answer using the same flow: requirements, scale, APIs, data model, architecture, deep dives, failures, observability.

## 1. Design A URL Shortener

Must discuss:

- Short code generation.
- Redirect latency.
- Read-heavy traffic.
- Cache and database.
- Analytics as async processing.
- Custom aliases.
- Expiry.

Good follow-up:

> What happens if one short URL becomes viral?

Expected concepts:

- Cache hot URLs.
- CDN or edge redirect if appropriate.
- Database index on short code.
- Rate limit URL creation.

## 2. Design A Food Delivery App

Must discuss:

- Restaurant discovery.
- Menu caching.
- Cart and checkout.
- Payment.
- Order lifecycle.
- Driver assignment.
- Real-time tracking.

Good follow-up:

> What should be strongly consistent and what can be eventual?

Expected concepts:

- Menu cache.
- Strong checkout/payment handling.
- Queue for notifications.
- Event stream for order status.
- WebSocket or polling for tracking.

## 3. Design A Chat Application

Must discuss:

- One-to-one and group chat.
- WebSocket gateway.
- Message persistence.
- Delivery status.
- Offline notifications.
- Ordering.

Good follow-up:

> How do you preserve message order?

Expected concepts:

- Partition by conversation ID.
- Store message sequence.
- Push notifications async.
- Object storage for media.

## 4. Design A Video Streaming Platform

Must discuss:

- Upload.
- Object storage.
- Transcoding.
- Metadata.
- CDN delivery.
- Recommendations.

Good follow-up:

> How does the system handle a viral video?

Expected concepts:

- CDN caching.
- Pre-generated video resolutions.
- Async transcoding queue.
- Metadata cache.
- Separate upload and playback paths.

## 5. Design An E-Commerce Checkout

Must discuss:

- Cart validation.
- Price verification.
- Inventory reservation.
- Payment.
- Order creation.
- Notification.

Good follow-up:

> How do you prevent duplicate orders or double payment?

Expected concepts:

- Idempotency key.
- Strong consistency for payment/order status.
- Queue for email.
- Circuit breaker around payment provider.
- Audit logs.

## 6. Design A Rate Limiter

Must discuss:

- Key choice.
- Algorithm.
- Distributed counters.
- Redis or local limits.
- Response headers.
- Fail-open vs fail-closed.

Good follow-up:

> How do you support different limits for free and premium users?

Expected concepts:

- Token bucket.
- User-tier configuration.
- Atomic updates.
- 429 and Retry-After.

## 7. Design A Notification System

Must discuss:

- Email, SMS, push.
- Templates.
- User preferences.
- Retries.
- Provider failures.
- Idempotency.

Good follow-up:

> How do you avoid sending the same notification twice?

Expected concepts:

- Queue.
- Command pattern.
- Provider adapter.
- Idempotency records.
- DLQ.

## 8. Design A News Feed

Must discuss:

- Follow graph.
- Feed generation.
- Fanout on write vs fanout on read.
- Celebrity users.
- Ranking.
- Pagination.

Good follow-up:

> How do you handle users with millions of followers?

Expected concepts:

- Hybrid fanout.
- Cache feed pages.
- Cursor pagination.
- Eventual consistency.
- Hot key mitigation.

## Practice Scoring Checklist

After each mock, check:

- Did you clarify requirements first?
- Did you state consistency needs?
- Did you identify read/write ratio?
- Did you define APIs?
- Did you explain data model and indexes?
- Did you include cache, queue, and database only where justified?
- Did you discuss failure modes?
- Did you mention metrics and alerts?
- Did you give tradeoffs instead of one-sided choices?
