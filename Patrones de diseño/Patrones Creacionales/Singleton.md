Claro, te proporcionaré ejemplos de cada uno de estos patrones de diseño en el contexto de .NET Core. Para cada patrón, mostraré cómo implementarlo con una explicación básica y un ejemplo en C#.

### **1. Singleton**

El patrón Singleton asegura que una clase tenga solo una instancia y proporciona un punto de acceso global a esa instancia.

**Ejemplo en .NET Core:**

En una aplicación ASP.NET Core, puedes utilizar el patrón Singleton para gestionar instancias de servicios que deben ser únicos a lo largo de la aplicación.

**Código:**

```csharp
// Clase Singleton
public class SingletonService
{
    private static SingletonService _instance;
    private static readonly object _lock = new object();

    private SingletonService()
    {
        // Constructor privado
    }

    public static SingletonService Instance
    {
        get
        {
            lock (_lock)
            {
                if (_instance == null)
                {
                    _instance = new SingletonService();
                }
                return _instance;
            }
        }
    }

    public string GetMessage()
    {
        return "Hello from Singleton!";
    }
}
```

**Registro en ASP.NET Core:**

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<SingletonService>(provider => SingletonService.Instance);
}
```

**Uso en un controlador:**

```csharp
public class HomeController : Controller
{
    private readonly SingletonService _singletonService;

    public HomeController(SingletonService singletonService)
    {
        _singletonService = singletonService;
    }

    public IActionResult Index()
    {
        var message = _singletonService.GetMessage();
        return View("Index", model: message);
    }
}
```