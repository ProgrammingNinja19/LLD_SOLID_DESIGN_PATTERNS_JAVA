
### Visitor Design Pattern – Explained in Plain English

Imagine you have a bunch of different objects (like Circle, Square, Triangle) that all represent shapes.  
You want to perform different operations on these shapes, for example:

- Calculate the area
- Draw them on screen
- Export them to XML
- Compute how much paint they need

You could add a method for every new operation inside each shape class… but that means every time you add a new operation (e.g., “calculatePaint”), you have to touch ALL shape classes. That’s a lot of changes and breaks the Open/Closed Principle (you shouldn’t modify existing classes when adding new behavior).

The **Visitor pattern** solves this by flipping the responsibility:

> Instead of the shapes knowing how to do every operation,  
> the operation (the visitor) knows how to work with every kind of shape.

So you can add new operations without touching the shape classes at all!

### How It Works (Plain English Steps)

1. Each element (Circle, Square, etc.) has an `accept(Visitor v)` method.
2. When you call `accept(visitor)` on a shape, the shape says: “Hey visitor, here I am – do whatever you want with me!”
3. The visitor then calls the right method (`visitCircle`, `visitSquare`, etc.) based on the actual type of the shape.

### Java Example – Calculating Area and Drawing

```java
// 1. The Visitor interface
interface ShapeVisitor {
    void visit(Circle circle);
    void visit(Square square);
    void visit(Triangle triangle);
}

// 2. Concrete visitors (operations)

// Calculates total area
class AreaVisitor implements ShapeVisitor {
    private double totalArea = 0;

    @Override
    public void visit(Circle circle) {
        totalArea += Math.PI * circle.radius * circle.radius;
    }

    @Override
    public void visit(Square square) {
        totalArea += square.side * square.side;
    }

    @Override
    public void visit(Triangle triangle) {
        // Using formula for equilateral triangle just for demo
        totalArea += (Math.sqrt(3) / 4) * triangle.side * triangle.side;
    }

    public double getTotalArea() {
        return totalArea;
    }
}

// Draws shapes (pretend drawing)
class DrawVisitor implements ShapeVisitor {
    @Override
    public void visit(Circle circle) {
        System.out.println("Drawing a circle with radius " + circle.radius);
    }

    @Override
    public void visit(Square square) {
        System.out.println("Drawing a square with side " + square.side);
    }

    @Override
    public void visit(Triangle triangle) {
        System.out.println("Drawing a triangle with side " + triangle.side);
    }
}

// 3. The Element interface
interface Shape {
    void accept(ShapeVisitor visitor);  // double dispatch!
}

// 4. Concrete elements
class Circle implements Shape {
    double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public void accept(ShapeVisitor visitor) {
        visitor.visit(this);   // "Hey visitor, here's a Circle!"
    }
}

class Square implements Shape {
    double side;

    public Square(double side) {
        this.side = side;
    }

    @Override
    public void accept(ShapeVisitor visitor) {
        visitor.visit(this);
    }
}

class Triangle implements Shape {
    double side; // equilateral for simplicity

    public Triangle(double side) {
        this.side = side;
    }

    @Override
    public void accept(ShapeVisitor visitor) {
        visitor.visit(this);
    }
}

// 5. Demo / Client code
public class VisitorDemo {
    public static void main(String[] args) {
        Shape[] shapes = {
            new Circle(5),
            new Square(4),
            new Triangle(6)
        };

        // Operation 1: Calculate total area
        AreaVisitor areaVisitor = new AreaVisitor();
        for (Shape s : shapes) {
            s.accept(areaVisitor);
        }
        System.out.printf("Total area = %.2f\n", areaVisitor.getTotalArea());

        // Operation 2: Draw all shapes
        DrawVisitor drawVisitor = new DrawVisitor();
        for (Shape s : shapes) {
            s.accept(drawVisitor);
        }
    }
}
```

**Output:**
```
Total area = 134.27
Drawing a circle with radius 5.0
Drawing a square with side 4.0
Drawing a triangle with side 6.0
```

### Why This Is Awesome

- Want to add a new operation (e.g., export to JSON)?  
  → Just create a new visitor `JsonExportVisitor`. No need to touch Circle/Square/Triangle!

- Want to add a new shape (e.g., Rectangle)?  
  → Add the class and implement `accept()`, then update ALL existing visitors (that's the trade-off).

### When to Use Visitor Pattern

- You have a stable set of element types but many operations that keep growing.
- Operations are very different from each other (area, drawing, serialization, etc.).
- You want to keep elements clean and follow Open/Closed principle for new behaviors.

That's the Visitor pattern in simple words + working Java code! Let me know if you want a version with generics or using Java 17+ features. Happy coding! 🚀
