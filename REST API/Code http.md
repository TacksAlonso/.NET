Los códigos de estado HTTP son respuestas del servidor que indican el resultado de la solicitud realizada por el cliente. Estos códigos se dividen en cinco categorías, cada una representada por el primer dígito del código. Aquí tienes una lista amplia con sus respectivas descripciones:

### **1xx: Informativos**
Estos códigos indican que la solicitud ha sido recibida y está siendo procesada, pero aún no se ha completado.

- **100 Continue:** El servidor ha recibido la primera parte de la solicitud y el cliente debe continuar enviando el resto.
- **101 Switching Protocols:** El servidor acepta el cambio de protocolo solicitado por el cliente.
- **102 Processing:** El servidor ha recibido y está procesando la solicitud, pero no hay una respuesta disponible aún. (WebDAV)

### **2xx: Éxito**
Estos códigos indican que la solicitud fue recibida, comprendida y aceptada con éxito.

- **200 OK:** La solicitud ha tenido éxito y la respuesta contiene el resultado esperado.
- **201 Created:** La solicitud ha tenido éxito y ha resultado en la creación de un nuevo recurso.
- **202 Accepted:** La solicitud ha sido aceptada para procesamiento, pero el procesamiento no se ha completado.
- **203 Non-Authoritative Information:** La solicitud ha tenido éxito, pero la información proporcionada puede no ser la más actual.
- **204 No Content:** La solicitud ha tenido éxito, pero no hay contenido que devolver en la respuesta.
- **205 Reset Content:** La solicitud ha tenido éxito y el cliente debe reiniciar la vista del recurso.
- **206 Partial Content:** La solicitud ha tenido éxito y el servidor está devolviendo solo una parte del recurso solicitado.

### **3xx: Redirección**
Estos códigos indican que se requiere acción adicional por parte del cliente para completar la solicitud.

- **300 Multiple Choices:** Hay múltiples opciones para el recurso solicitado. El cliente debe elegir una.
- **301 Moved Permanently:** El recurso solicitado ha sido movido permanentemente a una nueva URL. El cliente debe usar la nueva URL en futuras solicitudes.
- **302 Found:** El recurso solicitado se encuentra en una URL temporal. El cliente debe usar la URL temporal solo para esta solicitud.
- **303 See Other:** El recurso puede encontrarse en una URL diferente que debe ser solicitada mediante un método GET.
- **304 Not Modified:** El recurso no ha sido modificado desde la última solicitud. El cliente puede usar la versión en caché.
- **305 Use Proxy:** El recurso debe ser accedido a través de un proxy especificado en la URL. (Obsoleto)
- **306 Switch Proxy:** Código reservado para futuros usos. (Obsoleto)
- **307 Temporary Redirect:** El recurso se ha movido temporalmente a una nueva URL, y el cliente debe usar la URL temporal solo para esta solicitud.
- **308 Permanent Redirect:** El recurso se ha movido permanentemente a una nueva URL, y el cliente debe usar la nueva URL en futuras solicitudes.

### **4xx: Error del Cliente**
Estos códigos indican que la solicitud realizada por el cliente es incorrecta o no puede ser procesada.

- **400 Bad Request:** La solicitud es incorrecta o no se puede procesar debido a una sintaxis inválida.
- **401 Unauthorized:** La solicitud requiere autenticación del usuario. El cliente debe proporcionar credenciales válidas.
- **402 Payment Required:** Este código está reservado para futuros usos y no se utiliza actualmente.
- **403 Forbidden:** El cliente no tiene permiso para acceder al recurso solicitado.
- **404 Not Found:** El recurso solicitado no se pudo encontrar en el servidor.
- **405 Method Not Allowed:** El método HTTP utilizado en la solicitud no está permitido para el recurso solicitado.
- **406 Not Acceptable:** El recurso solicitado no puede generar una respuesta que sea aceptable según las cabeceras de aceptación del cliente.
- **407 Proxy Authentication Required:** El cliente debe autenticar con el proxy.
- **408 Request Timeout:** La solicitud del cliente ha tardado demasiado tiempo en llegar y ha sido cancelada.
- **409 Conflict:** La solicitud no pudo completarse debido a un conflicto con el estado actual del recurso.
- **410 Gone:** El recurso solicitado ya no está disponible y no se puede acceder a él.
- **411 Length Required:** El servidor requiere que la solicitud especifique el tamaño del contenido.
- **412 Precondition Failed:** Una de las condiciones previas especificadas en la solicitud falló.
- **413 Payload Too Large:** El tamaño del contenido de la solicitud es demasiado grande para que el servidor lo procese.
- **414 URI Too Long:** La URI solicitada es demasiado larga para que el servidor la procese.
- **415 Unsupported Media Type:** El tipo de medio de la solicitud no es compatible con el servidor.
- **416 Range Not Satisfiable:** El rango especificado en la solicitud no puede ser satisfecho por el servidor.
- **417 Expectation Failed:** La expectativa indicada en el encabezado de solicitud no se puede cumplir por el servidor.
- **426 Upgrade Required:** El servidor requiere que el cliente utilice un protocolo diferente.

### **5xx: Error del Servidor**
Estos códigos indican que el servidor falló al procesar una solicitud válida.

- **500 Internal Server Error:** El servidor encontró un error inesperado que impidió completar la solicitud.
- **501 Not Implemented:** El servidor no reconoce el método de solicitud o no tiene capacidad para cumplirlo.
- **502 Bad Gateway:** El servidor, al actuar como una puerta de enlace o proxy, recibió una respuesta inválida del servidor upstream.
- **503 Service Unavailable:** El servidor no está disponible actualmente, generalmente debido a sobrecarga o mantenimiento.
- **504 Gateway Timeout:** El servidor, al actuar como una puerta de enlace o proxy, no recibió una respuesta a tiempo del servidor upstream.
- **505 HTTP Version Not Supported:** El servidor no soporta la versión del protocolo HTTP utilizada en la solicitud.

Estos códigos de estado proporcionan información importante sobre el resultado de una solicitud y ayudan a los clientes a manejar las respuestas adecuadamente.