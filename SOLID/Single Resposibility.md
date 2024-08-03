#### **1. Single Responsibility Principle (SRP) - Principio de Responsabilidad Única**

**Fundamento:**
El principio de responsabilidad única establece que una clase debe tener una única razón para cambiar, es decir, una sola responsabilidad o función en el sistema. Esto mejora la cohesión de las clases y facilita el mantenimiento y la extensión del código.

**Ejemplo:**

Supongamos que tenemos una clase `Invoice` que maneja tanto la generación de la factura como el envío por correo electrónico:

```csharp
public class Invoice
{
    public void GenerateInvoice()
    {
        // Generar la factura
    }

    public void SendEmail()
    {
        // Enviar la factura por correo electrónico
    }
}
```

En este caso, la clase `Invoice` tiene dos responsabilidades: generación de la factura y envío por correo electrónico. Esto violaría el SRP.

**Solución Aplicando SRP:**

Separar las responsabilidades en dos clases diferentes:

```csharp
public class Invoice
{
    public void GenerateInvoice()
    {
        // Generar la factura
    }
}

public class EmailService
{
    public void SendEmail(Invoice invoice)
    {
        // Enviar la factura por correo electrónico
    }
}
```

Aquí, `Invoice` se encarga solo de generar la factura, y `EmailService` se encarga del envío por correo electrónico, respetando el SRP.