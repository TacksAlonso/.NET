# **Saga**
El patrón **Saga** es una técnica de gestión de transacciones distribuidas en una arquitectura de microservicios. Este patrón divide una transacción en una serie de pasos menores, cada uno de los cuales es una transacción completa en sí misma. Si alguna parte de la transacción falla, los pasos anteriores se deshacen mediante pasos de compensación.

En .NET Core, puedes implementar el patrón Saga usando herramientas como **MassTransit** junto con **RabbitMQ**. MassTransit facilita la creación de mensajes y la orquestación de procesos de manera asíncrona.

### Ejemplo de Implementación del Patrón Saga

En este ejemplo, vamos a implementar una saga para procesar un pedido en tres servicios diferentes: **OrderService**, **PaymentService** y **ShippingService**.

### 1. Configurar RabbitMQ

Primero, asegúrate de tener RabbitMQ en funcionamiento.

### 2. Crear los Microservicios

Crea tres proyectos de microservicio: **OrderService**, **PaymentService** y **ShippingService**.

#### **OrderService**

1. **Crear el Proyecto**:

   ```bash
   dotnet new webapi -n OrderService
   cd OrderService
   ```

2. **Agregar Paquetes de MassTransit y RabbitMQ**:

   ```bash
   dotnet add package MassTransit
   dotnet add package MassTransit.RabbitMQ
   ```

3. **Definir los Mensajes de la Saga**:

   Crea una carpeta `Messages` y define los mensajes que se usarán en la saga.

   ```csharp
   namespace OrderService.Messages
   {
       public class OrderCreated
       {
           public Guid OrderId { get; set; }
           public decimal Amount { get; set; }
       }

       public class OrderCancelled
       {
           public Guid OrderId { get; set; }
       }
   }
   ```

4. **Configurar MassTransit en `Startup.cs`**:

   ```csharp
   using MassTransit;
   using Microsoft.AspNetCore.Builder;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Configuration;
   using Microsoft.Extensions.DependencyInjection;
   using Microsoft.Extensions.Hosting;
   using OrderService.Messages;
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

           // Configurar MassTransit con RabbitMQ
           services.AddMassTransit(x =>
           {
               x.UsingRabbitMq((context, cfg) =>
               {
                   cfg.Host("localhost", "/", h =>
                   {
                       h.Username("guest");
                       h.Password("guest");
                   });

                   cfg.ReceiveEndpoint("order-service", ep =>
                   {
                       ep.ConfigureConsumer<OrderConsumer>(context);
                   });
               });
           });

           services.AddMassTransitHostedService();
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

5. **Implementar el Controlador y el Consumidor**:

   ```csharp
   using MassTransit;
   using Microsoft.AspNetCore.Mvc;
   using OrderService.Messages;
   using System;
   using System.Threading.Tasks;

   [ApiController]
   [Route("[controller]")]
   public class OrderController : ControllerBase
   {
       private readonly IPublishEndpoint _publishEndpoint;

       public OrderController(IPublishEndpoint publishEndpoint)
       {
           _publishEndpoint = publishEndpoint;
       }

       [HttpPost]
       public async Task<IActionResult> CreateOrder(decimal amount)
       {
           var orderId = Guid.NewGuid();
           await _publishEndpoint.Publish(new OrderCreated { OrderId = orderId, Amount = amount });
           return Ok(new { OrderId = orderId });
       }
   }

   public class OrderConsumer : IConsumer<OrderCreated>
   {
       public async Task Consume(ConsumeContext<OrderCreated> context)
       {
           // Lógica de procesamiento del pedido
           Console.WriteLine($"Order received: {context.Message.OrderId}, Amount: {context.Message.Amount}");
           // Enviar a PaymentService
           // ...
       }
   }
   ```

#### **PaymentService**

1. **Crear el Proyecto**:

   ```bash
   dotnet new webapi -n PaymentService
   cd PaymentService
   ```

2. **Agregar Paquetes de MassTransit y RabbitMQ**:

   ```bash
   dotnet add package MassTransit
   dotnet add package MassTransit.RabbitMQ
   ```

3. **Definir los Mensajes**:

   ```csharp
   namespace PaymentService.Messages
   {
       public class PaymentCompleted
       {
           public Guid OrderId { get; set; }
       }

       public class PaymentFailed
       {
           public Guid OrderId { get; set; }
       }
   }
   ```

4. **Configurar MassTransit en `Startup.cs`**:

   ```csharp
   using MassTransit;
   using Microsoft.AspNetCore.Builder;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Configuration;
   using Microsoft.Extensions.DependencyInjection;
   using Microsoft.Extensions.Hosting;
   using PaymentService.Messages;
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

           // Configurar MassTransit con RabbitMQ
           services.AddMassTransit(x =>
           {
               x.UsingRabbitMq((context, cfg) =>
               {
                   cfg.Host("localhost", "/", h =>
                   {
                       h.Username("guest");
                       h.Password("guest");
                   });

                   cfg.ReceiveEndpoint("payment-service", ep =>
                   {
                       ep.ConfigureConsumer<PaymentConsumer>(context);
                   });
               });
           });

           services.AddMassTransitHostedService();
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

