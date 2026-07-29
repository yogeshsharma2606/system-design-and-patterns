# Module 14 — Behavioral Design Patterns

## TL;DR

Behavioral patterns manage communication: Strategy (swap algorithms), Observer (notify subscribers), Command (action objects), Chain of Responsibility (middleware), State (lifecycle), Template Method (shared skeleton).

## Concept

### Daily-life analogy

Like a restaurant: different pricing strategies (strategy), kitchen notified on order (observer), order ticket (command).

Behavioral patterns describe how objects communicate and how responsibility is assigned.

## Strategy

### Problem

You need to switch algorithms or behavior without changing the caller.

### Worked Scenario

Pricing strategy in ride booking:

- Normal pricing.
- Surge pricing.
- Coupon pricing.
- Corporate pricing.

### Java-Like Example

```java
interface PricingStrategy {
    Money calculate(Ride ride);
}

class SurgePricing implements PricingStrategy {
    public Money calculate(Ride ride) {
        return ride.baseFare().multiply(ride.surgeMultiplier());
    }
}
```

### When To Use

- Many interchangeable algorithms.
- Avoid large `if/else` blocks.
- Behavior changes at runtime.

### Common Mistake

Creating separate strategies before variation actually exists.

## Observer

### Problem

Multiple subscribers need to react when something changes.

### Worked Scenario

When an order is placed:

- Notification service sends SMS.
- Analytics service records event.
- Inventory service updates stock.
- Loyalty service awards points.

### Java-Like Example

```java
interface OrderObserver {
    void onOrderPlaced(Order order);
}

class OrderService {
    private List<OrderObserver> observers;

    void placeOrder(Order order) {
        save(order);
        observers.forEach(o -> o.onOrderPlaced(order));
    }
}
```

### Interview Note

In distributed systems, Observer often becomes pub/sub or event-driven architecture.

### Common Mistake

Making observer execution synchronous and slowing the main transaction.

## Command

### Problem

Represent an action as an object.

### Worked Scenario

- Job queue tasks.
- Undo/redo operations.
- Scheduled commands.
- Workflow steps.

### Java-Like Example

```java
interface Command {
    void execute();
}

class SendEmailCommand implements Command {
    public void execute() {
        emailService.send(to, template);
    }
}
```

### When To Use

- Queue actions.
- Retry failed actions.
- Store action history.
- Implement undo.

### Common Mistake

Command objects becoming large service objects.

## Chain Of Responsibility

### Problem

A request should pass through a sequence of handlers.

### Worked Scenario

API middleware pipeline:

1. Authentication.
2. Rate limiting.
3. Request validation.
4. Authorization.
5. Handler execution.

### Java-Like Example

```java
interface Handler {
    void handle(Request request, Chain chain);
}

class AuthHandler implements Handler {
    public void handle(Request request, Chain chain) {
        authenticate(request);
        chain.next(request);
    }
}
```

### When To Use

- Middleware.
- Validation pipelines.
- Approval workflows.

### Common Mistake

Hiding control flow so it becomes hard to know which handler ran.

## State

### Problem

Object behavior changes based on internal state.

### Worked Scenario

Order lifecycle:

```text
CREATED -> PAID -> PACKED -> SHIPPED -> DELIVERED
       -> CANCELLED
```

### Java-Like Example

```java
interface OrderState {
    void cancel(Order order);
}

class ShippedState implements OrderState {
    public void cancel(Order order) {
        throw new IllegalStateException("Cannot cancel shipped order");
    }
}
```

### When To Use

- Lifecycle-heavy objects.
- Many state-specific rules.
- Avoid complex state `if/else` blocks.

### Common Mistake

Using State when a simple enum and validation table is enough.

## Template Method

### Problem

Several workflows share the same skeleton but differ in some steps.

### Worked Scenario

Report generation:

1. Fetch data.
2. Validate data.
3. Format report.
4. Export report.

PDF and CSV reports share the skeleton but differ in formatting.

### Java-Like Example

```java
abstract class ReportGenerator {
    final Report generate() {
        Data data = fetchData();
        validate(data);
        return format(data);
    }

    abstract Report format(Data data);
}
```

### Common Mistake

Creating rigid inheritance hierarchies where composition would be simpler.

## Quick Selection Guide

- Swap algorithms: Strategy.
- Notify subscribers: Observer.
- Queue or undo actions: Command.
- Middleware pipeline: Chain of Responsibility.
- State-specific behavior: State.
- Shared workflow skeleton: Template Method.

## Further Reading

- Practice with `cheatsheets/mock-system-design-prompts.md`
- Quick reference: `overview-concepts/`
