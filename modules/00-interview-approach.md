# Module 00 — Interview Approach & Requirements

## TL;DR

System design interviews test reasoning under ambiguity. Clarify requirements, estimate scale, define APIs and data model, propose architecture, then deep-dive into bottlenecks and failures.

## Concept

### Daily-life analogy

Think of it like building a house: you ask what rooms are needed, how many people will live there, then sketch the floor plan before choosing materials.

System design interviews test how you reason under ambiguity. Interviewers are not looking for one perfect architecture. They want to see whether you clarify requirements, choose reasonable tradeoffs, identify bottlenecks, and design for failure.

## The Winning Flow

### 1. Clarify Requirements

Start by separating functional and non-functional requirements.

Functional examples:

- Users can upload videos.
- Users can search products.
- Drivers can accept rides.
- Customers can place orders.

Non-functional examples:

- Low latency search results.
- High availability checkout.
- Strong consistency for payment status.
- Eventual consistency for social feed counts.

Strong interview phrase:

> Before jumping into architecture, I want to clarify the core user flows and the scale we are designing for.

### 2. Ask Scale Questions

You do not need perfect math. You need enough scale thinking to justify your design.

Ask:

- Daily active users?
- Peak requests per second?
- Read/write ratio?
- Data size per entity?
- Retention period?
- Geographic distribution?

Example:

For a food delivery app, order placement is write-heavy during lunch and dinner peaks. Menu browsing is read-heavy throughout the day. This suggests caching menus and restaurants, but keeping order state strongly consistent.

### 3. Define APIs

APIs clarify the system boundary.

Example for URL shortener:

```http
POST /short-links
GET /{shortCode}
GET /short-links/{id}/analytics
```

Call out:

- Auth requirements.
- Idempotency for writes.
- Pagination for lists.
- Rate limits for public endpoints.
- Error behavior.

Strong interview phrase:

> I would make create operations idempotent where retries could otherwise create duplicate side effects.

### 4. Define Data Model

Keep schema simple but practical.

Example for order system:

```text
Order(id, userId, restaurantId, status, totalAmount, createdAt)
OrderItem(orderId, itemId, quantity, price)
Payment(id, orderId, status, providerRef, idempotencyKey)
```

Mention indexes:

- `Order(userId, createdAt)` for order history.
- `Payment(idempotencyKey)` to prevent duplicate charges.
- `Order(status, createdAt)` for operations dashboards.

### 5. High-Level Architecture

Most designs start with:

```text
Client -> Load Balancer -> API Gateway -> Service -> Cache -> Database
                                      -> Queue -> Workers
```

Then specialize it.

For chat:

- WebSocket gateway.
- Message service.
- Message store.
- Delivery queue.
- Push notification service.

For video streaming:

- Upload service.
- Object storage.
- Transcoding queue.
- CDN.
- Metadata database.

### 6. Deep Dive

Interviewers usually pick one area to push.

Common deep dives:

- How do you scale reads?
- How do you scale writes?
- How do you handle hot keys?
- What happens if Redis is down?
- What if payment provider times out?
- How do you prevent duplicate orders?
- How do you debug high latency?

## Worked Scenario: Designing Checkout

Core flow:

1. User submits cart.
2. Order service validates items and price.
3. Payment service charges user.
4. Inventory service reserves stock.
5. Order is confirmed.
6. Notification is sent.

Interview-worthy concerns:

- Use idempotency key for `POST /checkout`.
- Use database transaction or saga depending on service boundaries.
- Use queue for email and invoice generation.
- Use circuit breaker around payment provider.
- Cache product catalog, not payment status.
- Monitor payment failures, checkout latency, and abandoned orders.

Good tradeoff statement:

> I would keep payment and order confirmation strongly consistent from the user's perspective, while making email, invoice generation, and analytics asynchronous.

## Common Mistakes

- Jumping to microservices without requirements.
- Ignoring failure cases.
- Caching everything without invalidation strategy.
- Saying "use Kafka" without explaining why.
- Over-indexing before understanding query patterns.
- Forgetting idempotency on payment, order, and retryable writes.
- Ignoring observability and security until the interviewer asks.

## Interview Closing Template

End with:

> The design starts simple with load-balanced stateless services, a primary database, cache for read-heavy paths, and queues for asynchronous work. The main tradeoffs are consistency versus latency, cache freshness versus database load, and operational complexity versus scalability. I would validate the design using metrics for latency, error rate, throughput, saturation, and business-level success rates.

## Further Reading

- Practice with `cheatsheets/mock-system-design-prompts.md`
- Quick reference: `overview-concepts/`
