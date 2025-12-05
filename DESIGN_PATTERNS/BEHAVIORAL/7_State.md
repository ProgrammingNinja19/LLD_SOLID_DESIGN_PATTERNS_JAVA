### State Design Pattern – Explained in Plain English

Imagine you have a TV remote.

- The TV can be **ON** or **OFF**.
- When the TV is **OFF**, pressing the power button turns it **ON**.
- When the TV is **ON**, pressing the power button turns it **OFF**.
- Pressing the volume button only works when the TV is **ON** – if it's OFF, nothing happens.

Instead of writing one big class full of `if (state == "on") ... else if (state == "off") ...` (which gets messy fast), the **State pattern** says:

> Let each state be its own object.  
> The main object (the TV) just holds a reference to the current state object.  
> When you press a button, you ask the **current state** what to do.

Each state object knows:
- What should happen when buttons are pressed
- How to change to another state when needed

This makes the code clean, easy to extend (add "Sleep" mode? Just add a new state class!), and avoids huge if-else chains.

### Real-World Java Example: A Simple TV

```java
// Step 1: Define the State interface
interface TvState {
    void pressPowerButton(TvContext tv);
    void pressVolumeUp(TvContext tv);
    void pressVolumeDown(TvContext tv);
}

// Step 2: The context class (the TV itself)
class TvContext {
    private TvState currentState;

    public TvContext() {
        // Start with TV off
        this.currentState = new OffState();
    }

    public void setState(TvState state) {
        this.currentState = state;
        System.out.println("TV is now: " + state.getClass().getSimpleName());
    }

    // Delegate button presses to the current state
    public void pressPower() {
        currentState.pressPowerButton(this);
    }

    public void volumeUp() {
        currentState.pressVolumeUp(this);
    }

    public void volumeDown() {
        currentState.pressVolumeDown(this);
    }
}

// Step 3: Concrete state classes

class OffState implements TvState {
    @Override
    public void pressPowerButton(TvContext tv) {
        System.out.println("Turning TV ON...");
        tv.setState(new OnState());
    }

    @Override
    public void pressVolumeUp(TvContext tv) {
        System.out.println("TV is OFF. No sound.");
    }

    @Override
    public void pressVolumeDown(TvContext tv) {
        System.out.println("TV is OFF. No sound.");
    }
}

class OnState implements TvState {
    private int volume = 10;

    @Override
    public void pressPowerButton(TvContext tv) {
        System.out.println("Turning TV OFF...");
        tv.setState(new OffState());
    }

    @Override
    public void pressVolumeUp(TvContext tv) {
        volume++;
        System.out.println("Volume increased to " + volume);
    }

    @Override
    public void pressVolumeDown(TvContext tv) {
        if (volume > 0) volume--;
        System.out.println("Volume decreased to " + volume);
    }
}

// Step 4: Let's use it!
public class Main {
    public static void main(String[] args) {
        TvContext tv = new TvContext();

        tv.pressPower();     // Turns ON
        tv.pressVolumeUp();  // Volume 11
        tv.pressVolumeUp();  // Volume 12
        tv.pressVolumeDown(); // Volume 11
        tv.pressPower();     // Turns OFF
        tv.pressVolumeUp();  // No sound (TV is off)
    }
}
```

### Output:
```
TV is now: OffState
Turning TV ON...
TV is now: OnState
Volume increased to 11
Volume increased to 12
Volume decreased to 11
Turning TV OFF...
TV is now: OffState
TV is OFF. No sound.
```

### Why This is Awesome

- No giant `if-else` or `switch` in the TV class
- Adding a new state (like MuteState or SleepState) is easy – just create a new class
- Each state controls its own behavior and transitions
- Very clean and follows "Open/Closed Principle" (open for extension, closed for modification)

### When to Use State Pattern?
Use it when:
- An object's behavior changes dramatically based on its internal state
- You have many `if (state == X)` conditions
- You expect to add new states in the future

Common examples: game characters (idle, running, jumping, attacking), order processing (pending → paid → shipped → delivered), TCP connection states, etc.

That's the State pattern – simple, powerful, and keeps your code tidy! 🚀
