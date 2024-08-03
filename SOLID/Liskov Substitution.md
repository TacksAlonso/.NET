#### **3. Liskov Substitution Principle (LSP) - Principio de Sustitución de Liskov**

El **Principio de Sustitución de Liskov** (LSP, por sus siglas en inglés: Liskov Substitution Principle) es uno de los principios SOLID en el diseño orientado a objetos. Este principio establece que los objetos de una clase derivada deben ser intercambiables por objetos de la clase base sin alterar la corrección del programa. En otras palabras, las clases derivadas deben ser capaces de sustituir a sus clases base sin introducir errores inesperados.

### ¿Qué es el Principio de Sustitución de Liskov (LSP)?

El LSP dice que, si una clase `S` es una subclase de una clase `T`, entonces los objetos de tipo `T` deberían poder ser reemplazados por objetos de tipo `S` sin afectar el funcionamiento del programa. Esto significa que una subclase debe cumplir con las expectativas establecidas por su clase base y no cambiar el comportamiento esperado.

### Beneficios del LSP

1. **Polimorfismo Seguro**: Asegura que las subclases puedan ser utilizadas de manera intercambiable con sus clases base sin causar errores.
2. **Mantenibilidad**: Facilita el mantenimiento del código al garantizar que las subclases no rompan la funcionalidad de las clases base.
3. **Extensibilidad**: Permite extender el sistema de manera segura añadiendo nuevas subclases que cumplan con las expectativas de las clases base.

### Cómo Implementar el LSP en .NET Core

Para aplicar el LSP en .NET Core, es fundamental seguir ciertas prácticas de diseño y evitar violaciones comunes. Aquí hay algunos enfoques para asegurar que las subclases cumplan con el LSP:

1. **Cumplir con el Contrato de la Clase Base**: Las subclases deben cumplir con las promesas hechas por la clase base, como las precondiciones, postcondiciones y las invariantes del estado.
   
2. **Evitar la Violación de las Expectativas**: Las subclases no deben cambiar el comportamiento esperado de las clases base. Esto incluye no modificar las expectativas sobre el resultado de las operaciones o el estado de los objetos.

3. **Utilizar Polimorfismo Correctamente**: Asegúrate de que las subclases no sólo se ajusten a la firma de los métodos, sino también a sus comportamientos.

### Ejemplos de LSP en .NET Core

#### Ejemplo Correcto

Supongamos que tienes una clase base `Forma` y dos subclases, `Circulo` y `Cuadrado`. El comportamiento de `Circulo` y `Cuadrado` debe ser intercambiable con `Forma`.

```csharp
public abstract class Forma
{
    public abstract double CalcularArea();
}

public class Circulo : Forma
{
    public double Radio { get; set; }

    public override double CalcularArea()
    {
        return Math.PI * Radio * Radio;
    }
}

public class Cuadrado : Forma
{
    public double Lado { get; set; }

    public override double CalcularArea()
    {
        return Lado * Lado;
    }
}

public class CalculadoraDeAreas
{
    public double ObtenerArea(Forma forma)
    {
        return forma.CalcularArea();
    }
}
```

En este ejemplo, `Circulo` y `Cuadrado` son subclases de `Forma`. Ambos implementan `CalcularArea()` de manera consistente con las expectativas establecidas por la clase base `Forma`. `CalculadoraDeAreas` puede usar cualquier subclase de `Forma` sin saber detalles sobre su implementación específica.

#### Ejemplo Incorrecto

Ahora, veamos un ejemplo que viola el LSP:

```csharp
public class Animal
{
    public virtual void HacerSonido()
    {
        Console.WriteLine("Sonido genérico");
    }
}

public class Perro : Animal
{
    public override void HacerSonido()
    {
        Console.WriteLine("Ladrido");
    }
}

public class Pez : Animal
{
    public override void HacerSonido()
    {
        throw new InvalidOperationException("Un pez no hace sonidos");
    }
}
```

En este caso, la clase `Pez` viola el LSP porque `HacerSonido()` no puede ser llamada de manera segura en una instancia de `Pez` sin causar una excepción. Esto rompe la expectativa de que todas las subclases de `Animal` pueden realizar un sonido de manera válida.

### Cómo Evitar Violaciones del LSP

1. **Diseño de Métodos**: Asegúrate de que los métodos en las subclases no arrojen excepciones inesperadas o comportamientos que no sean esperados por la clase base.
   
2. **No Modificar el Comportamiento Esperado**: Las subclases deben mantener el comportamiento y las reglas de la clase base.

3. **Usar Clases Abstractas con Cuidado**: Asegúrate de que las clases abstractas definan contratos claros y que las subclases los cumplan adecuadamente.

4. **Validar el Estado**: Las subclases no deben violar las invariantes establecidas por la clase base.

El **Principio de Sustitución de Liskov** es esencial para mantener un sistema de clases coherente y seguro en .NET Core. Al garantizar que las subclases puedan sustituir a sus clases base sin causar errores, se facilita la mantenibilidad y extensibilidad del sistema. Implementar el LSP implica diseñar clases y subclases que respeten el contrato establecido por la clase base y evitar comportamientos inesperados que puedan romper el funcionamiento del sistema.
