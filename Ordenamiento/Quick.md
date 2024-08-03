
### **4. Ordenamiento Rápido (Quick Sort)**

**Descripción:**
El algoritmo de ordenamiento rápido utiliza el enfoque de divide y vencerás. Selecciona un pivote y particiona la lista en dos sublistas: una con elementos menores que el pivote y otra con elementos mayores. Luego, aplica el mismo proceso recursivamente a las sublistas.

**Complejidad:**
- **Peor caso:** O(n²) (cuando el pivote es el menor o mayor elemento)
- **Mejor caso:** O(n log n)

**Ejemplo:**

```csharp
void QuickSort(int[] arr, int low, int high)
{
    if (low < high)
    {
        int pi = Partition(arr, low, high);

        QuickSort(arr, low, pi - 1);
        QuickSort(arr, pi + 1, high);
    }
}

int Partition(int[] arr, int low, int high)
{
    int pivot = arr[high];
    int i = (low - 1);

    for (int j = low; j < high; j++)
    {
        if (arr[j] < pivot)
        {
            i++;
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }

    int temp1 = arr[i + 1];
    arr[i + 1] = arr[high];
    arr[high] = temp1;

    return i + 1;
}
```