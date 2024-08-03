### **5. Facade**

El patrón Facade proporciona una interfaz unificada para un conjunto de interfaces en un subsistema. El patrón define una interfaz de alto nivel que hace que el subsistema sea más fácil de usar.

**Ejemplo en .NET Core:**

```csharp
// Subsystem A
public class SubsystemA
{
    public void OperationA()
    {
        Console.WriteLine("Subsystem A operation");
    }
}

// Subsystem B
public class SubsystemB
{
    public void OperationB()
    {
        Console.WriteLine("Subsystem B operation");
    }
}

// Facade
public class Facade
{
    private readonly SubsystemA _subsystemA = new SubsystemA();
    private readonly SubsystemB _subsystemB = new SubsystemB();

    public void Operation()
    {
        _subsystemA.OperationA();
        _subsystemB.OperationB();
    }
}
```

**Uso:**

```csharp
public class FacadeExample
{
    public static void Run()
    {
        Facade facade = new Facade();
        facade.Operation();
        // Output:
        // Subsystem A operation
        // Subsystem B operation
    }
}
```