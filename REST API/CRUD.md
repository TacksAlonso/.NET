#### **Operaciones CRUD (Crear, Leer, Actualizar, Eliminar)**
- **Crear (POST):** Utilizado para crear un nuevo recurso en el servidor. Ejemplo: `POST /api/users`.
- **Leer (GET):** Utilizado para obtener un recurso o una lista de recursos. Ejemplo: `GET /api/users/{id}`.
- **Actualizar (PUT/PATCH):** Utilizado para actualizar un recurso existente. `PUT` reemplaza todo el recurso, mientras que `PATCH` actualiza solo campos específicos. Ejemplo: `PUT /api/users/{id}` o `PATCH /api/users/{id}`.
- **Eliminar (DELETE):** Utilizado para eliminar un recurso. Ejemplo: `DELETE /api/users/{id}`.

Los verbos HTTP son métodos que indican la acción que se debe realizar sobre un recurso en una solicitud HTTP. Los verbos más comunes utilizados en APIs RESTful son:

### **Verbos HTTP**

1. **GET**
   - **Propósito:** Recuperar datos del servidor.
   - **Ejemplo de uso:** `GET /api/users` para obtener una lista de usuarios, o `GET /api/users/{id}` para obtener un usuario específico.
   - **Características:** No debe tener efectos secundarios (es decir, no debe modificar datos).

2. **POST**
   - **Propósito:** Enviar datos al servidor para crear un nuevo recurso.
   - **Ejemplo de uso:** `POST /api/users` para crear un nuevo usuario. Los datos del usuario se envían en el cuerpo de la solicitud.
   - **Características:** Puede modificar el estado del servidor al crear nuevos recursos.

3. **PUT**
   - **Propósito:** Actualizar un recurso existente en el servidor con la información proporcionada.
   - **Ejemplo de uso:** `PUT /api/users/{id}` para actualizar un usuario específico. Los datos del usuario actualizado se envían en el cuerpo de la solicitud.
   - **Características:** Reemplaza el recurso completo especificado por el identificador. Si el recurso no existe, a veces puede crear uno nuevo.

4. **PATCH**
   - **Propósito:** Aplicar modificaciones parciales a un recurso existente en el servidor.
   - **Ejemplo de uso:** `PATCH /api/users/{id}` para actualizar solo ciertos campos del usuario específico.
   - **Características:** Solo actualiza los campos especificados en la solicitud, dejando el resto del recurso intacto.

5. **DELETE**
   - **Propósito:** Eliminar un recurso del servidor.
   - **Ejemplo de uso:** `DELETE /api/users/{id}` para eliminar un usuario específico.
   - **Características:** Elimina el recurso especificado por el identificador.

6. **OPTIONS**
   - **Propósito:** Consultar qué métodos HTTP están permitidos para un recurso.
   - **Ejemplo de uso:** `OPTIONS /api/users` para obtener una lista de los métodos permitidos para el endpoint de usuarios.
   - **Características:** Utilizado principalmente para la configuración de CORS (Cross-Origin Resource Sharing) y para descubrir las capacidades de un recurso.

7. **HEAD**
   - **Propósito:** Obtener los encabezados de respuesta sin el cuerpo del mensaje.
   - **Ejemplo de uso:** `HEAD /api/users/{id}` para verificar si un usuario existe sin recuperar la información completa.
   - **Características:** Similar a `GET`, pero no devuelve el cuerpo de la respuesta.

### **Resumen de Operaciones CRUD con Verbos HTTP:**
- **Crear (POST)**
- **Leer (GET)**
- **Actualizar (PUT/PATCH)**
- **Eliminar (DELETE)**

Estos verbos permiten una comunicación efectiva con el servidor para realizar operaciones sobre los recursos en una API RESTful.


