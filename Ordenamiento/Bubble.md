
### **1. Ordenamiento por Burbuja (Bubble Sort)**

**Descripción:**
El algoritmo de burbuja compara cada par de elementos adyacentes y los intercambia si están en el orden incorrecto. Este proceso se repite hasta que la lista esté ordenada.

**Complejidad:**
- **Peor caso:** O(n²)
- **Mejor caso:** O(n) (si la lista ya está ordenada)

**Ejemplo:**

```csharp
void BubbleSort(int[] arr)
{
    int n = arr.Length;
    for (int i = 0; i < n - 1; i++)
    {
        for (int j = 0; j < n - i - 1; j++)
        {
            if (arr[j] > arr[j + 1])
            {
                // Intercambiar arr[j] y arr[j + 1]
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }
}
```