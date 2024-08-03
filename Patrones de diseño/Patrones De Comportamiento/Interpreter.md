### **3. Interpreter**

El patrón Interpreter define una gramática para un lenguaje y proporciona un intérprete para interpretar sentencias en ese lenguaje.

**Ejemplo en .NET Core:**

```csharp
// Expresión abstracta
public interface IExpression
{
    int Interpret(Dictionary<string, int> context);
}

// Expresión concreta
public class Number : IExpression
{
    private readonly int _number;

    public Number(int number)
    {
        _number = number;
    }

    public int Interpret(Dictionary<string, int> context)
    {
        return _number;
    }
}

public class Addition : IExpression
{
    private readonly IExpression _left;
    private readonly IExpression _right;

    public Addition(IExpression left, IExpression right)
    {
        _left = left;
        _right = right;
    }

    public int Interpret(Dictionary<string, int> context)
    {
        return _left.Interpret(context) + _right.Interpret(context);
    }
}
```

**Uso:**

```csharp
public class InterpreterExample
{
    public static void Run()
    {
        IExpression five = new Number(5);
        IExpression three = new Number(3);
        IExpression add = new Addition(five, three);

        Console.WriteLine("Result: " + add.Interpret(null));  // Result: 8
    }
}
```