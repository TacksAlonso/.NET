El patrón **Sidecar** se utiliza para añadir funcionalidades adicionales a un microservicio principal sin modificar su código. El Sidecar se despliega junto al microservicio principal y se comunica con él a través de la red local. Esto es útil para añadir características como logging, configuración, proxy, etc.

### Implementación del Patrón Sidecar en .NET Core

Para este ejemplo, vamos a implementar un simple microservicio y un servicio Sidecar que actúa como un proxy, añadiendo funcionalidad de logging a las solicitudes HTTP.

### 1. Crear el Microservicio Principal

#### 1.1 Crear un Nuevo Proyecto de API Web

```bash
dotnet new webapi -n MainService
cd MainService
```

#### 1.2 Implementar el Controlador del Microservicio Principal

**Controllers/MainController.cs**:

```csharp
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace MainService.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class MainController : ControllerBase
    {
        [HttpGet]
        public ActionResult<IEnumerable<string>> Get()
        {
            return new string[] { "value1", "value2" };
        }
    }
}
```

#### 1.3 Configurar `Startup.cs`

**Startup.cs**:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

namespace MainService
{
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
}
```

### 2. Crear el Servicio Sidecar

#### 2.1 Crear un Nuevo Proyecto de API Web

```bash
cd ..
dotnet new webapi -n SidecarService
cd SidecarService
```

#### 2.2 Implementar el Controlador del Servicio Sidecar

**Controllers/ProxyController.cs**:

```csharp
using Microsoft.AspNetCore.Mvc;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace SidecarService.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class ProxyController : ControllerBase
    {
        private readonly IHttpClientFactory _httpClientFactory;
        private readonly ILogger<ProxyController> _logger;

        public ProxyController(IHttpClientFactory httpClientFactory, ILogger<ProxyController> logger)
        {
            _httpClientFactory = httpClientFactory;
            _logger = logger;
        }

        [HttpGet]
        public async Task<IActionResult> Get()
        {
            var client = _httpClientFactory.CreateClient();
            var response = await client.GetAsync("http://localhost:5000/main"); // URL del microservicio principal
            var content = await response.Content.ReadAsStringAsync();

            _logger.LogInformation("Request to MainService: {Content}", content);

            return Ok(content);
        }
    }
}
```

#### 2.3 Configurar `Startup.cs`

**Startup.cs**:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

namespace SidecarService
{
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
            services.AddHttpClient();
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
}
```

### 3. Ejecutar Ambos Servicios

#### 3.1 Ejecutar el Microservicio Principal

```bash
cd MainService
dotnet run
```

#### 3.2 Ejecutar el Servicio Sidecar

```bash
cd ../SidecarService
dotnet run
```

### 4. Probar la Configuración

Accede al servicio Sidecar a través de la URL `http://localhost:5001/proxy`. Este servicio actuará como proxy y pasará la solicitud al microservicio principal en `http://localhost:5000/main`, registrando el contenido de la respuesta en los logs.

### Resumen

En este ejemplo, hemos implementado el patrón Sidecar en .NET Core utilizando dos servicios: el microservicio principal y el servicio Sidecar. El servicio Sidecar actúa como un proxy y añade funcionalidad de logging a las solicitudes HTTP, sin modificar el código del microservicio principal.