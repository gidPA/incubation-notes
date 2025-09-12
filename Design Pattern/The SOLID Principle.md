SOLID is a mnemonic acronym for five design principles intended to make object-oriented designs more understandable, flexible, and maintainable.

This principle was introduced by Robert C. Martin from his 2000 paper *Design Principles and Design Pattern*.

They are not rigid rules but **guidelines** to help structure software in a way that reduces coupling, increases cohesion, and makes systems easier to evolve over time.

## Principles
#### **Single-Responsibility Principle (SRP)**

> *There should never be more than one reason for a class to change.*

- A class should **encapsulate only one responsibility** or concern in the system.
- This prevents a class from becoming a "god class" that tries to do too many things at once.
- Benefits:
    - **Simpler maintenance** – changes in one responsibility do not accidentally affect others.
    - **Better testability** – focused classes are easier to unit test.
    - **Improved readability** – intent of the class is clearer.

**Example:** Instead of one `Report` class handling report generation _and_ printing, create `ReportGenerator` and `ReportPrinter` separately.
#### **Open-Closed Principle (OCP)** 

  > *Software entities should be open for extension but closed for modification.*

- Classes, modules, and functions should be designed so they can gain **new behavior without altering existing code**.
- Achieved via:
    - **Inheritance / polymorphism** (e.g., extending a base class).
    - **Composition** and **dependency injection** (passing in new strategies or behaviors).
- Prevents introducing bugs in tested code when adding new features.

**Example:** Instead of modifying a `Shape` class every time one adds a new shape, define an abstract `Shape` with a `draw()` method, and let each new shape subclass implement it.
#### **Liskov Substitution Principle (LSP)**

> *Subtypes must be substitutable for their base types.*

- If `S` is a subtype of `T`, then objects of type `T` should be replaceable with objects of type `S` **without breaking correctness**.
- This ensures **polymorphism works safely**.
- Violations usually occur when a subclass changes expected behavior of the parent in ways that break assumptions.

**Example (violation):**  
If a `Square` class extends `Rectangle` but changes how `setWidth()` or `setHeight()` behave, it may break code expecting a normal rectangle.
#### **Interface Segregation Principle (ISP)**

> *Clients should not be forced to depend upon interfaces they do not use.*

- It's better to have **smaller, role-specific interfaces** than one large "fat" interface.
- Clients should only know about the methods they actually need.
- Reduces unnecessary coupling and makes code easier to implement and test.

**Example:**  
Instead of one big `IMachine` interface with `print()`, `scan()`, and `fax()`, split into `IPrinter`, `IScanner`, and `IFax`. A simple printer class shouldn’t be forced to implement unused methods.
#### **Dependency Inversion Principle (DIP)**

> *Depend upon Abstractions. Do not depend upon concretions.*

- High-level modules (business rules) should not depend on low-level modules (details). Both should depend on **abstractions**.
- This makes it possible to swap out implementations without changing higher-level logic.
- Often implemented using **interfaces, abstract classes, and dependency injection**.

**Example:**  
Instead of `OrderService` directly creating a `MySQLDatabase`, it should depend on an `IDatabase` interface. This allows replacing `MySQLDatabase` with `PostgreSQLDatabase` or even an in-memory fake for testing.


In conclusion, the **SOLID** principle helps in building systems that are:

- **Easier to change** without breaking existing behavior (OCP, DIP).
- **Easier to understand** through separation of concerns (SRP, ISP).
- **Safe to extend** through correct use of polymorphism (LSP).