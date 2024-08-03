### **5. Mediator**

El patrón Mediator define un objeto que encapsula cómo interactúan un conjunto de objetos. Promueve el acoplamiento débil al evitar que los objetos se refieran explícitamente entre sí y permite que se comuniquen a través de un objeto mediador.

**Ejemplo en .NET Core:**

```csharp
// Mediador
public class Mediator
{
    private readonly List<Colleague> _colleagues = new List<Colleague>();

    public void Register(Colleague colleague)
    {
        _colleagues.Add(colleague);
    }

    public void Send(string message, Colleague sender)
    {
        foreach (var colleague in _colleagues)
        {
            if (colleague != sender)
            {
                colleague.Receive(message);
            }
        }
    }
}

// Colega abstracto
public abstract class Colleague
{
    protected Mediator _mediator;

    protected Colleague(Mediator mediator)
    {
        _mediator = mediator;
    }

    public abstract void Receive(string message);
    public abstract void Send(string message);
}

// Colega concreto
public class ConcreteColleague : Colleague
{
    public ConcreteColleague(Mediator mediator) : base(mediator) { }

    public override void Receive(string message)
    {
        Console.WriteLine("Received: " + message);
    }

    public override void Send(string message)
    {
        _mediator.Send(message, this);
    }
}
```

**Uso:**

```csharp
public class MediatorExample
{
    public static void Run()
    {
        Mediator mediator = new Mediator();

        Colleague colleague1 = new ConcreteColleague(mediator);
        Colleague colleague2 = new ConcreteColleague(mediator);

        mediator.Register(colleague1);
        mediator.Register(colleague2);

        colleague1.Send("Hello from colleague 1");
    }
}
```