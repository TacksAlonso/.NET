# **Patrones de diseño**
Los patrones de diseño en microservicios son enfoques y estrategias recomendados para resolver problemas comunes que surgen en la arquitectura de microservicios. Estos patrones ayudan a abordar desafíos relacionados con la comunicación entre servicios, la gestión de datos, la escalabilidad y la resiliencia. Aquí algunos patrones de diseño clave en microservicios:

1. **Patrón de API Gateway**: Utiliza un único punto de entrada para todas las solicitudes de los clientes. El API Gateway puede gestionar la autenticación, el enrutamiento de solicitudes a los microservicios adecuados, la agregación de respuestas y la aplicación de políticas de seguridad. Este patrón ayuda a simplificar la interacción con los microservicios y a manejar las complejidades del sistema. [Ejemplo](Gateway.md)

2. **Patrón de Orquestación**: En lugar de dejar que los microservicios interactúen directamente, se utiliza un servicio centralizado para coordinar el flujo de trabajo entre los servicios. Esto ayuda a gestionar la lógica de negocio compleja y las transacciones distribuidas, centralizando el control del proceso. [Ejemplo](Orquestacion.md)

3. **Patrón de Choreography**: A diferencia de la orquestación, en la coreografía los microservicios interactúan entre sí directamente sin un coordinador central. Cada servicio sabe cómo interactuar con otros servicios y sigue un conjunto de reglas predefinidas para la comunicación y la gestión de estados. [Ejemplo](Choreography.md)

4. **Patrón de Circuit Breaker**: Protege el sistema de fallos en cascada. Cuando un servicio detecta que otro servicio está fallando o está sobrecargado, el patrón de Circuit Breaker evita realizar más solicitudes a ese servicio hasta que se restablezca. Esto ayuda a mantener la resiliencia del sistema.[Ejemplo](CircuitBreaker.md)

5. **Patrón de Saga**: Maneja las transacciones distribuidas que abarcan varios microservicios. Una saga es una secuencia de transacciones locales en microservicios individuales, donde cada transacción tiene una operación compensatoria en caso de fallo. Esto asegura la consistencia eventual en el sistema.[Ejemplo](Saga.md)

6. **Patrón de CQRS (Command Query Responsibility Segregation)**: Separa las operaciones de escritura (comandos) y lectura (consultas) en diferentes modelos. Esto permite optimizar y escalar cada tipo de operación de manera independiente y puede mejorar el rendimiento y la flexibilidad en el acceso a los datos.[Ejemplo](CQRS.md)

7. **Patrón de Event Sourcing**: En lugar de almacenar el estado actual, se almacenan los eventos que han llevado al estado actual. Cada cambio en el sistema se representa como un evento, lo que permite reconstruir el estado de cualquier entidad a partir de estos eventos.[Ejemplo](EventSourcing.md)

8. **Patrón de Strangler Fig**: Permite la transición gradual de un sistema monolítico a una arquitectura de microservicios. Se introduce un nuevo sistema en paralelo al antiguo, y gradualmente se reemplazan partes del sistema antiguo por nuevos microservicios hasta que el monolito se elimina por completo.[Ejemplo](StranglerFig.md)

9. **Patrón de Anti-Corruption Layer**: Facilita la integración con sistemas externos o legados al proporcionar una capa que traduce y adapta los datos y las operaciones entre el sistema microservicio y el sistema externo. Esto evita que los cambios en los sistemas externos afecten directamente a la arquitectura de microservicios.[Ejemplo](AntiCorruptionLayer.md)

10. **Patrón de Bulkhead**: Aísla las fallas en una parte del sistema para evitar que se propaguen a otras partes. Similar a los compartimentos estancos en un barco, este patrón asegura que un fallo en un microservicio no afecte a otros microservicios o a toda la aplicación.[Ejemplo](Bulkhead.md)

11. **Patrón de Data Duplication**: Permite la duplicación de datos en diferentes microservicios para optimizar el rendimiento y la disponibilidad. Cada servicio puede tener su propia copia de los datos que necesita, lo que puede mejorar la velocidad de acceso y reducir la dependencia entre servicios.[Ejemplo](DataDuplication.md)

12. **Patrón de Sidecar**: Utiliza un contenedor o proceso auxiliar (el "sidecar") junto con el microservicio principal para gestionar tareas auxiliares como el registro, la supervisión, y la gestión de configuraciones. Esto ayuda a desacoplar estas responsabilidades del servicio principal.[Ejemplo](Sidecar.md)

Estos patrones ayudan a resolver problemas específicos y a mejorar la arquitectura de microservicios, pero es importante elegir y aplicar los patrones adecuados según las necesidades y el contexto de tu sistema.