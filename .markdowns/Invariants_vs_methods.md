# Beyond the Verb: Understanding Rules vs. Methods in Domain-Driven Design

In the world of software design, particularly Domain-Driven Design (DDD), we often get tangled up in the mechanics of our code. We obsess over method names, arguments, and return types. But there is a fundamental concept that sits above the code, a concept that defines the integrity of our software: the **Invariant**.

There is a common confusion I see often: mistaking a **method** (something an object can do) for a **rule** (a constraint on what that object is allowed to be).

Let’s unpack the statement: _"A rule is a constraint on how state is allowed to change over time."_

## The Capability vs. The Constitution

To understand the difference, we have to look at the "verbs" of our system versus its "laws."

### The Method: The Capability

A method represents a capability. It is a verb. It describes an action that _can_ be requested. In the real world, think of this as the steering wheel of a car. You have the capability to turn the wheel left, right, or keep it straight. You have the capability to press the gas pedal.

In a software system, this looks like "Change Email Address" or "Withdraw Funds" or "Add Item to Cart." These are the buttons on the dashboard. They represent the _intent_ to change the state of the system.

### The Invariant: The Constitution

A rule (or invariant) is not an action. It is a fact that must always be true for the system to make sense. It is the constitution that governs the system.

Returning to our car analogy:

- **Method:** Press the gas pedal (The action).
    
- **State Change:** The car accelerates from 0 to 100 mph.
    
- **The Invariant:** The engine RPM cannot exceed the "Red Line" limit.
    

The invariant isn't the act of driving; it is the physical constraint that prevents the engine from exploding while you drive. It doesn't matter how hard you press the pedal (the method); the governor (the rule) ensures the state of the engine remains within a valid range.

## The Element of Time

The definition mentions that a rule constrains how state changes _over time_. This is the most critical part.

Software objects are not static statues; they are living stories. They move from one state to another.

1. **State A (Now):** The system is valid.
    
2. **The Event:** A method is called.
    
3. **State B (Future):** The system is... ?
    

The invariant lives in that transition. It is the gatekeeper. It says, "I will allow the transition from State A to State B _only if_ the new state adheres to the laws of our reality."

If you have a bank account object, the method is "Transfer Money." The invariant is "You cannot spend money you do not have." The rule ensures that as time moves forward and transactions occur, the account never enters an impossible state (negative balance).

## Why This Distinction Matters

When we treat rules as methods, we end up with "Anemic Domain Models." This is where an object is just a bag of loose data, and we rely on external services or UI logic to check the rules.

If you confuse the two, you might allow the "Change Email" action to accept a string of text that has no "@" symbol. You have performed the action (the method), but you have violated the reality of what an email is (the invariant).

By treating rules as constraints on change, we ensure business integrity.

- **Methods** express user intent.
    
- **Invariants** protect business reality.
    

## Summary

Don't confuse the **trigger** with the **safety catch**.

- A **Method** is the trigger. It initiates change. It says, "I want to do X."
    
- A **Rule (Invariant)** is the safety catch. It validates change. It says, "You can only do X if it doesn't break Y."
    

Great Domain-Driven Design isn't just about collecting a list of features (methods) your software provides. It is about deeply understanding the boundaries (invariants) that define what is impossible within your business.