### **7. Proxy**

El patrón Proxy proporciona un sustituto o representante de otro objeto para controlar el acceso a este. Los proxies pueden realizar tareas como control de acceso o carga diferida.

**Ejemplo en .NET Core:**

```csharp
// Sujetos
public interface ISubject
{
    void Request();
}

// Sujeto real
public class RealSubject : ISubject
{
    public void Request()
    {
        Console.WriteLine("RealSubject request");
    }
}

// Proxy
public class Proxy : ISubject
{
    private readonly RealSubject _realSubject;

    public Proxy(RealSubject realSubject)
    {
        _realSubject = realSubject;
    }

    public void Request()
    {
        Console.WriteLine("Proxy handling");
        _realSubject.Request();
    }
}
```

**Uso:**

```csharp
public class ProxyExample
{
    public static void Run()
    {
        RealSubject realSubject = new RealSubject();
        ISubject proxy = new Proxy(realSubject);

        proxy.Request();
        // Output:
        // Proxy handling
        // RealSubject request
    }
}
```