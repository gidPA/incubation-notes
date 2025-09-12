
- **Intent**: Defines a one-to-many dependency between objects so that when one object (the _publisher_) changes state, all its dependents (_subscribers_) are automatically notified and updated.

### Participants

- **Publisher (Subject)**
    - Maintains a collection of subscribers.
    - Provides methods to manage subscriptions:
        - `Subscribe()` → adds a subscriber.
        - `Unsubscribe()` → removes a subscriber.
    - Provides a `NotifySubscribers()` method to call the update function on all registered subscribers.
- **Subscriber (Observer)**
    - Declares a common interface (e.g., `Update()`), ensuring that all subscribers can be notified in a consistent way.
    - Concrete subscribers can be very diverse, but they must implement this interface.

### Key Points

- The publisher is _decoupled_ from concrete subscriber implementations—it only depends on their shared interface.
- Multiple subscribers can react differently to the same notification.
- This pattern promotes **loose coupling** and is especially useful for event-driven systems or GUIs.
- Often implemented with a **push** model (publisher sends relevant data to subscribers) or a **pull** model (subscribers query the publisher for data after being notified).