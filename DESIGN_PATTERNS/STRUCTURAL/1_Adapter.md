### Adapter Design Pattern – Explained in Plain English

Imagine you have a **phone charger** that only works with **European plugs** (round pins), but you're in the **USA** where sockets have flat pins.

You don't throw away your charger.  
You buy a small **plug adapter** that has European pins on one side and American pins on the other.  
Now your European charger works perfectly in the USA.

That little plug adapter is exactly what the **Adapter Pattern** does in code.

### Why do we need it?

You have an existing class (the "European charger") that works great, but its interface (method names, parameters) doesn't match what your new code expects (the "American socket").

Instead of rewriting the old class (which might be huge, third-party, or legacy), you write a small **adapter class** that:
- Accepts the interface your new code wants
- Translates the calls into something the old class understands

### Real-World Java Example: Old Payment System

Let’s say you have an **old payment gateway** with this weird method:

```java
public class OldPaymentGateway {
    public void makePayment(String customerId, double dollars) {
        System.out.println("Payment of $" + dollars + " processed for customer " + customerId + " using OLD system");
    }
}
```

Now your new modern app expects every payment processor to implement this interface:

```java
public interface NewPaymentProcessor {
    void pay(String userEmail, int cents);
}
```

You can't change `OldPaymentGateway` (maybe it's from a vendor).  
So you write an **Adapter**:

```java
public class OldPaymentAdapter implements NewPaymentProcessor {
    
    private OldPaymentGateway oldGateway;
    
    public OldPaymentAdapter(OldPaymentGateway oldGateway) {
        this.oldGateway = oldGateway;
    }
    
    @Override
    public void pay(String userEmail, int cents) {
        // Convert cents → dollars
        double dollars = cents / 100.0;
        
        // Convert email to customerId (maybe extract part before @)
        String customerId = userEmail.split("@")[0];
        
        // Forward the call to the old system
        oldGateway.makePayment(customerId, dollars);
    }
}
```

### How to use it (client code)

```java
public class Shop {
    public static void main(String[] args) {
        // You still have the old gateway
        OldPaymentGateway oldGateway = new OldPaymentGateway();
        
        // Wrap it with an adapter so it looks like a modern processor
        NewPaymentProcessor processor = new OldPaymentAdapter(oldGateway);
        
        // Now your new code works without knowing anything about the old system!
        processor.pay("john.doe@example.com", 4999);  // $49.99
    }
}
```

**Output:**
```
Payment of $49.99 processed for customer john.doe using OLD system
```

Boom! Your new code thinks it's talking to a modern processor, but behind the scenes it's using the old one.

### Two Types of Adapter Pattern

1. **Class Adapter** – Uses multiple inheritance (not possible in Java for classes, only interfaces)
2. **Object Adapter** – Holds an instance of the old class (what we used above – recommended in Java)

### When to Use Adapter Pattern

- You want to use an existing class, but its interface doesn't match what you need
- You're working with legacy code or third-party libraries
- You want to make incompatible classes work together without changing their code

### Summary (Plain English)

> The Adapter pattern is like a universal power adapter for your code.  
> It lets an old or incompatible class "plug into" your new system by translating calls behind the scenes.

Just create a small wrapper class that speaks both languages – and everything works together peacefully! ✨
