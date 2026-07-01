# Rate Limiting

## What It Is

Rate limiting controls how many requests a user, IP, client, tenant, or service can make within a period of time.

It protects the system from abuse, accidental overload, scraping, brute-force attacks, and noisy tenants.

## Why Interviewers Care

Rate limiting is common in API gateway, login, payment, search, and public API designs. A strong answer covers the algorithm, key choice, distributed coordination, and user experience when requests are blocked.

## Where To Apply It

- API gateway for external traffic.
- Service layer for business-specific limits.
- Login endpoints to prevent brute force.
- Payment endpoints to prevent accidental duplicate attempts.
- Search endpoints to prevent expensive query abuse.
- Webhook endpoints to protect receivers.

## Algorithms

### Fixed Window

Allows N requests per fixed time window.

Example:

100 requests per minute from `user:123`.

Problem:

A user can send 100 requests at `12:00:59` and 100 more at `12:01:00`, creating a burst.

### Sliding Window

Tracks requests over the last rolling time period.

Better fairness than fixed window, but more expensive to store and calculate.

### Token Bucket

Tokens refill at a steady rate. Each request consumes one token. Bursts are allowed if tokens have accumulated.

Real example:

An API allows 10 requests per second with burst up to 50. This is useful for normal user bursts while protecting sustained capacity.

### Leaky Bucket

Requests flow out at a steady rate. Extra requests queue or get dropped.

Good for smoothing traffic.

## Designing A Distributed Rate Limiter

Basic architecture:

```text
Client -> API Gateway -> Rate Limiter -> Redis
                         -> Backend Service
```

Key design:

```text
rate_limit:{userId}:{endpoint}:{window}
```

For anonymous users, use IP address carefully. Many users may share one NAT IP.

## Real-Time Examples

### Login System

Limit by username and IP:

- 5 failed attempts per minute per username.
- 20 failed attempts per minute per IP.

This reduces brute-force attacks without locking out all users behind the same office network.

### Public API

Free users get 1000 requests per day. Paid users get 100000. Internal services may have different quotas.

### Food Delivery Search

Limit expensive search queries by user and location to prevent scraping restaurant data.

## Response Behavior

Return:

```http
429 Too Many Requests
Retry-After: 30
```

For paid APIs, also include remaining quota headers:

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 42
X-RateLimit-Reset: 1710000000
```

## Tradeoffs

- Strict limits protect systems but can hurt real users during spikes.
- Per-IP limits are simple but unfair for shared networks.
- Per-user limits are fairer but require authentication.
- Centralized Redis is accurate but can become a bottleneck.
- Local in-memory limits are fast but inconsistent across instances.

## Failure Modes

- Redis outage disables limiter or blocks all traffic.
- Wrong key choice blocks many users together.
- Attackers rotate IPs to bypass limits.
- Limits are too low for legitimate clients.
- Unlimited internal APIs overload downstream services.

## Interview Follow-Ups

### What happens if the rate limiter store is down?

Choose fail-open or fail-closed based on endpoint risk.

For public search, fail-open with degraded protection may be acceptable. For login or payment, fail-closed or apply local emergency limits.

### How do you handle distributed accuracy?

Use Redis atomic counters or Lua scripts for simple accuracy. For massive scale, use approximate counters, local pre-allocated tokens, or hierarchical limits.

### What should be rate limited?

Limit expensive, sensitive, or abuse-prone operations first: login, signup, password reset, search, checkout, OTP generation, and public APIs.

## Strong Interview Phrase

> I would use token bucket for controlled bursts, key limits by authenticated user where possible, and return 429 with retry information so clients can back off gracefully.
