
- **Intent**: Allows an object to alter its behavior when its internal state changes, modeling a finite state machine while avoiding excessive conditional branching (`if-else`, `switch`).

### Participants

1. **Context**
    - Maintains a reference to the current `State` object.
    - Delegates state-specific work to the `State` object.
    - Interacts with states only through the `State` interface.
    - Can change its state at runtime by replacing the current `State` reference.
2. **State (Interface/Abstract Class)**
    - Defines the contract for behaviors that all concrete states must implement.
    - Ensures the context can call state-specific logic in a uniform way.
3. **Concrete States**
    - Provide distinct implementations of the `State` interface, encapsulating behavior for each state.
    - May trigger transitions by instructing the `Context` to switch to another state.
    - Optional **abstract intermediate states** can encapsulate common behavior shared by multiple states.

### Key Points

- Eliminates sprawling conditional logic by distributing behavior across state classes.
- Both **Context** and **Concrete States** can initiate state transitions.
- Makes it easier to add or modify states without altering the context or other states.
- Promotes the **Open/Closed Principle**: new states can be introduced with minimal changes to existing code.
- Commonly used in **UI workflows**, **parsers**, **protocol handlers**, and **game development** (e.g., character states like Idle, Running, Jumping).