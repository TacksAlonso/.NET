# **API Gateway**
el Patrón de API Gateway en .NET Core se puede implementar utilizando el framework **Ocelot**, que es una popular biblioteca de enrutamiento y gateway de API para aplicaciones basadas en microservicios en .NET Core. Ocelot permite centralizar las solicitudes de los clientes y enrutar esas solicitudes a los microservicios adecuados, además de ofrecer funcionalidades como autenticación, autorización y balanceo de carga.

Aquí te muestro un ejemplo básico de cómo configurar un API Gateway con Ocelot en un proyecto de .NET Core:

### 1. Crear un Proyecto de API Gateway

Primero, crea un nuevo proyecto de **ASP.NET Core Web API**. Puedes hacerlo usando la línea de comandos de .NET o Visual Studio.

```bash
dotnet new web -n ApiGateway
cd ApiGateway
```

### 2. Agregar Ocelot al Proyecto

Agrega el paquete de Ocelot a tu proyecto usando NuGet:

```bash
dotnet add package Ocelot
```

### 3. Configurar Ocelot

Crea un archivo `ocelot.json` en la raíz de tu proyecto. Este archivo define las rutas y los servicios a los que el API Gateway debe enrutar las solicitudes.

**`ocelot.json`**:
```json
{
  "ReRoutes": [
    {
      "DownstreamPathTemplate": "/api/products",
      "DownstreamScheme": "http",
      "DownstreamHostAndPorts": [
        {
          "Host": "localhost",
          "Port": 5001
        }
      ],
      "UpstreamPathTemplate": "/products",
      "UpstreamHttpMethod": [ "Get" ]
    },
    {
      "DownstreamPathTemplate": "/api/orders",
      "DownstreamScheme": "http",
      "DownstreamHostAndPorts": [
        {
          "Host": "localhost",
          "Port": 5002
        }
      ],
      "UpstreamPathTemplate": "/orders",
      "UpstreamHttpMethod": [ "Get" ]
    }
  ],
  "GlobalConfiguration": {
    "BaseUrl": "http://localhost:5000"
  }
}
```

En este ejemplo:
- Las solicitudes a `/products` en el API Gateway se redirigen a `http://localhost:5001/api/products`.
- Las solicitudes a `/orders` se redirigen a `http://localhost:5002/api/orders`.

### 4. Configurar el Startup

Modifica el archivo `Startup.cs` para configurar Ocelot en tu aplicación.

**`Startup.cs`**:
```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Ocelot.DependencyInjection;
using Ocelot.Middleware;

public class Startup
{
    public Startup(IConfiguration configuration)
    {
        Configuration = configuration;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddOcelot(Configuration);
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseOcelot().Wait();
    }
}
```

### 5. Ejecutar el API Gateway

Ejecuta el proyecto del API Gateway. Ocelot escuchará en el puerto configurado (por defecto es el puerto 5000, pero puedes cambiarlo en el archivo `launchSettings.json`).

```bash
dotnet run
```

### 6. Crear Microservicios

Para probar el API Gateway, necesitas tener al menos dos microservicios corriendo en diferentes puertos. Por ejemplo, podrías tener un servicio de productos corriendo en el puerto 5001 y un servicio de órdenes corriendo en el puerto 5002. 

Estos microservicios deben exponer los endpoints que el API Gateway debe enrutar, como `/api/products` y `/api/orders`.

### Resumen

Con esta configuración básica, el API Gateway de Ocelot en .NET Core enruta las solicitudes a los microservicios correspondientes. Ocelot se encarga de las tareas de enrutamiento, balanceo de carga y puede ser configurado para manejar autenticación, autorización y otras características avanzadas.