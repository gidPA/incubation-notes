
### Intent

The Composite design pattern allows treating individual objects and groups of object (compositions) in the same way. It is useful when working with hierarchical tree structures such as file systems, UI components, or organizational charts.

### Participants

1. **Component (interface or abstract class)**
   - Declares operations that both simple and complex object should support.
   - Example: `Draw()`, `Add(Component c)`, `Remove(Component c)`

2. **Leaf (concrete class)**
   - Represents an individual object in the hierarchy.
   - Implements the component interface directly, but does not have children.
   - Example: A `Line` or `Circle` in a graphics editor.

3. **Composite (concrete class)**
   - Represents a container that can hold children (leaves or other composites).
   - Implements child management (`Add`, `Remove`, `GetChild`) as well as operations defined by the component.
   - Example: A `Group` in a graphics editor.

### Example: the DOM (Document Object Model) in Web Browsers

**1. Component (interface/abstract class)**
  - `Text` nodes (`document.createTextNode("Hello")`).
  - `Comment` nodes.
  - These cannot have children, but still inherit from `Node`.

**2. Composite (can contain children)**
  - Examples:
    - `Element` nodes (`<div>`, `<p>`, etc.).
    - `Document` itself.
  - They implement `appendChild()`, `removeChild()`, and can recursively contain both leaves (like text nodes) and other composites (nested elements).

##### Why DOM is a Composite

- Uniform interface (`Node`) for all types of nodes.    
- One can traverse (`childNodes`, `parentNode`) and manipulate (`appendChild`, `removeChild`) hierarchies recursively.
- Both simple (text) and complex (elements) nodes are treated the same way.