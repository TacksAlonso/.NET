Sí, el término **composición** en la programación orientada a objetos se refiere a una técnica para construir clases utilizando otras clases en lugar de heredar de ellas. En lugar de extender una clase base (herencia), una clase usa instancias de otras clases para agregar funcionalidad. Este enfoque promueve un diseño más flexible y menos acoplado, favoreciendo la reutilización del código.

### Composición vs. Herencia

- **Herencia**: Permite que una clase derive de otra y herede sus miembros. Es útil para crear una jerarquía de clases, pero puede llevar a un acoplamiento fuerte y a problemas de herencia múltiple.
- **Composición**: Permite que una clase incluya instancias de otras clases para delegar funcionalidades. Es útil para crear relaciones "tiene-un" (has-a) en lugar de "es-un" (is-a).

### Beneficios de la Composición

1. **Flexibilidad**: Permite cambiar o agregar comportamientos sin modificar las clases existentes.
2. **Desacoplamiento**: Reduce el acoplamiento entre clases, facilitando el mantenimiento y la evolución del código.
3. **Reutilización**: Facilita la reutilización de componentes sin necesidad de heredar de clases base.

### Ejemplos de Composición en .NET Core

#### Ejemplo 1: Composición Simple

Supongamos que estamos construyendo una aplicación de gestión de vehículos. En lugar de heredar de una clase base `Vehiculo`, podemos usar composición para agregar funcionalidades específicas.

```csharp
public class Motor
{
    public void Encender()
    {
        Console.WriteLine("Motor encendido.");
    }

    public void Apagar()
    {
        Console.WriteLine("Motor apagado.");
    }
}

public class Coche
{
    private Motor _motor;

    public Coche()
    {
        _motor = new Motor();
    }

    public void Arrancar()
    {
        _motor.Encender();
        Console.WriteLine("Coche arrancado.");
    }

    public void Apagar()
    {
        _motor.Apagar();
        Console.WriteLine("Coche apagado.");
    }
}
```

En este ejemplo, la clase `Coche` tiene una instancia de `Motor` en lugar de heredar de `Motor`. Esto permite que `Coche` use `Motor` para manejar las operaciones relacionadas con el motor.

#### Ejemplo 2: Composición con Interfaces

La composición también se puede utilizar con interfaces para proporcionar una implementación flexible y reutilizable.

```csharp
public interface IManejable
{
    void Manejar();
}

public class Volante : IManejable
{
    public void Manejar()
    {
        Console.WriteLine("Volante en uso.");
    }
}

public class Coche
{
    private IManejable _manejo;

    public Coche(IManejable manejo)
    {
        _manejo = manejo;
    }

    public void Conducir()
    {
        _manejo.Manejar();
        Console.WriteLine("Coche en movimiento.");
    }
}
```

Aquí, `Coche` puede usar cualquier implementación de `IManejable`. Esto permite cambiar la forma en que se maneja el coche simplemente pasando una instancia diferente de `IManejable`.

#### Ejemplo 3: Composición para Comportamientos Diversos

Supongamos que tenemos diferentes tipos de notificaciones que un sistema puede usar. En lugar de heredar de una clase base `Notificador`, utilizamos composición para delegar las notificaciones.

```csharp
public interface INotificador
{
    void Enviar(string mensaje);
}

public class EmailNotificador : INotificador
{
    public void Enviar(string mensaje)
    {
        Console.WriteLine($"Enviando email: {mensaje}");
    }
}

public class SmsNotificador : INotificador
{
    public void Enviar(string mensaje)
    {
        Console.WriteLine($"Enviando SMS: {mensaje}");
    }
}

public class ServicioDeNotificacion
{
    private INotificador _notificador;

    public ServicioDeNotificacion(INotificador notificador)
    {
        _notificador = notificador;
    }

    public void Notificar(string mensaje)
    {
        _notificador.Enviar(mensaje);
    }
}
```

En este caso, `ServicioDeNotificacion` puede usar cualquier implementación de `INotificador`, permitiendo que el comportamiento de notificación se ajuste sin modificar `ServicioDeNotificacion`.

