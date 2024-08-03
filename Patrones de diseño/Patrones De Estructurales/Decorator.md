### **4. Decorator**

El patrón Decorator permite añadir responsabilidades adicionales a un objeto de manera dinámica. Los decoradores proporcionan una alternativa flexible a la subclase para extender la funcionalidad.

**Ejemplo en .NET Core:**

```csharp
// Componente
public interface IComponent
{
    string Operation();
}

// Componente concreto
public class ConcreteComponent : IComponent
{
    public string Operation()
    {
        return "ConcreteComponent operation";
    }
}

// Decorador abstracto
public abstract class Decorator : IComponent
{
    protected readonly IComponent _component;

    protected Decorator(IComponent component)
    {
        _component = component;
    }

    public abstract string Operation();
}

// Decorador concreto
public class ConcreteDecorator : Decorator
{
    public ConcreteDecorator(IComponent component) : base(component) { }

    public override string Operation()
    {
        return $"ConcreteDecorator({ _component.Operation() })";
    }
}
```

**Uso:**

```csharp
public class DecoratorExample
{
    public static void Run()
    {
        IComponent component = new ConcreteComponent();
        IComponent decorated = new ConcreteDecorator(component);

        Console.WriteLine(component.Operation());      // Output: ConcreteComponent operation
        Console.WriteLine(decorated.Operation());      // Output: ConcreteDecorator(ConcreteComponent operation)
    }
}
```