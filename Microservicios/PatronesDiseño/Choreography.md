# **Choreography**
El patrón de **Choreography** (Coreografía) en microservicios se refiere a un enfoque en el que cada servicio interactúa con otros servicios de manera autónoma y coordina el flujo de trabajo mediante eventos. En lugar de tener un servicio centralizado que orquesta la comunicación entre servicios (como en el patrón de Orquestación), en la coreografía cada servicio sabe qué hacer a continuación y cómo interactuar con otros servicios a través de eventos.

En .NET Core, puedes implementar la coreografía utilizando un sistema de mensajería para la comunicación entre microservicios. Uno de los enfoques comunes es usar **Azure Service Bus**, **RabbitMQ** o **Kafka** para enviar y recibir mensajes.

A continuación, te muestro un ejemplo básico de cómo implementar la coreografía en .NET Core utilizando **RabbitMQ**.

### 1. Configurar RabbitMQ

Primero, necesitas tener RabbitMQ en funcionamiento. Puedes instalar RabbitMQ localmente o usar un servicio gestionado como RabbitMQ en Azure. 

Para instalar RabbitMQ localmente, sigue las instrucciones en la [página oficial de RabbitMQ](https://www.rabbitmq.com/download.html).

### 2. Crear un Proyecto de Microservicio

Crea dos proyectos de microservicio para ilustrar la coreografía. Uno será el **Microservicio de Pedido** y el otro el **Microservicio de Notificación**.

#### **Microservicio de Pedido**

1. **Crear el Proyecto**:

   ```bash
   dotnet new webapi -n OrderService
   cd OrderService
   ```

2. **Agregar Paquete RabbitMQ**:

   ```bash
   dotnet add package RabbitMQ.Client
   ```

3. **Configurar RabbitMQ en `Startup.cs`**:

   ```csharp
   using Microsoft.AspNetCore.Builder;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Configuration;
   using Microsoft.Extensions.DependencyInjection;
   using Microsoft.Extensions.Hosting;
   using RabbitMQ.Client;

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

           // Configuración de RabbitMQ
           services.AddSingleton(provider =>
           {
               var factory = new ConnectionFactory
               {
                   HostName = Configuration["RabbitMQ:HostName"],
                   Port = int.Parse(Configuration["RabbitMQ:Port"])
               };
               return factory.CreateConnection();
           });

           services.AddSingleton(provider =>
           {
               var connection = provider.GetRequiredService<IConnection>();
               return connection.CreateModel();
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

4. **Implementar el Controlador en `OrderController.cs`**:

   ```csharp
   using Microsoft.AspNetCore.Mvc;
   using RabbitMQ.Client;
   using System.Text;
   using System.Text.Json;

   [ApiController]
   [Route("[controller]")]
   public class OrderController : ControllerBase
   {
       private readonly IModel _channel;

       public OrderController(IModel channel)
       {
           _channel = channel;
           _channel.ExchangeDeclare("order_exchange", ExchangeType.Fanout);
       }

       [HttpPost]
       [Route("create")]
       public IActionResult CreateOrder([FromBody] OrderRequest request)
       {
           // Crear el pedido (lógica de negocio aquí)

           // Enviar mensaje a RabbitMQ
           var message = JsonSerializer.Serialize(request);
           var body = Encoding.UTF8.GetBytes(message);

           _channel.BasicPublish(exchange: "order_exchange",
                                routingKey: "",
                                basicProperties: null,
                                body: body);

           return Ok("Order created and message sent");
       }
   }

   public class OrderRequest
   {
       public int ProductId { get; set; }
       public int Quantity { get; set; }
   }
   ```

#### **Microservicio de Notificación**

1. **Crear el Proyecto**:

   ```bash
   dotnet new webapi -n NotificationService
   cd NotificationService
   ```

2. **Agregar Paquete RabbitMQ**:

   ```bash
   dotnet add package RabbitMQ.Client
   ```

3. **Configurar RabbitMQ en `Startup.cs`**:

   ```csharp
   using Microsoft.AspNetCore.Builder;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Configuration;
   using Microsoft.Extensions.DependencyInjection;
   using Microsoft.Extensions.Hosting;
   using RabbitMQ.Client;
   using RabbitMQ.Client.Events;
   using System;
   using System.Text;
   using System.Text.Json;

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

           // Configuración de RabbitMQ
           services.AddSingleton(provider =>
           {
               var factory = new ConnectionFactory
               {
                   HostName = Configuration["RabbitMQ:HostName"],
                   Port = int.Parse(Configuration["RabbitMQ:Port"])
               };
               return factory.CreateConnection();
           });

           services.AddSingleton(provider =>
           {
               var connection = provider.GetRequiredService<IConnection>();
               return connection.CreateModel();
           });

           services.AddHostedService<NotificationService>();
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

   public class NotificationService : BackgroundService
   {
       private readonly IModel _channel;

       public NotificationService(IModel channel)
       {
           _channel = channel;
           _channel.ExchangeDeclare("order_exchange", ExchangeType.Fanout);
           _channel.QueueDeclare(queue: "notification_queue",
                                 durable: false,
                                 exclusive: false,
                                 autoDelete: false,
                                 arguments: null);
           _channel.QueueBind(queue: "notification_queue",
                              exchange: "order_exchange",
                              routingKey: "");
       }

       protected override Task ExecuteAsync(CancellationToken stoppingToken)
       {
           stoppingToken.ThrowIfCancellationRequested();
           var consumer = new EventingBasicConsumer(_channel);
           consumer.Received += (model, ea) =>
           {
               var body = ea.Body.ToArray();
               var message = Encoding.UTF8.GetString(body);
               var orderRequest = JsonSerializer.Deserialize<OrderRequest>(message);

               // Procesar la notificación (lógica de negocio aquí)
               Console.WriteLine($"Received Order: ProductId={orderRequest.ProductId}, Quantity={orderRequest.Quantity}");
           };

           _channel.BasicConsume(queue: "notification_queue",
                                  autoAck: true,
                                  consumer: consumer);

           return Task.CompletedTask;
       }
   }

   public class OrderRequest
   {
       public int ProductId { get; set; }
       public int Quantity { get; set; }
   }
   ```

### 4. Configuración

Asegúrate de que tanto `OrderService` como `NotificationService` están configurados con la misma configuración de RabbitMQ en `appsettings.json` o a través de variables de entorno.

**`appsettings.json` para ambos servicios**:

```json
{
  "RabbitMQ": {
    "HostName": "localhost",
    "Port": 5672
  }
}
```

### 5. Ejecutar y Probar

1. Ejecuta RabbitMQ.
2. Ejecuta `OrderService` y `NotificationService`.
3. Envía una solicitud POST a `http://localhost:5000/order/create` en el `OrderService` con un cuerpo JSON que contenga `ProductId` y `Quantity`.

### Resumen

En este ejemplo de coreografía, el `OrderService` envía un mensaje a RabbitMQ cuando se crea un pedido. El `NotificationService` escucha en RabbitMQ para recibir estos mensajes y puede tomar medidas en respuesta, como enviar notificaciones. Este enfoque permite que los servicios se comuniquen de manera autónoma a través de eventos, promoviendo una arquitectura desacoplada y flexible.