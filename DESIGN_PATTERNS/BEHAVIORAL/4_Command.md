### Command Design Pattern – Explained in Plain English

Imagine you're in a restaurant.

- You (the customer) tell the waiter: "Bring me a chicken burger and a coke."
- The waiter doesn't cook the food himself. He just writes your order on a slip and puts it in the kitchen.
- The chef picks up the slip and cooks exactly what's written on it.
- Later you can say "Cancel my coke" or "Add fries" – again, the waiter just writes a new command.

Here, the **order slip** is the **Command** object.  
It contains everything needed to perform an action, but it doesn't do the work itself.

The Command Pattern turns a request (like "turn on the light" or "save the file") into a standalone object.  
This gives you tons of benefits:

- You can queue commands  
- You can undo/redo them  
- You can log them  
- You can send them over the network  
- The object that invokes the command doesn't need to know how it's done

### Real-life analogy → Code roles

| Restaurant       | Code Equivalent      | Job |
|------------------|----------------------|----------------------------------------|
| Customer         | Client               | Creates the command and gives it to the invoker |
| Waiter           | Invoker              | Holds the command and says "execute!" at the right time |
| Order slip       | Command (interface)  | Encapsulates "what to do" + parameters |
| Chef             | Receiver             | Actually knows how to cook (does the real work) |

### Java Example: Remote Control for Home Devices (Light + Fan)

```java
// 1. The "Receiver" – knows how to do the real work
class Light {
    public void turnOn() {
        System.out.println("Light is ON");
    }

    public void turnOff() {
        System.out.println("Light is OFF");
    }
}

class Fan {
    public void start() {
        System.out.println("Fan is spinning");
    }

    public void stop() {
        System.out.println("Fan stopped");
    }
}

// 2. The Command interface
interface Command {
    void execute();     // this is the "order slip"
    void undo();        // optional, for undo support
}

// 3. Concrete Commands (each holds a receiver)
class LightOnCommand implements Command {
    private Light light;

    public LightOnCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.turnOn();
    }

    @Override
    public void undo() {
        light.turnOff();
    }
}

class LightOffCommand implements Command {
    private Light light;

    public LightOffCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.turnOff();
    }

    @Override
    public void undo() {
        light.turnOn();
    }
}

class FanStartCommand implements Command {
    private Fan fan;

    public FanStartCommand(Fan fan) {
        this.fan = fan;
    }

    @Override
    public void execute() {
        fan.start();
    }

    @Override
    public void undo() {
        fan.stop();
    }
}

// A "do nothing" command (useful for initializing buttons)
class NoCommand implements Command {
    @Override public void execute() { }
    @Override public void undo() { }
}

// 4. The Invoker – the remote control
class RemoteControl {
    private Command[] onCommands = new Command[3];
    private Command[] offCommands = new Command[3];
    private Command lastCommand; // for undo

    public RemoteControl() {
        NoCommand noCmd = new NoCommand();
        for (int i = 0; i < 3; i++) {
            onCommands[i] = noCmd;
            offCommands[i] = noCmd;
        }
    }

    // Set which command goes to which button
    public void setCommand(int slot, Command onCmd, Command offCmd) {
        onCommands[slot] = onCmd;
        offCommands[slot] = offCmd;
    }

    public void pressOnButton(int slot) {
        onCommands[slot].execute();
        lastCommand = onCommands[slot];
    }

    public void pressOffButton(int slot) {
        offCommands[slot].execute();
        lastCommand = offCommands[slot];
    }

    public void pressUndoButton() {
        if (lastCommand != null) {
            lastCommand.undo();
        }
    }
}

// 5. Client code – the one who sets everything up
public class CommandPatternDemo {
    public static void main(String[] args) {
        RemoteControl remote = new RemoteControl();

        Light livingRoomLight = new Light();
        Fan ceilingFan = new Fan();

        LightOnCommand lightOn = new LightOnCommand(livingRoomLight);
        LightOffCommand lightOff = new LightOffCommand(livingRoomLight);
        FanStartCommand fanOn = new FanStartCommand(ceilingFan);

        // Assign buttons
        remote.setCommand(0, lightOn, lightOff);
        remote.setCommand(1, fanOn, new NoCommand());

        // Use the remote!
        remote.pressOnButton(0);   // Light is ON
        remote.pressOffButton(0);  // Light is OFF
        remote.pressOnButton(1);   // Fan is spinning

        remote.pressUndoButton();  // Fan stopped (undo last command)
    }
}
```

### Output
```
Light is ON
Light is OFF
Fan is spinning
Fan stopped
```

### Why this is awesome

- The **remote (Invoker)** has no idea what a Light or Fan is. It only knows "Command".
- You can add new devices/commands without touching the remote.
- Undo/redo is super easy – just call `undo()` on the command object.
- You can store commands in a list for macros, logging, queueing, etc.

That’s the Command pattern in plain English:  
**Turn a request into an object so you can pass it around, store it, and even undo it later.**