5. **Implementar el Consumidor**:

   ```csharp
   using MassTransit;
   using OrderService.Messages;
   using PaymentService.Messages;
   using System;
   using System.Threading.Tasks;

   public class PaymentConsumer : IConsumer<OrderCreated>
   {
       private readonly IPublishEndpoint _publishEndpoint;

       public PaymentConsumer(IPublishEndpoint publishEndpoint)
       {
           _publishEndpoint = publishEndpoint;
       }

       public async Task Consume(ConsumeContext<OrderCreated> context)
       {
           // Lógica de procesamiento de pago
           Console.WriteLine($"Processing payment for Order: {context.Message.OrderId}");
           bool paymentSuccess = true; // Simulación de pago

           if (paymentSuccess)
           {
               await _publishEndpoint.Publish(new PaymentCompleted { OrderId = context.Message.OrderId });
           }
           else
           {
               await _publishEndpoint.Publish(new PaymentFailed { OrderId = context.Message.OrderId });
           }
       }
   }
   ```

#### **ShippingService**

1. **Crear el Proyecto**:

   ```bash
   dotnet new webapi -n ShippingService
   cd ShippingService
   ```

2. **Agregar Paquetes de MassTransit y RabbitMQ**:

   ```bash
   dotnet add package MassTransit
   dotnet add package MassTransit.RabbitMQ
   ```

3. **Configurar MassTransit en `Startup.cs`**:

   ```csharp
   using MassTransit;
   using Microsoft.AspNetCore.Builder;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Configuration;
   using Microsoft.Extensions.DependencyInjection;
   using Microsoft.Extensions.Hosting;
   using PaymentService.Messages;
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

           // Configurar MassTransit con RabbitMQ
           services.AddMassTransit(x =>
           {
               x.UsingRabbitMq((context, cfg) =>
               {
                   cfg.Host("localhost", "/", h =>
                   {
                       h.Username("guest");
                       h.Password("guest");
                   });

                   cfg.ReceiveEndpoint("shipping-service", ep =>
                   {
                       ep.ConfigureConsumer<ShippingConsumer>(context);
                   });
               });
           });

           services.AddMassTransitHostedService();
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

4. **Implementar el Consumidor**:

   ```csharp
   using MassTransit;
   using PaymentService.Messages;
   using System;
   using System.Threading.Tasks;

   public class ShippingConsumer : IConsumer<PaymentCompleted>
   {
       public async Task Consume(ConsumeContext<PaymentCompleted> context)
       {
           // Lógica de procesamiento de envío
           Console.WriteLine($"Shipping order: {context.Message.OrderId}");
           // Procesar envío
       }
   }
   ```

### Resumen

En este ejemplo, hemos creado una saga distribuida con tres microservicios: `OrderService`, `PaymentService`, y `ShippingService`. Cada microservicio maneja su parte de la transacción y se comunica con los otros microservicios a través de eventos publicados en RabbitMQ utilizando MassTransit.

 En caso de falla en algún servicio, se pueden definir eventos de compensación para deshacer los pasos anteriores de la transacción.

Este patrón ayuda a mantener la consistencia y la integridad de los datos en una arquitectura de microservicios, especialmente cuando las operaciones abarcan múltiples servicios y bases de datos.