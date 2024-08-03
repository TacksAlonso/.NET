El patrón **Data Duplication** implica replicar datos entre diferentes servicios o bases de datos para mejorar la disponibilidad, el rendimiento y la resiliencia. En el contexto de microservicios, este patrón se usa para garantizar que cada servicio tenga acceso rápido y confiable a los datos que necesita, reduciendo la dependencia de servicios externos.

### Implementación del Patrón Data Duplication en .NET Core

### 1. Crear un Nuevo Proyecto de API Web

```bash
dotnet new webapi -n DataDuplicationExample
cd DataDuplicationExample
```

### 2. Configurar las Dependencias de EF Core

```bash
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### 3. Definir los Modelos y el Contexto de Datos

**Models/Product.cs**:

```csharp
namespace DataDuplicationExample.Models
{
    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public decimal Price { get; set; }
    }
}
```

**Models/ProductDto.cs**:

```csharp
namespace DataDuplicationExample.Models
{
    public class ProductDto
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public decimal Price { get; set; }
    }
}
```

**Data/ApplicationDbContext.cs**:

```csharp
using Microsoft.EntityFrameworkCore;
using DataDuplicationExample.Models;

namespace DataDuplicationExample.Data
{
    public class ApplicationDbContext : DbContext
    {
        public DbSet<Product> Products { get; set; }

        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options) { }
    }
}
```

### 4. Crear el Servicio para Manejar la Duplicación de Datos

**Services/ProductService.cs**:

```csharp
using DataDuplicationExample.Data;
using DataDuplicationExample.Models;
using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace DataDuplicationExample.Services
{
    public class ProductService
    {
        private readonly ApplicationDbContext _context;

        public ProductService(ApplicationDbContext context)
        {
            _context = context;
        }

        public async Task<IEnumerable<ProductDto>> GetAllProductsAsync()
        {
            return await _context.Products
                .Select(p => new ProductDto { Id = p.Id, Name = p.Name, Price = p.Price })
                .ToListAsync();
        }

        public async Task DuplicateDataAsync(IEnumerable<ProductDto> products)
        {
            foreach (var productDto in products)
            {
                var existingProduct = await _context.Products
                    .FirstOrDefaultAsync(p => p.Id == productDto.Id);

                if (existingProduct == null)
                {
                    var product = new Product
                    {
                        Id = productDto.Id,
                        Name = productDto.Name,
                        Price = productDto.Price
                    };
                    _context.Products.Add(product);
                }
                else
                {
                    existingProduct.Name = productDto.Name;
                    existingProduct.Price = productDto.Price;
                }
            }
            await _context.SaveChangesAsync();
        }
    }
}
```

### 5. Crear el Controlador para Exponer las Operaciones

**Controllers/ProductController.cs**:

```csharp
using DataDuplicationExample.Models;
using DataDuplicationExample.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace DataDuplicationExample.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class ProductController : ControllerBase
    {
        private readonly ProductService _productService;

        public ProductController(ProductService productService)
        {
            _productService = productService;
        }

        [HttpGet]
        public async Task<IEnumerable<ProductDto>> GetProducts()
        {
            return await _productService.GetAllProductsAsync();
        }

        [HttpPost("duplicate")]
        public async Task<IActionResult> DuplicateData([FromBody] IEnumerable<ProductDto> products)
        {
            await _productService.DuplicateDataAsync(products);
            return Ok();
        }
    }
}
```

### 6. Configurar `Startup.cs`

**Startup.cs**:

```csharp
using DataDuplicationExample.Data;
using DataDuplicationExample.Services;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

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

        services.AddDbContext<ApplicationDbContext>(options =>
            options.UseInMemoryDatabase("ProductDb"));

        services.AddScoped<ProductService>();
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

### 7. Probar la API

1. **Iniciar el Proyecto**:

   ```bash
   dotnet run
   ```

2. **Probar los Endpoints**:

   - GET `/product`: Obtiene todos los productos.
   - POST `/product/duplicate`: Duplica los datos de productos.

### Resumen

En este ejemplo, hemos implementado el patrón Data Duplication en .NET Core. Creamos un servicio `ProductService` que se encarga de duplicar los datos recibidos en una base de datos en memoria. El controlador `ProductController` expone los endpoints necesarios para obtener productos y duplicar datos. Esta configuración permite replicar datos de manera eficiente, mejorando la disponibilidad y el rendimiento del sistema.