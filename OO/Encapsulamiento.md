### **1. Encapsulación**

**Definición:**
La encapsulación es el concepto de ocultar los detalles internos de un objeto y exponer sólo lo necesario para su uso. Esto se logra mediante la creación de clases que encapsulan datos y comportamientos en una sola unidad.

**Cómo se Implementa:**

- **Modificadores de Acceso:** Utiliza modificadores de acceso (`private`, `protected`, `public`) para controlar la visibilidad de los miembros de una clase. Los datos sensibles se mantienen como `private` y se accede a ellos a través de métodos `public` (métodos de acceso o setters/getters).

**Ejemplo:**

```csharp
public class Person
{
    private string name;
    private int age;

    // Constructor
    public Person(string name, int age)
    {
        this.name = name;
        this.age = age;
    }

    // Método público para obtener el nombre
    public string GetName()
    {
        return name;
    }

    // Método público para establecer el nombre
    public void SetName(string name)
    {
        this.name = name;
    }

    // Método público para obtener la edad
    public int GetAge()
    {
        return age;
    }

    // Método público para establecer la edad
    public void SetAge(int age)
    {
        if (age > 0)
        {
            this.age = age;
        }
    }
}
```

La encapsulación es uno de los principios fundamentales de la programación orientada a objetos que se utiliza para mejorar la modularidad, la reutilización del código y la protección de los datos. La encapsulación se implementa principalmente a través de clases y control de acceso a los miembros de las clases (propiedades y métodos). Aquí tienes una explicación más detallada:

### Concepto de Encapsulación

La encapsulación en .NET Core se refiere a la agrupación de datos (campos) y métodos (funciones o procedimientos) que operan sobre esos datos en una única unidad llamada clase. Los detalles internos de la clase se ocultan del exterior y solo se exponen las interfaces necesarias para interactuar con los objetos de esa clase. Esto se logra mediante modificadores de acceso.

### Modificadores de Acceso

.NET Core proporciona varios modificadores de acceso para controlar la visibilidad de los miembros de una clase:

- **public**: Los miembros con este modificador son accesibles desde cualquier lugar del código.
- **private**: Los miembros con este modificador solo son accesibles desde dentro de la misma clase.
- **protected**: Los miembros con este modificador son accesibles dentro de su clase y en las clases derivadas.
- **internal**: Los miembros con este modificador son accesibles solo dentro del mismo ensamblado (proyecto).
- **protected internal**: Combina las restricciones de `protected` y `internal`.
- **private protected**: Los miembros con este modificador son accesibles solo dentro de su clase y en clases derivadas que estén en el mismo ensamblado.

### Propiedades y Métodos

Para implementar la encapsulación, se suelen utilizar propiedades en lugar de exponer directamente los campos. Las propiedades permiten controlar cómo se accede y modifica el estado interno de la clase.

```csharp
public class Persona
{
    // Campo privado
    private string nombre;
    
    // Propiedad pública
    public string Nombre
    {
        get { return nombre; }
        set 
        { 
            if (!string.IsNullOrWhiteSpace(value))
            {
                nombre = value;
            }
        }
    }

    // Método privado
    private void MetodoPrivado()
    {
        // Lógica interna
    }

    // Método público
    public void MetodoPublico()
    {
        // Puede acceder a métodos y campos privados
        MetodoPrivado();
    }
}
```

En este ejemplo, el campo `nombre` es privado y solo puede ser accedido y modificado a través de la propiedad pública `Nombre`. La propiedad incluye lógica para validar el valor antes de asignarlo al campo, protegiendo así la integridad del objeto.

### Beneficios de la Encapsulación

1. **Protección de Datos**: Los datos sensibles o críticos se pueden proteger del acceso y modificación indebidos, manteniendo la integridad del objeto.
2. **Modularidad**: Las clases se pueden modificar y mejorar sin afectar el código que las utiliza, siempre que la interfaz pública permanezca consistente.
3. **Mantenibilidad**: La encapsulación facilita el mantenimiento del código, ya que los cambios internos no afectan al exterior.
4. **Reusabilidad**: Las clases bien encapsuladas se pueden reutilizar en diferentes contextos y proyectos.

### Ejemplo Completo

Imaginemos una clase `CuentaBancaria` en .NET Core:

```csharp
public class CuentaBancaria
{
    // Campo privado
    private decimal saldo;

    // Propiedad pública
    public decimal Saldo
    {
        get { return saldo; }
        private set 
        {
            if (value >= 0)
            {
                saldo = value;
            }
        }
    }

    // Constructor
    public CuentaBancaria(decimal saldoInicial)
    {
        Saldo = saldoInicial;
    }

    // Método público
    public void Depositar(decimal cantidad)
    {
        if (cantidad > 0)
        {
            Saldo += cantidad;
        }
    }

    // Método público
    public void Retirar(decimal cantidad)
    {
        if (cantidad > 0 && cantidad <= Saldo)
        {
            Saldo -= cantidad;
        }
    }

    // Método privado
    private void RegistrarTransaccion(string tipo, decimal cantidad)
    {
        // Lógica de registro
    }
}
```

