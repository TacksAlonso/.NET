### **3. Ordenamiento por Selección (Selection Sort)**

**Descripción:**
El algoritmo de selección divide la lista en una parte ordenada y una no ordenada. En cada iteración, selecciona el elemento mínimo (o máximo) de la parte no ordenada y lo coloca en la parte ordenada.

**Complejidad:**
- **Peor caso:** O(n²)
- **Mejor caso:** O(n²)

**Ejemplo:**

```csharp
void SelectionSort(int[] arr)
{
    int n = arr.Length;
    for (int i = 0; i < n - 1; i++)
    {
        // Encontrar el mínimo en el arreglo sin ordenar
        int minIndex = i;
        for (int j = i + 1; j < n; j++)
        {
            if (arr[j] < arr[minIndex])
            {
                minIndex = j;
            }
        }

        // Intercambiar el elemento mínimo con el primero no ordenado
        int temp = arr[minIndex];
        arr[minIndex] = arr[i];
        arr[i] = temp;
    }
}
```