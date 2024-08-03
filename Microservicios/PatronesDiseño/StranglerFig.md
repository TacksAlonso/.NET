El patrón **Strangler Fig** se utiliza para refactorizar o reescribir partes de un sistema heredado (monolítico) en nuevos servicios (microservicios), migrando de manera incremental sin interrumpir el sistema en funcionamiento. La idea es que el nuevo sistema "estrangule" gradualmente al antiguo hasta que este último pueda ser eliminado.

### Implementación del Patrón Strangler Fig en .NET Core

En este ejemplo, asumimos que tenemos una aplicación monolítica que gestiona productos. Vamos a migrar una parte de la funcionalidad de productos a un nuevo microservicio utilizando el patrón Strangler Fig. La funcionalidad que vamos a migrar es la obtención de detalles del producto.

### 1. Crear el Proyecto del Microservicio

1. **Crear un Nuevo Proyecto**:

   ```bash
   dotnet new webapi -n ProductService
   cd ProductService
   ```

2. **Agregar Paquetes Necesarios**:

   ```bash
   dotnet add package Microsoft.EntityFrameworkCore
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

### 2. Configurar el DbContext y el Modelo en el Microservicio

**Models/Product.cs**:

```csharp
namespace ProductService.Models
{
    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public decimal Price { get; set; }
    }
}
```

**Data/ProductContext.cs**:

```csharp
using Microsoft.EntityFrameworkCore;
using ProductService.Models;

namespace ProductService.Data
{
    public class ProductContext : DbContext
    {
        public DbSet<Product> Products { get; set; }

        public ProductContext(DbContextOptions<ProductContext> options) : base(options) { }
    }
}
```

### 3. Crear el Controlador en el Microservicio

**Controllers/ProductsController.cs**:

```csharp
using Microsoft.AspNetCore.Mvc;
using ProductService.Data;
using ProductService.Models;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace ProductService.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class ProductsController : ControllerBase
    {
        private readonly ProductContext _context;

        public ProductsController(ProductContext context)
        {
            _context = context;
        }

        [HttpGet]
        public async Task<ActionResult<IEnumerable<Product>>> GetProducts()
        {
            return await _context.Products.ToListAsync();
        }

        [HttpGet("{id}")]
        public async Task<ActionResult<Product>> GetProduct(int id)
        {
            var product = await _context.Products.FindAsync(id);
            if (product == null)
            {
                return NotFound();
            }
            return product;
        }
    }
}
```

### 4. Configurar el `Startup.cs`

**Startup.cs**:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using ProductService.Data;

public class Startup
{
    public Startup(IConfiguration configuration)
    {
        Configuration = configuration;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();
        services.AddDbContext<ProductContext>(options =>
            options.UseInMemoryDatabase("ProductDb"));
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
}
```

### 5. Actualizar el Proyecto Monolítico para Usar el Microservicio

Supongamos que tenemos un proyecto monolítico llamado `LegacyProductApp`. Necesitamos actualizar este proyecto para redirigir las solicitudes de obtención de detalles del producto al nuevo microservicio.

1. **Agregar Paquete de HttpClientFactory**:

   ```bash
   dotnet add package Microsoft.Extensions.Http
   ```

2. **Configurar el HttpClient en el Monolito**:

**Startup.cs** (en el monolito):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();
    services.AddHttpClient("ProductService", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000/"); // URL del microservicio
    });
}
```

3. **Actualizar el Controlador en el Monolito**:

**Controllers/ProductsController.cs** (en el monolito):

```csharp
using Microsoft.AspNetCore.Mvc;
using System.Net.Http;
using System.Text.Json;
using System.Threading.Tasks;

namespace LegacyProductApp.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class ProductsController : ControllerBase
    {
        private readonly IHttpClientFactory _clientFactory;

        public ProductsController(IHttpClientFactory clientFactory)
        {
            _clientFactory = clientFactory;
        }

        [HttpGet("{id}")]
        public async Task<IActionResult> GetProduct(int id)
        {
            var client = _clientFactory.CreateClient("ProductService");
            var response = await client.GetAsync($"products/{id}");

            if (response.IsSuccessStatusCode)
            {
                var product = await JsonSerializer.DeserializeAsync<Product>(await response.Content.ReadAsStreamAsync());
                return Ok(product);
            }

            return NotFound();
        }
    }

    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public decimal Price { get; set; }
    }
}
```

### Resumen

En este ejemplo, hemos utilizado el patrón Strangler Fig para migrar la funcionalidad de obtención de detalles de productos desde un monolito hacia un nuevo microservicio en .NET Core. Configuramos un nuevo microservicio para gestionar productos y actualizamos el monolito para redirigir las solicitudes de obtención de detalles de productos a este microservicio. Con el tiempo, otras funcionalidades del monolito pueden migrarse al microservicio hasta que el monolito sea completamente "estrangulado".