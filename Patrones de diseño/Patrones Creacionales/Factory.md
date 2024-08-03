
### **2. Factory Method**

El patrón Factory Method define una interfaz para crear un objeto, pero permite a las subclases alterar el tipo de objeto que se va a crear.

**Ejemplo en .NET Core:**

En una aplicación ASP.NET Core, puedes utilizar el patrón Factory Method para crear instancias de servicios basados en diferentes configuraciones.

**Código:**

```csharp
// Interfaz del producto
public interface IProduct
{
    string GetName();
}

// Productos concretos
public class ProductA : IProduct
{
    public string GetName() => "Product A";
}

public class ProductB : IProduct
{
    public string GetName() => "Product B";
}

// Interfaz de la fábrica
public interface IProductFactory
{
    IProduct CreateProduct();
}

// Fábricas concretas
public class ProductAFactory : IProductFactory
{
    public IProduct CreateProduct() => new ProductA();
}

public class ProductBFactory : IProductFactory
{
    public IProduct CreateProduct() => new ProductB();
}
```

**Registro en ASP.NET Core:**

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<IProductFactory, ProductAFactory>();
    // Puedes cambiar a ProductBFactory para obtener un producto diferente
}
```

**Uso en un controlador:**

```csharp
public class ProductController : Controller
{
    private readonly IProductFactory _productFactory;

    public ProductController(IProductFactory productFactory)
    {
        _productFactory = productFactory;
    }

    public IActionResult Index()
    {
        var product = _productFactory.CreateProduct();
        return View("Index", model: product.GetName());
    }
}
```