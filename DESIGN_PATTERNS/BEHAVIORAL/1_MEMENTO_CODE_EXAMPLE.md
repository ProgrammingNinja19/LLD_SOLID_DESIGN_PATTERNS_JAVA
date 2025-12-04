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

// 3. Caretaker - manages the history (undo and redo stacks)
class History {
    private final Deque<EditorMemento> undoStack = new ArrayDeque<>();
    private final Deque<EditorMemento> redoStack = new ArrayDeque<>();

    // Save a new state (push to undo stack and clear redo stack)
    public void save(EditorMemento memento) {
        undoStack.push(memento);
        redoStack.clear();  // New changes invalidate redo history
    }

    // Undo: pop from undo, push to redo, return the memento to restore
    public EditorMemento undo() {
        if (undoStack.isEmpty()) {
            return null;
        }
        EditorMemento memento = undoStack.pop();
        redoStack.push(memento);  // Note: push the current state before restoring
        return memento;
    }

    // Redo: pop from redo, push to undo, return the memento to restore
    public EditorMemento redo() {
        if (redoStack.isEmpty()) {
            return null;
        }
        EditorMemento memento = redoStack.pop();
        undoStack.push(memento);
        return memento;
    }

    public boolean canUndo() {
        return !undoStack.isEmpty();
    }

    public boolean canRedo() {
        return !redoStack.isEmpty();
    }
}

// Demo
public class MementoDemo {
    public static void main(String[] args) {
        TextEditor editor = new TextEditor();
        History history = new History();

        System.out.println("Start typing...");
        editor.type("Hello ");
        history.save(editor.save());           // save point 1

        editor.type("World!");
        history.save(editor.save());           // save point 2

        editor.type(" This is awesome.");
        history.save(editor.save());           // save point 3

        System.out.println("Current: " + editor);

        // Undo twice
        System.out.println("\n--- Undo once ---");
        EditorMemento undo1 = history.undo();
        if (undo1 != null) {
            editor.restore(undo1);
        }
        System.out.println("After undo: " + editor);

        System.out.println("\n--- Undo again ---");
        EditorMemento undo2 = history.undo();
        if (undo2 != null) {
            editor.restore(undo2);
        }
        System.out.println("After second undo: " + editor);

        // Redo once
        System.out.println("\n--- Redo once ---");
        EditorMemento redo1 = history.redo();
        if (redo1 != null) {
            editor.restore(redo1);
        }
        System.out.println("After redo: " + editor);

        // Make a new change (should clear redo stack)
        editor.type(" Everyone.");
        history.save(editor.save());           // new save point

        System.out.println("\n--- After new change ---");
        System.out.println("Current: " + editor);

        // Try redo (should not work)
        System.out.println("\n--- Try redo (should fail) ---");
        EditorMemento redoFail = history.redo();
        if (redoFail == null) {
            System.out.println("No more redo available.");
        }
    }
}
```
