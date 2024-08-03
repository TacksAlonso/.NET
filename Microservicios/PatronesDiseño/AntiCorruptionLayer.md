El patrón **Anti-Corruption Layer (ACL)** se utiliza para proteger un sistema nuevo de la complejidad y las inconsistencias de un sistema heredado. Este patrón actúa como una capa intermedia que traduce las interacciones entre los dos sistemas, evitando que el nuevo sistema tenga que lidiar directamente con las peculiaridades del sistema legado.

### Implementación del Patrón Anti-Corruption Layer en .NET Core

En este ejemplo, asumimos que tenemos un sistema heredado que gestiona productos y queremos proteger nuestro nuevo sistema de microservicios de la complejidad del sistema heredado.

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

### 3. Crear la Capa de Anti-Corrupción (ACL)

Esta capa se encargará de traducir las interacciones entre el microservicio y el sistema heredado.

**Services/LegacyProductService.cs**:

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Text.Json;
using System.Threading.Tasks;

namespace ProductService.Services
{
    public class LegacyProductService
    {
        private readonly HttpClient _httpClient;

        public LegacyProductService(HttpClient httpClient)
        {
            _httpClient = httpClient;
        }

        public async Task<IEnumerable<LegacyProduct>> GetLegacyProductsAsync()
        {
            var response = await _httpClient.GetAsync("http://legacy-system/api/products");
            response.EnsureSuccessStatusCode();
            var content = await response.Content.ReadAsStreamAsync();
            return await JsonSerializer.DeserializeAsync<IEnumerable<LegacyProduct>>(content);
        }

        public async Task<LegacyProduct> GetLegacyProductAsync(int id)
        {
            var response = await _httpClient.GetAsync($"http://legacy-system/api/products/{id}");
            response.EnsureSuccessStatusCode();
            var content = await response.Content.ReadAsStreamAsync();
            return await JsonSerializer.DeserializeAsync<LegacyProduct>(content);
        }
    }

    public class LegacyProduct
    {
        public int ProductId { get; set; }
        public string ProductName { get; set; }
        public decimal ProductPrice { get; set; }
    }
}
```

**Services/ProductAdapter.cs**:

```csharp
using ProductService.Models;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace ProductService.Services
{
    public class ProductAdapter
    {
        private readonly LegacyProductService _legacyProductService;

        public ProductAdapter(LegacyProductService legacyProductService)
        {
            _legacyProductService = legacyProductService;
        }

        public async Task<IEnumerable<Product>> GetProductsAsync()
        {
            var legacyProducts = await _legacyProductService.GetLegacyProductsAsync();
            return legacyProducts.Select(lp => new Product
            {
                Id = lp.ProductId,
                Name = lp.ProductName,
                Price = lp.ProductPrice
            });
        }

        public async Task<Product> GetProductAsync(int id)
        {
            var legacyProduct = await _legacyProductService.GetLegacyProductAsync(id);
            return new Product
            {
                Id = legacyProduct.ProductId,
                Name = legacyProduct.ProductName,
                Price = legacyProduct.ProductPrice
            };
        }
    }
}
```

### 4. Crear el Controlador en el Microservicio

**Controllers/ProductsController.cs**:

```csharp
using Microsoft.AspNetCore.Mvc;
using ProductService.Models;
using ProductService.Services;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace ProductService.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class ProductsController : ControllerBase
    {
        private readonly ProductAdapter _productAdapter;

        public ProductsController(ProductAdapter productAdapter)
        {
            _productAdapter = productAdapter;
        }

        [HttpGet]
        public async Task<ActionResult<IEnumerable<Product>>> GetProducts()
        {
            var products = await _productAdapter.GetProductsAsync();
            return Ok(products);
        }

        [HttpGet("{id}")]
        public async Task<ActionResult<Product>> GetProduct(int id)
        {
            var product = await _productAdapter.GetProductAsync(id);
            if (product == null)
            {
                return NotFound();
            }
            return Ok(product);
        }
    }
}
```

### 5. Configurar el `Startup.cs`

**Startup.cs**:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using ProductService.Data;
using ProductService.Services;
using System;

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

        services.AddHttpClient<LegacyProductService>(client =>
        {
            client.BaseAddress = new Uri("http://legacy-system/");
        });

        services.AddScoped<ProductAdapter>();
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

### Resumen

En este ejemplo, hemos implementado el patrón Anti-Corruption Layer (ACL) en .NET Core para proteger un nuevo microservicio de la complejidad de un sistema heredado. Creamos una capa de servicio (`LegacyProductService`) que interactúa con el sistema heredado, y un adaptador (`ProductAdapter`) que traduce las respuestas del sistema heredado a los modelos utilizados por el nuevo microservicio. Esta configuración permite que el nuevo microservicio se comunique con el sistema heredado sin estar directamente acoplado a su complejidad, facilitando la transición y la protección del nuevo sistema.