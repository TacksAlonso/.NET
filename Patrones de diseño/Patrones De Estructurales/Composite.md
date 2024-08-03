### **3. Composite**

El patrón Composite permite que los objetos se compongan en estructuras de árbol para representar jerarquías parte-todo. Los clientes tratan de manera uniforme tanto a los objetos individuales como a las composiciones de objetos.

**Ejemplo en .NET Core:**

```csharp
// Componente
public abstract class Component
{
    public abstract void Operation();
}

// Hoja
public class Leaf : Component
{
    public override void Operation()
    {
        Console.WriteLine("Leaf operation");
    }
}

// Compuesto
public class Composite : Component
{
    private readonly List<Component> _children = new List<Component>();

    public void Add(Component component)
    {
        _children.Add(component);
    }

    public void Remove(Component component)
    {
        _children.Remove(component);
    }

    public override void Operation()
    {
        foreach (var child in _children)
        {
            child.Operation();
        }
    }
}
```

**Uso:**

```csharp
public class CompositeExample
{
    public static void Run()
    {
        Component leaf1 = new Leaf();
        Component leaf2 = new Leaf();
        Composite composite = new Composite();
        composite.Add(leaf1);
        composite.Add(leaf2);

        composite.Operation();
        // Output:
        // Leaf operation
        // Leaf operation
    }
}
```