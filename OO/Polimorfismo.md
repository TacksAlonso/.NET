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