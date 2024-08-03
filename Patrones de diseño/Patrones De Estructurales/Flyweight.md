### **6. Flyweight**

El patrón Flyweight utiliza el compartimiento de objetos para soportar grandes cantidades de objetos de manera eficiente. Los objetos compartidos se pueden reutilizar en lugar de crear nuevos objetos.

**Ejemplo en .NET Core:**

```csharp
// Flyweight
public interface IFlyweight
{
    void Operation(string extrinsicState);
}

// Flyweight concreto
public class ConcreteFlyweight : IFlyweight
{
    public void Operation(string extrinsicState)
    {
        Console.WriteLine($"ConcreteFlyweight: {extrinsicState}");
    }
}

// Fábrica de Flyweights
public class FlyweightFactory
{
    private readonly Dictionary<string, IFlyweight> _flyweights = new Dictionary<string, IFlyweight>();

    public IFlyweight GetFlyweight(string key)
    {
        if (!_flyweights.ContainsKey(key))
        {
            _flyweights[key] = new ConcreteFlyweight();
        }
        return _flyweights[key];
    }
}
```

**Uso:**

```csharp
public class FlyweightExample
{
    public static void Run()
    {
        FlyweightFactory factory = new FlyweightFactory();
        IFlyweight flyweight1 = factory.GetFlyweight("A");
        IFlyweight flyweight2 = factory.GetFlyweight("A");

        flyweight1.Operation("Operation 1");
        flyweight2.Operation("Operation 2");
        // Output:
        // ConcreteFlyweight: Operation 1
        // ConcreteFlyweight: Operation 2
    }
}
```