### **9. Strategy**

El patrón Strategy define una familia de algoritmos, encapsula cada uno y hace que sean intercambiables. Strategy permite que el algoritmo varíe independientemente de los clientes que lo usan.

**Ejemplo en .NET Core:**

```csharp
// Estrategia
public interface IStrategy
{
    void Execute();
}

// Estrategias concretas
public class ConcreteStrategyA : IStrategy
{
    public void Execute()
    {
        Console.WriteLine("Strategy A executed");
    }
}

public class ConcreteStrategyB : IStrategy
{
    public void Execute()
    {
        Console.WriteLine("Strategy B executed");
    }
}

// Contexto
public class Context
{
    private IStrategy _strategy;

    public void SetStrategy(IStrategy strategy)
    {
        _strategy = strategy;
    }

    public void ExecuteStrategy()
    {
        _strategy.Execute();
    }
}
```

**Uso:**

```csharp
public class StrategyExample
{
    public static void Run()
    {
        Context context = new Context();

        context.SetStrategy(new ConcreteStrategyA());
        context.ExecuteStrategy();  // Strategy A executed

        context.SetStrategy(new ConcreteStrategyB());
        context.ExecuteStrategy();  // Strategy B executed
    }
}
```