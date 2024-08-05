Azure Notification Hubs es un servicio que permite enviar notificaciones push a cualquier plataforma y a cualquier número de dispositivos. Es ideal para aplicaciones móviles, aplicaciones web y otros servicios que requieren la capacidad de enviar mensajes en tiempo real a usuarios específicos o grupos de usuarios. Notification Hubs se integra con diversos servicios de notificación como Apple Push Notification Service (APNs), Google Firebase Cloud Messaging (FCM), Windows Push Notification Services (WNS) y más.

### Características Clave de Azure Notification Hubs

- **Multiplataforma**: Soporta notificaciones push para iOS, Android, Windows y otros dispositivos.
- **Alta Escalabilidad**: Puede manejar millones de dispositivos y notificaciones con baja latencia.
- **Personalización**: Permite enviar notificaciones personalizadas a usuarios específicos o segmentos de usuarios basados en tags y plantillas.
- **Integración**: Fácil integración con otras aplicaciones y servicios mediante API RESTful.
- **Broadcast y Notificaciones Dirigidas**: Soporta tanto la difusión masiva de mensajes como el envío de notificaciones a dispositivos específicos.

### Integración de Azure Notification Hubs en .NET Core

#### 1. Crear un Notification Hub en Azure

1. **Iniciar sesión en el Portal de Azure**.
2. **Crear un Namespace de Notification Hub**:
   - Navegue a **Crear un recurso** > **Mobile** > **Notification Hub**.
   - Complete los detalles como el nombre del namespace, la ubicación y el grupo de recursos.
   - Haga clic en **Revisar y crear** y luego en **Crear**.

3. **Crear un Notification Hub**:
   - En el namespace creado, navegue a **Notification Hubs** y cree un nuevo hub.
   - Configure las propiedades como el nombre del hub y las configuraciones de plataforma (APNs, FCM, etc.).

4. **Obtener las Credenciales**:
   - En el namespace, navegue a **Acceso compartido** y copie las cadenas de conexión.

#### 2. Configurar el Proyecto en .NET Core

1. **Crear un Proyecto en Visual Studio**

   - Abra Visual Studio y seleccione **Crear un nuevo proyecto**.
   - Elija **Aplicación Web de ASP.NET Core** o **Aplicación de Consola de .NET Core** y configure el nombre y la ubicación del proyecto.

2. **Agregar Paquete NuGet**

   - Añada el paquete `Microsoft.Azure.NotificationHubs` para interactuar con Azure Notification Hubs.

     ```bash
     dotnet add package Microsoft.Azure.NotificationHubs
     ```

#### 3. Enviar Notificaciones Push

1. **Configurar la Cadena de Conexión en la Aplicación**

   - En el archivo `appsettings.json`, añada la cadena de conexión y el nombre del Notification Hub.

     ```json
     {
       "NotificationHub": {
         "ConnectionString": "Endpoint=sb://<your-namespace>.servicebus.windows.net/;SharedAccessKeyName=<policy-name>;SharedAccessKey=<key>",
         "HubName": "<your-hub-name>"
       }
     }
     ```

2. **Enviar Notificaciones**

   - Cree un servicio para enviar notificaciones a Azure Notification Hubs.

     ```csharp
     using Microsoft.Azure.NotificationHubs;
     using Microsoft.Extensions.Configuration;
     using System.Threading.Tasks;

     public class NotificationHubService
     {
         private readonly NotificationHubClient _hubClient;

         public NotificationHubService(IConfiguration configuration)
         {
             var connectionString = configuration["NotificationHub:ConnectionString"];
             var hubName = configuration["NotificationHub:HubName"];
             _hubClient = NotificationHubClient.CreateClientFromConnectionString(connectionString, hubName);
         }

         public async Task SendNotificationAsync(string message)
         {
             var androidPayload = "{ \"data\" : {\"message\":\"" + message + "\"}}";
             var iosPayload = "{ \"aps\" : {\"alert\":\"" + message + "\"}}";

             await _hubClient.SendFcmNativeNotificationAsync(androidPayload);
             await _hubClient.SendAppleNativeNotificationAsync(iosPayload);
         }
     }
     ```

#### 4. Registro de Dispositivos

