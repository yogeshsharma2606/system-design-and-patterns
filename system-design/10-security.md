# Security Basics For System Design

## What It Is

Security in system design means protecting users, data, infrastructure, and business operations from unauthorized access, abuse, leakage, and tampering.

Interviewers do not expect a full security audit, but they expect you to mention the right controls for sensitive systems.

## Authentication

Authentication answers: who are you?

Common approaches:

- Session cookies.
- JWT tokens.
- OAuth.
- API keys.
- Mutual TLS for service-to-service calls.

Real example:

A banking app uses session or token authentication plus multi-factor authentication for sensitive operations.

## Authorization

Authorization answers: what are you allowed to do?

Example:

An authenticated user can call:

```http
GET /orders/123
```

But Order Service must verify that order `123` belongs to that user.

Gateway auth is not enough for object-level permissions.

## Encryption

Use encryption in transit and at rest.

In transit:

- HTTPS from client to gateway.
- TLS between internal services for sensitive environments.

At rest:

- Encrypt database disks.
- Encrypt object storage.
- Protect backups.

## Secrets Management

Do not store secrets in code or config files committed to source control.

Use:

- Secret manager.
- Environment injection.
- Key rotation.
- Least privilege access.

Examples:

- Payment provider API key.
- Database password.
- JWT signing key.

## Rate Limiting And Abuse Prevention

Security-sensitive endpoints need rate limits:

- Login.
- Signup.
- Password reset.
- OTP generation.
- Search.
- Public APIs.

Real example:

Limit OTP sends per phone number and per IP to prevent SMS cost abuse.

## Audit Logs

Audit logs track important actions.

Use for:

- Admin actions.
- Payment changes.
- Permission changes.
- Data exports.
- Login anomalies.

Audit logs should be tamper-resistant and searchable.

## Data Privacy

Protect personally identifiable information.

Practices:

- Store only necessary data.
- Mask sensitive values in logs.
- Apply access controls.
- Define retention policies.
- Use tokenization for payment data.

## Real-Time Examples

### E-Commerce

Product browsing is public, but cart, address, and payment methods require authentication. Payment data should be handled by a compliant provider when possible.

### Food Delivery

Drivers should see customer address only when assigned to an active order. Support agents should have audited access.

### SaaS Multi-Tenant App

Every query must be scoped by tenant ID. Missing tenant filters can leak data across companies.

## Failure Modes

- Broken object-level authorization exposes other users' data.
- Tokens logged accidentally.
- API keys leaked in code.
- Weak rate limits allow brute force.
- Over-permissive internal services trust all requests.
- Private data cached publicly.

## Interview Follow-Ups

### JWT or session?

JWT is useful for stateless auth and distributed systems, but revocation is harder. Sessions are easier to revoke centrally but need session storage.

### Where should authorization live?

Coarse checks can happen at the gateway. Fine-grained ownership and business authorization should live in the owning service.

### How do you secure internal services?

Use network policies, service identity, mTLS where appropriate, least privilege, and audit logging.

## Strong Interview Phrase

> I would authenticate at the edge, enforce object-level authorization in the owning service, encrypt sensitive data, avoid logging secrets, and rate-limit abuse-prone endpoints.
