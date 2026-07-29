# Common Follow-Up Questions

## Load Balancer

### What if one server is unhealthy?

Use health checks, readiness checks, and connection draining. The load balancer should stop sending traffic to unhealthy instances.

### What if users need session state?

Prefer stateless services with session data in Redis or database. Sticky sessions are possible but reduce flexibility.

## API Gateway

### Should business logic be in the gateway?

Usually no. Put cross-cutting concerns in the gateway and domain logic in owning services.

### What if gateway fails?

Run multiple gateway instances behind a load balancer across availability zones.

## Caching

### How do you invalidate cache?

Use TTL, delete-on-write, update-on-write, event-based invalidation, or versioned keys.

### How do you prevent stale price?

Cache browsing data, but verify authoritative price during checkout.

### What if cache is down?

Fallback carefully to database with rate limits and circuit breakers. Avoid letting cache failure overload the DB.

## Rate Limiting

### Which algorithm would you choose?

Token bucket is a good default because it allows controlled bursts while limiting sustained traffic.

### How do you rate limit in distributed systems?

Use Redis atomic counters, Lua scripts, or distributed token allocation. At extreme scale, approximate limits may be acceptable.

## Circuit Breaker

### How is it different from retry?

Retry attempts a failed operation again. Circuit breaker stops calling an unhealthy dependency for some time.

### What fallback would you use?

Depends on feature. Recommendations can fallback to popular items. Payments should fail clearly rather than pretending success.

## Queues

### How do you handle duplicate messages?

Make consumers idempotent using event IDs, operation IDs, or database uniqueness constraints.

### How do you handle poison messages?

Use retry limits and dead-letter queues.

### What if consumer lag grows?

Scale consumers, add partitions, optimize processing, or apply backpressure to producers.

## Database

### When do you use read replicas?

When reads dominate and stale reads are acceptable.

### When do you shard?

When one primary cannot handle write throughput or storage after simpler optimizations.

### How do you avoid hot shards?

Choose high-cardinality evenly distributed keys and avoid keys correlated with traffic spikes.

## Consistency

### Strong or eventual consistency?

Choose by user harm. Payments, inventory, and account security need stronger consistency. Likes, views, and feeds can often be eventual.

### How do you provide read-your-writes?

Route immediate reads to primary, use session consistency, or update local/read model synchronously for the author.

## Observability

### What metrics would you track?

Latency, traffic, errors, saturation, cache hit rate, queue lag, DB latency, dependency failures, and business success metrics.

### How do you debug slow checkout?

Check p95 latency, traces across Order and Payment services, dependency timeouts, queue lag, and recent deploys.

## Security

### JWT or session?

JWT works well for stateless distributed auth but revocation is harder. Sessions are easier to revoke centrally but need session storage.

### Where does authorization happen?

Coarse checks can happen at gateway. Object-level authorization must happen in the owning service.

## Design Patterns

### Strategy vs Factory?

Factory creates objects. Strategy selects interchangeable behavior.

### Adapter vs Facade?

Adapter converts an incompatible interface. Facade simplifies a complex subsystem.

### Decorator vs Proxy?

Decorator adds behavior. Proxy controls access to another object.

## SOLID

### SRP does not mean one method. What does it mean?

It means one reason to change.

### Is OCP always good?

No. Premature abstraction can make code harder. Apply it when variation is real or likely.

### What is DIP in practical terms?

High-level business logic should depend on interfaces, not vendor SDKs or low-level implementations.
