# Creational Design Patterns

Creational patterns deal with object creation. They help when construction logic is complex, variable, or should be hidden from callers.

## Singleton

### Problem

Only one instance should exist for a shared resource.

### Real-World Example

Application configuration loaded once at startup.

### Java-Like Example

```java
class AppConfig {
    private static final AppConfig INSTANCE = new AppConfig();

    private AppConfig() {}

    public static AppConfig getInstance() {
        return INSTANCE;
    }
}
```

### When To Use

- Shared config.
- Logger.
- Metrics registry.

### Common Mistake

Using Singleton as global mutable state. It makes testing harder and hides dependencies.

### Interview Phrase

> I would use Singleton only when one shared instance is truly required and the hidden global dependency does not hurt testability.

## Factory Method

### Problem

Caller should not know exact class creation logic.

### Real-Time Example

Payment method selected at runtime:

- Credit card.
- UPI.
- Wallet.
- Net banking.

### Java-Like Example

```java
interface PaymentProcessor {
    void pay(Money amount);
}

class PaymentProcessorFactory {
    PaymentProcessor create(String method) {
        if (method.equals("CARD")) return new CardPaymentProcessor();
        if (method.equals("UPI")) return new UpiPaymentProcessor();
        throw new IllegalArgumentException("Unsupported method");
    }
}
```

### When To Use

- Object type depends on input.
- Construction details should be centralized.
- New implementations may be added later.

### Common Mistake

Letting the factory grow into a huge `if/else` block. For many types, use registration or dependency injection.

## Abstract Factory

### Problem

Create families of related objects without mixing incompatible types.

### Real-World Example

UI toolkit creates Windows buttons and Windows dialogs together, or Mac buttons and Mac dialogs together.

### Java-Like Example

```java
interface UIFactory {
    Button createButton();
    Dialog createDialog();
}

class WindowsFactory implements UIFactory {
    public Button createButton() { return new WindowsButton(); }
    public Dialog createDialog() { return new WindowsDialog(); }
}
```

### When To Use

- Products come in families.
- You want to switch the whole family together.

### Common Mistake

Using Abstract Factory when a simple Factory Method is enough.

## Builder

### Problem

An object has many optional fields or construction steps.

### Real-Time Example

Create a complex notification:

- recipient.
- channel.
- template.
- variables.
- priority.
- retry policy.

### Java-Like Example

```java
Notification notification = Notification.builder()
    .to("user@example.com")
    .template("ORDER_CONFIRMED")
    .priority(HIGH)
    .build();
```

### When To Use

- Constructors have too many parameters.
- Object should be immutable after construction.
- Construction needs validation.

### Common Mistake

Using Builder for tiny objects with two fields.

## Prototype

### Problem

Creating an object from scratch is expensive, so clone an existing template.

### Real-Time Example

A game creates many enemies from a preconfigured enemy prototype.

### Java-Like Example

```java
interface Prototype<T> {
    T copy();
}

class ReportTemplate implements Prototype<ReportTemplate> {
    public ReportTemplate copy() {
        return new ReportTemplate(this);
    }
}
```

### When To Use

- Object creation is expensive.
- Objects differ slightly from a base template.

### Common Mistake

Shallow copying mutable nested objects and accidentally sharing state.

## Quick Selection Guide

- Need one instance: Singleton.
- Need runtime object selection: Factory Method.
- Need families of related objects: Abstract Factory.
- Need many optional construction fields: Builder.
- Need cheap copies of configured objects: Prototype.
