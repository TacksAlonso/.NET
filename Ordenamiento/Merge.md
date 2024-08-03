### **5. Ordenamiento por Mezcla (Merge Sort)**

**Descripción:**
El algoritmo de ordenamiento por mezcla también utiliza el enfoque de divide y vencerás. Divide la lista en dos mitades, ordena cada mitad de manera recursiva y luego combina las dos mitades ordenadas.

**Complejidad:**
- **Peor caso:** O(n log n)
- **Mejor caso:** O(n log n)

**Ejemplo:**

```csharp
void MergeSort(int[] arr, int left, int right)
{
    if (left < right)
    {
        int mid = (left + right) / 2;

        MergeSort(arr, left, mid);
        MergeSort(arr, mid + 1, right);

        Merge(arr, left, mid, right);
    }
}

void Merge(int[] arr, int left, int mid, int right)
{
    int n1 = mid - left + 1;
    int n2 = right - mid;

    int[] L = new int[n1];
    int[] R = new int[n2];

    for (int i = 0; i < n1; i++)
        L[i] = arr[left + i];
    for (int j = 0; j < n2; j++)
        R[j] = arr[mid + 1 + j];

    int k = left;
    int i1 = 0, i2 = 0;

    while (i1 < n1 && i2 < n2)
    {
        if (L[i1] <= R[i2])
        {
            arr[k] = L[i1];
            i1++;
        }
        else
        {
            arr[k] = R[i2];
            i2++;
        }
        k++;
    }

    while (i1 < n1)
    {
        arr[k] = L[i1];
        i1++;
        k++;
    }

    while (i2 < n2)
    {
        arr[k] = R[i2];
        i2++;
        k++;
    }
}
```