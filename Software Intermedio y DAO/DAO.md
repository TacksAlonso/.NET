
#### **DAO (Data Access Object)**

**1. ¿Qué es DAO?**

El patrón DAO (Data Access Object) es un patrón de diseño que proporciona una interfaz abstracta para interactuar con una base de datos. El objetivo es separar la lógica de acceso a datos del resto de la aplicación, lo que facilita el mantenimiento y la escalabilidad del código.

**2. Componentes del Patrón DAO:**

- **DAO Interface:** Define los métodos para interactuar con la base de datos (por ejemplo, `create`, `read`, `update`, `delete`).
- **DAO Implementation:** Implementa la lógica para interactuar con la base de datos, como consultas SQL y operaciones CRUD.
- **Model (Entidad):** Representa la estructura de los datos que se almacenan en la base de datos.

**3. Ventajas del Patrón DAO:**

- **Separación de Preocupaciones:** Aísla la lógica de acceso a datos del resto de la aplicación, facilitando el mantenimiento y la prueba.
- **Facilita el Cambio:** Permite cambiar la implementación de acceso a datos sin afectar a la lógica de negocio.
- **Reutilización:** Permite reutilizar el acceso a datos en diferentes partes de la aplicación.

**4. Ejemplo de Implementación en .NET:**

**Paso 1: Crear el Modelo (Entidad):**

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
}
```

**Paso 2: Definir la Interfaz DAO:**

```csharp
public interface IProductDao
{
    Product GetById(int id);
    IEnumerable<Product> GetAll();
    void Add(Product product);
    void Update(Product product);
    void Delete(int id);
}
```

**Paso 3: Implementar la Interfaz DAO:**

```csharp
public class ProductDao : IProductDao
{
    private readonly string _connectionString;

    public ProductDao(string connectionString)
    {
        _connectionString = connectionString;
    }

    public Product GetById(int id)
    {
        using (var connection = new SqlConnection(_connectionString))
        {
            var query = "SELECT * FROM Products WHERE Id = @Id";
            return connection.QuerySingleOrDefault<Product>(query, new { Id = id });
        }
    }

    public IEnumerable<Product> GetAll()
    {
        using (var connection = new SqlConnection(_connectionString))
        {
            var query = "SELECT * FROM Products";
            return connection.Query<Product>(query);
        }
    }

    public void Add(Product product)
    {
        using (var connection = new SqlConnection(_connectionString))
        {
            var query = "INSERT INTO Products (Name, Price) VALUES (@Name, @Price)";
            connection.Execute(query, product);
        }
    }

    public void Update(Product product)
    {
        using (var connection = new SqlConnection(_connectionString))
        {
            var query = "UPDATE Products SET Name = @Name, Price = @Price WHERE Id = @Id";
            connection.Execute(query, product);
        }
    }

    public void Delete(int id)
    {
        using (var connection = new SqlConnection(_connectionString))
        {
            var query = "DELETE FROM Products WHERE Id = @Id";
            connection.Execute(query, new { Id = id });
        }
    }
}
```

**Paso 4: Uso del DAO en la Aplicación:**

```csharp
public class ProductService
{
    private readonly IProductDao _productDao;

    public ProductService(IProductDao productDao)
    {
        _productDao = productDao;
    }

    public void AddProduct(Product product)
    {
        _productDao.Add(product);
    }

    public Product GetProduct(int id)
    {
        return _productDao.GetById(id);
    }

    // Otros métodos de servicio...
}
```

**5. Práctica Recomendada:**

- **Pruebas Unitarias:** Asegúrate de probar tus DAOs con pruebas unitarias para verificar que las operaciones de acceso a datos funcionan correctamente.
- **Manejo de Excepciones:** Implementa un manejo adecuado de excepciones en los DAOs para manejar errores de conexión y problemas con la base de datos.
- **Optimización:** Utiliza técnicas de optimización como el uso de consultas parametrizadas y la administración de conexiones para mejorar el rendimiento.
