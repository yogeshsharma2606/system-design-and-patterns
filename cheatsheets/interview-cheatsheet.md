# Interview Cheatsheet

## System Design Opening

Use this every time:

1. Clarify requirements.
2. Identify read/write ratio.
3. Estimate scale enough to justify choices.
4. Define APIs.
5. Define data model.
6. Propose high-level architecture.
7. Deep dive into bottlenecks and failure handling.

## Common Architecture

```text
Client
-> CDN
-> Load Balancer
-> API Gateway
-> Application Services
-> Cache
-> Database
-> Queue
-> Workers
-> Observability
```

## When To Use What

Use load balancer when:

- You have multiple stateless servers.
- You need high availability.
- You need rolling deploys and health checks.

Use API gateway when:

- Multiple backend services serve clients.
- You need centralized auth, routing, rate limiting, and logging.

Use cache when:

- Reads are frequent.
- Data is expensive to compute or fetch.
- Some staleness is acceptable.

Use queue when:

- Work can happen asynchronously.
- You need spike absorption.
- You need retries or background processing.

Use circuit breaker when:

- Calling unreliable or slow dependencies.
- You need to prevent cascading failures.

Use rate limiting when:

- Protecting public APIs.
- Preventing brute force or scraping.
- Enforcing tenant fairness.

Use sharding when:

- One database cannot handle write/storage load.
- Simpler options are not enough.

## Strong Phrases

- "I would first clarify consistency requirements because they affect database and cache choices."
- "This path is user-critical, so I would keep it synchronous and strongly consistent."
- "This side effect can be asynchronous because the user does not need to wait for it."
- "I would assume at-least-once delivery and make consumers idempotent."
- "I would use TTL, jitter, and request coalescing to reduce cache stampede risk."
- "I would choose a shard key with high cardinality and even distribution."
- "I would monitor latency, traffic, errors, saturation, and business success rate."

## Red Flags To Avoid

- "Just use microservices."
- "Just use Kafka."
- "Cache everything."
- "Database can scale automatically."
- "Exactly-once is easy."
- "Load balancer solves all scaling."
- "JWT means authorization is solved."

## Design Pattern Cheat Sheet

- Singleton: one shared instance.
- Factory: create object based on runtime input.
- Builder: construct complex object step by step.
- Adapter: wrap incompatible interface.
- Decorator: add behavior around object.
- Facade: simplify complex subsystem.
- Strategy: swap algorithms.
- Observer: notify subscribers.
- Command: represent action as object.
- Chain of Responsibility: pass request through handlers.
- State: behavior depends on lifecycle state.

## SOLID Cheat Sheet

- SRP: one reason to change.
- OCP: extend without modifying stable code.
- LSP: subtype should not break parent contract.
- ISP: keep interfaces focused.
- DIP: depend on abstractions, not concrete details.
