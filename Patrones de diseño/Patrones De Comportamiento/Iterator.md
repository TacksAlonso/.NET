### **4. Iterator**

El patrón Iterator proporciona una manera de acceder secuencialmente a los elementos de un objeto agregado sin exponer su representación subyacente.

**Ejemplo en .NET Core:**

```csharp
// Iterador
public interface IIterator<T>
{
    bool HasNext();
    T Next();
}

// Colección
public class Collection<T>
{
    private readonly List<T> _items = new List<T>();

    public void Add(T item)
    {
        _items.Add(item);
    }

    public IIterator<T> CreateIterator()
    {
        return new Iterator<T>(_items);
    }
}

public class Iterator<T> : IIterator<T>
{
    private readonly List<T> _items;
    private int _position;

    public Iterator(List<T> items)
    {
        _items = items;
    }

    public bool HasNext()
    {
        return _position < _items.Count;
    }

    public T Next()
    {
        return _items[_position++];
    }
}
```

**Uso:**

```csharp
public class IteratorExample
{
    public static void Run()
    {
        var collection = new Collection<string>();
        collection.Add("Item 1");
        collection.Add("Item 2");
        collection.Add("Item 3");

        var iterator = collection.CreateIterator();
        while (iterator.HasNext())
        {
            Console.WriteLine(iterator.Next());
        }
    }
}
```

