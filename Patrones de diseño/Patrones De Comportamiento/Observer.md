### **7. Observer**

El patrón Observer define una dependencia uno a muchos entre objetos, de modo que cuando un objeto cambia de estado, todos sus dependientes son notificados y actualizados automáticamente.

**Ejemplo en .NET Core:**

```csharp
// Sujeto
public class Subject
{
    private readonly List<IObserver> _observers = new List<IObserver>();

    public void Attach(IObserver observer)
    {
        _

observers.Add(observer);
    }

    public void Detach(IObserver observer)
    {
        _observers.Remove(observer);
    }

    public void Notify(string message)
    {
        foreach (var observer in _observers)
        {
            observer.Update(message);
        }
    }
}

// Observador
public interface IObserver
{
    void Update(string message);
}

// Observador concreto
public class ConcreteObserver : IObserver
{
    private readonly string _name;

    public ConcreteObserver(string name)
    {
        _name = name;
    }

    public void Update(string message)
    {
        Console.WriteLine($"{_name} received message: {message}");
    }
}
```

**Uso:**

```csharp
public class ObserverExample
{
    public static void Run()
    {
        Subject subject = new Subject();

        IObserver observer1 = new ConcreteObserver("Observer 1");
        IObserver observer2 = new ConcreteObserver("Observer 2");

        subject.Attach(observer1);
        subject.Attach(observer2);

        subject.Notify("Hello Observers!");
    }
}
```