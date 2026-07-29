# Module 09 — Observability

## TL;DR

Observability uses logs, metrics, and traces to understand system behavior. Monitor the four golden signals: latency, traffic, errors, saturation. Alert on symptoms, not causes.

## Concept

### Daily-life analogy

Like a car dashboard: speedometer (metrics), check-engine light (alerts), and dashcam (logs/traces).

## How It Really Works

Observability is the ability to understand what a system is doing from its outputs: logs, metrics, and traces.

In interviews, observability proves that you can operate the system after designing it.

## The Three Pillars

### Logs

Logs explain events.

Good logs include:

- Request ID.
- User or tenant ID when safe.
- Service name.
- Operation name.
- Error code.
- Latency.

Avoid logging secrets, passwords, tokens, or full payment data.

### Metrics

Metrics show trends and system health.

Common metrics:

- Request rate.
- Error rate.
- Latency percentiles.
- CPU and memory.
- Queue lag.
- Cache hit rate.
- Database connection count.

### Traces

Traces show the path of one request across services.

Example:

```text
API Gateway -> Order Service -> Payment Service -> Payment Provider
                         -> Inventory Service
```

Traces help identify which dependency caused latency.

## Golden Signals

Use these in interviews:

- Latency: how long requests take.
- Traffic: how many requests are flowing.
- Errors: how many requests fail.
- Saturation: how full resources are.

## Worked Scenario

### Checkout Latency Spike

Metrics show p95 checkout latency jumped from 300 ms to 4 seconds. Traces show Payment Provider calls are slow. Logs show timeout errors. Circuit breaker metrics show the circuit is opening.

### Queue Backlog

Order emails are delayed. Queue lag metric grows. Worker logs show SMTP provider throttling. Scaling workers alone does not help because the downstream provider is the bottleneck.

### Cache Issue

Database CPU jumps. Cache hit rate dropped from 95 percent to 40 percent after deploy. This suggests cache key format changed or cache invalidation removed too many keys.

## Alerts

Alert on symptoms, not only causes.

Good alerts:

- Checkout success rate below threshold.
- Payment error rate high.
- p95 API latency above SLO.
- Queue lag exceeds acceptable delay.

Weak alerts:

- CPU above 80 percent without user impact.
- Any single error log.

## SLI, SLO, SLA

SLI: measurement, such as p95 latency.

SLO: internal target, such as 99.9 percent successful checkout requests.

SLA: external promise with business or legal impact.

## Gotchas & Failure Modes

- No correlation ID makes debugging distributed requests hard.
- Too many logs increase cost and hide signal.
- Missing business metrics hides user impact.
- Alerts are noisy and get ignored.
- Metrics average hides tail latency.

## Interview Q&A

### What would you monitor for a rate limiter?

Allowed requests, blocked requests, Redis latency, limiter errors, top limited users, and 429 response rate.

### What would you monitor for caching?

Hit rate, miss rate, eviction count, hot keys, cache latency, and database fallback load.

### What would you monitor for queues?

Producer rate, consumer rate, lag, retry count, DLQ size, and processing latency.

## Interview Q&A

> I would track latency, traffic, errors, saturation, and business success metrics so we can detect both technical failures and user-visible failures.

## Further Reading

- Practice with `cheatsheets/mock-system-design-prompts.md`
- Quick reference: `overview-concepts/`
