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