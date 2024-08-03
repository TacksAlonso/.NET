# **CQRS**
El patrón **CQRS (Command Query Responsibility Segregation)** es una arquitectura que separa las operaciones de lectura y escritura en dos modelos diferentes. Esto permite optimizar las consultas de lectura y mejorar el rendimiento de las operaciones de escritura. A continuación, te mostraré cómo implementar CQRS en un proyecto .NET Core utilizando MediatR y Entity Framework Core.

### Ejemplo de Implementación de CQRS en .NET Core

Supongamos que estamos construyendo una aplicación de gestión de productos. Tendremos operaciones de comandos para crear y actualizar productos, y operaciones de consultas para obtener productos.

### 1. Configurar el Proyecto

1. **Crear un Nuevo Proyecto**:

   ```bash
   dotnet new webapi -n ProductService
   cd ProductService
   ```

2. **Agregar Paquetes Necesarios**:

   ```bash
   dotnet add package MediatR
   dotnet add package MediatR.Extensions.Microsoft.DependencyInjection
   dotnet add package Microsoft.EntityFrameworkCore
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   ```

### 2. Configurar Entity Framework Core

1. **Definir el Modelo de Datos**:

   Crea una carpeta `Models` y añade el modelo `Product`.

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

2. **Crear el DbContext**:

   Crea una carpeta `Data` y añade el contexto `ProductContext`.

   ```csharp
   using Microsoft.EntityFrameworkCore;
   using ProductService.Models;

   namespace ProductService.Data
   {
       public class ProductContext : DbContext
       {
           public ProductContext(DbContextOptions<ProductContext> options) : base(options) { }

           public DbSet<Product> Products { get; set; }
       }
   }
   ```

### 3. Configurar CQRS con MediatR

1. **Definir los Comandos y Consultas**:

   Crea una carpeta `CQRS` con subcarpetas `Commands` y `Queries`.

   **Commands/CreateProductCommand.cs**:

   ```csharp
   using MediatR;

   namespace ProductService.CQRS.Commands
   {
       public class CreateProductCommand : IRequest<int>
       {
           public string Name { get; set; }
           public decimal Price { get; set; }
       }
   }
   ```

   **Queries/GetProductByIdQuery.cs**:

   ```csharp
   using MediatR;
   using ProductService.Models;

   namespace ProductService.CQRS.Queries
   {
       public class GetProductByIdQuery : IRequest<Product>
       {
           public int Id { get; set; }

           public GetProductByIdQuery(int id)
           {
               Id = id;
           }
       }
   }
   ```

2. **Implementar los Manejadores**:

   Crea una carpeta `Handlers`.

   **Handlers/CreateProductCommandHandler.cs**:

   ```csharp
   using MediatR;
   using ProductService.CQRS.Commands;
   using ProductService.Data;
   using ProductService.Models;
   using System.Threading;
   using System.Threading.Tasks;

   namespace ProductService.Handlers
   {
       public class CreateProductCommandHandler : IRequestHandler<CreateProductCommand, int>
       {
           private readonly ProductContext _context;

           public CreateProductCommandHandler(ProductContext context)
           {
               _context = context;
           }

           public async Task<int> Handle(CreateProductCommand request, CancellationToken cancellationToken)
           {
               var product = new Product { Name = request.Name, Price = request.Price };
               _context.Products.Add(product);
               await _context.SaveChangesAsync();
               return product.Id;
           }
       }
   }
   ```

   **Handlers/GetProductByIdQueryHandler.cs**:

   ```csharp
   using MediatR;
   using ProductService.CQRS.Queries;
   using ProductService.Data;
   using ProductService.Models;
   using System.Threading;
   using System.Threading.Tasks;

   namespace ProductService.Handlers
   {
       public class GetProductByIdQueryHandler : IRequestHandler<GetProductByIdQuery, Product>
       {
           private readonly ProductContext _context;

           public GetProductByIdQueryHandler(ProductContext context)
           {
               _context = context;
           }

           public async Task<Product> Handle(GetProductByIdQuery request, CancellationToken cancellationToken)
           {
               return await _context.Products.FindAsync(request.Id);
           }
       }
   }
   ```

### 4. Configurar el `Startup.cs`

Configura los servicios de MediatR y Entity Framework Core.

```csharp
using MediatR;
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
            options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

        services.AddMediatR(typeof(Startup).Assembly);
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

### 5. Crear el Controlador

Añade un controlador para manejar las solicitudes HTTP.

```csharp
using MediatR;
using Microsoft.AspNetCore.Mvc;
using ProductService.CQRS.Commands;
using ProductService.CQRS.Queries;
using System.Threading.Tasks;

namespace ProductService.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class ProductsController : ControllerBase
    {
        private readonly IMediator _mediator;

        public ProductsController(IMediator mediator)
        {
            _mediator = mediator;
        }

        [HttpPost]
        public async Task<IActionResult> CreateProduct([FromBody] CreateProductCommand command)
        {
            var productId = await _mediator.Send(command);
            return Ok(productId);
        }

        [HttpGet("{id}")]
        public async Task<IActionResult> GetProductById(int id)
        {
            var query = new GetProductByIdQuery(id);
            var product = await _mediator.Send(query);
            return Ok(product);
        }
    }
}
```

### Resumen

En este ejemplo, hemos separado las responsabilidades de lectura y escritura utilizando el patrón CQRS en un proyecto .NET Core. Utilizamos MediatR para manejar los comandos y las consultas, y Entity Framework Core para la interacción con la base de datos. Esta separación nos permite optimizar y escalar de manera independiente las operaciones de lectura y escritura en la aplicación.