### **3. Abstract Factory**

El patrón Abstract Factory proporciona una interfaz para crear familias de objetos relacionados o dependientes sin especificar sus clases concretas.

**Ejemplo en .NET Core:**

Puedes utilizar el patrón Abstract Factory para crear diferentes tipos de servicios dependiendo del entorno o configuración.

**Código:**

```csharp
// Interfaces de productos
public interface IButton
{
    string Render();
}

public interface ICheckbox
{
    string Render();
}

// Productos concretos
public class WindowsButton : IButton
{
    public string Render() => "Windows Button";
}

public class MacButton : IButton
{
    public string Render() => "Mac Button";
}

public class WindowsCheckbox : ICheckbox
{
    public string Render() => "Windows Checkbox";
}

public class MacCheckbox : ICheckbox
{
    public string Render() => "Mac Checkbox";
}

// Interfaz de la fábrica abstracta
public interface IGUIFactory
{
    IButton CreateButton();
    ICheckbox CreateCheckbox();
}

// Fábricas concretas
public class WindowsFactory : IGUIFactory
{
    public IButton CreateButton() => new WindowsButton();
    public ICheckbox CreateCheckbox() => new WindowsCheckbox();
}

public class MacFactory : IGUIFactory
{
    public IButton CreateButton() => new MacButton();
    public ICheckbox CreateCheckbox() => new MacCheckbox();
}
```

**Registro en ASP.NET Core:**

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<IGUIFactory, WindowsFactory>(); // O MacFactory
}
```

**Uso en un controlador:**

```csharp
public class GUIController : Controller
{
    private readonly IGUIFactory _guiFactory;

    public GUIController(IGUIFactory guiFactory)
    {
        _guiFactory = guiFactory;
    }

    public IActionResult Index()
    {
        var button = _guiFactory.CreateButton();
        var checkbox = _guiFactory.CreateCheckbox();
        return View("Index", model: new { Button = button.Render(), Checkbox = checkbox.Render() });
    }
}
```