1. **Registro de Dispositivos**

   - Para enviar notificaciones a dispositivos específicos, primero debes registrar los dispositivos con Notification Hubs. Este proceso suele hacerse desde el cliente (aplicación móvil/web).

   - Aquí hay un ejemplo de cómo se puede registrar un dispositivo desde una aplicación móvil usando FCM.

     ```csharp
     using FirebaseAdmin;
     using FirebaseAdmin.Messaging;
     using Google.Apis.Auth.OAuth2;
     using System.Threading.Tasks;

     public class DeviceRegistrationService
     {
         public DeviceRegistrationService()
         {
             FirebaseApp.Create(new AppOptions()
             {
                 Credential = GoogleCredential.GetApplicationDefault()
             });
         }

         public async Task<string> RegisterDeviceAsync(string registrationToken)
         {
             var message = new Message()
             {
                 Token = registrationToken,
                 Notification = new Notification()
                 {
                     Title = "Registration",
                     Body = "Your device has been registered."
                 }
             };

             string response = await FirebaseMessaging.DefaultInstance.SendAsync(message);
             return response;
         }
     }
     ```

2. **Backend para Registro de Dispositivos**

   - En el backend de .NET Core, puedes exponer una API para recibir y registrar tokens de dispositivos.

     ```csharp
     [ApiController]
     [Route("api/[controller]")]
     public class RegistrationController : ControllerBase
     {
         private readonly NotificationHubService _notificationHubService;

         public RegistrationController(NotificationHubService notificationHubService)
         {
             _notificationHubService = notificationHubService;
         }

         [HttpPost]
         public async Task<IActionResult> Register([FromBody] DeviceRegistrationModel model)
         {
             // Registrar el dispositivo en Notification Hubs
             await _notificationHubService.RegisterDeviceAsync(model.Platform, model.Handle, model.Tags);
             return Ok();
         }
     }

     public class DeviceRegistrationModel
     {
         public string Platform { get; set; }
         public string Handle { get; set; }
         public string[] Tags { get; set; }
     }
     ```

#### 5. Personalización y Envío de Notificaciones Dirigidas

1. **Envío de Notificaciones Dirigidas**

   - Puedes enviar notificaciones dirigidas a dispositivos específicos o a grupos de dispositivos usando tags.

     ```csharp
     public async Task SendNotificationAsync(string message, string[] tags)
     {
         var androidPayload = "{ \"data\" : {\"message\":\"" + message + "\"}}";
         var iosPayload = "{ \"aps\" : {\"alert\":\"" + message + "\"}}";

         await _hubClient.SendFcmNativeNotificationAsync(androidPayload, tags);
         await _hubClient.SendAppleNativeNotificationAsync(iosPayload, tags);
     }
     ```

2. **Personalización de Notificaciones**

   - Puedes personalizar las notificaciones utilizando plantillas. Por ejemplo, una plantilla para notificaciones FCM puede lucir así:

     ```csharp
     var templatePayload = "{ \"data\" : {\"message\":\"$(message)\"}}";
     await _hubClient.SendTemplateNotificationAsync(templatePayload, tags);
     ```

#### 6. Manejo de Errores y Retries

1. **Errores en el Envío de Notificaciones**

   - Maneje excepciones que puedan ocurrir durante el envío de notificaciones.

     ```csharp
     try
     {
         await SendNotificationAsync("Hello, Notification Hub!");
     }
     catch (Exception ex)
     {
         Console.WriteLine($"Error sending notification: {ex.Message}");
     }
     ```

2. **Errores en el Registro de Dispositivos**

   - Capture y maneje errores durante el registro de dispositivos para asegurar que todos los dispositivos se registren correctamente.

     ```csharp
     [HttpPost]
     public async Task<IActionResult> Register([FromBody] DeviceRegistrationModel model)
     {
         try
         {
             await _notificationHubService.RegisterDeviceAsync(model.Platform, model.Handle, model.Tags);
             return Ok();
         }
         catch (Exception ex)
         {
             return StatusCode(500, $"Internal server error: {ex.Message}");
         }
     }
     ```

### Conclusión

Azure Notification Hubs es una herramienta poderosa para enviar notificaciones push a diversas plataformas de manera escalable y eficiente. En .NET Core, puedes utilizar el paquete `Microsoft.Azure.NotificationHubs` para interactuar con Notification Hubs, enviando notificaciones tanto de manera masiva como dirigida a dispositivos específicos. El proceso incluye la configuración del hub, el envío de notificaciones, el registro de dispositivos y el manejo de errores, permitiendo una integración robusta y funcional en tus aplicaciones.