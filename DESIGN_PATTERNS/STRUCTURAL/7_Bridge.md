### Bridge Pattern – Explained in Plain English

Imagine you have **two things that can change independently**:

1. **What something is** (e.g., shapes: Circle, Square)
2. **How it’s drawn** (e.g., on screen with red color, on paper with blue ink, as vector graphics, as pixels, etc.)

If you mix them together in one class hierarchy, you get an explosion of classes:

```
RedCircle, BlueCircle, GreenCircle
RedSquare, BlueSquare, GreenSquare
VectorCircle, RasterCircle, etc.
```

That’s messy and hard to maintain.

The **Bridge pattern** says:  
**Separate the “what” from the “how”** and connect them with a bridge (a reference).

- One hierarchy = the **abstraction** (Shape)
- Another hierarchy = the **implementation** (how it’s rendered: API, color, etc.)

Now you can add new shapes OR new rendering ways without creating tons of new classes.

### Real-Life Analogy
Think of a TV and remote controls:
- TV = abstraction (different brands)
- Remote = implementation (basic remote, smart remote, universal remote)
You can pair any TV with any remote without making a “SamsungBasicRemote”, “LGBasicRemote”, etc.

### Java Example: Drawing Shapes with Different Rendering APIs

```java
// ========================
// 1. Implementation Hierarchy (the "how")
// ========================

// This is the "bridge" interface
interface Renderer {
    void renderCircle(double x, double y, double radius);
    void renderSquare(double x, double y, double side);
}

// Concrete implementation #1: Vector graphics
class VectorRenderer implements Renderer {
    @Override
    public void renderCircle(double x, double y, double radius) {
        System.out.printf("Vector: Circle at (%.1f,%.1f) radius %.1f%n", x, y, radius);
    }

    @Override
    public void renderSquare(double x, double y, double side) {
        System.out.printf("Vector: Square at (%.1f,%.1f) side %.1f%n", x, y, side);
    }
}

// Concrete implementation #2: Raster (pixel) graphics
class RasterRenderer implements Renderer {
    @Override
    public void renderCircle(double x, double y, double radius) {
        System.out.printf("Raster: Drawing pixels for circle at (%.1f,%.1f), radius %.1f%n", x, y, radius);
    }

    @Override
    public void renderSquare(double x, double y, double side) {
        System.out.printf("Raster: Drawing pixels for square at (%.1f,%.1f), side %.1f%n", x, y, side);
    }
}

// ========================
// 2. Abstraction Hierarchy (the "what")
// ========================

abstract class Shape {
    protected Renderer renderer;  // This is the BRIDGE

    protected Shape(Renderer renderer) {
        this.renderer = renderer;
    }

    abstract void draw();
    abstract void resize(double factor);
}

// Refined abstraction
class Circle extends Shape {
    private double x, y, radius;

    public Circle(Renderer renderer, double x, double y, double radius) {
        super(renderer);
        this.x = x;
        this.y = y;
        this.radius = radius;
    }

    @Override
    void draw() {
        renderer.renderCircle(x, y, radius);
    }

    @Override
    void resize(double factor) {
        radius *= factor;
    }

    public void move(double dx, double dy) {
        x += dx;
        y += dy;
    }
}

class Square extends Shape {
    private double x, y, side;

    public Square(Renderer renderer, double x, double y, double side) {
        super(renderer);
        this.x = x;
        this.y = y;
        this.side = side;
    }

    @Override
    void draw() {
        renderer.renderSquare(x, y, side);
    }

    @Override
    void resize(double factor) {
        side *= factor;
    }
}

// ========================
// 3. Client code
// ========================
public class BridgeDemo {
    public static void main(String[] args) {
        Renderer vector = new VectorRenderer();
        Renderer raster = new RasterRenderer();

        Shape circle = new Circle(vector, 5, 5, 10);
        circle.draw();                     // Vector circle

        circle = new Circle(raster, 5, 5, 10);
        circle.draw();                     // Raster circle

        Shape square = new Square(vector, 20, 20, 15);
        square.draw();

        // You can easily switch rendering at runtime!
        ((Circle)circle).resize(2);
        circle.draw();
    }
}
```

### Output
```
Vector: Circle at (5.0,5.0) radius 10.0
Raster: Drawing pixels for circle at (5.0,5.0), radius 10.0
Vector: Square at (20.0,20.0) side 15.0
Raster: Drawing pixels for circle at (5.0,5.0), radius 20.0
```

### Why This Is Awesome

- Add a new shape (Triangle)? → Just make a new class extending `Shape`
- Add a new renderer (OpenGL, SVG, 3D)? → Just implement `Renderer`
- No explosion of classes
- You can change renderer at runtime if needed

### Summary – Bridge in One Sentence
> **Decouple an abstraction from its implementation so that the two can vary independently.**

That’s the Bridge pattern – super useful when you have multiple dimensions of variation (like GUI toolkits + widgets, drivers + devices, etc.).
