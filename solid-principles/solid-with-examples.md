# SOLID Principles With Examples

## S: Single Responsibility Principle

### Simple Definition

A class should have one reason to change.

This does not mean one method. It means one responsibility or one business reason to change.

### Bad Example

```java
class InvoiceService {
    Money calculateTotal(Invoice invoice) { }
    void saveToDatabase(Invoice invoice) { }
    void printPdf(Invoice invoice) { }
    void sendEmail(Invoice invoice) { }
}
```

This class changes when calculation rules change, database schema changes, PDF format changes, or email template changes.

### Improved Example

```java
class InvoiceCalculator { Money calculate(Invoice invoice) { } }
class InvoiceRepository { void save(Invoice invoice) { } }
class InvoicePdfRenderer { Pdf render(Invoice invoice) { } }
class InvoiceEmailSender { void send(Invoice invoice) { } }
```

### Real Project Example

In an e-commerce system, order pricing, order persistence, payment, and notification should not all live in one class.

### Interview Phrase

> I would split this because pricing rules and email delivery change for different reasons.

## O: Open/Closed Principle

### Simple Definition

Code should be open for extension but closed for modification.

You should be able to add new behavior without constantly editing stable existing code.

### Bad Example

```java
class PaymentService {
    void pay(String method) {
        if (method.equals("CARD")) payByCard();
        else if (method.equals("UPI")) payByUpi();
        else if (method.equals("WALLET")) payByWallet();
    }
}
```

Every new payment method modifies `PaymentService`.

### Improved Example

```java
interface PaymentProcessor {
    void pay(Money amount);
}

class CardPaymentProcessor implements PaymentProcessor { }
class UpiPaymentProcessor implements PaymentProcessor { }
class WalletPaymentProcessor implements PaymentProcessor { }
```

`PaymentService` depends on `PaymentProcessor`, and new methods are added as new implementations.

### Real Project Example

Add a new notification channel like WhatsApp without editing the core notification workflow.

### Common Mistake

Over-abstracting too early. If there is only one implementation and no expected variation, keep it simple.

## L: Liskov Substitution Principle

### Simple Definition

A subtype should be usable anywhere the parent type is expected without breaking behavior.

### Bad Example

```java
class Bird {
    void fly() { }
}

class Penguin extends Bird {
    void fly() {
        throw new UnsupportedOperationException();
    }
}
```

Code expecting a `Bird` that can fly breaks when given a `Penguin`.

### Improved Example

```java
interface Bird { }
interface FlyingBird extends Bird {
    void fly();
}

class Sparrow implements FlyingBird { }
class Penguin implements Bird { }
```

### Real Project Example

If `PaymentProcessor.pay()` promises to either charge or return a clear failure, a subclass should not silently ignore payment.

### Interview Phrase

> Subclasses should not weaken the contract or surprise callers.

## I: Interface Segregation Principle

### Simple Definition

Clients should not depend on methods they do not use.

### Bad Example

```java
interface Machine {
    void print();
    void scan();
    void fax();
}

class SimplePrinter implements Machine {
    public void scan() {
        throw new UnsupportedOperationException();
    }
}
```

### Improved Example

```java
interface Printer { void print(); }
interface Scanner { void scan(); }
interface Fax { void fax(); }

class SimplePrinter implements Printer { }
class MultiFunctionPrinter implements Printer, Scanner, Fax { }
```

### Real Project Example

Separate `ReadableRepository`, `WritableRepository`, and `SearchableRepository` if not all clients need all operations.

### Common Mistake

Creating many tiny interfaces without meaningful boundaries.

## D: Dependency Inversion Principle

### Simple Definition

High-level modules should depend on abstractions, not low-level details.

### Bad Example

```java
class OrderService {
    private StripeClient stripeClient = new StripeClient();

    void checkout(Order order) {
        stripeClient.charge(order.total());
    }
}
```

`OrderService` is tightly coupled to Stripe.

### Improved Example

```java
interface PaymentGateway {
    void charge(Money amount);
}

class OrderService {
    private final PaymentGateway paymentGateway;

    OrderService(PaymentGateway paymentGateway) {
        this.paymentGateway = paymentGateway;
    }
}
```

Now tests can use a fake gateway, and production can use Stripe, Razorpay, or another provider.

### Real Project Example

Order Service should depend on a `PaymentGateway` abstraction, not a vendor SDK directly.

### Interview Phrase

> Dependency injection is one way to apply dependency inversion, but the principle is about depending on stable abstractions.

## SOLID In One Design

Example: notification system.

- SRP: Template rendering, channel selection, and delivery are separate.
- OCP: Add WhatsApp by adding a new `NotificationSender`.
- LSP: Every sender follows the same send contract.
- ISP: Email sender does not depend on SMS-only methods.
- DIP: Notification workflow depends on `NotificationSender`, not `SmtpClient`.

## Common Interview Mistakes

- Reciting definitions without examples.
- Applying every principle mechanically.
- Creating abstractions for imaginary future needs.
- Ignoring testability.
- Confusing dependency injection with dependency inversion.
