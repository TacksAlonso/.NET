### **2. Herencia**

**Definición:**
La herencia es el mecanismo por el cual una clase puede heredar características (métodos y propiedades) de otra clase. La clase que hereda se denomina clase derivada o subclase, mientras que la clase de la que se hereda se denomina clase base o superclase.

**Cómo se Implementa:**

- **Palabra Clave `:base`:** Usa `:` para indicar que una clase hereda de otra.

**Ejemplo:**

```csharp
public class Animal
{
    public void Eat()
    {
        Console.WriteLine("Eating...");
    }
}

public class Dog : Animal
{
    public void Bark()
    {
        Console.WriteLine("Barking...");
    }
}
```

En este ejemplo, `Dog` hereda el método `Eat` de la clase `Animal` y agrega un método adicional `Bark`.

Claro, la herencia es uno de los pilares fundamentales de la programación orientada a objetos (OOP) y juega un papel crucial en .NET Core. La herencia permite crear nuevas clases basadas en clases existentes, reutilizando y extendiendo su funcionalidad. Aquí te proporcionaré una explicación detallada de la herencia en .NET Core, acompañada de ejemplos prácticos.

### Concepto de Herencia

La herencia permite definir una nueva clase que toma todas las funcionalidades (propiedades y métodos) de una clase existente y puede agregar nuevas funcionalidades o modificar las existentes. La clase existente se denomina "clase base" (o superclase), y la nueva clase se denomina "clase derivada" (o subclase).

### Beneficios de la Herencia

1. **Reutilización de Código**: Permite reutilizar el código existente, lo que reduce la duplicación y facilita el mantenimiento.
2. **Extensibilidad**: Permite extender la funcionalidad de las clases existentes sin modificar su código.
3. **Jerarquía de Clases**: Facilita la creación de jerarquías de clases lógicas que reflejan relaciones del mundo real.

### Sintaxis de la Herencia en .NET Core

Para definir una clase derivada que herede de una clase base en .NET Core, se utiliza el siguiente formato:

```csharp
public class ClaseBase
{
    // Propiedades y métodos de la clase base
}

public class ClaseDerivada : ClaseBase
{
    // Propiedades y métodos adicionales de la clase derivada
}
```

### Ejemplo de Herencia

Supongamos que tenemos una clase `Animal` como clase base y queremos crear una clase `Perro` como clase derivada.

```csharp
// Clase base
public class Animal
{
    public string Nombre { get; set; }
    public int Edad { get; set; }

    public void Comer()
    {
        Console.WriteLine($"{Nombre} está comiendo.");
    }

    public void Dormir()
    {
        Console.WriteLine($"{Nombre} está durmiendo.");
    }
}

// Clase derivada
public class Perro : Animal
{
    public string Raza { get; set; }

    public void Ladrar()
    {
        Console.WriteLine($"{Nombre} está ladrando.");
    }
}
```

### Uso de las Clases

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        Perro miPerro = new Perro();
        miPerro.Nombre = "Firulais";
        miPerro.Edad = 3;
        miPerro.Raza = "Labrador";

        miPerro.Comer();  // Método heredado de la clase base
        miPerro.Dormir(); // Método heredado de la clase base
        miPerro.Ladrar(); // Método de la clase derivada
    }
}
```

### Sobrescritura de Métodos (Polimorfismo)

La herencia también permite a las clases derivadas sobrescribir métodos de la clase base para cambiar o extender su comportamiento. Esto se hace utilizando las palabras clave `virtual` y `override`.

```csharp
public class Animal
{
    public string Nombre { get; set; }

    public virtual void HacerSonido()
    {
        Console.WriteLine($"{Nombre} está haciendo un sonido.");
    }
}

public class Perro : Animal
{
    public override void HacerSonido()
    {
        Console.WriteLine($"{Nombre} está ladrando.");
    }
}

public class Gato : Animal
{
    public override void HacerSonido()
    {
        Console.WriteLine($"{Nombre} está maullando.");
    }
}
```

### Uso de las Clases con Sobrescritura de Métodos

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        Animal miAnimal = new Animal { Nombre = "Animal" };
        miAnimal.HacerSonido();

        Perro miPerro = new Perro { Nombre = "Firulais" };
        miPerro.HacerSonido();

        Gato miGato = new Gato { Nombre = "Mish" };
        miGato.HacerSonido();
    }
}
```

### Acceso a Constructores de la Clase Base

Las clases derivadas pueden llamar a los constructores de la clase base usando la palabra clave `base`.

```csharp
public class Animal
{
    public string Nombre { get; set; }

    public Animal(string nombre)
    {
        Nombre = nombre;
    }
}

public class Perro : Animal
{
    public string Raza { get; set; }

    public Perro(string nombre, string raza) : base(nombre)
    {
        Raza = raza;
    }
}
```

### Uso de Constructores

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        Perro miPerro = new Perro("Firulais", "Labrador");
        Console.WriteLine($"{miPerro.Nombre} es un {miPerro.Raza}");
    }
}
```
