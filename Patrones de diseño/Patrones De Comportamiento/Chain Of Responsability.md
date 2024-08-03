### **1. Chain of Responsibility**

El patrón Chain of Responsibility permite que un objeto pase una solicitud a lo largo de una cadena de objetos receptores, donde cada receptor decide si maneja la solicitud o la pasa al siguiente en la cadena.

**Ejemplo en .NET Core:**

```csharp
// Manejo de solicitudes
public abstract class Handler
{
    protected Handler _nextHandler;

    public void SetNext(Handler handler)
    {
        _nextHandler = handler;
    }

    public abstract void HandleRequest(int request);
}

// Receptor concreto
public class ConcreteHandlerA : Handler
{
    public override void HandleRequest(int request)
    {
        if (request < 10)
        {
            Console.WriteLine("Handler A handled request " + request);
        }
        else if (_nextHandler != null)
        {
            _nextHandler.HandleRequest(request);
        }
    }
}

public class ConcreteHandlerB : Handler
{
    public override void HandleRequest(int request)
    {
        if (request >= 10)
        {
            Console.WriteLine("Handler B handled request " + request);
        }
        else if (_nextHandler != null)
        {
            _nextHandler.HandleRequest(request);
        }
    }
}
```

**Uso:**

```csharp
public class ChainOfResponsibilityExample
{
    public static void Run()
    {
        Handler handlerA = new ConcreteHandlerA();
        Handler handlerB = new ConcreteHandlerB();
        handlerA.SetNext(handlerB);

        handlerA.HandleRequest(5);  // Handled by Handler A
        handlerA.HandleRequest(15); // Handled by Handler B
    }
}
```
