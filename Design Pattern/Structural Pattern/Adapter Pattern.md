### Intent

Provides a unified interface to work with classes or services that have similar purposes but incompatible interfaces.
    - Example: Accessing different data sources (e.g., JSON, XML, CSV) through a common interface for reading and storing data.

### Participants

- **Client Interface (Target)**
    - Defines the expected set of methods (e.g., `Read()`, `Store()`), representing how the client wants to interact with the service.
- **Adapter**
    - Implements the `ClientInterface`.
    - Translates calls from the client into the appropriate calls for the adapted `Service`.
    - Acts as a wrapper so the client can use the service seamlessly, without modifying either the client or the service.
- **Service (Adaptee)**
    - The existing useful class with a concrete implementation (often third-party or legacy code).
    - Provides the actual functionality, but through an interface that is incompatible with the client.
    - Different services (e.g., `JsonService`, `XmlService`) may require different adapters.

### Key Points

- The adapter enables **reusability** of existing services without altering their code.
- Promotes **loose coupling** between client and service.
- Often used when integrating **legacy systems** or **third-party libraries** into a unified workflow.
- Two main types:
    - **Class Adapter** (uses inheritance).
    - **Object Adapter** (uses composition, more common in modern OOP).