Claro, aquí te presento ejemplos detallados de cómo manejar cada verbo HTTP en una aplicación ASP.NET Core usando controladores. Cada verbo se utiliza para realizar diferentes tipos de operaciones en recursos. Estos ejemplos cubren la creación, lectura, actualización y eliminación de recursos en un API RESTful.

### **1. GET: Leer Recursos**

El verbo GET se usa para recuperar información de un servidor. Por ejemplo, puedes usar GET para obtener una lista de usuarios o un usuario específico.

```csharp
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Linq;

namespace MyApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class UsersController : ControllerBase
    {
        private static List<User> Users = new List<User>
        {
            new User { Id = 1, Name = "John Doe", Email = "john.doe@example.com" },
            new User { Id = 2, Name = "Jane Smith", Email = "jane.smith@example.com" }
        };

        // GET: api/users
        [HttpGet]
        public ActionResult<IEnumerable<User>> GetUsers()
        {
            return Ok(Users);
        }

        // GET: api/users/1
        [HttpGet("{id}")]
        public ActionResult<User> GetUser(int id)
        {
            var user = Users.FirstOrDefault(u => u.Id == id);
            if (user == null)
            {
                return NotFound();
            }
            return Ok(user);
        }
    }

    public class User
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
    }
}
```

### **2. POST: Crear Recursos**

El verbo POST se usa para enviar datos al servidor para crear un nuevo recurso.

```csharp
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace MyApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class UsersController : ControllerBase
    {
        private static List<User> Users = new List<User>();

        // POST: api/users
        [HttpPost]
        public ActionResult<User> CreateUser(User newUser)
        {
            newUser.Id = Users.Count + 1; // Simple ID assignment for demo purposes
            Users.Add(newUser);
            return CreatedAtAction(nameof(GetUser), new { id = newUser.Id }, newUser);
        }
    }

    public class User
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
    }
}
```

### **3. PUT: Actualizar Recursos**

El verbo PUT se usa para actualizar un recurso existente con nueva información.

```csharp
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Linq;

namespace MyApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class UsersController : ControllerBase
    {
        private static List<User> Users = new List<User>
        {
            new User { Id = 1, Name = "John Doe", Email = "john.doe@example.com" },
            new User { Id = 2, Name = "Jane Smith", Email = "jane.smith@example.com" }
        };

        // PUT: api/users/1
        [HttpPut("{id}")]
        public IActionResult UpdateUser(int id, User updatedUser)
        {
            var user = Users.FirstOrDefault(u => u.Id == id);
            if (user == null)
            {
                return NotFound();
            }

            user.Name = updatedUser.Name;
            user.Email = updatedUser.Email;
            return NoContent();
        }
    }

    public class User
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
    }
}
```

### **4. DELETE: Eliminar Recursos**

El verbo DELETE se usa para eliminar un recurso existente en el servidor.

```csharp
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Linq;

namespace MyApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class UsersController : ControllerBase
    {
        private static List<User> Users = new List<User>
        {
            new User { Id = 1, Name = "John Doe", Email = "john.doe@example.com" },
            new User { Id = 2, Name = "Jane Smith", Email = "jane.smith@example.com" }
        };

        // DELETE: api/users/1
        [HttpDelete("{id}")]
        public IActionResult DeleteUser(int id)
        {
            var user = Users.FirstOrDefault(u => u.Id == id);
            if (user == null)
            {
                return NotFound();
            }

            Users.Remove(user);
            return NoContent();
        }
    }

    public class User
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
    }
}
```

### **5. PATCH: Actualizar Recursos Parciales**

El verbo `PATCH` se utiliza para aplicar modificaciones parciales a un recurso existente. A diferencia de `PUT`, que reemplaza el recurso completo, `PATCH` permite actualizar solo los campos que se proporcionan.

