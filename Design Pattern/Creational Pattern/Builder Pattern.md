### Intent

Provides a way to construct complex object step-by-step as an alternative to constructing object by constructors. This pattern also allows construction of different types and representation of an object using the same construction code.

The builder pattern avoids the problem of creating a constructor with large amount of parameters (`new House(6, 9, true, 420, true, true, false, ...)`) or creating many constructor overloads to cater to different options. Instead, this can be used:

```
+-----------------------------+
|        HouseBuilder         |
+-----------------------------+
| ...                         |
+-----------------------------+
| + BuildWalls()              |
| + AttachWindows()           |
| + AttachDoors()             |
| + BuildRoof()               |
| + InstallPlumbing()         |
| + InstallElectricalWiring() |
| + GetResult()               |
+-----------------------------+    
```

### Participants

1. **Builder Interface/Abstract Class**
   Declares product construction steps that are common to all types of builders.
2. **Concrete Builders**
   Provide different implementations of the construction steps
3. **Products**
   Resulting complex objects to be constructed and returned by the builder.
4. **Director (optional)**
   Controls the construction order. 
5. **Client**
   Initiates the building process by choosing a builder and (optionally) a director.

### Example in C\#

Suppose one wants to build a `Computer` object that has optional parts like GPU, SSD, etc.

```csharp
// Product
public class Computer
{
    public string CPU { get; set; }
    public string GPU { get; set; }
    public int RAM { get; set; }
    public int Storage { get; set; }

    public override string ToString()
    {
        return $"CPU: {CPU}, GPU: {GPU}, RAM: {RAM}GB, Storage: {Storage}GB";
    }
}

// Builder Interface
public interface IComputerBuilder
{
    IComputerBuilder SetCPU(string cpu);
    IComputerBuilder SetGPU(string gpu);
    IComputerBuilder SetRAM(int ram);
    IComputerBuilder SetStorage(int storage);
    Computer Build();
}

// Concrete Builder
public class ComputerBuilder : IComputerBuilder
{
    private Computer _computer = new Computer();

    public IComputerBuilder SetCPU(string cpu)
    {
        _computer.CPU = cpu;
        return this;
    }

    public IComputerBuilder SetGPU(string gpu)
    {
        _computer.GPU = gpu;
        return this;
    }

    public IComputerBuilder SetRAM(int ram)
    {
        _computer.RAM = ram;
        return this;
    }

    public IComputerBuilder SetStorage(int storage)
    {
        _computer.Storage = storage;
        return this;
    }

    public Computer Build()
    {
        return _computer;
    }
}

// Client
class Program
{
    static void Main()
    {
        var gamingPC = new ComputerBuilder()
            .SetCPU("Intel i9")
            .SetGPU("NVIDIA RTX 4090")
            .SetRAM(32)
            .SetStorage(2000)
            .Build();

        Console.WriteLine(gamingPC);
    }
}
```

### When to Use Builder

- When an object has **many optional parameters**.
- When construction involves a **sequence of steps**.
- When different configurations of the same object are needed (e.g., "gaming PC" vs "office PC").

### Useful Notes About Director

The **Director** is useful when:
- The construction process has to be **standardized** or **reused**.
- You want to encapsulate **recipes** for building products (e.g., "Gaming PC" vs "Office PC").
- You don’t want the client to worry about the order of steps.