### **3. Polimorfismo**

**Definición:**
El polimorfismo permite que un objeto se comporte de diferentes maneras dependiendo del contexto. Hay dos tipos principales: polimorfismo de sobrecarga y polimorfismo de sobreescritura.

**Cómo se Implementa:**

- **Sobrecarga:** Permite definir varios métodos con el mismo nombre pero diferentes parámetros en la misma clase.

- **Sobreescritura:** Permite redefinir un método en una clase derivada que ya está definido en la clase base. Se utiliza la palabra clave `virtual` en la clase base y `override` en la clase derivada.

**Ejemplo:**

```csharp
public class Animal
{
    public virtual void MakeSound()
    {
        Console.WriteLine("Some generic animal sound");
    }
}

public class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Bark");
    }
}

public class Cat : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Meow");
    }
}
```

Aquí, `MakeSound` es un método polimórfico que se comporta de manera diferente según la clase derivada (`Dog` o `Cat`).

Claro, el polimorfismo es uno de los pilares fundamentales de la programación orientada a objetos (OOP). En .NET Core, el polimorfismo permite a los objetos de diferentes clases ser tratados a través de una interfaz común, lo que facilita el diseño de sistemas flexibles y escalables. Aquí tienes una explicación detallada del polimorfismo en .NET Core, junto con ejemplos prácticos.

### Concepto de Polimorfismo

El polimorfismo permite que el mismo método tenga diferentes comportamientos según el objeto que lo invoque. En términos simples, significa "muchas formas". Existen dos tipos principales de polimorfismo en OOP:

1. **Polimorfismo en tiempo de compilación (Sobrecarga de métodos)**
2. **Polimorfismo en tiempo de ejecución (Sobrescritura de métodos)**

### Polimorfismo en Tiempo de Compilación

La sobrecarga de métodos es una forma de polimorfismo en tiempo de compilación donde varios métodos en la misma clase tienen el mismo nombre pero diferentes parámetros.

#### Ejemplo de Sobrecarga de Métodos

```csharp
public class Calculadora
{
    // Sobrecarga del método Sumar
    public int Sumar(int a, int b)
    {
        return a + b;
    }

    public double Sumar(double a, double b)
    {
        return a + b;
    }

    public int Sumar(int a, int b, int c)
    {
        return a + b + c;
    }
}

// Uso de la sobrecarga de métodos
public class Program
{
    public static void Main(string[] args)
    {
        Calculadora calc = new Calculadora();
        Console.WriteLine(calc.Sumar(2, 3));       // Llama a Sumar(int, int)
        Console.WriteLine(calc.Sumar(2.5, 3.5));   // Llama a Sumar(double, double)
        Console.WriteLine(calc.Sumar(1, 2, 3));    // Llama a Sumar(int, int, int)
    }
}
```

### Polimorfismo en Tiempo de Ejecución

La sobrescritura de métodos es una forma de polimorfismo en tiempo de ejecución donde una clase derivada implementa un método definido en la clase base. Esto se logra mediante el uso de las palabras clave `virtual` y `override`.

#### Ejemplo de Sobrescritura de Métodos

```csharp
public class Animal
{
    public string Nombre { get; set; }

    // Método virtual que puede ser sobrescrito por una clase derivada
    public virtual void HacerSonido()
    {
        Console.WriteLine($"{Nombre} está haciendo un sonido.");
    }
}

public class Perro : Animal
{
    // Sobrescribe el método virtual de la clase base
    public override void HacerSonido()
    {
        Console.WriteLine($"{Nombre} está ladrando.");
    }
}

public class Gato : Animal
{
    // Sobrescribe el método virtual de la clase base
    public override void HacerSonido()
    {
        Console.WriteLine($"{Nombre} está maullando.");
    }
}

// Uso del polimorfismo en tiempo de ejecución
public class Program
{
    public static void Main(string[] args)
    {
        List<Animal> animales = new List<Animal>
        {
            new Perro { Nombre = "Firulais" },
            new Gato { Nombre = "Mish" }
        };

        foreach (var animal in animales)
        {
            animal.HacerSonido(); // Llama al método sobrescrito según el tipo de objeto
        }
    }
}
```

### Interfaces y Polimorfismo

Las interfaces también juegan un papel crucial en el polimorfismo. Una interfaz define un contrato que las clases deben cumplir, lo que permite que diferentes clases implementen la misma interfaz y sean tratadas de manera uniforme.

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

