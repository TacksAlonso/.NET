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