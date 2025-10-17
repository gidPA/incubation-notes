### Intent

The **Mediator design pattern** is a **behavioral pattern** that defines an object (the _mediator_) to **encapsulate communication between a set of objects**, so that they don’t communicate with each other directly.

Instead of objects referring to and calling each other, they go through the mediator. This reduces coupling and centralizes control over interactions.

### Key Idea

- Without a mediator, objects often form a **"spaghetti mesh"** of direct references and dependencies.
- With a mediator, objects interact indirectly via a central hub -> **looser coupling** and easier maintenance.

### Structure

1. **Mediator (interface/abstract class)**
	Defines the contract for communication between _colleagues_.
2. **Concrete Mediator**  
	Implements the mediator interface, coordinates communication logic, and holds references to _colleague_ objects.
3. **Colleague classes**  
	Individual objects that need to interact. Instead of calling each other directly, they call the mediator.