// Uso del polimorfismo con interfaces
public class Program
{
    public static void Main(string[] args)
    {
        List<ITransporte> transportes = new List<ITransporte>
        {
            new Coche(),
            new Avion()
        };

        foreach (var transporte in transportes)
        {
            transporte.Mover(); // Llama al método implementado según el tipo de objeto
        }
    }
}
```

### Ventajas del Polimorfismo

1. **Flexibilidad y Reutilización**: Permite escribir código más flexible y reutilizable. Las funciones pueden operar en objetos de diferentes clases sin saber exactamente qué clase es.
2. **Extensibilidad**: Facilita la adición de nuevas clases que implementen interfaces o hereden de clases base sin necesidad de modificar el código existente.
3. **Mantenimiento**: Hace que el código sea más fácil de mantener y extender, ya que las nuevas funcionalidades se pueden agregar sin afectar a las clases existentes.

En .NET, una interfaz es un contrato que define un conjunto de métodos, propiedades, eventos o indexadores que una clase o una estructura debe implementar. Las interfaces permiten que clases y estructuras de tipos distintos compartan un conjunto común de capacidades sin necesidad de compartir una implementación específica.

### Características de las Interfaces

1. **Definición de Contratos**: Las interfaces solo contienen las declaraciones de miembros (sin implementación). Las clases o estructuras que implementan la interfaz deben proporcionar la implementación de estos miembros.
2. **Polimorfismo**: Las interfaces permiten que diferentes clases se traten de manera uniforme si implementan la misma interfaz, facilitando el uso del polimorfismo.
3. **Herencia Múltiple**: En C#, una clase puede implementar múltiples interfaces, permitiendo una forma de herencia múltiple.

### Sintaxis de las Interfaces

La palabra clave `interface` se utiliza para definir una interfaz. Aquí tienes un ejemplo básico de una interfaz:

```csharp
public interface IAnimal
{
    void HacerSonido();
    void Comer();
}
```

### Implementación de una Interfaz

Una clase que implementa una interfaz debe proporcionar implementaciones para todos los miembros de la interfaz:

```csharp
public class Perro : IAnimal
{
    public void HacerSonido()
    {
        Console.WriteLine("El perro está ladrando.");
    }

    public void Comer()
    {
        Console.WriteLine("El perro está comiendo.");
    }
}

public class Gato : IAnimal
{
    public void HacerSonido()
    {
        Console.WriteLine("El gato está maullando.");
    }

    public void Comer()
    {
        Console.WriteLine("El gato está comiendo.");
    }
}
```

### Uso de la Interfaz

Puedes usar la interfaz para crear objetos y llamarlos a través de sus métodos comunes:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        IAnimal miPerro = new Perro();
        IAnimal miGato = new Gato();

        miPerro.HacerSonido(); // Salida: El perro está ladrando.
        miPerro.Comer();       // Salida: El perro está comiendo.

        miGato.HacerSonido(); // Salida: El gato está maullando.
        miGato.Comer();       // Salida: El gato está comiendo.
    }
}
```

### Interfaces y Polimorfismo

Las interfaces permiten utilizar el polimorfismo, lo que significa que puedes tratar objetos de diferentes clases de manera uniforme si implementan la misma interfaz. Esto es útil para escribir código genérico y reutilizable.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        List<IAnimal> animales = new List<IAnimal>
        {
            new Perro(),
            new Gato()
        };

        foreach (var animal in animales)
        {
            animal.HacerSonido();
            animal.Comer();
        }
    }
}
```

### Propiedades en Interfaces

Las interfaces también pueden definir propiedades:

```csharp
public interface IVehiculo
{
    string Marca { get; set; }
    void Conducir();
}

public class Coche : IVehiculo
{
    public string Marca { get; set; }

    public void Conducir()
    {
        Console.WriteLine($"El coche de marca {Marca} está conduciendo.");
    }
}
```

### Ventajas de Usar Interfaces

1. **Desacoplamiento**: Permiten desacoplar el código, lo que facilita el mantenimiento y la prueba.
2. **Flexibilidad**: Facilitan la implementación de componentes intercambiables.
3. **Reutilización de Código**: Permiten reutilizar código a través de diferentes clases que implementan la misma interfaz.

A partir de C# 8.0, .NET introdujo la posibilidad de definir métodos con implementación por defecto (default methods) en interfaces, una característica similar a la que existe en Java desde Java 8. Esto permite que las interfaces no solo definan contratos, sino que también proporcionen implementaciones por defecto para algunos de sus métodos.

### Implementaciones por Defecto en Interfaces

Las implementaciones por defecto permiten a las interfaces añadir métodos sin romper las implementaciones existentes. Esto es especialmente útil para añadir nuevas funcionalidades a las interfaces existentes sin necesidad de cambiar todas las clases que ya las implementan.

#### Ejemplo de Implementación por Defecto

```csharp
public interface IAnimal
{
    void HacerSonido();

    // Método con implementación por defecto
    void Dormir()
    {
        Console.WriteLine("El animal está durmiendo.");
    }
}

public class Perro : IAnimal
{
    public void HacerSonido()
    {
        Console.WriteLine("El perro está ladrando.");
    }
}

