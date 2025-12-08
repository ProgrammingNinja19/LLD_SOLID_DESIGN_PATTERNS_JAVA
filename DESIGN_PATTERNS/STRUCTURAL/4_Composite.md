### Composite Pattern – Explained in Plain English

Imagine you have a **folder system** on your computer.

- A folder can contain **files**
- A folder can also contain **other folders** (which themselves contain files and folders)
- From the outside, you don’t care whether you’re dealing with a single file or a whole folder — you just want to do the same operation (e.g., show size, delete, print name) on everything inside.

The **Composite pattern** lets you treat **individual objects** and **groups of objects** the same way.

#### Key Players
1. **Component** – common interface/abstract class for everything (both leaves and composites)
2. **Leaf** – individual object (like a File) that has no children
3. **Composite** – a container (like a Folder) that can hold many children (leaves or other composites)

This way, the client code can work with a single object or an entire tree using exactly the same code.

### Real-World Example: File System

```java
import java.util.ArrayList;
import java.util.List;

// 1. Component - common interface
interface FileSystemItem {
    long getSize();           // size in bytes
    void print(String indent); // pretty print the structure
}

// 2. Leaf - a single file
class File implements FileSystemItem {
    private String name;
    private long size;

    public File(String name, long size) {
        this.name = name;
        this.size = size;
    }

    @Override
    public long getSize() {
        return size;
    }

    @Override
    public void print(String indent) {
        System.out.println(indent + "File: " + name + " (" + size + " bytes)");
    }
}

// 3. Composite - a directory/folder that can contain other items
class Directory implements FileSystemItem {
    private String name;
    private List<FileSystemItem> children = new ArrayList<>();

    public Directory(String name) {
        this.name = name;
    }

    public void add(FileSystemItem item) {
        children.add(item);
    }

    public void remove(FileSystemItem item) {
        children.remove(item);
    }

    @Override
    public long getSize() {
        long total = 0;
        for (FileSystemItem item : children) {
            total += item.getSize();  // works for both File and Directory!
        }
        return total;
    }

    @Override
    public void print(String indent) {
        System.out.println(indent + "Directory: " + name + " [size: " + getSize() + " bytes]");
        for (FileSystemItem item : children) {
            item.print(indent + "  ");  // same method works on everything
        }
    }
}

// Client code – works uniformly with files and folders
public class CompositeDemo {
    public static void main(String[] args) {
        // Create some files
        FileSystemItem report = new File("report.pdf", 2048);
        FileSystemItem photo  = new File("vacation.jpg", 512000);
        FileSystemItem song   = new File("song.mp3", 4096);

        // Create folders
        Directory docs = new Directory("Documents");
        Directory pictures = new Directory("Pictures");
        Directory music = new Directory("Music");

        // Build the tree
        docs.add(report);

        pictures.add(photo);
        pictures.add(new File("selfie.png", 120000));

        music.add(song);

        Directory home = new Directory("Home");
        home.add(docs);
        home.add(pictures);
        home.add(music);
        home.add(new File("readme.txt", 1024));

        // Use it uniformly!
        System.out.println("=== File System Structure ===");
        home.print("");

        System.out.println("\nTotal size of Home folder: " + home.getSize() + " bytes");
    }
}
```

### Output
```
=== File System Structure ===
Directory: Home [size: 639168 bytes]
  Directory: Documents [size: 2048 bytes]
    File: report.pdf (2048 bytes)
  Directory: Pictures [size: 632000 bytes]
    File: vacation.jpg (512000 bytes)
    File: selfie.png (120000 bytes)
  Directory: Music [size: 4096 bytes]
    File: song.mp3 (4096 bytes)
  File: readme.txt (1024 bytes)

Total size of Home folder: 639168 bytes
```

### Why This is Awesome

- The client (`main`) calls `print()` and `getSize()` on **any** `FileSystemItem` — it doesn’t care if it’s a file or a huge folder tree.
- Adding new operations (e.g., `delete()`, `move()`, `search()`) is easy: just add them to the `FileSystemItem` interface, and implement in both `File` and `Directory`.
- The structure can be as deep as you want (folders inside folders inside folders…).

### When to Use Composite Pattern
- You have part-whole hierarchies (trees)
- You want clients to treat individual objects and compositions uniformly
- Classic examples: GUI components (a button is a component, a panel containing buttons is also a component), organization charts, graphic drawing programs, file systems, etc.

That’s the Composite pattern in plain English + working Java code!
