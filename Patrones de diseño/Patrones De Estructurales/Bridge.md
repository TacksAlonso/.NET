### **2. Bridge**

El patrón Bridge separa una abstracción de su implementación para que las dos puedan variar independientemente.

**Ejemplo en .NET Core:**

```csharp
// Implementación abstracta
public interface IImplementor
{
    void OperationImpl();
}

// Implementaciones concretas
public class ConcreteImplementorA : IImplementor
{
    public void OperationImpl()
    {
        Console.WriteLine("ConcreteImplementorA operation");
    }
}

public class ConcreteImplementorB : IImplementor
{
    public void OperationImpl()
    {
        Console.WriteLine("ConcreteImplementorB operation");
    }
}

// Abstracción
public abstract class Abstraction
{
    protected readonly IImplementor _implementor;

    protected Abstraction(IImplementor implementor)
    {
        _implementor = implementor;
    }

    public abstract void Operation();
}

// Abstracción refinada
public class RefinedAbstraction : Abstraction
{
    public RefinedAbstraction(IImplementor implementor) : base(implementor) { }

    public override void Operation()
    {
        _implementor.OperationImpl();
    }
}
```

**Uso:**

```csharp
public class BridgeExample
{
    public static void Run()
    {
        IImplementor implementorA = new ConcreteImplementorA();
        Abstraction abstractionA = new RefinedAbstraction(implementorA);
        abstractionA.Operation(); // Output: ConcreteImplementorA operation

        IImplementor implementorB = new ConcreteImplementorB();
        Abstraction abstractionB = new RefinedAbstraction(implementorB);
        abstractionB.Operation(); // Output: ConcreteImplementorB operation
    }
}
```