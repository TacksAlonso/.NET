
#### **5. Dependency Inversion Principle (DIP) - Principio de Inversión de Dependencias**

**Fundamento:**
El principio de inversión de dependencias establece que las dependencias deben estar en abstracciones (interfaces o clases abstractas) en lugar de en clases concretas. Los detalles (implementaciones concretas) deben depender de las abstracciones, no al revés.

**Ejemplo:**

Supongamos que tienes una clase `OrderService` que depende directamente de una clase `EmailService`:

```csharp
public class EmailService
{
    public void SendEmail(string message)
    {
        // Enviar un correo electrónico
    }
}

public class OrderService
{
    private readonly EmailService _emailService;

    public OrderService()
    {
        _emailService = new EmailService();
    }

    public void PlaceOrder()
    {
        // Procesar el pedido
        _emailService.SendEmail("Order placed");
    }
}
```

Aquí, `OrderService` depende de una implementación concreta de `EmailService`.

**Solución Aplicando DIP:**

Introducir una interfaz para `EmailService` y hacer que `OrderService` dependa de esta interfaz:

```csharp
public interface IEmailService
{
    void SendEmail(string message);
}

public class EmailService : IEmailService
{
    public void SendEmail(string message)
    {
        // Enviar un correo electrónico
    }
}

public class OrderService
{
    private readonly IEmailService _emailService;

    public OrderService(IEmailService emailService)
    {
        _emailService = emailService;
    }

    public void PlaceOrder()
    {
        // Procesar el pedido
        _emailService.SendEmail("Order placed");
    }
}
```

De esta forma, `OrderService` depende de una abstracción (`IEmailService`) en lugar de una implementación concreta, respetando el DIP.