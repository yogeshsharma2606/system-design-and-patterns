# Consistency And Availability

## What It Is

Consistency describes what value a read returns after a write. Availability describes whether the system continues serving requests despite failures.

In distributed systems, network partitions force tradeoffs between consistency and availability.

## CAP Theorem

During a network partition, a distributed system must choose between:

- Consistency: every read sees the latest write.
- Availability: every request receives a non-error response.

Partition tolerance is not optional in real distributed systems because networks fail.

## Consistency Models

### Strong Consistency

Reads always see the latest committed write.

Use for:

- Bank balance.
- Payment status.
- Inventory reservation.
- Password change.

Tradeoff:

Higher latency and lower availability under failures.

### Eventual Consistency

Reads may be stale temporarily, but data converges later.

Use for:

- Like counts.
- View counts.
- Social feeds.
- Analytics dashboards.

Tradeoff:

Better availability and performance, but users may see temporary inconsistencies.

### Read-Your-Writes

A user sees their own updates immediately, even if others may see them later.

Real example:

After posting a tweet, the author should see it immediately. Followers can receive it eventually.

## Real-Time Examples

### Banking Transfer

If money is debited from one account and credited to another, strong consistency is expected. An eventually consistent transfer can create user panic and accounting problems.

### Social Media Likes

If a like count shows 101 instead of 102 for a few seconds, it is acceptable. Eventual consistency improves scale.

### Food Delivery Order Status

The customer should see accurate order state. Some side systems like analytics or email can lag.

## Quorum

Quorum systems require reads and writes to contact enough replicas.

Example:

If there are 3 replicas:

```text
write quorum = 2
read quorum = 2
```

This increases chance that reads see latest writes.

Tradeoff:

More coordination increases latency.

## Conflict Resolution

Conflicts happen when writes occur in different replicas during partitions.

Strategies:

- Last-write-wins.
- Version vectors.
- Merge rules.
- Manual reconciliation.

Use last-write-wins carefully. It can lose data.

Example:

For profile bio, last-write-wins may be acceptable. For bank transactions, it is not.

## Failure Modes

- Users see stale data after writes.
- Conflicting writes overwrite each other.
- System chooses availability and accepts writes that later need reconciliation.
- System chooses consistency and rejects requests during partition.

## Interview Follow-Ups

### How do you choose consistency?

Choose per feature, not per entire system.

Example:

In e-commerce, product reviews can be eventual. Checkout price and payment must be strongly consistent.

### How do you explain eventual consistency to users?

Use UI states like "processing", "syncing", or "pending confirmation". Do not show false certainty.

### Is strong consistency always better?

No. It can reduce availability and increase latency. Many high-scale user-facing systems deliberately use eventual consistency where business impact is low.

## Strong Interview Phrase

> I would choose consistency based on user harm. Payments and inventory need stronger guarantees, while likes, feeds, and analytics can usually be eventually consistent.
