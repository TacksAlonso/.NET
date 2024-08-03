#### **3. Liskov Substitution Principle (LSP) - Principio de Sustitución de Liskov**

**Fundamento:**
El principio de sustitución de Liskov establece que los objetos de una clase base deben poder ser reemplazados por objetos de una clase derivada sin alterar el correcto funcionamiento del programa. En otras palabras, las subclases deben ser sustituibles por sus clases base.

**Ejemplo:**

Supongamos que tenemos una clase base `Bird` y una subclase `Penguin`:

```csharp
public class Bird
{
    public virtual void Fly()
    {
        // Implementación para volar
    }
}

public class Penguin : Bird
{
    public override void Fly()
    {
        throw new NotSupportedException("Penguins can't fly");
    }
}
```

Aquí, `Penguin` no puede volar, lo que viola el LSP si `Fly` es esperado para todas las subclases de `Bird`.

**Solución Aplicando LSP:**

Refactorizar para que `Fly` no sea parte de la clase base si no es aplicable a todas las subclases:

```csharp
public abstract class Bird
{
    // Métodos y propiedades comunes a todos los pájaros
}

public interface IFlyable
{
    void Fly();
}

public class Sparrow : Bird, IFlyable
{
    public void Fly()
    {
        // Implementación para volar
    }
}

public class Penguin : Bird
{
    // Implementación para pingüinos sin volar
}
```

De esta manera, solo las clases que realmente pueden volar implementan `IFlyable`, respetando el LSP.