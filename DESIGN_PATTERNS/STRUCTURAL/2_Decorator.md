### Decorator Design Pattern – Explained in Plain English

Imagine you have a cup of basic coffee.  
You can **add** milk, sugar, whipped cream, caramel, etc., and each addition changes (decorates) the coffee without creating a completely new type of coffee from scratch.

The **Decorator pattern** lets you **wrap** an object with extra behavior (or features) at runtime, while still keeping the same interface.

It’s useful when:
- You want to add responsibilities to objects dynamically and transparently.
- You have many combinations of features (e.g., Coffee, Coffee+Milk, Coffee+Milk+Sugar, etc.), but don’t want to create a class for every combination (that would explode into dozens of classes!).

### Key Players

1. **Component** – The common interface (e.g., `Beverage`)
2. **Concrete Component** – The original object (e.g., `Espresso`, `HouseBlend`)
3. **Decorator** – Abstract class that holds a reference to a Component and has the same interface
4. **Concrete Decorators** – Actual add-ons (e.g., `Milk`, `Sugar`, `Whip`)

### Java Example: Coffee Shop

```java
// 1. Component interface
public interface Beverage {
    String getDescription();
    double cost();
}
```

```java
// 2. Concrete components (basic drinks)
public class Espresso implements Beverage {
    @Override
    public String getDescription() {
        return "Espresso";
    }

    @Override
    public double cost() {
        return 1.99;
    }
}

public class HouseBlend implements Beverage {
    @Override
    public String getDescription() {
        return "House Blend Coffee";
    }

    @Override
    public double cost() {
        return 0.89;
    }
}
```

```java
// 3. Abstract Decorator
public abstract class CondimentDecorator implements Beverage {
    protected Beverage beverage;  // the thing we're wrapping

    public CondimentDecorator(Beverage beverage) {
        this.beverage = beverage;
    }

    // Most decorators just delegate description and cost
    public abstract String getDescription();
}
```

```java
// 4. Concrete Decorators
public class Milk extends CondimentDecorator {

    public Milk(Beverage beverage) {
        super(beverage);
    }

    @Override
    public String getDescription() {
        return beverage.getDescription() + ", Milk";
    }

    @Override
    public double cost() {
        return beverage.cost() + 0.30;
    }
}

public class Sugar extends CondimentDecorator {

    public Sugar(Beverage beverage) {
        super(beverage);
    }

    @Override
    public String getDescription() {
        return beverage.getDescription() + ", Sugar";
    }

    @Override
    public double cost() {
        return beverage.cost() + 0.10;
    }
}

public class WhippedCream extends CondimentDecorator {

    public WhippedCream(Beverage beverage) {
        super(beverage);
    }

    @Override
    public String getDescription() {
        return beverage.getDescription() + ", Whipped Cream";
    }

    @Override
    public double cost() {
        return beverage.cost() + 0.40;
    }
}
```

### Using It (Client Code)

```java
public class CoffeeShop {
    public static void main(String[] args) {
        // Simple espresso, no extras
        Beverage espresso = new Espresso();
        System.out.println(espresso.getDescription() + " $ " + espresso.cost());
        // Output: Espresso $ 1.99

        // House blend with Milk, Sugar, and Whipped Cream
        Beverage houseBlend = new HouseBlend();
        houseBlend = new Milk(houseBlend);
        houseBlend = new Sugar(houseBlend);
        houseBlend = new WhippedCream(houseBlend);

        System.out.println(houseBlend.getDescription() + " $ " + houseBlend.cost());
        // Output: House Blend Coffee, Milk, Sugar, Whipped Cream $ 1.69
    }
}
```

### Why This Is Cool

- You can stack decorators in any order, any number of times.
- No need for 20+ classes like `HouseBlendWithMilkAndSugarAndWhip`.
- Open for extension, closed for modification (Open/Closed Principle).
- Same interface (`Beverage`) everywhere – client code doesn’t care if it’s plain or heavily decorated.

### Real-World Uses
- Java I/O (`BufferedReader` decorates `FileReader`)
- GUI components (adding borders, scrollbars)
- Logging, caching, authentication wrappers around services

That’s the Decorator pattern – wrapping objects like layers on a cake (or coffee)! ☕
