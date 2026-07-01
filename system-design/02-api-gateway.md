# API Gateway

## What It Is

An API gateway is the entry point between clients and backend services. It routes requests, applies cross-cutting policies, and hides internal service topology from clients.

```text
Mobile App -> API Gateway -> User Service
                         -> Order Service
                         -> Payment Service
```

## Why Interviewers Care

API gateways are common in microservice designs. They help you discuss routing, authentication, rate limiting, versioning, observability, and client simplicity.

## Responsibilities

Common gateway responsibilities:

- Route requests to backend services.
- Authenticate requests.
- Enforce authorization policies at a coarse level.
- Apply rate limits.
- Terminate TLS.
- Validate request shape.
- Transform requests or responses.
- Aggregate responses for client convenience.
- Log request metadata.

## Real-Time Examples

### Food Delivery App

The mobile app calls:

```http
GET /home
```

The gateway may aggregate:

- User profile from User Service.
- Nearby restaurants from Restaurant Service.
- Active offers from Promotion Service.
- Cart summary from Cart Service.

This reduces mobile round trips, which matters on slow networks.

### E-Commerce

The gateway routes:

- `/products` to Catalog Service.
- `/cart` to Cart Service.
- `/checkout` to Order Service.
- `/payments` to Payment Service.

It also validates JWT tokens and applies stricter rate limits to checkout and login endpoints.

### Banking

The gateway enforces TLS, request signing, audit logging, and strict rate limits. Sensitive operations may require additional authorization in the backend service too.

## Gateway vs Load Balancer

A load balancer distributes traffic across instances of the same service.

An API gateway routes requests across different services and applies API policies.

In many systems, both exist:

```text
Client -> Load Balancer -> API Gateway -> Service Load Balancer -> Service Instances
```

## Request Aggregation

Aggregation reduces client complexity but can make the gateway heavy.

Good use:

- Mobile home screen needs data from many services.

Bad use:

- Gateway contains business rules like payment eligibility or inventory reservation.

Interview phrase:

> I would keep cross-cutting concerns in the gateway, but avoid putting domain business logic there.

## Authentication And Authorization

Gateway can verify tokens:

- Is token valid?
- Is token expired?
- Which user is making the call?
- Which tenant or role is attached?

Backend services should still enforce business-level authorization.

Example:

Gateway can verify that the user is authenticated. Order Service must verify that the user owns `orderId`.

## Versioning

Gateway can route:

```text
/v1/orders -> old Order Service
/v2/orders -> new Order Service
```

It can also support gradual migration by routing a percentage of traffic to a new version.

## Tradeoffs

Benefits:

- Simplifies clients.
- Centralizes cross-cutting logic.
- Improves observability at the edge.
- Helps rate limiting and abuse prevention.

Costs:

- Adds latency.
- Can become a bottleneck.
- Can become a single point of failure.
- Can become too complex if business logic leaks in.

## Failure Modes

- Gateway outage blocks all clients.
- Bad routing config sends traffic to wrong services.
- Too much aggregation increases latency.
- Gateway retries amplify traffic during downstream failure.
- Incorrect caching leaks private user data.

## Interview Follow-Ups

### What if one downstream service is slow?

Use timeouts, circuit breakers, fallback responses, and partial responses where business allows.

Example:

Food delivery home page can still show restaurants even if promotions are temporarily unavailable.

### How do you avoid gateway bottleneck?

Run multiple gateway instances behind a load balancer, keep them stateless, cache safe metadata, and avoid heavy business logic.

### Should internal services trust gateway auth?

They can trust identity propagated by the gateway only within a secure internal network, but important authorization checks should still happen in the owning service.

## Strong Interview Phrase

> The gateway is best for cross-cutting concerns like routing, authentication, rate limiting, and logging. Domain decisions should remain inside the owning service.
