#### **4. Interface Segregation Principle (ISP) - Principio de Segregación de Interfaces**

**Fundamento:**
El principio de segregación de interfaces establece que una clase no debe estar obligada a implementar interfaces que no utiliza. En otras palabras, las interfaces deben ser específicas para el cliente.

**Ejemplo:**

Supongamos que tenemos una interfaz `IMultiFunctionPrinter`:

```csharp
public interface IMultiFunctionPrinter
{
    void Print();
    void Scan();
    void Fax();
}
```

Una impresora que solo imprime, pero no escanea ni envía fax, se vería obligada a implementar métodos que no necesita.

**Solución Aplicando ISP:**

Dividir la interfaz en interfaces más específicas:

```csharp
public interface IPrinter
{
    void Print();
}

public interface IScanner
{
    void Scan();
}

public interface IFaxMachine
{
    void Fax();
}

public class SimplePrinter : IPrinter
{
    public void Print()
    {
        // Implementación de impresión
    }
}
```

De esta forma, las clases implementan solo las interfaces que necesitan, respetando el ISP.