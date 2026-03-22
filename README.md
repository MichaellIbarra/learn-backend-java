# learn-backend-java

## Aquitectura de software 


### ESTILOS ARQUITECTÓNICOS (El nivel MACRO o de Despliegue)
Define cómo se distribuye y despliega el sistema a gran escala. Responde a la pregunta: "¿El sistema es una sola pieza o varias piezas comunicándose?"

Monolítica: Toda la aplicación (lógica, acceso a datos, interfaz) se compila y despliega como un single block. Si algo falla, puede caer todo el sistema.
 - Código unificado: Toda la funcionalidad de la aplicación está contenida en un solo código base.
 - Despliegue único: La aplicación se despliega como una sola unidad.
 - Escalabilidad limitada: La escalabilidad se realiza a nivel de toda la aplicación, lo que puede ser ineficiente.
 - Mantenimiento complejo: Las actualizaciones y cambios pueden afectar a toda la aplicación, lo que dificulta el mantenimiento.
 - Desarrollo lento: Los equipos de desarrollo pueden enfrentarse a cuellos de botella debido a la dependencia del código compartido.
Microservicios: La aplicación se divide en servicios pequeños, independientes y delimitados por contexto de negocio. Cada uno tiene su propia base de datos y se comunican por red (APIs, eventos).
 - Independencia: cada módulo es un ejecutable en sí mismo, y puede ser desplegado sin afectar al resto.
 - Granularidad: cada microservicio se encarga de una única funcionalidad de negocio (ej. gestión de usuarios, procesamiento de pagos).
 - Aplicación modular: posibilidad de amplicar o reducir en función de la demanda, y de usar tecnologías distintas para cada servicio.
 - Arquitectura distribuida: cada microservicio dispone de su propia base de datos. Los modelos de software distribuido se han visto respaldados por la proliferación de proveedores Cloud, que allanan el camino a la puesta en marcha de este tipo de arquitecturas.
SOA (Arquitectura Orientada a Servicios): Similar a los microservicios pero más tradicional/enpresarial ("Service by Service"). Los servicios suelen ser más grandes y se comunican a través de un bus central (ESB).
Serverless (Sin Servidor): El código se ejecuta en funciones temporales gestionadas por la nube (ej. AWS Lambda) que solo se activan cuando hay una petición.

### ARQUITECTURA DE APLICACIÓN (El nivel MICRO o Estructura Interna)
Define cómo se organiza el código dentro de un proyecto. Responde a la pregunta: "¿Cómo separo las responsabilidades en mi código para que sea mantenible?"
(Nota: Un Monolito o un Microservicio individual pueden usar internamente cualquiera de estas arquitecturas).

Arquitectura en Capas (N-Tier): El código se organiza de forma horizontal (Controlador -> Servicio -> Repositorio). Es la más tradicional.
Arquitectura Hexagonal (Puertos y Adaptadores): El "Core" (la lógica de negocio) está en el centro, aislado totalmente de la base de datos o de la web. Se usan interfaces (puertos) e implementaciones (adaptadores) para conectarse con el exterior.
Arquitectura Limpia (Clean Architecture / Onion): Muy similar a la hexagonal. Define reglas estrictas donde las dependencias siempre apuntan hacia adentro, protegiendo las "Entidades" y "Casos de Uso" del negocio.

### PATRONES ARQUITECTÓNICOS (Soluciones a flujos específicos)
Son tácticas de diseño grandes que resuelven flujos de información y comunicación entre componentes del sistema. Pertenece totalmente a la arquitectura.
- **Alcance**: Macro (Nivel de componentes, servicios, bases de datos, APIs).
- **Problema que resuelven**: Cómo se comunican grandes bloques de la aplicación, cómo escalan y cómo fluye la información.
- **Ejemplos**: CQRS, Event-Driven, Event Sourcing, BFF.

---

## Diseño de Software

A diferencia de la Arquitectura (que estructura el sistema a nivel macro), el Diseño de Software se enfoca en el nivel micro: cómo escribir el código internamente para que sea mantenible y limpio.

### 1. Principios de Diseño
Son reglas fundamentales o filosofías para escribir código limpio, mantenible y escalable. No son recetas exactas, sino buenas prácticas.
- **SOLID**: Los 5 pilares fundamentales de la Programación Orientada a Objetos (Responsabilidad Única, Abierto/Cerrado, Sustitución de Liskov, Segregación de Interfaces e Inversión de Dependencia).
- **DRY (Don't Repeat Yourself)**: Evitar a toda costa la duplicación de bloques de lógica y código.
- **KISS (Keep It Simple, Stupid)**: Mantener las soluciones lo más simples, cortas y legibles posibles. Evitar la sobreingeniería.
- **YAGNI (You Aren't Gonna Need It)**: Implementar el código cuando realmente lo necesites, nunca programar cosas complejas "por si acaso se necesitan en el futuro".

### 2. Patrones de Diseño (GoF - Gang of Four)
Son soluciones estandarizadas a problemas de codificación comunes que ocurren de forma recurrente.
- **Alcance**: Micro (Nivel de clases, interfaces y objetos).
- **Problema que resuelven**: Cómo se crean los objetos, cómo evitar acoplamiento entre clases y cómo interactúan en memoria.
- **Ejemplos**: Singleton, Factory, Observer, Strategy, Decorator.
- **Dónde viven**: Son detalles de implementación de código dentro de un servicio o controlador.

### 3. Patrones GRASP
Son directrices enfocadas en asignar y delegar correctamente las responsabilidades a las distintas clases y objetos.
- **Alcance**: Micro/Medio.
- **Problema que resuelven**: Responden preguntas comunes como: *¿De quién es la responsabilidad de crear este objeto? ¿De quién es la responsabilidad de procesar esta información?*
- **Ejemplos**: Creador, Experto en Información, Alta Cohesión, Bajo Acoplamiento, Controlador.
