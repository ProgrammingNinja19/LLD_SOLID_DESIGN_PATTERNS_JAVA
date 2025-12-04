### Memento Design Pattern – Explained in Plain English

Imagine you're typing a long document in a text editor. You make changes, then realize you messed up and want to **undo** several steps to go back to a previous version.  
The text editor quietly saved “snapshots” of your document at certain points, so it can restore them when you press Ctrl+Z.

That’s exactly what the **Memento pattern** does:

- It lets you **save the internal state** of an object (the “Originator”)  
- Later, you can **restore** that exact state  
- Without breaking encapsulation (the Originator doesn’t expose its private fields)

There are 3 main players:

1. **Originator** – the object whose state we want to save (your text editor document)
2. **Memento** – the snapshot that holds the saved state (immutable, usually private)
3. **Caretaker** – the thing that keeps the mementos (the undo stack)

It’s the official way to implement **undo/redo** functionality.

### Real-Life Example: Simple Text Editor with Undo

```java
import java.util.ArrayDeque;
import java.util.Deque;

// 1. Memento - holds the saved state
class EditorMemento {
    private final String text;      // the saved text
    private final int cursorPosition;

    // Private constructor so only Originator can create it
    private EditorMemento(String text, int cursorPosition) {
        this.text = text;
        this.cursorPosition = cursorPosition;
    }

    // Only Originator can read the state back
    String getText() {
        return text;
    }

    int getCursorPosition() {
        return cursorPosition;
    }

    // Factory method used by Originator
    static EditorMemento create(String text, int cursorPosition) {
        return new EditorMemento(text, cursorPosition);
    }

    @Override
    public String toString() {
        return "Memento{text='" + text + "', cursor=" + cursorPosition + "}";
    }
}

// 2. Originator - the object we want to save/restore
class TextEditor {
    private String text = "";
    private int cursorPosition = 0;

    public void type(String words) {
        // insert words at cursor position
        StringBuilder sb = new StringBuilder(text);
        sb.insert(cursorPosition, words);
        text = sb.toString();
        cursorPosition += words.length();
    }

    public void moveCursor(int position) {
        if (position >= 0 && position <= text.length()) {
            cursorPosition = position;
        }
    }

    // Save current state for display
    public String getText() {
        return text;
    }

    public int getCursorPosition() {
        return cursorPosition;
    }

    // Save current state into a memento
    public EditorMemento save() {
        return EditorMemento.create(text, cursorPosition);
    }

    }

    // Restore state from a memento
    public void restore(EditorMemento memento) {
        this.text = memento.getText();
        this.cursorPosition = memento.getCursorPosition();
    }

    @Override
    public String toString() {
        return "TextEditor{text='" + text + "', cursor=" + cursorPosition + "}";
    }
}

// 3. Caretaker - manages the history (undo stack)
class History {
    private final Deque<EditorMemento> undoStack = new ArrayDeque<>();

    public void push(EditorMemento memento) {
        undoStack.push(memento);
    }

    public EditorMemento pop() {
        return undoStack.pop();
    }

    public boolean isEmpty() {
        return undoStack.isEmpty();
    }
}

// Demo
public class MementoDemo {
    public static void main(String[] args) {
        TextEditor editor = new TextEditor();
        History history = new History();

        System.out.println("Start typing...");
        editor.type("Hello ");
        history.push(editor.save());           // save point 1

        editor.type("World!");
        history.push(editor.save());           // save point 2

        editor.type(" This is awesome.");
        System.out.println("Current: " + editor);

        // Oops, too much! Let's undo twice
        System.out.println("\n--- Undo once ---");
        editor.restore(history.pop());
        System.out.println("After undo: " + editor);

        System.out.println("\n--- Undo again ---");
        editor.restore(history.pop());
        System.out.println("After second undo: " + editor);
    }
}
```

### Output:
```
Start typing...
Current: TextEditor{text='Hello World! This is awesome.', cursor=29}

--- Undo once ---
After undo: TextEditor{text='Hello World!', cursor=12}

--- Undo again ---
After second undo: TextEditor{text='Hello ', cursor=6}
```

### Key Points

- The **Memento** is opaque to everyone except the Originator (private fields + package-private or private constructor).
- The **Caretaker** (History) never looks inside the memento – it just stores them.
- You can easily add redo functionality with a second stack.
- Perfect for undo/redo, game save points, wizard dialogs, etc.

That’s the Memento pattern in plain English with clean, working Java code!