public class Gato : IAnimal
{
    public void HacerSonido()
    {
        Console.WriteLine("El gato está maullando.");
    }

    // Sobrescribir el método con implementación por defecto
    public void Dormir()
    {
        Console.WriteLine("El gato está durmiendo en su cama.");
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        IAnimal miPerro = new Perro();
        IAnimal miGato = new Gato();

        miPerro.HacerSonido(); // Salida: El perro está ladrando.
        miPerro.Dormir();      // Salida: El animal está durmiendo.

        miGato.HacerSonido();  // Salida: El gato está maullando.
        miGato.Dormir();       // Salida: El gato está durmiendo en su cama.
    }
}
```

### Ventajas de las Implementaciones por Defecto

1. **Compatibilidad hacia Atrás**: Permite añadir nuevos métodos a las interfaces sin romper las clases existentes que ya las implementan.
2. **Reutilización de Código**: Permite proporcionar una implementación común que puede ser reutilizada por todas las clases que implementan la interfaz.
3. **Flexibilidad**: Las clases que implementan la interfaz pueden optar por sobrescribir los métodos con implementación por defecto si necesitan un comportamiento diferente.

### Consideraciones

- **Simplicidad**: Aunque las implementaciones por defecto proporcionan flexibilidad, es importante usarlas con moderación para mantener las interfaces simples y claras.
- **Diseño de Interfaces**: Las interfaces deben seguir siendo principalmente contratos que definen capacidades y comportamientos, no deben ser usadas como un lugar principal para implementar lógica de negocio compleja.

Las palabras clave `virtual` y `override` en C# son fundamentales para implementar el polimorfismo, uno de los principios clave de la programación orientada a objetos (OOP). Aquí tienes una explicación detallada de cada una:

### `virtual`

La palabra clave `virtual` se utiliza en la declaración de un método en una clase base para indicar que el método puede ser anulado (sobrescrito) por clases derivadas. Cuando un método se declara como `virtual`, se permite que una clase derivada proporcione su propia implementación de ese método.

#### Ejemplo de `virtual`

```csharp
public class Animal
{
    public string Nombre { get; set; }

    // Método virtual que puede ser sobrescrito por una clase derivada
    public virtual void HacerSonido()
    {
        Console.WriteLine($"{Nombre} está haciendo un sonido.");
    }
}
```

### `override`

La palabra clave `override` se utiliza en una clase derivada para anular (sobrescribir) un método virtual o abstracto de la clase base. Esto permite que la clase derivada proporcione una implementación específica del método que ya está definido en la clase base.

#### Ejemplo de `override`

```csharp
public class Perro : Animal
{
    // Sobrescribe el método virtual de la clase base
    public override void HacerSonido()
    {
        Console.WriteLine($"{Nombre} está ladrando.");
    }
}

public class Gato : Animal
{
    // Sobrescribe el método virtual de la clase base
    public override void HacerSonido()
    {
        Console.WriteLine($"{Nombre} está maullando.");
    }
}
```

### Uso de `virtual` y `override` juntos

Al combinar `virtual` y `override`, se permite que las clases derivadas proporcionen implementaciones específicas para métodos que se comportan de manera general en la clase base.

#### Ejemplo completo

```csharp
public class Animal
{
    public string Nombre { get; set; }

    // Método virtual en la clase base
    public virtual void HacerSonido()
    {
        Console.WriteLine($"{Nombre} está haciendo un sonido.");
    }
}

public class Perro : Animal
{
    // Sobrescribe el método virtual de la clase base
    public override void HacerSonido()
    {
        Console.WriteLine($"{Nombre} está ladrando.");
    }
}

public class Gato : Animal
{
    // Sobrescribe el método virtual de la clase base
    public override void HacerSonido()
    {
        Console.WriteLine($"{Nombre} está maullando.");
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        Animal miAnimal = new Animal { Nombre = "Animal" };
        miAnimal.HacerSonido(); // Salida: Animal está haciendo un sonido.

        Perro miPerro = new Perro { Nombre = "Firulais" };
        miPerro.HacerSonido(); // Salida: Firulais está ladrando.

        Gato miGato = new Gato { Nombre = "Mish" };
        miGato.HacerSonido(); // Salida: Mish está maullando.
    }
}
```

- **`virtual`**: Utilizada en la clase base para permitir que un método sea sobrescrito en una clase derivada.
- **`override`**: Utilizada en la clase derivada para proporcionar una implementación específica de un método virtual definido en la clase base.

Estas palabras clave permiten que el comportamiento de los métodos en las clases derivadas se adapte y especialice según las necesidades específicas, manteniendo al mismo tiempo una interfaz común definida en la clase base. Esto es esencial para el polimorfismo, permitiendo que una clase base defina comportamientos generales que pueden ser especificados en las clases derivadas.
