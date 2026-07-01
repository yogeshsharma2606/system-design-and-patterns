# Circuit Breaker

## What It Is

A circuit breaker prevents a service from repeatedly calling a failing dependency. Instead of waiting for slow failures, calls fail fast for a short period.

It protects systems from cascading failures.

## States

### Closed

Normal state. Requests go to the dependency.

### Open

Failure threshold is crossed. Requests fail fast without calling the dependency.

### Half-Open

After a cooldown, a few trial requests are allowed. If they succeed, the circuit closes. If they fail, it opens again.

## Why Interviewers Care

Circuit breakers show production thinking. They are important when designing systems that call payment providers, inventory services, fraud services, recommendation services, or any external API.

## Real-Time Example: Checkout

Checkout Service calls Payment Provider.

If Payment Provider becomes slow:

1. Checkout requests start waiting.
2. Threads or connections get exhausted.
3. Checkout Service becomes slow.
4. Other services calling Checkout also become slow.
5. A payment provider issue becomes a platform outage.

With circuit breaker:

1. Timeouts and failures cross threshold.
2. Circuit opens.
3. Checkout fails fast with a clear message.
4. System resources are protected.
5. Trial requests check recovery later.

## Circuit Breaker vs Retry

Retries help with temporary failures. Circuit breakers prevent repeated calls when the dependency is already unhealthy.

Use retries with:

- Timeouts.
- Exponential backoff.
- Jitter.
- Idempotency.

Avoid retry storms. If every service retries three times, downstream traffic can multiply.

## Fallbacks

Fallback depends on business context.

Examples:

- Recommendation Service down: show popular items.
- Promotion Service down: continue checkout without promotional suggestions.
- Payment Service down: tell user payment is temporarily unavailable.
- Inventory Service down: do not confirm order if stock correctness is critical.

## Tradeoffs

- Protects resources but may reject requests that might have succeeded.
- Requires careful thresholds.
- Needs observability to tune.
- Fallback behavior can hide serious problems if overused.

## Failure Modes

- Timeout too high causes thread exhaustion.
- Timeout too low causes false failures.
- Circuit threshold too sensitive causes unnecessary outages.
- No fallback creates poor user experience.
- Retrying non-idempotent operations creates duplicate side effects.

## Interview Follow-Ups

### Where do you place circuit breakers?

At callers of remote dependencies. Each service protects itself from dependencies it calls.

### Is a circuit breaker enough?

No. You also need timeouts, retries with backoff, bulkheads, rate limits, and monitoring.

### How do you monitor it?

Track circuit state, failure rate, timeout count, fallback count, dependency latency, and business impact.

## Strong Interview Phrase

> I would combine timeouts, retries with backoff, idempotency, and circuit breakers so a slow dependency does not cascade into a full system outage.
