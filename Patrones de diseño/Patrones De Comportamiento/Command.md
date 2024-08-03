### **2. Command**

El patrón Command encapsula una solicitud como un objeto, lo que permite parametrizar clientes con diferentes solicitudes, colas o solicitudes de deshacer.

**Ejemplo en .NET Core:**

```csharp
// Comando
public interface ICommand
{
    void Execute();
}

// Comando concreto
public class LightOnCommand : ICommand
{
    private readonly Light _light;

    public LightOnCommand(Light light)
    {
        _light = light;
    }

    public void Execute()
    {
        _light.TurnOn();
    }
}

public class Light
{
    public void TurnOn()
    {
        Console.WriteLine("Light is on");
    }
}

// Invocador
public class RemoteControl
{
    private ICommand _command;

    public void SetCommand(ICommand command)
    {
        _command = command;
    }

    public void PressButton()
    {
        _command.Execute();
    }
}
```

**Uso:**

```csharp
public class CommandExample
{
    public static void Run()
    {
        Light light = new Light();
        ICommand lightOn = new LightOnCommand(light);
        RemoteControl remote = new RemoteControl();

        remote.SetCommand(lightOn);
        remote.PressButton();  // Light is on
    }
}
```