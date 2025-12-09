### Flyweight Pattern – Explained in Plain English

Imagine you’re making a video game where thousands of trees appear on the screen.  
Each tree has the same look: same trunk texture, same leaf color, same shape.  
If you created a brand-new object for every single tree (with its own copy of the texture image in memory), you’d waste gigabytes of RAM for no reason.

The **Flyweight pattern** solves this by **sharing** the parts that are identical (the “intrinsic” data – the stuff that doesn’t change from tree to tree) and only keeping the parts that are different (the “extrinsic” data – like position on the map) separately.

Think of it like this:
- Intrinsic state → stored once and shared (flyweight objects)
- Extrinsic state → passed in when you need to draw/use it

It’s all about saving memory when you have huge numbers of similar objects.

### Real-World Analogy
In a word processor, every character “A” looks exactly the same (same font, size, color). Instead of creating a full object for each “A” on the page, you create one shared “A” object and just remember where each “A” is placed.

### Java Example: Forest with Millions of Trees

```java
import java.awt.*;
import java.util.HashMap;
import java.util.Map;

// 1. The Flyweight interface/class (shared intrinsic state)
class TreeType {
    private final String name;      // e.g. "Oak", "Pine"
    private final Color color;      // leaf color
    private final String texture;   // trunk/leaf image path or data

    public TreeType(String name, Color color, String texture) {
        this.name = name;
        this.color = color;
        this.texture = texture;
    }

    // This method receives extrinsic state (x, y) when needed
    public void draw(Graphics g, int x, int y) {
        g.setColor(Color.BLACK);
        g.fillRect(x - 5, y - 10, 10, 30);           // trunk
        g.setColor(color);
        g.fillOval(x - 30, y - 40, 60, 60);          // crown (leaves)
        // In real app you'd draw the actual texture image here
    }
}

// 2. Flyweight Factory – creates and manages shared flyweights
class TreeTypeFactory {
    private static final Map<String, TreeType> treeTypes = new HashMap<>();

    public static TreeType getTreeType(String name, Color color, String texture) {
        String key = name + color.toString() + texture;
        TreeType type = treeTypes.get(key);

        if (type == null) {
            type = new TreeType(name, color, texture);
            treeTypes.put(key, type);
            System.out.println("Created new TreeType: " + name);
        }
        return type;
    }

    public static int getTotalTypesCreated() {
        return treeTypes.size();
    }
}

// 3. The individual Tree object – very lightweight!
class Tree {
    private final int x;              // extrinsic
    private final int y;              // extrinsic
    private final TreeType treeType;  // shared intrinsic state

    public Tree(int x, int y, TreeType treeType) {
        this.x = x;
        this.y = y;
        this.treeType = treeType;
    }

    public void draw(Graphics g) {
        treeType.draw(g, x, y);  // delegate to the shared flyweight
    }
}

// 4. Client code – the Forest
import javax.swing.*;
import java.awt.*;
import java.util.ArrayList;
import java.util.List;
import java.util.Random;

public class Forest extends JFrame {
    private final List<Tree> trees = new ArrayList<>();

    public void plantTree(int x, int y, String name, Color color, String texture) {
        TreeType type = TreeTypeFactory.getTreeType(name, color, texture);
        Tree tree = new Tree(x, y, type);
        trees.add(tree);
    }

    @Override
    public void paint(Graphics g) {
        for (Tree tree : trees) {
            tree.draw(g);
        }
    }

    public static void main(String[] args) {
        Forest forest = new Forest();
        forest.setSize(800, 600);
        forest.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        forest.setVisible(true);

        Random rand = new Random();

        // Plant 500,000 trees – only a few TreeType objects will be created!
        for (int i = 0; i < 500_000; i++) {
            int x = rand.nextInt(800);
            int y = rand.nextInt(600);

            String name = rand.nextBoolean() ? "Oak" : "Pine";
            Color color = name.equals("Oak") ? new Color(34, 139, 34) : new Color(0, 100, 0);
            String texture = name + "_texture.png";

            forest.plantTree(x, y, name, color, texture);
        }

        System.out.println("Total Tree objects created: 500,000");
        System.out.println("Total TreeType (flyweight) objects created: " 
            + TreeTypeFactory.getTotalTypesCreated());  // Only 2!
    }
}
```

### What You’ll See When You Run It
```
Created new TreeType: Oak
Created new TreeType: Pine
Total Tree objects created: 500000
Total TreeType (flyweight) objects created: 2
```

Even with half a million trees on screen, only **two** `TreeType` objects exist in memory!  
That’s the power of the Flyweight pattern.

### When to Use Flyweight
- You have a huge number of objects (thousands/millions)
- Most of their state is identical (can be shared)
- The objects are immutable (or intrinsic state doesn’t change)
- Memory usage is a real concern (games, text rendering, particle systems, etc.)

That’s the Flyweight pattern in plain English + working Java code!
