### **2. Ordenamiento por Inserción (Insertion Sort)**

**Descripción:**
El algoritmo de inserción construye la lista ordenada uno a uno. Toma cada elemento y lo inserta en su posición correcta en la parte ya ordenada de la lista.

**Complejidad:**
- **Peor caso:** O(n²)
- **Mejor caso:** O(n) (si la lista ya está ordenada)

**Ejemplo:**

```csharp
void InsertionSort(int[] arr)
{
    int n = arr.Length;
    for (int i = 1; i < n; i++)
    {
        int key = arr[i];
        int j = i - 1;

        // Mover los elementos de arr[0..i-1] que son mayores que key
        while (j >= 0 && arr[j] > key)
        {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
}
```