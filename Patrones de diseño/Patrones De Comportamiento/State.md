### **8. State**

El patrón State permite a un objeto alterar su comportamiento cuando su estado interno cambia. El objeto parecerá cambiar su clase.

**Ejemplo en .NET Core:**

```csharp
// Estado
public interface IState
{
    void Handle();
}

// Estados concretos
public class ConcreteStateA : IState
{
    public void Handle()
    {
        Console.WriteLine("Handling request in State A");
    }
}

public class ConcreteStateB : IState
{
    public void Handle()
    {
        Console.WriteLine("Handling request in State B");
    }
}

// Contexto
public class Context
{
    private IState _state;

    public void SetState(IState state)
    {
        _state = state;
    }

    public void Request()
    {
        _state.Handle();
    }
}
```

**Uso:**

```csharp
public class StateExample
{
    public static void Run()
    {
        Context context = new Context();

        context.SetState(new ConcreteStateA());
        context.Request();  // Handling request in State A

        context.SetState(new ConcreteStateB());
        context.Request();  // Handling request in State B
    }
}
```