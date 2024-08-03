### **Reflexión en C#**

La **reflexión** es una característica del lenguaje C# que permite inspeccionar y modificar la estructura de un programa en tiempo de ejecución. A través de la reflexión, puedes obtener información sobre los tipos, métodos, propiedades y campos de un ensamblado en tiempo de ejecución, así como instanciar tipos dinámicamente y llamar métodos. Esto es especialmente útil para aplicaciones que necesitan realizar operaciones en tipos desconocidos en tiempo de compilación o para implementar características avanzadas como la serialización, el mapeo de datos y la inyección de dependencias.

#### **Fundamentos de la Reflexión**

1. **Obtención de Información sobre Tipos:**
   - Puedes obtener información sobre un tipo, como sus métodos, propiedades y campos, utilizando la clase `Type` del espacio de nombres `System`.

2. **Creación Dinámica de Instancias:**
   - La reflexión permite crear instancias de tipos en tiempo de ejecución, incluso si el tipo no se conoce en tiempo de compilación.

3. **Invocación Dinámica de Métodos:**
   - Puedes invocar métodos de un tipo de forma dinámica, lo que es útil cuando el nombre del método se conoce solo en tiempo de ejecución.

4. **Acceso y Modificación de Propiedades y Campos:**
   - Puedes leer y modificar los valores de propiedades y campos de un tipo en tiempo de ejecución.

#### **Ejemplos de Reflexión en C#**

**1. Obtener Información sobre un Tipo**

   Utiliza la clase `Type` para obtener información sobre un tipo en tiempo de ejecución, como sus métodos, propiedades y campos.

   ```csharp
   using System;
   using System.Reflection;

   public class Program
   {
       public static void Main()
       {
           // Obtén el tipo de la clase Program
           Type tipo = typeof(Program);

           // Imprime el nombre de la clase
           Console.WriteLine($"Nombre de la clase: {tipo.Name}");

           // Obtén y muestra los métodos de la clase
           MethodInfo[] metodos = tipo.GetMethods();
           foreach (MethodInfo metodo in metodos)
           {
               Console.WriteLine($"Método: {metodo.Name}");
           }

           // Obtén y muestra las propiedades de la clase
           PropertyInfo[] propiedades = tipo.GetProperties();
           foreach (PropertyInfo propiedad in propiedades)
           {
               Console.WriteLine($"Propiedad: {propiedad.Name}");
           }
       }
   }
   ```

**2. Crear Instancias Dinámicamente**

   Puedes usar la reflexión para crear instancias de un tipo cuando el tipo se conoce solo en tiempo de ejecución.

   ```csharp
   using System;

   public class Program
   {
       public static void Main()
       {
           // Nombre del tipo a instanciar
           string nombreTipo = "System.Text.StringBuilder";

           // Obtén el tipo de la clase usando su nombre
           Type tipo = Type.GetType(nombreTipo);

           if (tipo != null)
           {
               // Crea una instancia del tipo
               object instancia = Activator.CreateInstance(tipo);

               // Usa la instancia creada
               Console.WriteLine($"Instancia creada: {instancia}");
           }
           else
           {
               Console.WriteLine("Tipo no encontrado.");
           }
       }
   }
   ```

**3. Invocar Métodos Dinámicamente**

   La reflexión permite llamar a métodos en tipos desconocidos en tiempo de compilación.

   ```csharp
   using System;
   using System.Reflection;

   public class Program
   {
       public static void Main()
       {
           // Nombre del tipo y método a invocar
           string nombreTipo = "System.Text.StringBuilder";
           string nombreMetodo = "Append";

           // Obtén el tipo de la clase
           Type tipo = Type.GetType(nombreTipo);
           if (tipo != null)
           {
               // Crea una instancia del tipo
               object instancia = Activator.CreateInstance(tipo);

               // Obtén el método usando su nombre
               MethodInfo metodo = tipo.GetMethod(nombreMetodo, new Type[] { typeof(string) });

               if (metodo != null)
               {
                   // Invoca el método en la instancia creada
                   object resultado = metodo.Invoke(instancia, new object[] { "Hello, Reflection!" });

                   // Muestra el resultado
                   Console.WriteLine(instancia.ToString()); // Muestra el resultado de la operación
               }
           }
       }
   }
   ```

**4. Acceder y Modificar Propiedades y Campos**

   La reflexión permite leer y escribir valores en propiedades y campos de un tipo.

   ```csharp
   using System;
   using System.Reflection;

   public class Persona
   {
       public string Nombre { get; set; }
       private int Edad { get; set; }
   }

   public class Program
   {
       public static void Main()
       {
           // Crea una instancia de la clase Persona
           Persona persona = new Persona();

           // Obtén el tipo de la clase Persona
           Type tipo = typeof(Persona);

           // Establece el valor de la propiedad Nombre
           PropertyInfo propiedadNombre = tipo.GetProperty("Nombre");
           propiedadNombre.SetValue(persona, "Juan");

           // Establece el valor del campo privado Edad
           FieldInfo campoEdad = tipo.GetField("Edad", BindingFlags.NonPublic | BindingFlags.Instance);
           campoEdad.SetValue(persona, 30);

           // Obtén y muestra el valor de la propiedad Nombre
           string nombre = (string)propiedadNombre.GetValue(persona);
           Console.WriteLine($"Nombre: {nombre}");

           // Obtén y muestra el valor del campo Edad
           int edad = (int)campoEdad.GetValue(persona);
           Console.WriteLine($"Edad: {edad}");
       }
   }
   ```

#### **Casos de Uso Comunes**

1. **Serialización y Deserialización:**
   - La reflexión se utiliza para convertir objetos en una representación serializada y viceversa, permitiendo trabajar con datos en diferentes formatos (como JSON o XML).

2. **Inyección de Dependencias:**
   - Los contenedores de inyección de dependencias utilizan reflexión para resolver y crear instancias de servicios e inyectarlas en otras clases.

3. **Implementación de Plugins y Extensibilidad:**
   - La reflexión permite cargar y utilizar módulos o plugins en tiempo de ejecución, proporcionando flexibilidad para extender la funcionalidad de una aplicación.

4. **Automatización de Pruebas:**
   - En los frameworks de prueba, la reflexión se utiliza para descubrir y ejecutar métodos de prueba dinámicamente.

### **Consideraciones**

1. **Rendimiento:**
   - La reflexión puede ser costosa en términos de rendimiento. Utilízala con cuidado y evita el uso excesivo en partes críticas del código.

2. **Seguridad:**
   - Manipular tipos y miembros de forma dinámica puede introducir riesgos de seguridad si no se controla adecuadamente. Asegúrate de validar y manejar entradas de forma segura.

3. **Legibilidad del Código:**
   - Aunque la reflexión ofrece flexibilidad, puede hacer que el código sea más difícil de leer y mantener. Utiliza la reflexión solo cuando sea necesario y busca alternativas si están disponibles.

La reflexión es una herramienta poderosa en C# que permite la inspección y manipulación dinámica de tipos y miembros en tiempo de ejecución, facilitando la creación de aplicaciones flexibles y extensibles.