```csharp
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Linq;

namespace MyApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class UsersController : ControllerBase
    {
        private static List<User> Users = new List<User>
        {
            new User { Id = 1, Name = "John Doe", Email = "john.doe@example.com" },
            new User { Id = 2, Name = "Jane Smith", Email = "jane.smith@example.com" }
        };

        // PATCH: api/users/1
        [HttpPatch("{id}")]
        public IActionResult PatchUser(int id, [FromBody] JsonPatchDocument<User> patchDoc)
        {
            var user = Users.FirstOrDefault(u => u.Id == id);
            if (user == null)
            {
                return NotFound();
            }

            patchDoc.ApplyTo(user, ModelState);
            if (!TryValidateModel(user))
            {
                return ValidationProblem(ModelState);
            }

            return NoContent();
        }
    }

    public class User
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
    }
}
```

- **Notas:**
  - Utiliza `JsonPatchDocument<T>` para aplicar los cambios parciales.
  - Asegúrate de validar el modelo después de aplicar los parches.

### **6. HEAD: Obtener Encabezados de Respuesta**

El verbo `HEAD` se utiliza para obtener solo los encabezados de una respuesta, sin el cuerpo del mensaje. Es útil para comprobar si un recurso existe o para obtener metadatos.

```csharp
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Linq;

namespace MyApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class UsersController : ControllerBase
    {
        private static List<User> Users = new List<User>
        {
            new User { Id = 1, Name = "John Doe", Email = "john.doe@example.com" },
            new User { Id = 2, Name = "Jane Smith", Email = "jane.smith@example.com" }
        };

        // HEAD: api/users/1
        [HttpHead("{id}")]
        public IActionResult HeadUser(int id)
        {
            var user = Users.FirstOrDefault(u => u.Id == id);
            if (user == null)
            {
                return NotFound();
            }

            return Ok();
        }
    }

    public class User
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
    }
}
```

- **Notas:**
  - La respuesta de `HEAD` no incluye el cuerpo del mensaje, solo los encabezados.

### **7. OPTIONS: Obtener Opciones de Comunicación**

El verbo `OPTIONS` se utiliza para describir las opciones de comunicación disponibles para el recurso de destino, como los métodos HTTP soportados.

```csharp
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Linq;

namespace MyApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class UsersController : ControllerBase
    {
        private static List<User> Users = new List<User>
        {
            new User { Id = 1, Name = "John Doe", Email = "john.doe@example.com" },
            new User { Id = 2, Name = "Jane Smith", Email = "jane.smith@example.com" }
        };

        // OPTIONS: api/users/1
        [HttpOptions("{id}")]
        public IActionResult OptionsUser(int id)
        {
            Response.Headers.Add("Allow", "GET, POST, PUT, DELETE, PATCH, OPTIONS");
            return Ok();
        }
    }

    public class User
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
    }
}
```

- **Notas:**
  - El encabezado `Allow` se utiliza para indicar los métodos HTTP permitidos para el recurso.

### **Resumen de los Métodos**

- **GET:** Se utiliza para obtener datos del servidor. Los métodos GET en el controlador permiten recuperar una lista de recursos o un recurso específico.
- **POST:** Se utiliza para enviar datos al servidor para crear un nuevo recurso. El método POST en el controlador permite agregar un nuevo recurso a la lista.
- **PUT:** Se utiliza para actualizar un recurso existente. El método PUT en el controlador permite modificar un recurso específico con nueva información.
- **DELETE:** Se utiliza para eliminar un recurso existente. El método DELETE en el controlador permite quitar un recurso específico de la lista.
- **PATCH:** Aplica cambios parciales a un recurso existente. Se usa para actualizaciones incrementales.
- **HEAD:** Similar a GET, pero solo recupera los encabezados de respuesta sin el cuerpo. Útil para verificar la existencia de un recurso y metadatos.
- **OPTIONS:** Permite al cliente descubrir qué métodos HTTP están permitidos en un recurso específico. Responde con los métodos permitidos en el encabezado `Allow`.


Estos ejemplos muestran cómo manejar las operaciones básicas de un API RESTful usando los verbos HTTP en ASP.NET Core.