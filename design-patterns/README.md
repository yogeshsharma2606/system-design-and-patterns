# Design Patterns For Interviews

Design patterns are reusable solutions to common design problems. In interviews, do not just name a pattern. Explain the problem, why the pattern fits, and what tradeoff it introduces.

## How To Answer Pattern Questions

Use this structure:

1. Identify the changing part of the system.
2. Pick the pattern that isolates that change.
3. Explain the collaborators.
4. Give a small example.
5. Mention one downside.

## Most Useful Patterns By Interview Scenario

- Payment methods: Strategy or Factory.
- Notification channels: Strategy, Factory, Observer.
- Middleware pipeline: Chain of Responsibility.
- Order lifecycle: State.
- Undo/redo: Command.
- Third-party API wrapper: Adapter.
- Add logging/caching/retry: Decorator.
- Complex object construction: Builder.
- Hide subsystem complexity: Facade.

## Common Mistake

Do not force patterns into simple code. Patterns are useful when they reduce real complexity, not when they make code look advanced.
