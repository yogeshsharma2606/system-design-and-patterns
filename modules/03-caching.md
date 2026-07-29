# Module 03 — Caching

## TL;DR

Caching stores frequently read data closer to the consumer. Define what to cache, TTL, invalidation strategy, and what happens when cache fails. Cache-aside is the most common pattern.

## Concept

### Daily-life analogy

Like keeping your most-used tools on your desk instead of walking to the storage room every time.

## How It Really Works

Caching stores frequently used data in a faster location so requests do not always hit the source of truth. The source of truth is usually a database, object store, or external service.

```text
Client -> Service -> Cache -> Database
```

## Why / When / Trade-offs

Caching reduces latency and database load, but it introduces freshness and invalidation problems. A strong answer explains what to cache, how long to cache it, how to invalidate it, and what happens when the cache fails.

## Cache Locations

- Client cache: browser or mobile cache for assets and local data.
- CDN cache: edge cache for public content, images, videos, and static files.
- Application cache: in-memory data inside one service instance.
- Distributed cache: Redis or Memcached shared across service instances.
- Database cache: internal DB page or query cache.

## Cache-Aside

The service controls cache reads and writes.

```text
value = cache.get(key)
if value is missing:
    value = database.read(id)
    cache.set(key, value, ttl)
return value
```

Use it for read-heavy data where slight staleness is acceptable.

Real example:

An e-commerce service caches `product:{id}` because product pages are read far more often than updated.

Tradeoff:

Cache misses are slower because they hit both cache and database.

## Write-Through

Writes go to cache and database together.

Use it when the cache should stay fresh and extra write latency is acceptable.

Real example:

A user profile service updates Redis and the database when a user changes display name.

Tradeoff:

Every write becomes more expensive, and partial failure handling matters.

## Write-Back

Writes go to cache first and are persisted later.

Use it for high-throughput, loss-tolerant workloads.

Real example:

Analytics counters can be buffered in memory or cache and flushed later.

Avoid it for payments, order confirmation, and bank balances.

## TTL And Invalidation

TTL controls how long data can remain cached.

Short TTL gives fresher data but more database load. Long TTL reduces database load but increases stale-data risk.

Invalidation strategies:

- Delete cache entry when source data changes.
- Update cache entry after write.
- Use versioned cache keys.
- Publish invalidation events.

Real example:

When a restaurant updates its menu, Restaurant Service publishes `MenuUpdated`, and cache entries like `menu:{restaurantId}` are deleted.

## Cache Stampede

A cache stampede happens when many requests miss the cache at once and all hit the database.

Real example:

A celebrity profile cache expires during a viral event, and millions of requests hit the database.

Solutions:

- Add TTL jitter.
- Use request coalescing.
- Serve stale data while refreshing in background.
- Pre-warm critical keys.

## Hot Keys

A hot key receives too much traffic for one cache node.

Real example:

`world-cup-final-score` or `trending-post:123` gets huge traffic.

Solutions:

- Replicate hot keys.
- Add local in-process cache.
- Split logical keys.
- Put public content behind CDN.

## What To Cache

Good candidates:

- Product catalog.
- Restaurant menus.
- Public profile summaries.
- Popular search results.
- Feature flags.
- Static configuration.

Risky candidates:

- Payment status.
- Bank balance.
- Rapidly changing inventory.
- Private user data without strict user-scoped keys.

## Gotchas & Failure Modes

- Cache outage overloads the database.
- Stale data shows wrong price or status.
- Incorrect key design leaks data between users.
- Hot keys overload one shard.
- Invalidation bugs keep old data alive.

## Interview Q&A

### What if Redis is down?

Fallback to database with rate limits, circuit breakers, and load shedding. If all traffic falls through to the database, the database may go down too.

### How do you prevent stale price in checkout?

Cache product pages for browsing, but re-read authoritative price during checkout.

### How do you choose TTL?

Based on business tolerance for stale data. A restaurant menu may tolerate minutes; payment status should not rely on stale cache.

## Interview Q&A

> I would cache read-heavy, low-risk data first, define a TTL and invalidation strategy, and make sure cache failure does not take down the database.

## Further Reading

- Practice with `cheatsheets/mock-system-design-prompts.md`
- Quick reference: `overview-concepts/`
