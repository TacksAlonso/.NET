### **4. Abstracción**

**Definición:**
La abstracción es el proceso de simplificar un sistema al definir una interfaz que describe las operaciones que se pueden realizar sin especificar cómo se implementan. Permite a los desarrolladores trabajar con un nivel de detalle más alto y más general.

**Cómo se Implementa:**

- **Clases Abstractas:** Una clase abstracta es una clase que no se puede instanciar directamente y puede contener métodos abstractos (sin implementación) que deben ser implementados por las clases derivadas.

- **Interfaces:** Una interfaz es una colección de métodos sin implementación. Las clases que implementan una interfaz deben proporcionar implementaciones para todos los métodos definidos en la interfaz.

**Ejemplo de Clase Abstracta:**

```csharp
public abstract class Animal
{
    public abstract void MakeSound(); // Método abstracto

    public void Sleep()
    {
        Console.WriteLine("Sleeping...");
    }
}

public class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Bark");
    }
}
```

**Ejemplo de Interfaz:**

```csharp
public interface IShape
{
    double CalculateArea();
}

public class Rectangle : IShape
{
    public double Width { get; set; }
    public double Height { get; set; }

    public double CalculateArea()
    {
        return Width * Height;
    }
}
```

En el caso de la interfaz `IShape`, cualquier clase que implemente esta interfaz debe proporcionar una implementación para el método `CalculateArea`.

Claro, la abstracción es uno de los conceptos fundamentales de la programación orientada a objetos (OOP). En .NET Core, la abstracción permite a los desarrolladores definir clases y métodos abstractos que proporcionan una estructura para otras clases sin especificar todos los detalles de la implementación. Este enfoque ayuda a gestionar la complejidad al permitir que los desarrolladores se centren en el nivel de detalle relevante para la tarea en cuestión.

### Concepto de Abstracción

La abstracción se refiere a la capacidad de simplificar la representación de un objeto complejo mostrando solo sus características esenciales y ocultando los detalles innecesarios. En .NET Core, la abstracción se logra mediante el uso de clases y métodos abstractos.

### Clases Abstractas

Una clase abstracta es una clase que no puede ser instanciada directamente. Solo puede servir como base para otras clases. Contiene uno o más métodos abstractos que deben ser implementados por las clases derivadas.

#### Ejemplo de Clase Abstracta

```csharp
public abstract class Animal
{
    public string Nombre { get; set; }

    // Método abstracto (debe ser implementado por las clases derivadas)
    public abstract void HacerSonido();

    // Método no abstracto (opcionalmente puede ser sobrescrito por las clases derivadas)
    public void Dormir()
    {
        Console.WriteLine($"{Nombre} está durmiendo.");
    }
}

public class Perro : Animal
{
    public override void HacerSonido()
    {
        Console.WriteLine("El perro está ladrando.");
    }
}

public class Gato : Animal
{
    public override void HacerSonido()
    {
        Console.WriteLine("El gato está maullando.");
    }
}
```

En el ejemplo anterior, la clase `Animal` es abstracta y contiene un método abstracto `HacerSonido`. Las clases `Perro` y `Gato` derivan de `Animal` y proporcionan sus propias implementaciones de `HacerSonido`.

### Métodos Abstractos

Los métodos abstractos son declaraciones de métodos sin implementación. Deben ser implementados por las clases derivadas. Los métodos abstractos solo pueden existir dentro de clases abstractas.

#### Ejemplo de Métodos Abstractos

```csharp
public abstract class Figura
{
    public abstract double CalcularArea();

    public void Mostrar()
    {
        Console.WriteLine("Mostrando figura.");
    }
}

public class Circulo : Figura
{
    public double Radio { get; set; }

    public override double CalcularArea()
    {
        return Math.PI * Radio * Radio;
    }
}

public class Rectangulo : Figura
{
    public double Ancho { get; set; }
    public double Alto { get; set; }

    public override double CalcularArea()
    {
        return Ancho * Alto;
    }
}
```

En este ejemplo, la clase abstracta `Figura` define el método abstracto `CalcularArea`, que es implementado por las clases `Circulo` y `Rectangulo`.

### Interfaces y Abstracción

Además de las clases abstractas, las interfaces también proporcionan una forma de abstracción en .NET Core. Una interfaz define un contrato que las clases deben cumplir sin proporcionar ninguna implementación.

#### Ejemplo de Interfaces

