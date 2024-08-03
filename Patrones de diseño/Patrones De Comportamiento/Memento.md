### **6. Memento**

El patrón Memento permite capturar y externalizar el estado interno de un objeto sin violar la encapsulación, de modo que el objeto pueda restaurarse a ese estado más tarde.

**Ejemplo en .NET Core:**

```csharp
// Memento
public class Memento
{
    public string State { get; }

    public Memento(string state)
    {
        State = state;
    }
}

// Originador
public class Originator
{
    public string State { get; set; }

    public Memento SaveStateToMemento()
    {
        return new Memento(State);
    }

    public void GetStateFromMemento(Memento memento)
    {
        State = memento.State;
    }
}

// Cuidador de Memento
public class Caretaker
{
    private Memento _memento;

    public void SaveState(Originator originator)
    {
        _memento = originator.SaveStateToMemento();
    }

    public void RestoreState(Originator originator)
    {
        originator.GetStateFromMemento(_memento);
    }
}
```

**Uso:**

```csharp
public class MementoExample
{
    public static void Run()
    {
        Originator originator = new Originator();
        Caretaker caretaker = new Caretaker();

        originator.State = "State #1";
        caretaker.SaveState(originator);

        originator.State = "State #2";
        Console.WriteLine("Current State: " + originator.State);

        caretaker.RestoreState(originator);
        Console.WriteLine("Restored State: " + originator.State);
    }
}
```