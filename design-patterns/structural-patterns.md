# Structural Design Patterns

Structural patterns describe how classes and objects are composed. They help integrate systems, add behavior, or simplify complex subsystems.

## Adapter

### Problem

Two interfaces do not match, but you need them to work together.

### Real-Time Example

Your system expects `PaymentGateway`, but Stripe, Razorpay, and PayPal each expose different APIs.

### Java-Like Example

```java
interface PaymentGateway {
    PaymentResult charge(PaymentRequest request);
}

class StripeAdapter implements PaymentGateway {
    private final StripeClient stripe;

    public PaymentResult charge(PaymentRequest request) {
        StripeResponse response = stripe.createCharge(request.amount(), request.cardToken());
        return new PaymentResult(response.id(), response.status());
    }
}
```

### When To Use

- Wrapping third-party APIs.
- Migrating legacy systems.
- Keeping internal interfaces stable.

### Common Mistake

Letting third-party models leak into your domain.

## Decorator

### Problem

Add behavior without modifying the original object.

### Real-Time Example

Add logging, retry, metrics, or caching around a service client.

### Java-Like Example

```java
class RetryingPaymentGateway implements PaymentGateway {
    private final PaymentGateway delegate;

    public PaymentResult charge(PaymentRequest request) {
        try {
            return delegate.charge(request);
        } catch (TimeoutException e) {
            return delegate.charge(request);
        }
    }
}
```

### When To Use

- Cross-cutting behavior.
- Optional behavior combinations.
- Avoid subclass explosion.

### Common Mistake

Stacking too many decorators until debugging becomes difficult.

## Facade

### Problem

Clients should not deal with a complex subsystem directly.

### Real-Time Example

Checkout facade hides cart validation, inventory reservation, payment, and order creation.

### Java-Like Example

```java
class CheckoutFacade {
    Order checkout(User user, Cart cart) {
        inventory.reserve(cart.items());
        Payment payment = payments.charge(user, cart.total());
        return orders.create(user, cart, payment);
    }
}
```

### When To Use

- Simplify a complex workflow.
- Provide a clean API over multiple services.
- Reduce caller coupling.

### Common Mistake

Facade becoming a god object with all business logic.

## Proxy

### Problem

Control access to another object.

### Real-Time Examples

- Authorization proxy checks permission before calling service.
- Caching proxy returns cached values.
- Remote proxy represents service over network.
- Lazy proxy loads expensive object only when needed.

### Java-Like Example

```java
class AuthorizedDocumentService implements DocumentService {
    private final DocumentService delegate;

    public Document get(String documentId, User user) {
        if (!user.canRead(documentId)) throw new ForbiddenException();
        return delegate.get(documentId, user);
    }
}
```

### Common Mistake

Putting too much unrelated logic into proxy layers.

## Composite

### Problem

Treat individual objects and groups uniformly.

### Real-Time Example

File system:

- File is a leaf.
- Folder contains files and folders.

### Java-Like Example

```java
interface FileSystemNode {
    long size();
}

class File implements FileSystemNode {
    public long size() { return bytes; }
}

class Folder implements FileSystemNode {
    public long size() {
        return children.stream().mapToLong(FileSystemNode::size).sum();
    }
}
```

### When To Use

- Tree structures.
- Menus and submenus.
- Organization hierarchy.
- Nested comments.

### Common Mistake

Forcing Composite when leaf and container operations are very different.

## Quick Selection Guide

- Interface mismatch: Adapter.
- Add behavior dynamically: Decorator.
- Simplify complex subsystem: Facade.
- Control access: Proxy.
- Tree-like structure: Composite.
