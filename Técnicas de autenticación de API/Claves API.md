**Claves API** son un método simple para autenticar solicitudes a una API. Se utilizan principalmente para identificar y autorizar aplicaciones, no usuarios individuales.

**Características de las Claves API:**
- **Simples de Implementar:** Generalmente, solo necesitas incluir una clave en los encabezados o parámetros de consulta de la solicitud.
- **Menos Seguridad:** Ofrecen menos seguridad que otros métodos, ya que la clave API puede ser expuesta fácilmente.

**Ejemplo de Implementación de Claves API en .NET Core:**

1. **Validación de Clave API en el Middleware:**

   ```csharp
   using Microsoft.AspNetCore.Http;
   using System.Threading.Tasks;

   public class ApiKeyMiddleware
   {
       private readonly RequestDelegate _next;
       private const string ApiKeyHeader = "X-Api-Key";
       private const string ApiKeyValue = "your_api_key_here";

       public ApiKeyMiddleware(RequestDelegate next)
       {
           _next = next;
       }

       public async Task Invoke(HttpContext context)
       {
           if (context.Request.Headers.ContainsKey(ApiKeyHeader))
           {
               var apiKey = context.Request.Headers[ApiKeyHeader].ToString();
               if (apiKey == ApiKeyValue)
               {
                   await _next(context);
                   return;
               }
           }

           context.Response.StatusCode = StatusCodes.Status401Unauthorized;
           await context.Response.WriteAsync("Unauthorized");
       }
   }

   public class Startup
   {
       public void Configure(IApplicationBuilder app, IHostingEnvironment env)
       {
           app.UseMiddleware<ApiKeyMiddleware>();

           app.UseRouting();

           app.UseEndpoints(endpoints =>
           {
               endpoints.MapControllers();
           });
       }
   }
   ```

**Ventajas de las Claves API:**
- **Fácil Implementación:** Sencillo de implementar y utilizar.
- **Adecuado para Aplicaciones Internas:** Bueno para autenticación en aplicaciones controladas y entornos de servidor a servidor.

**Desventajas de las Claves API:**
- **Seguridad Limitada:** Menos seguro en comparación con métodos como JWT y OAuth, ya que las claves pueden ser fácilmente compartidas o expuestas.
- **No Granular:** No proporciona control detallado sobre los permisos a nivel de usuario o aplicación.

---

### **Práctica de Implementación**

**1. Implementación de JWT en una API:**
   - Utiliza la clase `JwtSecurityTokenHandler` para generar y validar JWT.
   - Configura el middleware de autenticación para aceptar tokens JWT y validar su firma y contenido.

**2. Implementación de OAuth:**
   - Configura el servidor de autorización para manejar el flujo de autorización y emitir tokens de acceso.
   - En el cliente, maneja la redirección al servidor de autorización y el intercambio de códigos por tokens.

**3. Implementación de Claves API:**
   -

 Configura un middleware en la API para validar las claves API en las solicitudes.
   - Asegúrate de almacenar y gestionar las claves API de forma segura.
