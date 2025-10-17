### Intent

The Command design pattern turns a request into a stand-alone objects that contains all information about the request. This transformation lets one pass requests as a method arguments, delay, or queue a request's execution, and support undoable operations.

### Structure

1. **Command (interface/abstract class)**
     - Declares the `execute()` method (sometimes `undo()` as well.)
2. **ConcreteCommand**
	 - Implements `execute()` by calling methods on a Receiver.
	 - Stores the receiver and any paramaters needed for the request.
3. **Receiver**
     - The actual object that knows how to perform the work.
4. **Invoker**
     - Asks the command to execute. Doesn't know how the action is carried out.
5. **Client**
     - Creates the command and assigns it to the invoker.