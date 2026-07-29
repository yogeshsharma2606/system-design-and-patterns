# Module 01 — Load Balancer

## TL;DR

A load balancer distributes traffic across multiple backend servers for availability and horizontal scaling. Choose L4 for throughput, L7 for HTTP routing. Use health checks, avoid sticky sessions when possible.

## Concept

### Daily-life analogy

Like a restaurant host seating guests across multiple tables so one waiter is not overwhelmed.

## How It Really Works

A load balancer distributes incoming traffic across multiple backend servers. It improves availability, scalability, and fault tolerance by preventing one machine from handling all requests.

In interviews, load balancers usually appear right after clients and before stateless application servers.

```text
Users -> DNS -> Load Balancer -> App Server 1
                              -> App Server 2
                              -> App Server 3
```

## Why / When / Trade-offs

Load balancing shows that you understand horizontal scaling. Instead of making one server bigger, you add more servers and distribute traffic.

Interviewers expect you to discuss:

- How traffic is distributed.
- How unhealthy servers are removed.
- What happens during deploys.
- Whether services are stateless.
- Whether sessions need stickiness.

## L4 vs L7 Load Balancing

Layer 4 load balancing works at TCP/UDP level. It is fast and simple.

Use it when:

- You only need to route by IP and port.
- You need high throughput.
- You are load balancing databases, TCP services, or generic network traffic.

Layer 7 load balancing works at HTTP level. It can inspect paths, headers, cookies, and hostnames.

Use it when:

- `/api/orders` should go to Order Service.
- `/api/payments` should go to Payment Service.
- You need header-based routing for A/B tests.
- You need TLS termination or request-level rules.

Tradeoff:

> L7 gives smarter routing but adds more processing overhead than L4.

## Common Algorithms

### Round Robin

Requests go to servers one by one.

Good for:

- Similar servers.
- Similar request cost.

Weakness:

- Does not account for slow or overloaded servers.

### Least Connections

Routes to the server with the fewest active connections.

Good for:

- Long-running requests.
- WebSocket or streaming workloads.

Weakness:

- Connection count does not always equal CPU or memory load.

### Weighted Routing

More powerful servers receive more traffic.

Real example:

If one instance has 16 CPU cores and another has 8, assign higher weight to the larger instance.

### Consistent Hashing

Maps a key to a server so the same key usually lands on the same backend.

Useful for:

- Distributed caches.
- Session-like routing.
- Reducing remapping when nodes join or leave.

Real example:

Redis cluster can use hash slots so a key like `user:123` consistently maps to a specific shard.

## Health Checks

A load balancer should stop routing traffic to unhealthy servers.

Basic health check:

```http
GET /health
```

Better health check:

```http
GET /ready
```

`/ready` should verify whether the service can actually handle traffic, such as database connectivity, required configuration, and dependency readiness.

Interview warning:

Do not make health checks too heavy. If every health check hits the database aggressively, the health check itself can create load.

## Sticky Sessions

Sticky sessions route the same client to the same backend.

Use when:

- Session state is stored in application memory.
- WebSocket connection affinity is needed.

Avoid when:

- You can make services stateless.
- Session state can live in Redis or a database.

Tradeoff:

Sticky sessions simplify state handling but reduce flexibility. If one server becomes hot, users stuck to that server suffer.

## Worked Scenario

### E-Commerce

Product browsing traffic is distributed across many stateless catalog servers. If one server dies, the load balancer removes it and sends users to healthy instances.

### Ride Booking

The API layer receives driver location updates and rider requests. Load balancing prevents one application server from becoming a bottleneck during peak traffic.

### Netflix-Like Streaming

API requests for metadata go through load balancers. Actual video content is usually served from CDN nodes, not app servers.

## Gotchas & Failure Modes

- Load balancer itself becomes a single point of failure.
- Health check says server is healthy even when dependencies are broken.
- Sticky sessions overload one server.
- Bad deploy sends traffic to new broken instances.
- No connection draining causes in-flight requests to fail during deploy.

## How To Design For Failure

- Use multiple load balancers in active-active or active-passive mode.
- Use health checks and readiness checks.
- Use connection draining during deployments.
- Keep app servers stateless.
- Use autoscaling based on CPU, memory, latency, or request queue length.

## Interview Q&A

### What if one backend is slow but not down?

Use latency-aware health checks, outlier detection, circuit breakers, and least-connections routing. A server can be technically alive but still unhealthy for users.

### Where does TLS terminate?

Often at the load balancer or API gateway. This reduces work for app servers and centralizes certificate management. For high-security internal systems, traffic may be re-encrypted between services.

### Can load balancer solve database bottlenecks?

No. It scales stateless application traffic. Database scaling needs read replicas, indexing, sharding, caching, or partitioning.

## Interview Q&A

> I would keep the application servers stateless behind the load balancer so failed instances can be replaced without user-visible impact.

## Further Reading

- Practice with `cheatsheets/mock-system-design-prompts.md`
- Quick reference: `overview-concepts/`
