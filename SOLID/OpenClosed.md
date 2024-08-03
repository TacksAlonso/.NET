#### **2. Open/Closed Principle (OCP) - Principio de Abierto/Cerrado**

**Fundamento:**
El principio de abierto/cerrado establece que las entidades del software (como clases, módulos, funciones, etc.) deben estar abiertas para su extensión pero cerradas para su modificación. Esto significa que deberías poder agregar nuevas funcionalidades sin cambiar el código existente.

**Ejemplo:**

Supongamos que tenemos una clase `PaymentProcessor` que maneja diferentes tipos de pagos:

```csharp
public class PaymentProcessor
{
    public void ProcessPayment(string paymentType)
    {
        if (paymentType == "CreditCard")
        {
            // Procesar pago con tarjeta de crédito
        }
        else if (paymentType == "PayPal")
        {
            // Procesar pago con PayPal
        }
    }
}
```

Si necesitamos agregar un nuevo tipo de pago, como "Bitcoin", tendríamos que modificar `PaymentProcessor`, violando el OCP.

**Solución Aplicando OCP:**

Utilizar la herencia o interfaces para permitir la extensión sin modificar el código existente:

```csharp
public interface IPaymentMethod
{
    void ProcessPayment();
}

public class CreditCardPayment : IPaymentMethod
{
    public void ProcessPayment()
    {
        // Procesar pago con tarjeta de crédito
    }
}

public class PayPalPayment : IPaymentMethod
{
    public void ProcessPayment()
    {
        // Procesar pago con PayPal
    }
}

public class PaymentProcessor
{
    public void ProcessPayment(IPaymentMethod paymentMethod)
    {
        paymentMethod.ProcessPayment();
    }
}
```

Con esta implementación, puedes agregar nuevos métodos de pago implementando `IPaymentMethod` sin modificar `PaymentProcessor`.