```csharp
public interface ITransporte
{
    void Mover();
}

public class Coche : ITransporte
{
    public void Mover()
    {
        Console.WriteLine("El coche se está moviendo.");
    }
}

public class Avion : ITransporte
{
    public void Mover()
    {
        Console.WriteLine("El avión está volando.");
    }
}
```

En este ejemplo, la interfaz `ITransporte` define el método `Mover`, que debe ser implementado por cualquier clase que implemente la interfaz.

### Ventajas de la Abstracción

1. **Ocultamiento de la Complejidad**: La abstracción permite ocultar los detalles de implementación y mostrar solo las funcionalidades esenciales, lo que facilita la comprensión del código.
2. **Reutilización de Código**: Las clases abstractas y las interfaces permiten la reutilización de código, ya que las clases derivadas pueden compartir una estructura común.
3. **Mantenibilidad**: Al reducir la complejidad y aumentar la modularidad, la abstracción hace que el código sea más fácil de mantener y actualizar.
4. **Flexibilidad**: Las interfaces y las clases abstractas proporcionan flexibilidad, permitiendo que diferentes clases implementen la misma funcionalidad de diferentes maneras.

En .NET, no existen diferentes "tipos" de clases abstractas per se; una clase abstracta es una clase que no puede ser instanciada directamente y puede contener métodos abstractos (sin implementación) y no abstractos (con implementación). Sin embargo, las clases abstractas pueden variar en complejidad y propósito, dependiendo de cómo se utilicen dentro de una jerarquía de clases.

### Tipos de Clases Abstractas Basados en su Uso

1. **Clases Abstractas Simples**: Contienen solo métodos abstractos que deben ser implementados por las clases derivadas.
2. **Clases Abstractas Complejas**: Pueden contener tanto métodos abstractos como métodos concretos, propiedades, y otros miembros con implementación.
3. **Clases Abstractas Base**: Sirven como base genérica para múltiples clases derivadas que comparten un comportamiento común.

### Ejemplos de Clases Abstractas en .NET

#### Ejemplo 1: Clase Abstracta Simple

```csharp
public abstract class Forma
{
    public abstract double CalcularArea();
}
```

#### Ejemplo 2: Clase Abstracta Compleja

```csharp
public abstract class Vehiculo
{
    public string Marca { get; set; }
    public string Modelo { get; set; }

    public void Arrancar()
    {
        Console.WriteLine("El vehículo está arrancando.");
    }

    public abstract void Conducir();
}
```

#### Ejemplo 3: Clase Abstracta Base

```csharp
public abstract class Empleado
{
    public string Nombre { get; set; }
    public string Posicion { get; set; }

    public abstract double CalcularSalario();

    public void MostrarInformacion()
    {
        Console.WriteLine($"Nombre: {Nombre}, Posición: {Posicion}");
    }
}

public class EmpleadoTiempoCompleto : Empleado
{
    public double SalarioMensual { get; set; }

    public override double CalcularSalario()
    {
        return SalarioMensual;
    }
}

public class EmpleadoPorHoras : Empleado
{
    public double TarifaPorHora { get; set; }
    public int HorasTrabajadas { get; set; }

    public override double CalcularSalario()
    {
        return TarifaPorHora * HorasTrabajadas;
    }
}
```

#### Uso de Clases Abstractas

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        Empleado empleado1 = new EmpleadoTiempoCompleto
        {
            Nombre = "Alice",
            Posicion = "Desarrollador",
            SalarioMensual = 5000
        };

        Empleado empleado2 = new EmpleadoPorHoras
        {
            Nombre = "Bob",
            Posicion = "Consultor",
            TarifaPorHora = 50,
            HorasTrabajadas = 100
        };

        empleado1.MostrarInformacion();
        Console.WriteLine($"Salario: {empleado1.CalcularSalario()}");

        empleado2.MostrarInformacion();
        Console.WriteLine($"Salario: {empleado2.CalcularSalario()}");
    }
}
```

### Ventajas y Desventajas de las Clases Abstractas

#### Ventajas

1. **Reutilización de Código**: Facilitan la reutilización de código compartiendo la lógica común entre varias clases derivadas.
2. **Claridad**: Proporcionan una estructura clara para las jerarquías de clases.
3. **Mantenimiento**: Facilitan el mantenimiento del código, ya que los cambios en la clase base se reflejan automáticamente en las clases derivadas.

#### Desventajas

1. **Rigidez**: Pueden ser menos flexibles que las interfaces, ya que una clase solo puede heredar de una clase abstracta.
2. **Complejidad**: Pueden aumentar la complejidad del diseño si no se usan correctamente.
