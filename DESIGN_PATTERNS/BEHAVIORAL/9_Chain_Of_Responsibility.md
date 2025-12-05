### Chain of Responsibility – Explained in Plain English

Imagine you’re at a restaurant and you ask a waiter for something:

- If the waiter can handle it (e.g., bring water), he does it.
- If not, he passes your request to the manager.
- If the manager can’t, he passes it to the chef or the owner.

Nobody says “not my job” and leaves you hanging – the request travels up the chain until someone can handle it.

That’s exactly what the **Chain of Responsibility** pattern does in code.

It lets you pass a request along a chain of objects.  
Each object decides:  
→ “Can I handle this?”  
   → Yes → handle it and stop.  
   → No → pass it to the next object in the chain.

Very useful for:
- Logging (different levels: DEBUG → INFO → WARN → ERROR)
- Authentication/Authorization (multiple checks)
- Approval workflows (junior → senior → manager)
- HTTP request middlewares, etc.

### Java Example: Simple Approval System for Expense Requests

Let’s say a company approves expense requests like this:

- Team Lead can approve up to $1000  
- Manager can approve up to $5000  
- Director can approve any amount  
- If no one can approve → rejected

```java
// Step 1: The common interface (or abstract class)
abstract class Approver {
    protected Approver next;  // next in chain

    // Set the next handler (builds the chain)
    public Approver setNext(Approver next) {
        this.next = next;
        return next; // allows fluent chaining
    }

    // Each concrete handler must implement this
    abstract void approveRequest(ExpenseRequest request);
}

// Step 2: The request object
class ExpenseRequest {
    private final double amount;
    private final String purpose;

    public ExpenseRequest(double amount, String purpose) {
        this.amount = amount;
        this.purpose = purpose;
    }

    public double getAmount() { return amount; }
    public String getPurpose() { return purpose; }
}

// Step 3: Concrete handlers
class TeamLead extends Approver {
    @Override
    void approveRequest(ExpenseRequest request) {
        if (request.getAmount() <= 1000) {
            System.out.println("Team Lead approved $" + request.getAmount() 
                             + " for " + request.getPurpose());
        } else if (next != null) {
            next.approveRequest(request); // pass to next
        } else {
            System.out.println("Nobody can approve $" + request.getAmount() + ". Rejected.");
        }
    }
}

class Manager extends Approver {
    @Override
    void approveRequest(ExpenseRequest request) {
        if (request.getAmount() <= 5000) {
            System.out.println("Manager approved $" + request.getAmount() 
                             + " for " + request.getPurpose());
        } else if (next != null) {
            next.approveRequest(request);
        } else {
            System.out.println("Nobody can approve $" + request.getAmount() + ". Rejected.");
        }
    }
}

class Director extends Approver {
    @Override
    void approveRequest(ExpenseRequest request) {
        // Director can approve anything
        System.out.println("Director approved $" + request.getAmount() 
                         + " for " + request.getPurpose());
    }
}

// Step 4: Using the chain
public class ChainOfResponsibilityDemo {
    public static void main(String[] args) {
        // Build the chain: TeamLead → Manager → Director
        Approver teamLead = new TeamLead();
        Approver manager   = new Manager();
        Approver director  = new Director();

        teamLead.setNext(manager).setNext(director);
        // or: teamLead.setNext(manager); manager.setNext(director);

        // Test different requests
        teamLead.approveRequest(new ExpenseRequest(800,  "team lunch"));     // TeamLead
        teamLead.approveRequest(new ExpenseRequest(3000, "new monitor"));    // Manager
        teamLead.approveRequest(new ExpenseRequest(10000,"conference trip"));// Director
        teamLead.approveRequest(new ExpenseRequest(50000,"new car"));        // Nobody → rejected
    }
}
```

### Output:
```
Team Lead approved $800.0 for team lunch
Manager approved $3000.0 for new monitor
Director approved $10000.0 for conference trip
Nobody can approve $50000.0. Rejected.
```

### Key Points
- Each handler only knows about the next one (loose coupling).
- You can dynamically change or reorder the chain at runtime.
- A request can be fully handled, partially handled, or rejected.
- Very flexible and follows the Single Responsibility + Open/Closed principles.

That’s Chain of Responsibility in plain English with real, runnable Java code!
