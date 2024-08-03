### **4. Builder**

El patrón Builder permite construir un objeto complejo paso a paso. Separa la construcción de un objeto de su representación, de modo que el mismo proceso de construcción puede crear diferentes representaciones.

**Ejemplo en .NET Core:**

En una aplicación ASP.NET Core, puedes utilizar el patrón Builder para construir configuraciones complejas o entidades.

**Código:**

```csharp
// Producto
public class Car
{
    public string Make { get; set; }
    public string Model { get; set; }
    public int Year { get; set; }
}

// Interfaz del constructor
public interface ICarBuilder
{
    ICarBuilder SetMake(string make);
    ICarBuilder SetModel(string model);
    ICarBuilder SetYear(int year);
    Car Build();
}

// Constructor concreto
public class CarBuilder : ICarBuilder
{
    private Car _car = new Car();

    public ICarBuilder SetMake(string make)
    {
        _car.Make = make;
        return this;
    }

    public ICarBuilder SetModel(string model)
    {
        _car.Model = model;
        return this;
    }

    public ICarBuilder SetYear(int year)
    {
        _car.Year = year;
        return this;
    }

    public Car Build()
    {
        return _car;
    }
}
```

**Registro en ASP.NET Core:**

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<ICarBuilder, CarBuilder>();
}
```

**Uso en un controlador:**

```csharp
public class CarController : Controller
{
    private readonly ICarBuilder _carBuilder;

    public CarController(ICarBuilder carBuilder)
    {
        _carBuilder = carBuilder;
    }

    public IActionResult Index()
    {
        var car = _carBuilder.SetMake("Toyota")
                             .SetModel("Camry")
                             .SetYear(2024)
                             .Build();
        return View("Index", model: car);
    }
}
```