### **11. Visitor**

El patrón Visitor permite definir una nueva operación sin cambiar las clases de los elementos sobre los que opera. Un visitante acepta un objeto y realiza una operación sobre él.

**Ejemplo en .NET Core:**

```csharp
// Elemento
public interface IElement
{
    void Accept(IVisitor visitor);
}

// Visitantes
public interface IVisitor
{
    void Visit(ConcreteElementA element);
    void Visit(ConcreteElementB element);
}

public class ConcreteElementA : IElement
{
    public void Accept(IVisitor visitor)
    {
        visitor.Visit(this);
    }

    public string OperationA()
    {
        return "Element A operation";
    }
}

public class ConcreteElementB : IElement
{
    public void Accept(IVisitor visitor)
    {
        visitor.Visit(this);
    }

    public string OperationB()
    {
        return "Element B operation";
    }
}

public class ConcreteVisitor : IVisitor
{
    public void Visit(ConcreteElementA element)
    {
        Console.WriteLine(element.OperationA());
    }

    public void Visit(ConcreteElementB element)
    {
        Console.WriteLine(element.OperationB());
    }
}
```

**Uso:**

```csharp
public class VisitorExample
{
    public static void Run()
    {
        IElement elementA = new ConcreteElementA();
        IElement elementB = new ConcreteElementB();
        IVisitor visitor = new ConcreteVisitor();

        elementA.Accept(visitor);
        elementB.Accept(visitor);
    }
}
```