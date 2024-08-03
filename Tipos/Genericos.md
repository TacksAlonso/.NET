
#### **Tipos Genéricos**

**Definición:**
Los tipos genéricos en C# permiten crear clases, interfaces, y métodos que trabajan con cualquier tipo de datos sin especificar el tipo exacto en el momento de la definición. Los genéricos proporcionan una forma de definir tipos y métodos que pueden operar sobre cualquier tipo de datos, proporcionando mayor flexibilidad y reutilización de código.

**Características:**

1. **Definición:**
   - Puedes definir una clase, interfaz, o método genérico utilizando parámetros de tipo. Estos parámetros de tipo actúan como "marcadores de posición" para los tipos reales que se especifican cuando se instancia o se llama al método.
   ```csharp
   public class Caja<T>
   {
       private T _contenido;

       public void MeterContenido(T contenido)
       {
           _contenido = contenido;
       }

       public T SacarContenido()
       {
           return _contenido;
       }
   }
   ```

2. **Instanciación:**
   - Al crear una instancia de una clase genérica, debes especificar el tipo concreto que se utilizará para el parámetro de tipo.
   ```csharp
   var cajaDeEnteros = new Caja<int>();
   cajaDeEnteros.MeterContenido(123);

   var cajaDeCadenas = new Caja<string>();
   cajaDeCadenas.MeterContenido("Hola Mundo");
   ```

3. **Métodos Genéricos:**
   - Los métodos genéricos permiten definir métodos que operan sobre parámetros de tipo. Puedes usar métodos genéricos dentro de clases genéricas o independientes.
   ```csharp
   public class Utilidades
   {
       public static void Imprimir<T>(T valor)
       {
           Console.WriteLine(valor);
       }
   }
   ```

4. **Restricciones:**
   - Puedes restringir los tipos que se pueden usar con un parámetro genérico mediante restricciones. Esto permite especificar que un tipo genérico debe ser una clase, una interfaz específica, o tener un constructor sin parámetros.
   ```csharp
   public class Comparador<T> where T : IComparable<T>
   {
       public bool Comparar(T x, T y)
       {
           return x.CompareTo(y) == 0;
       }
   }
   ```

5. **Beneficios:**
   - **Reutilización de Código:** Permite reutilizar clases y métodos para diferentes tipos sin necesidad de duplicar código.
   - **Seguridad de Tipos:** Aumenta la seguridad de tipos en tiempo de compilación al evitar errores comunes relacionados con el tipo de datos.

- **Tipos Genéricos:**
  Utiliza tipos genéricos cuando necesites definir clases o métodos que deben operar con diferentes tipos de datos. Los genéricos te permitirán escribir código más flexible y reutilizable, asegurando la seguridad de tipos en tiempo de compilación.

