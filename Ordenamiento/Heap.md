### **6. Ordenamiento por Montículo (Heap Sort)**

**Descripción:**
El algoritmo de ordenamiento por montículo convierte la lista en un montículo (heap), luego extrae el elemento máximo (o mínimo) del montículo y lo coloca en la posición correcta en la lista. Repite este proceso hasta que la lista esté ordenada.

**Complejidad:**
- **Peor caso:** O(n log n)
- **Mejor caso:** O(n log n)

**Ejemplo:**

```csharp
void HeapSort(int[] arr)
{
    int n = arr.Length;

    // Construir un montículo
    for (int i = n / 2 - 1; i >= 0; i--)
        Heapify(arr, n, i);

    // Extraer elementos del montículo uno por uno
    for (int i = n - 1; i >= 0; i--)
    {
        // Mover el elemento raíz al final
        int temp = arr[0];
        arr[0] = arr[i];
        arr[i] = temp;

        // Llamar a heapify en el montículo reducido
        Heapify(arr, i, 0);
    }
}

void Heapify(int[] arr, int n, int i)
{
    int largest = i;
    int l = 2 * i + 1;
    int r = 2 * i + 2;

    if (l < n && arr[l] > arr[largest])
        largest = l;

    if (r < n && arr[r] > arr[largest])
        largest = r;

    if (largest != i)
    {
        int swap = arr[i];
        arr[i] = arr[largest];
        arr[largest] = swap;

        Heapify(arr, n, largest);
    }
}
```