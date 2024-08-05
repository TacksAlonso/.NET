Azure Functions es una plataforma de computación sin servidor que permite ejecutar pequeñas piezas de código o "funciones" en la nube. Estas funciones se ejecutan en respuesta a eventos como cambios en la base de datos, solicitudes HTTP, mensajes en cola y mucho más. Azure Functions es una opción ideal para tareas que pueden dividirse en pequeñas unidades de trabajo que no necesitan una infraestructura completa dedicada.

### Características Clave de Azure Functions

1. **Modelo de Facturación por Uso**: Paga solo por el tiempo de ejecución de tus funciones.
2. **Escalado Automático**: Escala automáticamente en función de la demanda de tus aplicaciones.
3. **Soporte para Múltiples Lenguajes**: C#, JavaScript, F#, Python, Java, y más.
4. **Desarrollo Local y Despliegue Fácil**: Desarrolla y prueba funciones localmente y luego despliega en Azure.
5. **Integraciones Integradas**: Fácil integración con otros servicios de Azure como Cosmos DB, Service Bus, Event Hubs, etc.
6. **Triggers y Bindings**: Facilita la configuración de eventos que desencadenan la ejecución de funciones y la vinculación de datos de entrada y salida.

### Creación de Azure Functions con .NET Core

A continuación, se detallan los pasos para crear y desplegar Azure Functions con .NET Core.

#### 1. Configuración del Entorno

Primero, asegúrate de tener las siguientes herramientas instaladas:

- [Visual Studio](https://visualstudio.microsoft.com/downloads/) (con la carga de trabajo de desarrollo de Azure)
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
- [Azure Functions Core Tools](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local)

#### 2. Crear un Proyecto de Azure Functions

1. **Crear un Proyecto Nuevo en Visual Studio**

   - Abre Visual Studio y selecciona **Crear un nuevo proyecto**.
   - Busca y selecciona **Azure Functions** y luego haz clic en **Siguiente**.
   - Configura el nombre del proyecto y la ubicación, y haz clic en **Crear**.
   - Selecciona **.NET Core** como el entorno de tiempo de ejecución y **Azure Functions v3** o superior como versión.
   - Elige un **Tipo de Trigger** (por ejemplo, HTTP trigger) y haz clic en **Crear**.

2. **Estructura del Proyecto**

   El proyecto se generará con una estructura similar a esta:

   ```
   MyFunctionApp
   ├── local.settings.json
   ├── host.json
   ├── MyFunctionApp.csproj
   └── Functions
       └── HttpTriggerFunction.cs
   ```

#### 3. Implementar una Función HTTP Trigger

En el archivo `HttpTriggerFunction.cs`, tendrás un ejemplo de función HTTP:

```csharp
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace MyFunctionApp
{
    public static class HttpTriggerFunction
    {
        [FunctionName("HttpTriggerFunction")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            string name = req.Query["name"];

            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            dynamic data = JsonConvert.DeserializeObject(requestBody);
            name = name ?? data?.name;

            return name != null
                ? (ActionResult)new OkObjectResult($"Hello, {name}")
                : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
        }
    }
}
```

#### 4. Ejecutar y Probar Localmente

1. **Ejecutar la Función Localmente**

   - Abre el terminal y navega al directorio de tu proyecto.
   - Ejecuta `func start` para iniciar el runtime de Azure Functions y ejecutar tu función localmente.

   ```bash
   func start
   ```

2. **Probar la Función Localmente**

   Usa herramientas como [Postman](https://www.postman.com/) o simplemente un navegador para hacer una solicitud GET o POST a la URL que se muestra en la consola, generalmente algo como `http://localhost:7071/api/HttpTriggerFunction`.

#### 5. Desplegar en Azure

1. **Iniciar Sesión en Azure**

   ```bash
   az login
   ```

2. **Crear un Grupo de Recursos y una Cuenta de Funciones**

   ```bash
   az group create --name MyResourceGroup --location eastus
   az storage account create --name mystorageaccount --location eastus --resource-group MyResourceGroup --sku Standard_LRS
   az functionapp create --resource-group MyResourceGroup --consumption-plan-location eastus --runtime dotnet --functions-version 3 --name MyFunctionApp --storage-account mystorageaccount
   ```

3. **Publicar desde Visual Studio**

   - Haz clic derecho en el proyecto en el Explorador de Soluciones y selecciona **Publicar**.
   - Sigue el asistente para seleccionar **Azure** como destino, elige tu suscripción y la cuenta de funciones creada anteriormente.
   - Publica el proyecto.

#### 6. Monitorear y Administrar

1. **Supervisión**

   - Desde el portal de Azure, navega a tu cuenta de funciones.
   - Utiliza **Application Insights** para supervisar las métricas y registros.

2. **Escalabilidad y Configuración**

   - Configura la escalabilidad desde el portal de Azure ajustando los planes de consumo.
   - Administra configuraciones avanzadas como las conexiones, variables de entorno y más desde el portal de Azure.

### Ejemplo Completo de una Función HTTP Trigger

Aquí tienes un ejemplo completo de cómo crear una función HTTP Trigger en .NET Core, ejecutarla localmente y desplegarla en Azure.

```csharp
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace MyFunctionApp
{
    public static class HttpTriggerFunction
    {
        [FunctionName("HttpTriggerFunction")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            string name = req.Query["name"];

            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            dynamic data = JsonConvert.DeserializeObject(requestBody);
            name = name ?? data?.name;

            return name != null
                ? (ActionResult)new OkObjectResult($"Hello, {name}")
                : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
        }
    }
}
```

### Pasos Finales

1. **Ejecutar Localmente**

   ```bash
   func start
   ```

2. **Desplegar en Azure**

   ```bash
   az login
   az group create --name MyResourceGroup --location eastus
   az storage account create --name mystorageaccount --location eastus --resource-group MyResourceGroup --sku Standard_LRS
   az functionapp create --resource-group MyResourceGroup --consumption-plan-location eastus --runtime dotnet --functions-version 3 --name MyFunctionApp --storage-account mystorageaccount
   ```

   - Publica desde Visual Studio.

Azure Functions en .NET Core proporciona una forma potente y flexible de construir y desplegar microservicios y tareas en la nube, permitiendo a los desarrolladores centrarse en escribir código en lugar de gestionar la infraestructura.