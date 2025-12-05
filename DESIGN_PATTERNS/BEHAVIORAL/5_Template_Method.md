### Template Method Design Pattern – Explained in Plain English

Imagine you're running a fast-food chain.  
Every burger (Big Mac, Cheeseburger, Veggie Burger) follows the **exact same steps** when being made:

1. Toast the bun  
2. Cook the patty  
3. Add sauce  
4. Add cheese and veggies  
5. Wrap and serve  

The **overall process never changes** – that's the template.  
But **some steps are different** depending on the burger: the patty can be beef, chicken, or veggie, and the sauce might be different.

The Template Method pattern does exactly this in code:

- A parent class defines the skeleton of an algorithm (the "template").
- It declares some steps as `abstract` or overridable so subclasses can customize them.
- Subclasses **must** follow the same order, but they fill in the details.

This ensures consistency while allowing flexibility.

### Java Example: Making Different Types of Burgers

```java
// The "template" – defines the fixed order of steps
abstract class BurgerMaker {
    
    // This is the TEMPLATE METHOD (final so nobody changes the order)
    public final void makeBurger() {
        toastBun();
        cookPatty();
        addSauce();
        addToppings();
        wrapAndServe();
        
        System.out.println("=== " + getBurgerName() + " is ready! ===\n");
    }
    
    // Common steps – same for all burgers
    private void toastBun() {
        System.out.println("Toasting the bun...");
    }
    
    private void wrapAndServe() {
        System.out.println("Wrapping burger and serving to customer.");
    }
    
    // These steps vary → subclasses MUST implement
    protected abstract void cookPatty();
    protected abstract void addSauce();
    protected abstract void addToppings();
    
    // Optional: let subclasses give their own name
    protected String getBurgerName() {
        return "Generic Burger";
    }
}

// Concrete subclass 1
class BigMacMaker extends BurgerMaker {
    
    @Override
    protected void cookPatty() {
        System.out.println("Cooking two beef patties...");
    }
    
    @Override
    protected void addSauce() {
        System.out.println("Adding special Big Mac sauce");
    }
    
    @Override
    protected void addToppings() {
        System.out.println("Adding lettuce, cheese, pickles, onions");
    }
    
    @Override
    protected String getBurgerName() {
        return "Big Mac";
    }
}

// Concrete subclass 2
class VeggieBurgerMaker extends BurgerMaker {
    
    @Override
    protected void cookPatty() {
        System.out.println("Grilling a plant-based patty...");
    }
    
    @Override
    protected void addSauce() {
        System.out.println("Adding vegan mayo and mustard");
    }
    
    @Override
    protected void addToppings() {
        System.out.println("Adding tomato, lettuce, avocado");
    }
    
    @Override
    protected String getBurgerName() {
        return "Veggie Burger";
    }
}

// Client code – super simple!
public class BurgerShop {
    public static void main(String[] args) {
        System.out.println("--- Preparing a Big Mac ---");
        BurgerMaker bigMac = new BigMacMaker();
        bigMac.makeBurger();
        
        System.out.println("--- Preparing a Veggie Burger ---");
        BurgerMaker veggie = new VeggieBurgerMaker();
        veggie.makeBurger();
    }
}
```

### Output when you run it:

```
--- Preparing a Big Mac ---
Toasting the bun...
Cooking two beef patties...
Adding special Big Mac sauce
Adding lettuce, cheese, pickles, onions
Wrapping burger and serving to customer.
=== Big Mac is ready! ===

--- Preparing a Veggie Burger ---
Toasting the bun...
Grilling a plant-based patty...
Adding vegan mayo and mustard
Adding tomato, lettuce, avocado
Wrapping burger and serving to customer.
=== Veggie Burger is ready! ===
```

### Key Points Summary

| What                  | How it's done in the pattern                                  |
|-----------------------|---------------------------------------------------------------|
| Fixed algorithm order | `makeBurger()` is `final` – no one can change the sequence    |
| Common steps          | Private methods in parent (toasting, wrapping)                |
| Customizable steps    | `abstract` methods that subclasses must override              |
| Optional customization| Overridable `protected` methods (like `getBurgerName()`)      |

### When to use Template Method?

- When you have a fixed process but some steps vary (building houses, processing payments, game character AI, data import/export, etc.)
- You want to enforce that all subclasses follow the same overall flow
- You want to avoid code duplication for the common parts

That’s the Template Method pattern – a super useful way to say:  
**"Here’s how we do things around here… but you can tweak these parts."**
