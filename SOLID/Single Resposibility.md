#### **1. Single Responsibility Principle (SRP) - Principio de Responsabilidad Única**

El **Principio de Responsabilidad Única** (SRP, por sus siglas en inglés: Single Responsibility Principle) es uno de los principios SOLID en el diseño orientado a objetos y tiene un papel crucial en el desarrollo de software mantenible y flexible. En el contexto de .NET Core, aplicar SRP significa diseñar tus clases y módulos de manera que cada uno tenga una sola responsabilidad o razón para cambiar.

### ¿Qué es el Principio de Responsabilidad Única (SRP)?

El SRP establece que una clase o módulo debe tener una sola responsabilidad, es decir, una sola razón para cambiar. En otras palabras, cada clase o módulo debe encargarse de una única parte del funcionalidad del sistema, y esa responsabilidad debe estar completamente encapsulada dentro de la clase.

### Beneficios del SRP

1. **Mantenibilidad**: Facilita la comprensión y el mantenimiento del código, ya que cada clase tiene una única responsabilidad.
2. **Flexibilidad**: Permite realizar cambios en una parte del sistema sin afectar otras partes.
3. **Pruebas Unitarias**: Hace que las pruebas unitarias sean más simples y efectivas al permitir que cada clase se enfoque en una única funcionalidad.
4. **Desacoplamiento**: Reduce el acoplamiento entre componentes, haciendo que el sistema sea más modular.

### Ejemplo en .NET Core

#### Ejemplo Incorrecto (Violación de SRP)

Imaginemos una clase que maneja tanto la lógica de negocio como la persistencia de datos:

```csharp
public class Usuario
{
    public string Nombre { get; set; }
    public string Email { get; set; }

    public void Guardar()
    {
        // Lógica para guardar el usuario en la base de datos
        // Esto podría implicar la conexión a la base de datos, crear comandos SQL, etc.
        Console.WriteLine("Usuario guardado en la base de datos.");
    }

    public void EnviarEmail()
    {
        // Lógica para enviar un email al usuario
        // Esto podría implicar la configuración del servidor SMTP, construir el mensaje, etc.
        Console.WriteLine("Email enviado al usuario.");
    }
}
```

En este ejemplo, la clase `Usuario` tiene dos responsabilidades:
1. Manejo de datos del usuario.
2. Envío de correos electrónicos.

Esto viola el SRP porque hay más de una razón para cambiar esta clase: si se cambian los requisitos de persistencia de datos o el formato del correo electrónico, ambos cambios afectarían la misma clase.

#### Ejemplo Correcto (Cumpliendo SRP)

Para cumplir con SRP, se deben separar las responsabilidades en diferentes clases:

```csharp
public class Usuario
{
    public string Nombre { get; set; }
    public string Email { get; set; }
}

public interface IUsuarioRepository
{
    void Guardar(Usuario usuario);
}

public class UsuarioRepository : IUsuarioRepository
{
    public void Guardar(Usuario usuario)
    {
        // Lógica para guardar el usuario en la base de datos
        Console.WriteLine("Usuario guardado en la base de datos.");
    }
}

public interface IEmailService
{
    void EnviarEmail(string email, string mensaje);
}

public class EmailService : IEmailService
{
    public void EnviarEmail(string email, string mensaje)
    {
        // Lógica para enviar un email al usuario
        Console.WriteLine($"Email enviado a {email} con el mensaje: {mensaje}");
    }
}
```

En este diseño:
- La clase `Usuario` solo contiene datos relacionados con el usuario.
- `UsuarioRepository` se encarga de la persistencia de datos.
- `EmailService` se encarga de enviar correos electrónicos.

Cada clase tiene una sola responsabilidad y, por lo tanto, una sola razón para cambiar.

### Implementación en .NET Core

En .NET Core, puedes aplicar SRP en el contexto de aplicaciones web o servicios al dividir las responsabilidades en servicios y repositorios. Aquí hay un ejemplo básico de cómo podrías estructurar una aplicación web en .NET Core cumpliendo SRP:

#### Estructura del Proyecto

- **Models**: Contiene las clases de datos (e.g., `Usuario`).
- **Repositories**: Contiene la lógica de acceso a datos (e.g., `UsuarioRepository`).
- **Services**: Contiene la lógica de negocio y otras operaciones (e.g., `UsuarioService`, `EmailService`).
- **Controllers**: Maneja las solicitudes HTTP y delega en los servicios (e.g., `UsuarioController`).

#### Ejemplo en un Controlador

```csharp
public class UsuarioController : ControllerBase
{
    private readonly IUsuarioRepository _usuarioRepository;
    private readonly IEmailService _emailService;

    public UsuarioController(IUsuarioRepository usuarioRepository, IEmailService emailService)
    {
        _usuarioRepository = usuarioRepository;
        _emailService = emailService;
    }

    [HttpPost]
    public IActionResult CrearUsuario([FromBody] Usuario usuario)
    {
        _usuarioRepository.Guardar(usuario);
        _emailService.EnviarEmail(usuario.Email, "Bienvenido!");
        return Ok();
    }
}
```

Aquí, el controlador `UsuarioController` delega las responsabilidades de guardar datos y enviar correos a sus respectivos servicios, cumpliendo con el SRP.

El Principio de Responsabilidad Única es fundamental para mantener un diseño limpio y manejable en aplicaciones .NET Core. Al asegurar que cada clase o módulo tenga una sola responsabilidad, facilitas la mantenibilidad, pruebas y evolución de tu código. Implementar SRP de manera efectiva implica dividir las responsabilidades en clases o servicios específicos y utilizar patrones como el repositorio y el servicio para organizar el código de manera modular.
