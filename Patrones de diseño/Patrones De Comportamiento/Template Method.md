### **10. Template Method**

El patrón Template Method define el esqueleto de un algoritmo en una operación, defiriendo algunos pasos a las subclases. El patrón permite que las subclases redefinan ciertos pasos del algoritmo sin cambiar su estructura.

**Ejemplo en .NET Core:**

```csharp
// Clase abstracta
public abstract class AbstractClass
{
    public void TemplateMethod()
    {
        Step1();
        Step2();
        Step3();
    }

    protected abstract void Step1();
    protected abstract void Step2();

    protected void Step3()
    {
        Console.WriteLine("Common step");
    }
}

// Clase concreta
public class ConcreteClass : AbstractClass
{
    protected override void Step1()
    {
        Console.WriteLine("Step 1 implementation");
    }

    protected override void Step2()
    {
        Console.WriteLine("Step 2 implementation");
    }
}
```

**Uso:**

```csharp
public class TemplateMethodExample
{
    public static void Run()
    {
        AbstractClass myClass = new ConcreteClass();
        myClass.TemplateMethod();
    }
}
```