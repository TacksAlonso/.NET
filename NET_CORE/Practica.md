¡Claro! Vamos a profundizar en la práctica de crear APIs RESTful con .NET Core, implementar middleware y servicios, y trabajar con Entity Framework Core.

### **1. Crear APIs RESTful con .NET Core**

Para crear una API RESTful en .NET Core, sigue estos pasos:

#### **Paso 1: Crear un Proyecto de API Web**

1. **Crear el Proyecto:**

   Abre una terminal y usa el siguiente comando para crear un nuevo proyecto de API:
   ```bash
   dotnet new webapi -n MyApi
   cd MyApi
   ```

2. **Estructura del Proyecto:**

   El proyecto contendrá una estructura básica con archivos como `Program.cs` y `Controllers/WeatherForecastController.cs`. Aquí es donde puedes agregar tus propios controladores y modelos.

#### **Paso 2: Definir Modelos**

Define las clases que representarán los datos en tu API. Crea una carpeta `Models` y añade una clase, por ejemplo, `Product.cs`:

```csharp
namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public decimal Price { get; set; }
    }
}
```

#### **Paso 3: Crear un Controlador**

Crea un controlador para manejar las solicitudes HTTP. Añade una nueva clase en la carpeta `Controllers`, por ejemplo, `ProductsController.cs`:

```csharp
using Microsoft.AspNetCore.Mvc;
using MyApi.Models;
using System.Collections.Generic;
using System.Linq;

namespace MyApi.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class ProductsController : ControllerBase
    {
        private static readonly List<Product> Products = new List<Product>
        {
            new Product { Id = 1, Name = "Laptop", Price = 999.99M },
            new Product { Id = 2, Name = "Smartphone", Price = 699.99M }
        };

        [HttpGet]
        public IActionResult Get()
        {
            return Ok(Products);
        }

        [HttpGet("{id}")]
        public IActionResult Get(int id)
        {
            var product = Products.FirstOrDefault(p => p.Id == id);
            if (product == null)
                return NotFound();
            return Ok(product);
        }

        [HttpPost]
        public IActionResult Post([FromBody] Product product)
        {
            product.Id = Products.Max(p => p.Id) + 1;
            Products.Add(product);
            return CreatedAtAction(nameof(Get), new { id = product.Id }, product);
        }
    }
}
```

#### **Paso 4: Configurar Servicios y Middleware**

En el archivo `Program.cs` (o `Startup.cs`), configura los servicios y el middleware necesarios para tu API.

```csharp
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllers();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}

app.UseRouting();
app.UseAuthorization();

app.MapControllers();

app.Run();
```

### **2. Implementar Middleware y Servicios**

#### **Middleware**

El middleware en .NET Core permite procesar solicitudes y respuestas en una cadena de componentes. Puedes crear middleware personalizado para agregar funcionalidad global, como el manejo de errores o la autorización.

**Ejemplo de Middleware Personalizado:**

1. **Crear Middleware:**

   Crea una nueva clase para el middleware, por ejemplo, `RequestLoggingMiddleware.cs`:

   ```csharp
   public class RequestLoggingMiddleware
   {
       private readonly RequestDelegate _next;

       public RequestLoggingMiddleware(RequestDelegate next)
       {
           _next = next;
       }

       public async Task InvokeAsync(HttpContext context)
       {
           Console.WriteLine($"Request: {context.Request.Method} {context.Request.Path}");
           await _next(context);
       }
   }
   ```

2. **Registrar Middleware:**

   En `Program.cs` o `Startup.cs`, añade el middleware a la tubería de solicitudes:

   ```csharp
   app.UseMiddleware<RequestLoggingMiddleware>();
   ```

#### **Servicios**

Los servicios en .NET Core son clases que encapsulan la lógica de negocio y la interacción con recursos externos. Puedes inyectar estos servicios en tus controladores y otros servicios.

**Ejemplo de Servicio:**

1. **Crear Servicio:**

   Crea una interfaz y una implementación, por ejemplo, `IProductService.cs` y `ProductService.cs`:

   ```csharp
   public interface IProductService
   {
       IEnumerable<Product> GetProducts();
       Product GetProductById(int id);
       void AddProduct(Product product);
   }
   ```

   ```csharp
   public class ProductService : IProductService
   {
       private readonly List<Product> _products = new List<Product>();

       public IEnumerable<Product> GetProducts()
       {
           return _products;
       }

       public Product GetProductById(int id)
       {
           return _products.FirstOrDefault(p => p.Id == id);
       }

       public void AddProduct(Product product)
       {
           product.Id = _products.Count + 1;
           _products.Add(product);
       }
   }
   ```

2. **Registrar Servicio:**

   En `Program.cs` o `Startup.cs`, registra el servicio en el contenedor de dependencias:

   ```csharp
   builder.Services.AddSingleton<IProductService, ProductService>();
   ```

   Luego, inyecta el servicio en el controlador:

   ```csharp
   public class ProductsController : ControllerBase
   {
       private readonly IProductService _productService;

       public ProductsController(IProductService productService)
       {
           _productService = productService;
       }

       // Actions that use _productService
   }
   ```

### **3. Trabajar con Entity Framework Core**

Entity Framework Core (EF Core) es un ORM que facilita el acceso a bases de datos en aplicaciones .NET.

#### **Paso 1: Instalar Paquetes**

Instala los paquetes necesarios para EF Core. Puedes usar NuGet para agregar `Microsoft.EntityFrameworkCore` y el proveedor de base de datos que necesitas, como `Microsoft.EntityFrameworkCore.SqlServer`:

```bash
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

#### **Paso 2: Configurar el DbContext**

Define el contexto de base de datos que representa la sesión con la base de datos. Crea una clase, por ejemplo, `MyDbContext.cs`:

```csharp
using Microsoft.EntityFrameworkCore;
using MyApi.Models;

namespace MyApi.Data
{
    public class MyDbContext : DbContext
    {
        public MyDbContext(DbContextOptions<MyDbContext> options)
            : base(options)
        {
        }

        public DbSet<Product> Products { get; set; }
    }
}
```

#### **Paso 3: Configurar la Conexión a la Base de Datos**

En `Program.cs` o `Startup.cs`, configura el contexto de base de datos y la cadena de conexión:

```csharp
builder.Services.AddDbContext<MyDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
```

#### **Paso 4: Crear y Aplicar Migraciones**

Las migraciones permiten aplicar cambios al esquema de la base de datos.

1. **Crear una Migración:**

   Ejecuta el siguiente comando para crear una migración basada en los cambios en tu modelo:

   ```bash
   dotnet ef migrations add InitialCreate
   ```

2. **Actualizar la Base de Datos:**

   Aplica las migraciones a la base de datos:

   ```bash
   dotnet ef database update
   ```

Con estos pasos, deberías tener una comprensión sólida de cómo crear APIs RESTful, implementar middleware y servicios, y trabajar con Entity Framework Core en .NET Core. Si necesitas más detalles o tienes preguntas adicionales, no dudes en preguntar. ¡Buena suerte con tu preparación!