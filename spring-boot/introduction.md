# Spring y Spring Boot: Introducción
## ¿Qué es Spring?
Spring es un framework de desarrollo para Java que facilita la creación de aplicaciones empresariales. Proporciona una amplia gama de funcionalidades, como inyección de dependencias, programación orientada a aspectos, manejo de transacciones, y soporte para diversos tipos de bases de datos (SQL y NoSQL). Spring Boot es una extensión de Spring que simplifica aún más el proceso de configuración y despliegue, permitiendo crear aplicaciones listas para producción con mínima configuración.
## ¿Por qué usar Spring?
- **Inyección de dependencias**: Facilita la gestión de objetos y sus dependencias
- **Programación orientada a aspectos**: Permite separar preocupaciones transversales como logging, seguridad, y manejo de transacciones.
- **Soporte para bases de datos**: Compatible con SQL (JPA/Hibernate) y NoSQL (MongoDB, Redis).
- **Comunidad y ecosistema**: Amplia comunidad de desarrolladores y una gran cantidad de proyectos y bibliotecas compatibles.

## ¿Qué es Spring Boot?
Spring Boot es una herramienta que se basa en Spring para simplificar la creación de aplicaciones. Proporciona convenciones sobre configuración, lo que significa que puedes empezar a trabajar con una aplicación Spring sin tener que configurar manualmente cada componente. Spring Boot incluye un servidor web embebido (como Tomcat o Jetty), lo que permite ejecutar aplicaciones como un simple JAR sin necesidad de desplegarlas en un servidor externo.
## ¿Por qué usar Spring Boot?
- **Configuración automática**: Detecta automáticamente las dependencias y configura los componentes necesarios.
- **Servidor embebido**: Permite ejecutar la aplicación sin necesidad de un servidor externo.
- **Actuadores**: Proporciona endpoints para monitoreo y gestión de la aplicación
- **Facilidad de desarrollo**: Reduce el tiempo de configuración y permite enfocarse en la lógica de negocio.

## Persistencia y Acceso a Datos en Spring (SQL y NoSQL)

Spring ofrece múltiples herramientas (desde el nivel base hasta abstracciones de alto nivel) para gestionar el acceso a los datos. A continuación se detalla qué es cada tecnología y cómo se relacionan y conectan entre sí:

### Ecosistema Relacional (SQL)
Cuando se trabaja con bases de datos relacionales (MySQL, PostgreSQL, Oracle, etc.), las tecnologías se estructuran como capas que se construyen una sobre otra:

1. **JDBC (Java Database Connectivity)**
   - **¿Qué es?**: La API base de muy bajo nivel en Java para bases relacionales. Implica escribir sentencias de SQL puro, y gestionar manualmente las conexiones (`Connection`), consultas (`PreparedStatement`) y resultados (`ResultSet`).
   - **¿Cómo se conecta?**: Es la base absoluta. Todo ORM de Java o librería relacional termina traduciendo sus procesos y enviándolos mediante JDBC al motor SQL.

2. **JPA (Java Persistence API)**
   - **¿Qué es?**: Es una **especificación** o regla estándar. Define el concepto de ORM (mapeo objeto-relacional) usando anotaciones propias de `jakarta.persistence` como `@Entity`, `@Id`, y la definición de relaciones a través de `@Table` o `@Column`, pero **sólo dicta el qué, no el cómo** (no ejecuta código por sí sola).
   - **¿Cómo se conecta?**: Al ser solo la teoría y contratos, se conecta con una **implementación** en específico (por ejemplo, Hibernate) para que las anotaciones hagan la magia.

3. **Hibernate**
   - **¿Qué es?**: Es el ORM más popular en Java y la implementación por defecto de la especificación de JPA en Spring. 
   - **¿Cómo se conecta?**: Toma todas las clases decoradas con las reglas/anotaciones de JPA, traduce de forma automática las operaciones en sentencias SQL completas y usa la capa base (JDBC) por debajo para ejecutarlas.

4. **Spring Data JPA**
   - **¿Qué es?**: Una abstracción de gran nivel por parte de Spring que auto-implementa repositorios enteros (las interfaces tipo `JpaRepository`). Te exime de crear todo ese código repetitivo de CRUD.
   - **¿Cómo se conecta?**: Funciona **encima** de Hibernate. Coordina y ejecuta internamente toda la implementación del ORM, unificando cómo usas la persistencia. Es lo que directamente invocas desde tus clases en los Controladores y Servicios de Spring.

*Flujo típico: Controlador -> Servicio -> Repositorio (Spring Data JPA) -> Hibernate -> JDBC -> SQL*
---

### Ecosistema NoSQL (Documentos y Caché)
Las tecnologías base como Hibernate, JPA, o @Entity no actúan aquí porque bases NoSQL no tienen tablas ni joins. En su lugar hay herramientas equivalentes de Spring:

1. **Spring Data MongoDB**
   - **¿Qué es?**: El equivalente NoSQL para bases de documentos (Mongo). Usa sus propias anotaciones desde sus paquetes (`@Document(collection="...")`, `@Field`) para mapear clases estables a estructuras flexibles tipo JSON o BSON.
   - **¿Cómo se conecta?**: Utiliza la misma filosofía de *Spring Data* (ej: `MongoRepository` en lugar de `JpaRepository`), dando la misma flexibilidad del autocompletado y CRUDs prehechos a los desarrolladores, pero guardando comúnmente conjuntos embebidos envés de hacer joins relacionales.

2. **Spring Data Redis**
   - **¿Qué es?**: Es un acceso directo para el uso del almacén clave-valor de Redis. Mapea la información a un almacenamiento rápido apoyándose en anotaciones como `@RedisHash`. 
   - **¿Cómo se conecta?**: Redis suele co-existir frente a la base relacional principal (o de Mongo principal). Funciona como una capa intermedia o Caché para almacenar aquello que tiene lecturas masivas reduciendo la carga y latencia.

---

### Conexión arquitectónica: Reactivo (WebFlux) vs MVC
Finalmente, depende también de la arquitectura global y cómo las peticiones atraviesan los datos:

- **Spring MVC**: Es la forma convencional y bloqueante basada en hilos. Conecta de forma típica a tecnologías (JDBC normal, Hibernate habitual), deteniendo el sub-proceso de la capa base de lectura por las respuestas.
- **Spring Reactive (WebFlux)**: Requiere ecosistemas **No Bloqueantes**. Se conecta devolviendo Streams Reactivos (`Mono` o `Flux`). Para que sirva totalmente es necesario usar capas de base de datos preparadas, como por ejemplo *Spring Data Reactive MongoDB* o *Reactive Redis*, previniendo el ahogo de servidor frente a operaciones intensivas bajo extrema peticiones en tiempo real.

