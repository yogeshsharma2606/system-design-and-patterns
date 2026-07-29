# Module 07 — Database Scaling

## TL;DR

Scale databases with indexes, read replicas, caching, partitioning, and sharding — in that order. Shard only when simpler options are exhausted. Choose shard keys with high cardinality.

## Concept

### Daily-life analogy

Like organizing a library: first label shelves (indexes), then open reading rooms (replicas), then split into branches (sharding).

## How It Really Works

Database scaling means improving how the system handles growing reads, writes, storage, and query complexity.

In interviews, the database is often the real bottleneck after stateless services are horizontally scaled.

## Start With Query Patterns

Before choosing replication or sharding, ask:

- What are the main reads?
- What are the main writes?
- What is the read/write ratio?
- Which queries need low latency?
- Which fields are filtered or sorted?
- Is strong consistency required?

## Indexing

Indexes speed reads but slow writes and use storage.

Example:

For order history:

```text
CREATE INDEX orders_user_created ON orders(user_id, created_at DESC)
```

This helps:

```http
GET /users/{id}/orders
```

Common mistake:

Adding indexes without knowing query patterns. Too many indexes hurt inserts and updates.

## Read Replicas

Replicas copy data from primary database and serve reads.

```text
Writes -> Primary DB -> Replication -> Read Replica
Reads  -> Read Replica
```

Use when:

- System is read-heavy.
- Slight read lag is acceptable.

Real example:

Product catalog reads can go to replicas. Checkout writes should go to primary.

Failure mode:

Replica lag means user may not immediately see their latest order.

Solution:

For read-your-writes, route the user's immediate post-write read to primary or use session consistency.

## Partitioning

Partitioning splits a large table into smaller parts, often by time or range.

Example:

Store logs by month:

```text
logs_2026_01
logs_2026_02
logs_2026_03
```

Use when:

- Tables are very large.
- Queries usually target a subset of data.
- Old data can be archived or deleted easily.

## Sharding

Sharding splits data across multiple databases.

Example:

```text
shard = hash(userId) % numberOfShards
```

Use when:

- One database cannot handle write volume or storage.
- Data can be distributed by a good shard key.

Good shard keys:

- High cardinality.
- Evenly distributed.
- Commonly used in queries.

Bad shard keys:

- Country if most users are in one country.
- Date if all current writes hit today's shard.
- Celebrity user ID if one user becomes extremely hot.

## Denormalization

Denormalization duplicates data to make reads faster.

Example:

Store `restaurantName` in `Order` so order history does not need a join every time.

Tradeoff:

Faster reads, but duplicated data can become inconsistent.

## Worked Scenario

### Instagram Feed

User data, posts, likes, comments, and feed data may be partitioned or sharded differently. Feed reads are optimized heavily because they dominate traffic.

### Banking Ledger

Strong consistency matters more than raw speed. Use transactions, append-only ledger entries, careful indexes, and auditability.

### E-Commerce Catalog

Catalog reads can use cache and read replicas. Inventory writes need stronger correctness to avoid overselling.

## Gotchas & Failure Modes

- Hot shard receives too much traffic.
- Replica lag causes stale reads.
- Missing index causes full table scans.
- Cross-shard joins become expensive.
- Rebalancing shards is operationally difficult.

## Interview Q&A

### When do you shard?

Shard only when simpler options are insufficient: indexing, caching, read replicas, query optimization, and vertical scaling.

### How do you handle cross-shard queries?

Avoid them in the critical path. Use denormalized read models, search indexes, or async aggregation.

### SQL or NoSQL?

Choose based on access pattern and consistency needs. SQL is strong for relational data and transactions. NoSQL can fit high-scale key-value, document, or wide-column access patterns.

## Interview Q&A

> I would first optimize query patterns with indexes, caching, and read replicas. I would introduce sharding only when write volume or storage exceeds what a single primary can safely handle.

## Further Reading

- Practice with `cheatsheets/mock-system-design-prompts.md`
- Quick reference: `overview-concepts/`
