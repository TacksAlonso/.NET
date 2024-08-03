# **Orquestacion**
El Patrón de Orquestación en microservicios implica usar un servicio centralizado para coordinar el flujo de trabajo y la interacción entre varios microservicios. En .NET, puedes implementar este patrón utilizando un servicio de orquestación que maneje las llamadas entre los microservicios y coordine el proceso completo.

Para ilustrar el Patrón de Orquestación en .NET, vamos a construir un ejemplo simple utilizando **ASP.NET Core** para crear un servicio de orquestación que coordine la creación de una orden en un sistema que consta de un microservicio de productos y un microservicio de pedidos.

### 1. Crear el Proyecto de Orquestación

Primero, crea un nuevo proyecto de **ASP.NET Core Web API** para el servicio de orquestación.

```bash
dotnet new webapi -n OrderOrchestrationService
cd OrderOrchestrationService
```

### 2. Agregar Referencias a los Microservicios

Necesitarás agregar referencias HTTP para comunicarte con los microservicios de productos y pedidos. Puedes usar `HttpClient` para esto. Agrega el paquete `Microsoft.Extensions.Http` para simplificar la configuración de `HttpClient`.

```bash
dotnet add package Microsoft.Extensions.Http
```

### 3. Configurar el Servicio de Orquestación

#### **`OrderOrchestrationService/Startup.cs`**:

Configura los servicios y el `HttpClient` en `Startup.cs` para interactuar con los microservicios.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
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
        
        // Configurar HttpClient para los microservicios
        services.AddHttpClient("ProductService", client =>
        {
            client.BaseAddress = new Uri(Configuration["ProductService:BaseUrl"]);
        });

        services.AddHttpClient("OrderService", client =>
        {
            client.BaseAddress = new Uri(Configuration["OrderService:BaseUrl"]);
        });
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

#### **`OrderOrchestrationService/appsettings.json`**:

Configura las URL de los microservicios en el archivo `appsettings.json`.

```json
{
  "ProductService": {
    "BaseUrl": "http://localhost:5001"
  },
  "OrderService": {
    "BaseUrl": "http://localhost:5002"
  }
}
```

### 4. Implementar el Controlador de Orquestación

Implementa un controlador en el servicio de orquestación que coordine las interacciones entre los microservicios de productos y pedidos.

#### **`OrderOrchestrationService/Controllers/OrderController.cs`**:

```csharp
using Microsoft.AspNetCore.Mvc;
using System.Net.Http;
using System.Threading.Tasks;

[ApiController]
[Route("[controller]")]
public class OrderController : ControllerBase
{
    private readonly HttpClient _productClient;
    private readonly HttpClient _orderClient;

    public OrderController(IHttpClientFactory httpClientFactory)
    {
        _productClient = httpClientFactory.CreateClient("ProductService");
        _orderClient = httpClientFactory.CreateClient("OrderService");
    }

    [HttpPost]
    [Route("create")]
    public async Task<IActionResult> CreateOrder([FromBody] OrderRequest request)
    {
        // Consultar la información del producto
        var productResponse = await _productClient.GetAsync($"/api/products/{request.ProductId}");
        if (!productResponse.IsSuccessStatusCode)
        {
            return BadRequest("Product not found");
        }

        var product = await productResponse.Content.ReadAsStringAsync();

        // Crear el pedido
        var orderResponse = await _orderClient.PostAsJsonAsync("/api/orders", new
        {
            ProductId = request.ProductId,
            Quantity = request.Quantity,
            ProductDetails = product
        });

        if (orderResponse.IsSuccessStatusCode)
        {
            return Ok("Order created successfully");
        }

        return StatusCode((int)orderResponse.StatusCode, "Failed to create order");
    }
}

public class OrderRequest
{
    public int ProductId { get; set; }
    public int Quantity { get; set; }
}
```

### 5. Crear Microservicios de Productos y Pedidos

Aquí están los pasos básicos para crear los microservicios de productos y pedidos. Estos microservicios deben estar en ejecución para probar la orquestación.

#### **Microservicio de Productos**:
- Crea un nuevo proyecto ASP.NET Core Web API para el microservicio de productos.
- Expón un endpoint `/api/products/{id}` que devuelva información sobre un producto.

#### **Microservicio de Pedidos**:
- Crea otro proyecto ASP.NET Core Web API para el microservicio de pedidos.
- Expón un endpoint `/api/orders` que permita la creación de un pedido.

### 6. Ejecutar y Probar

1. Ejecuta los microservicios de productos y pedidos en los puertos configurados (`5001` y `5002`, respectivamente).
2. Ejecuta el servicio de orquestación en el puerto `5000`.
3. Usa herramientas como Postman para enviar solicitudes POST a `http://localhost:5000/order/create` con un cuerpo JSON que contenga `ProductId` y `Quantity`.

### Resumen

Este ejemplo muestra cómo implementar un servicio de orquestación en .NET Core que coordina la interacción entre dos microservicios, uno para productos y otro para pedidos. El servicio de orquestación actúa como un coordinador central, manejando la lógica para consultar información del producto y crear un pedido en base a esa información.