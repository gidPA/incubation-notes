### Intent

Provides an interface for creating objects, but lets subclasses decide which class to instantiate.
- Replaces direct construction (`new`) with calls to a **factory method**.
- Allows one interface (the **Product**) to have multiple implementations (concrete products), all created through a controlled, unified process.
- Overcomes the limitation of constructors by centralizing creation logic and supporting extensibility.

### Participants

1. **Product (Interface/Abstract Class)**
    - Defines the contract (methods/properties) that all concrete products must fulfill.
    - Represents the common “type” expected by the client.
2. **Concrete Products**
    - Implement the `Product` interface.
    - Each concrete class provides a distinct implementation of the product’s functionality.
3. **Creator (Abstract Class/Base Class)**
    - Declares the **factory method** that returns a `Product`.
    - The return type must be the `Product` interface, ensuring loose coupling.
    - Can provide default behavior, but is often made abstract to force subclasses to define specific creation logic.
4. **Concrete Creators**
    - Override the factory method to instantiate and return a particular `ConcreteProduct`.
    - Encapsulate object creation so the client code is isolated from product instantiation details.

### Key Points

- Decouples **product creation** from **product usage**.
- Clients only work with the `Product` interface, never the concrete classes directly.
- Supports the **Open/Closed Principle**: new products can be introduced by adding new concrete creators, without modifying existing client code.
- Often used with frameworks where the library code defines the creator interface, and users extend it to provide application-specific products.