# Requirements Clarification

Separate functional requirements (what the system does) from non-functional requirements (latency, availability, consistency).

**Functional:** Users can upload videos, search products, place orders.

**Non-functional:** Low latency search, high availability checkout, strong consistency for payments.

> Before jumping into architecture, I want to clarify the core user flows and the scale we are designing for.
