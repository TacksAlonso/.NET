#### **1. Azure Service Bus**

**Fundamento:**
Azure Service Bus es un servicio de mensajería en la nube que permite la comunicación entre aplicaciones y servicios distribuidos. Proporciona una plataforma confiable para la transferencia de mensajes y soporta el patrón de comunicación asincrónica.

**Características:**
- **Colas:** Permiten que los mensajes se envíen a una cola y sean procesados por los consumidores.
- **Temas y suscripciones:** Permiten la publicación de mensajes a múltiples suscriptores que reciben los mensajes según sus filtros.
- **Fiabilidad:** Soporta transacciones y garantiza la entrega de mensajes.

**Ejemplo:**

Supongamos que tienes una aplicación que procesa pedidos y deseas enviar mensajes a un servicio de procesamiento de pagos. Puedes usar Azure Service Bus para manejar esta comunicación.

1. **Crear un Service Bus Namespace:**
   - En el portal de Azure, crea un nuevo "Namespace" de Service Bus.

2. **Crear una Cola:**
   - Dentro del Namespace, crea una nueva cola llamada `OrderQueue`.

3. **Enviar un Mensaje a la Cola:**
   ```csharp
   var connectionString = "<ServiceBusConnectionString>";
   var queueName = "OrderQueue";

   var client = new QueueClient(connectionString, queueName);

   var message = new Message(Encoding.UTF8.GetBytes("Order details"));
   await client.SendAsync(message);
   ```

4. **Recibir un Mensaje de la Cola:**
   ```csharp
   var receiver = new QueueClient(connectionString, queueName, ReceiveMode.PeekLock);

   receiver.RegisterMessageHandler(
       async (message, token) =>
       {
           var orderDetails = Encoding.UTF8.GetString(message.Body);
           // Procesar el mensaje
           await receiver.CompleteAsync(message.SystemProperties.LockToken);
       },
       new MessageHandlerOptions(exceptionReceivedHandler) { MaxConcurrentCalls = 1, AutoComplete = false });
   ```

#### **2. Azure Storage Account & Key Vault**

**Azure Storage Account:**

**Fundamento:**
Azure Storage Account proporciona almacenamiento en la nube para datos estructurados y no estructurados. Incluye diferentes servicios como blobs, colas, tablas y archivos.

**Características:**
- **Blobs:** Almacenamiento de datos no estructurados, como imágenes y videos.
- **Files:** Almacenamiento de archivos accesibles mediante el protocolo SMB.
- **Queues:** Almacenamiento de mensajes para aplicaciones distribuidas.

**Ejemplo:**

1. **Crear una Cuenta de Almacenamiento:**
   - En el portal de Azure, crea una nueva cuenta de almacenamiento.

2. **Subir un Blob:**
   ```csharp
   var storageAccount = CloudStorageAccount.Parse("<StorageAccountConnectionString>");
   var blobClient = storageAccount.CreateCloudBlobClient();
   var container = blobClient.GetContainerReference("mycontainer");
   await container.CreateIfNotExistsAsync();

   var blockBlob = container.GetBlockBlobReference("myfile.txt");
   await blockBlob.UploadTextAsync("Hello, Azure!");
   ```

**Azure Key Vault:**

**Fundamento:**
Azure Key Vault es un servicio para almacenar y gestionar secretos, claves de cifrado y certificados. Permite asegurar los secretos y acceder a ellos de manera segura.

**Características:**
- **Secretos:** Almacena contraseñas, cadenas de conexión y otros datos sensibles.
- **Claves:** Maneja claves de cifrado y realiza operaciones criptográficas.

**Ejemplo:**

1. **Crear un Key Vault:**
   - En el portal de Azure, crea un nuevo Key Vault.

2. **Añadir un Secreto:**
   ```csharp
   var keyVaultUrl = "https://<YourKeyVaultName>.vault.azure.net/";
   var client = new SecretClient(new Uri(keyVaultUrl), new DefaultAzureCredential());
   
   await client.SetSecretAsync("MySecret", "SecretValue");
   ```

3. **Leer un Secreto:**
   ```csharp
   var secret = await client.GetSecretAsync("MySecret");
   var secretValue = secret.Value.Value;
   ```

#### **3. Azure Event Hub**

**Fundamento:**
Azure Event Hub es un servicio de ingesta de datos en tiempo real que permite la ingestión de grandes volúmenes de datos desde diversas fuentes y su procesamiento en tiempo real.

**Características:**
- **Eventos:** Recibe y procesa eventos de múltiples fuentes.
- **Particiones:** Permite la distribución de eventos para el procesamiento paralelo.

**Ejemplo:**

1. **Crear un Event Hub Namespace:**
   - En el portal de Azure, crea un nuevo Namespace de Event Hub.

2. **Crear un Event Hub:**
   - Dentro del Namespace, crea un nuevo Event Hub llamado `myEventHub`.

3. **Enviar un Evento:**
   ```csharp
   var connectionString = "<EventHubConnectionString>";
   var eventHubName = "myEventHub";

   var producer = new EventHubProducerClient(connectionString, eventHubName);
   using var eventBatch = await producer.CreateBatchAsync();

   eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Event data")));
   await producer.SendAsync(eventBatch);
   ```

4. **Recibir un Evento:**
   ```csharp
   var consumer = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName);

   await foreach (PartitionEvent partitionEvent in consumer.ReadEventsAsync())
   {
       var data = Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray());
       // Procesar el evento
   }
   ```

#### **4. Azure Event Grid**

**Fundamento:**
Azure Event Grid es un servicio de enrutamiento de eventos que permite la creación de aplicaciones basadas en eventos al manejar eventos y dirigirlos a diferentes destinos.

**Características:**
- **Eventos:** Proporciona un modelo basado en eventos para conectar aplicaciones y servicios.
- **Suscripciones:** Permite definir cómo y dónde se deben entregar los eventos.

**Ejemplo:**

1. **Crear un Topic de Event Grid:**
   - En el portal de Azure, crea un nuevo Topic de Event Grid.

2. **Publicar un Evento:**
   ```csharp
   var topicEndpoint = "<EventGridTopicEndpoint>";
   var topicKey = "<EventGridTopicKey>";
   var client = new EventGridPublisherClient(new Uri(topicEndpoint), new AzureKeyCredential(topicKey));

   var eventData = new EventGridEvent("myEvent", "EventType", "1.0", "Event data");
   await client.SendEventAsync(eventData);
   ```

3. **Suscribirse a un Evento:**
   - Configura una suscripción en el portal de Azure para que los eventos se dirijan a un endpoint específico, como una función de Azure o una cola de almacenamiento.

### **Resumen:**

- **Service Bus:** Servicio de mensajería para la comunicación entre aplicaciones distribuidas, soporta colas y temas.
- **Storage Account:** Almacenamiento en la nube para datos estructurados y no estructurados, incluye blobs, archivos, colas y tablas.
- **Key Vault:** Servicio para almacenar y gestionar secretos, claves y certificados de manera segura.
- **Event Hub:** Servicio de ingesta de datos en tiempo real para procesar grandes volúmenes de eventos.
- **Event Grid:** Servicio de enrutamiento de eventos que permite la conexión de aplicaciones basadas en eventos.

Estos componentes son fundamentales para construir aplicaciones y sistemas escalables en la nube, y su integración permite una arquitectura más robusta y eficiente en Azure.