### Ventajas de la Composición

1. **Escalabilidad**: Puedes agregar nuevas funcionalidades sin alterar las clases existentes.
2. **Pruebas Unitarias**: Facilita la prueba de componentes individuales al permitir el reemplazo de dependencias.
3. **Diseño Flexible**: Permite el uso de diferentes implementaciones de un componente sin necesidad de cambiar el diseño general del sistema.

La composición y la inyección de dependencias están estrechamente relacionadas y a menudo se usan juntas en el diseño de software, especialmente en sistemas basados en .NET Core. Ambas técnicas se utilizan para promover un diseño desacoplado y flexible, pero tienen enfoques y propósitos específicos:

### Composición

La **composición** es una técnica de diseño donde una clase usa instancias de otras clases para delegar ciertas funcionalidades. Es una forma de construir objetos combinando otros objetos, en lugar de usar la herencia para extender comportamientos.

#### Ejemplo de Composición

```csharp
public class Motor
{
    public void Encender() { /* ... */ }
}

public class Coche
{
    private Motor _motor;

    public Coche()
    {
        _motor = new Motor(); // Composición
    }

    public void Arrancar()
    {
        _motor.Encender();
    }
}
```

En este ejemplo, la clase `Coche` tiene una instancia de `Motor` y delega la funcionalidad de encender el motor a esa instancia. Aquí, `Coche` está compuesto por un `Motor`.

### Inyección de Dependencias (DI)

La **inyección de dependencias** es una técnica que facilita la inversión de control (IoC) al proporcionar las dependencias de una clase desde el exterior en lugar de crear esas dependencias dentro de la clase. Es una forma de implementar la composición, especialmente en un marco de trabajo que soporta DI como .NET Core.

En .NET Core, la inyección de dependencias se usa ampliamente para gestionar y resolver dependencias automáticamente a través del contenedor de servicios.

#### Ejemplo de Inyección de Dependencias

```csharp
public interface IMotor
{
    void Encender();
}

public class Motor : IMotor
{
    public void Encender() { /* ... */ }
}

public class Coche
{
    private readonly IMotor _motor;

    public Coche(IMotor motor) // Inyección de dependencias
    {
        _motor = motor;
    }

    public void Arrancar()
    {
        _motor.Encender();
    }
}
```

En este ejemplo, `Coche` no crea una instancia de `Motor` por sí misma. En cambio, recibe una instancia de `IMotor` a través de su constructor. Esto permite que `Coche` sea más flexible y fácil de probar.

### Configuración de DI en .NET Core

En .NET Core, la inyección de dependencias se configura en el archivo `Startup.cs` o `Program.cs` (dependiendo de la versión de .NET Core o .NET 5+).

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddTransient<IMotor, Motor>();
        services.AddTransient<Coche>();
    }
}
```

En este código, `IMotor` se registra para que el contenedor de DI proporcione instancias de `Motor` cuando se solicite un `IMotor`. `Coche` también se registra para que el contenedor pueda proporcionar instancias de `Coche` con sus dependencias inyectadas.

### Comparación

1. **Composición**:
   - Se refiere a la técnica general de construir clases utilizando otras clases.
   - Es una forma de delegar funcionalidad a otros objetos.
   - Puedes implementar composición manualmente en cualquier código.

2. **Inyección de Dependencias (DI)**:
   - Es una forma específica de implementar la composición mediante un contenedor de servicios.
   - Automatiza la creación y el suministro de dependencias a través de un contenedor.
   - Facilita el desacoplamiento y la gestión de la vida útil de los objetos.

La inyección de dependencias es una técnica avanzada para aplicar composición de manera eficiente y flexible, especialmente en aplicaciones grandes y complejas. Utiliza un contenedor de servicios para gestionar la creación y el ciclo de vida de las dependencias, mientras que la composición en general se refiere a la técnica de construir objetos a partir de otros objetos. Ambas técnicas se complementan para promover un diseño desacoplado y modular en el desarrollo de software.
