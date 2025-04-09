### What's formal verification?

I recently learned about software formal verification tools like [TLA+](https://lamport.azurewebsites.net/tla/tla.html) and [Alloy](https://alloytools.org/applications.html). These tools are magical - they let you model your distributed system as a state machine and verify that conditions you define, known as `invariants` are satisfied in all possible states.

Questions like:

```
What if our database write fails here?
What if we can't get the lock on this shared resource?
What if I get hit by a bus on the way to work tomorrow?
What if this asynchronous operation finishes before another one?
```

and more can be defined by modelling and verifying your software's operations exhaustively. 

### Ok cool, what's a model and how do I make one?

Tools like Alloy require a model. A model is some representation of your system's states, data, state transitions, and invariants. Let's start with a simple example and eventually use Alloy to verify it. We prefer to use Alloy here to demonstrate a higher-level use-case. TLA+ is much more verbose, based on its origins in mathematics.

Say that your side-hustle is selling personalized knitted sweaters. You make a website where anyone can place an order. You write some code to automate the checkout process. You decide that when you receive an order, you should *first* charge the customer, and then subtract from your inventory to account for the order.



