Structural design patterns are like “proven blueprints” for putting classes and objects together to build bigger, more flexible systems. They help you organize your code so it’s easier to maintain, extend, and reuse, without everything turning into a tangled mess.

There are 7 classic structural patterns (from the famous Gang of Four book). Here’s each one explained in plain English, with a real-life analogy:

| Pattern       | What it solves | Plain English explanation | Real-life analogy |
|---------------|----------------|------------------------|-----------------|
| **Adapter**    | You have an existing class, but its interface doesn’t match what the client needs. | It’s a power plug adapter when you travel abroad. Your European laptop doesn’t fit the US wall socket → you use an adapter that converts the shape so everything works. The laptop. | The laptop stays the same, the socket stays the same, the adapter translates between them. |
| **Bridge**    | You have two things that can vary independently (e.g. shapes and colors, platforms and devices). | Instead of making a class for every combination (WindowsCircle, MacCircle, WindowsSquare…), you split them. One hierarchy for “abstraction” (Circle, Square) and another for “implementation” (WindowsDrawer, MacDrawer). The abstraction holds a reference to the implementation. | TV + remote. The remote (abstraction) works with different TV brands (implementation). You can change the remote or change the TV without buying a new combination. |
| **Composite** | You have parts and wholes that should be treated the same way (tree structure). | You build a file system: File and Folder. Both can have .printName(), .getSize(). A folder just loops over its children. The client code treats a single file and a folder with 1000 files exactly the same. | A menu in a restaurant. A menu can contain items or sub-menus, but you call .print() on the menu the top-level menu and it prints everything recursively. |
| **Decorator** | You want to add new behavior to objects dynamically without changing their code. | Starbucks coffee: You start with PlainCoffee, then add Milk, then Mocha, then Whip. Each decorator wraps the previous one and adds its cost and description. | Christmas tree: tree → add lights → add ornaments → add star. The tree is still a tree, but now it’s decorated. |
| **Facade**    | A subsystem has dozens of complex classes, and most clients just want to do one simple thing. | You want to watch a movie at home. Instead of calling Projector.on(), Amplifier.on(), DVDPlayer.on(), PopcornMachine.start() yourself, you just call HomeTheater.watchMovie() and the Facade does all the steps (and also turns everything off later). | The facade hides the subsystem complexity. |
| **Flyweight**  | You need billions of objects, but they share a lot of data (e.g. characters in a text editor). | Every letter “A” doesn’t need its own font, size, color, position… Font+size+color is shared (intrinsic state), only x,y position is different (extrinsic). The Flyweight factory returns the same “A” object for every A with the same font. | Games: 10,000 trees. Instead of 10,000 full Tree objects, you have one TreeType (texture, mesh) and thousands of TreeInstances with only position. |
| **Proxy**     | You want to control access to an object (lazy loading, access control, logging, remote, etc.). | You have a huge image. You show a tiny thumbnail Proxy first. Only when the user clicks “show full size” does the proxy load the real 50 MB image. Other proxies: security proxy (checks permission), remote proxy (talks over network), logging proxy, etc. | A bouncer at a nightclub = proxy for the club. He decides if you’re allowed in before you reach the actual party inside. |

Quick cheat sheet when to use which:

- Need to use an existing class that has the wrong interface → **Adapter**  
- Two independent dimensions that explode in combinations → **Bridge**  
- Tree structure where leaf and node behave the same → **Composite**  
- Add responsibilities dynamically (like mixins, but at runtime) → **Decorator**  
- Too many classes to deal with, want one simple door → **Facade**  
- Memory explosion because of thousands of similar objects → **Flyweight**  
- Need lazy loading / access control / remote / logging → **Proxy**

That’s it! These patterns are all about “how do we compose objects and classes” instead of “how do we create them” (creational) or “how do they talk to each other” (behavioral). Once you know them, you’ll start seeing them everywhere in real frameworks (Spring, Hibernate, GUI toolkits, etc.).
