# **CircuitBreaker.md**
El patrón **Circuit Breaker** es una estrategia de diseño utilizada en microservicios para mejorar la resiliencia del sistema al manejar fallos de servicios externos. Funciona de manera similar a un interruptor de circuito eléctrico: detecta fallos en un servicio y evita que esos fallos se propaguen a otras partes del sistema, permitiendo que el sistema continúe funcionando incluso si un servicio está temporalmente inoperativo.

### Cómo Funciona el Circuit Breaker

1. **Estado del Circuito**:
   - **Closed (Cerrado)**: El circuito permite el paso de solicitudes al servicio. Si las solicitudes fallan, el contador de fallos se incrementa.
   - **Open (Abierto)**: El circuito corta el paso de solicitudes al servicio para evitar sobrecargarlo y permitirle recuperarse. Durante este tiempo, las solicitudes se rechazan inmediatamente y se devuelve un error, evitando así que el servicio sea sobrecargado.
   - **Half-Open (Medio Abierto)**: El circuito permite un número limitado de solicitudes al servicio para ver si se ha recuperado. Si las solicitudes tienen éxito, el circuito se cierra nuevamente. Si fallan, el circuito se abre de nuevo y las solicitudes son rechazadas hasta que se estabilice el servicio.

2. **Mecanismo de Recuperación**: Durante el estado abierto, el sistema permite que el servicio reciba solicitudes limitadas en el estado medio abierto para probar si se ha recuperado. Si el servicio responde correctamente, el circuito se cierra; si no, el circuito se mantiene abierto.

### Implementación del Circuit Breaker en .NET Core

En .NET Core, puedes implementar el patrón Circuit Breaker utilizando bibliotecas como **Polly**, que proporciona una manera sencilla de manejar la resiliencia y los fallos de servicio.

#### Ejemplo con Polly

Aquí te muestro cómo implementar el patrón Circuit Breaker en un proyecto ASP.NET Core utilizando Polly:

1. **Crear el Proyecto**

   Crea un nuevo proyecto de **ASP.NET Core Web API** si aún no lo tienes.

   ```bash
   dotnet new webapi -n CircuitBreakerExample
   cd CircuitBreakerExample
   ```

2. **Agregar Polly**

   Agrega el paquete de Polly al proyecto:

   ```bash
   dotnet add package Polly
   dotnet add package Microsoft.Extensions.Http.Polly
   ```

3. **Configurar Polly en Startup.cs**

   Configura Polly para usar el patrón Circuit Breaker en `Startup.cs` mediante la inyección de `HttpClient`.

   ```csharp
   using Microsoft.AspNetCore.Builder;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Configuration;
   using Microsoft.Extensions.DependencyInjection;
   using Microsoft.Extensions.Hosting;
   using Polly;
   using System;
   using System.Net.Http;

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

           // Configurar HttpClient con Circuit Breaker
           services.AddHttpClient("MyServiceClient", client =>
           {
               client.BaseAddress = new Uri(Configuration["MyService:BaseUrl"]);
           })
           .AddTransientHttpErrorPolicy(builder =>
               builder.CircuitBreakerAsync(
                   exceptionsAllowedBeforeBreaking: 5,
                   durationOfBreak: TimeSpan.FromSeconds(30)
               ));
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

   En este ejemplo:
   - `exceptionsAllowedBeforeBreaking` define el número de fallos consecutivos permitidos antes de abrir el circuito.
   - `durationOfBreak` define el tiempo que el circuito permanecerá abierto antes de intentar permitir algunas solicitudes de nuevo.

4. **Implementar el Controlador**

   Implementa un controlador que use `HttpClient` con el Circuit Breaker configurado.

   ```csharp
   using Microsoft.AspNetCore.Mvc;
   using System.Net.Http;
   using System.Threading.Tasks;

   [ApiController]
   [Route("[controller]")]
   public class SampleController : ControllerBase
   {
       private readonly HttpClient _httpClient;

       public SampleController(IHttpClientFactory httpClientFactory)
       {
           _httpClient = httpClientFactory.CreateClient("MyServiceClient");
       }

       [HttpGet]
       [Route("fetch-data")]
       public async Task<IActionResult> FetchData()
       {
           try
           {
               var response = await _httpClient.GetAsync("/api/data");
               response.EnsureSuccessStatusCode();
               var data = await response.Content.ReadAsStringAsync();
               return Ok(data);
           }
           catch (HttpRequestException e)
           {
               return StatusCode(503, $"Service unavailable: {e.Message}");
           }
       }
   }
   ```

   Aquí, el `HttpClient` configurado con Polly intentará hacer la solicitud al servicio y, si el servicio está inactivo o responde con errores, el Circuit Breaker actuará para evitar la propagación de fallos.

### Resumen

El patrón Circuit Breaker ayuda a mantener la resiliencia del sistema al prevenir fallos en cascada y permitir que los servicios se recuperen sin sobrecargar el sistema. Polly es una biblioteca poderosa en .NET Core para implementar este patrón y otros patrones de resiliencia, como la reintentos y el tiempo de espera.