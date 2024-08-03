### **5. Prototype**

El patrón Prototype permite copiar objetos existentes sin hacer su clase depender de sus hijos. Utiliza un prototipo que define cómo copiarse a sí mismo.

**Ejemplo en .NET Core:**

En una aplicación ASP.NET Core, puedes usar el patrón Prototype para clonar objetos.

**Código:**

```csharp
// Prototipo
public abstract class Prototype
{
    public abstract Prototype Clone();
}

// Implementación concreta
public class ConcretePrototype : Prototype
{
    public string Data { get; set; }

    public ConcretePrototype(string data)
    {
        Data = data;
    }

    public override Prototype Clone()
    {
        return new ConcretePrototype(this.Data);
    }
}
```

**Registro en ASP.NET Core:**

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<ConcretePrototype>();
}
```

**Uso en un controlador:**

```csharp
public class PrototypeController : Controller
{
    private readonly ConcretePrototype _prototype;

    public PrototypeController(ConcretePrototype prototype)
    {
        _prototype = prototype;
    }

    public IActionResult Index()
    {
        var clonedPrototype = _prototype.Clone() as ConcretePrototype;
        return View("Index", model: clonedPrototype.Data);
    }
}
```