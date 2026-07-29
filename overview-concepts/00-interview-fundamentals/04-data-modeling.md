# Data Modeling

Keep schema simple. Mention indexes for common queries.

Example: `Order(userId, createdAt)` for history, `Payment(idempotencyKey)` for duplicate prevention.