En este ejemplo, `saldo` es un campo privado y solo puede ser modificado a través de la propiedad `Saldo`, que tiene un `set` privado. Los métodos públicos `Depositar` y `Retirar` permiten modificar el saldo, mientras que el método privado `RegistrarTransaccion` se utiliza internamente para registrar las transacciones sin exponer su implementación fuera de la clase.

### 1. Public (`public`)

Los miembros con el modificador `public` son accesibles desde cualquier parte del código.

```csharp
public class EjemploPublico
{
    // Campo público
    public int CampoPublico;

    // Propiedad pública
    public int PropiedadPublica { get; set; }

    // Método público
    public void MetodoPublico()
    {
        // Lógica del método
    }
}

// Uso del miembro público desde otra clase
var ejemplo = new EjemploPublico();
ejemplo.CampoPublico = 10;
ejemplo.PropiedadPublica = 20;
ejemplo.MetodoPublico();
```

### 2. Private (`private`)

Los miembros con el modificador `private` solo son accesibles desde dentro de la misma clase.

```csharp
public class EjemploPrivado
{
    // Campo privado
    private int CampoPrivado;

    // Propiedad privada
    private int PropiedadPrivada { get; set; }

    // Método privado
    private void MetodoPrivado()
    {
        // Lógica del método
    }

    // Método público para acceder a miembros privados
    public void MetodoPublico()
    {
        CampoPrivado = 10;
        PropiedadPrivada = 20;
        MetodoPrivado();
    }
}

// Uso de la clase desde otra clase
var ejemplo = new EjemploPrivado();
ejemplo.MetodoPublico(); // No se puede acceder directamente a CampoPrivado, PropiedadPrivada ni MetodoPrivado
```

### 3. Protected (`protected`)

Los miembros con el modificador `protected` son accesibles dentro de su clase y en las clases derivadas.

```csharp
public class Base
{
    // Campo protegido
    protected int CampoProtegido;

    // Método protegido
    protected void MetodoProtegido()
    {
        // Lógica del método
    }
}

public class Derivada : Base
{
    public void MetodoDerivado()
    {
        CampoProtegido = 10;
        MetodoProtegido();
    }
}

// Uso de la clase derivada
var derivada = new Derivada();
derivada.MetodoDerivado(); // No se puede acceder directamente a CampoProtegido ni MetodoProtegido
```

### 4. Internal (`internal`)

Los miembros con el modificador `internal` son accesibles solo dentro del mismo ensamblado (proyecto).

```csharp
internal class EjemploInterno
{
    // Campo interno
    internal int CampoInterno;

    // Método interno
    internal void MetodoInterno()
    {
        // Lógica del método
    }
}

// Uso del miembro interno dentro del mismo ensamblado
var ejemplo = new EjemploInterno();
ejemplo.CampoInterno = 10;
ejemplo.MetodoInterno();
```

### 5. Protected Internal (`protected internal`)

Los miembros con el modificador `protected internal` son accesibles dentro del mismo ensamblado y en las clases derivadas.

```csharp
public class Base
{
    // Campo protegido interno
    protected internal int CampoProtegidoInterno;

    // Método protegido interno
    protected internal void MetodoProtegidoInterno()
    {
        // Lógica del método
    }
}

public class Derivada : Base
{
    public void MetodoDerivado()
    {
        CampoProtegidoInterno = 10;
        MetodoProtegidoInterno();
    }
}

// Uso del miembro protegido interno dentro del mismo ensamblado
var baseObj = new Base();
baseObj.CampoProtegidoInterno = 10;
baseObj.MetodoProtegidoInterno();
```

### 6. Private Protected (`private protected`)

Los miembros con el modificador `private protected` son accesibles dentro de su clase y en clases derivadas que estén en el mismo ensamblado.

```csharp
public class Base
{
    // Campo privado protegido
    private protected int CampoPrivadoProtegido;

    // Método privado protegido
    private protected void MetodoPrivadoProtegido()
    {
        // Lógica del método
    }
}

public class Derivada : Base
{
    public void MetodoDerivado()
    {
        CampoPrivadoProtegido = 10;
        MetodoPrivadoProtegido();
    }
}

// Uso de la clase derivada dentro del mismo ensamblado
var derivada = new Derivada();
derivada.MetodoDerivado(); // No se puede acceder directamente a CampoPrivadoProtegido ni MetodoPrivadoProtegido
```
