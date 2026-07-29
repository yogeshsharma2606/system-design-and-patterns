# API Design

APIs clarify the system boundary. Call out auth, idempotency for writes, pagination, rate limits, and error behavior.

> I would make create operations idempotent where retries could otherwise create duplicate side effects.
