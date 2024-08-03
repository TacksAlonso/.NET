La **composición** es un principio fundamental en la programación orientada a objetos que se refiere a una forma de construir clases mediante la combinación de otras clases. A diferencia de la herencia, donde una clase deriva de otra, la composición se basa en el concepto de "tener un" en lugar de "ser un". Esto significa que una clase puede contener objetos de otras clases como parte de su propia definición.

### **Definición de Composición**

La composición es una técnica de diseño en la que una clase utiliza instancias de otras clases para construir su funcionalidad. En lugar de heredar de una clase base, una clase puede incluir una o más instancias de otras clases para delegar tareas a esos objetos. Esto fomenta la reutilización del código y la modularidad, y ayuda a crear sistemas más flexibles y mantenibles.

### **Ventajas de la Composición**

1. **Flexibilidad:** La composición permite cambiar la implementación de los objetos compuestos sin alterar la clase que los utiliza.
2. **Reutilización del Código:** Puedes reutilizar las funcionalidades de las clases composicionales en diferentes contextos.
3. **Menor Acoplamiento:** Promueve la separación de responsabilidades, lo que reduce la dependencia entre clases.

### **Cómo se Implementa la Composición**

**Ejemplo en C#:**

Imagina que estás construyendo un sistema para representar vehículos. Puedes tener una clase `Engine` y una clase `Car`. En lugar de que `Car` herede de `Engine`, puedes usar composición para que `Car` tenga un `Engine` como parte de su definición.

```csharp
// Clase Engine
public class Engine
{
    public int Horsepower { get; set; }

    public void Start()
    {
        Console.WriteLine("Engine started.");
    }
}

// Clase Car que usa composición
public class Car
{
    public Engine Engine { get; private set; }
    public string Model { get; set; }

    public Car(string model, Engine engine)
    {
        Model = model;
        Engine = engine;
    }

    public void Drive()
    {
        Engine.Start();
        Console.WriteLine($"Driving {Model}");
    }
}
```

**Uso del Código:**

```csharp
Engine myEngine = new Engine { Horsepower = 150 };
Car myCar = new Car("Sedan", myEngine);

myCar.Drive();
```

En este ejemplo:

- La clase `Engine` proporciona una funcionalidad específica (arrancar el motor).
- La clase `Car` tiene una instancia de `Engine` como uno de sus miembros. Esto significa que un `Car` puede usar un `Engine` para realizar acciones relacionadas con el motor, como arrancar.

### **Comparación con Herencia**

**Herencia:**

- **"Es un"**: La herencia representa una relación "es un", como un `Dog` que "es un" `Animal`.
- **Acoplamiento más fuerte**: Cambiar la clase base puede afectar a todas las clases derivadas.

**Composición:**

- **"Tiene un"**: La composición representa una relación "tiene un", como un `Car` que "tiene un" `Engine`.
- **Acoplamiento más débil**: Las clases composicionales pueden cambiar sin afectar a las clases que las utilizan, siempre que la interfaz pública se mantenga consistente.

La composición es especialmente útil cuando se quiere diseñar sistemas en los que la flexibilidad y la reutilización son importantes. Permite construir clases complejas a partir de componentes más simples, manteniendo el código limpio y fácil de mantener.