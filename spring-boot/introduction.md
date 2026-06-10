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

## DTO (Data Transfer Object)
Un DTO es un objeto simple que se utiliza para transferir datos entre diferentes capas de una aplicación, especialmente entre la capa de presentación (Controladores) y la capa de negocio (Servicios). Los DTOs son útiles para encapsular los datos que se necesitan en una operación específica, evitando exponer directamente las entidades del dominio o modelos de base de datos.

- **¿Por qué usar DTOs?**
  - **Encapsulación**: Permiten ocultar detalles internos de las entidades del dominio, exponiendo solo lo necesario.
  - **Validación**: Facilitan la validación de datos de entrada antes de que lleguen a la lógica de negocio.
  - **Flexibilidad**: Permiten adaptar la estructura de los datos a las necesidades específicas de cada operación o endpoint, sin afectar el modelo de dominio.
  - **Desacoplamiento**: Ayudan a desacoplar la capa de presentación de la capa de persistencia, permitiendo cambios en una sin afectar a la otra.

## Proyecto Spring MVC
- Funciona bajo un modelo tradicional de servidor bloqueante, Thread-per-request (cada petición se maneja en un hilo separado).
- Ideal para aplicaciones con cargas moderadas y donde la latencia no es crítica.
### Dependencias típicas:
- Spring Web (spring-boot-starter-web): Proporciona las herramientas necesarias para construir aplicaciones web tradicionales con Spring MVC.
- Spring Data JPA (spring-boot-starter-data-jpa): Para la capa de persistencia con bases de datos relacionales, su función es facilitar el acceso a datos mediante JPA y Hibernate.
- Base de datos (MySQL, PostgreSQL, etc.): Para almacenar los datos de la aplicación.
- Spring Boot DevTools (spring-boot-devtools): Para facilitar el desarrollo con recarga automática y otras herramientas útiles.
- Lombok (lombok): Para reducir el código boilerplate en las entidades y DTOs, generando automáticamente getters, setters, constructores, etc.
- Spring Boot Actuator (spring-boot-starter-actuator): Para monitorear y gestionar la aplicación en producción.
### Project Metadata:
- Group: está relacionado con el paquete base de la aplicación, comúnmente se usa un formato de dominio invertido (com.ejemplo), aunque no es obligatorio. Es una forma de organizar el código y evitar conflictos de nombres con otros proyectos.
- Artifact: es el nombre del proyecto o módulo, que se convertirá en el nombre del archivo JAR generado.
- Name: es el nombre del proyecto, que puede ser igual al artifact.
- Description: una breve descripción del proyecto.
- Package name: el nombre del paquete base para las clases de la aplicación, generalmente coincide con el group y es utilizado para organizar el código fuente de la aplicación.
- Packaging: el formato de empaquetado, comúnmente JAR para aplicaciones Spring Boot por que se ejecutan como aplicaciones independientes y WAR para aplicaciones que se despliegan en servidores de aplicaciones tradicionales. Ejmplo: `jar` o `war`: .jar es el formato más común para aplicaciones Spring Boot, ya que permite ejecutar la aplicación directamente con `java -jar`. El formato .war se utiliza cuando se desea desplegar la aplicación en un servidor de aplicaciones como Tomcat o WildFly.

## Proyecto Gateway
- Es un proyecto que actúa como un punto de entrada único para todas las solicitudes a los
servicios backend. Utiliza Spring Cloud Gateway para enrutar las solicitudes a los servicios correspondientes, proporcionando funcionalidades como balanceo de carga, autenticación, y manejo de errores.
### Dependencias típicas:
- Spring Cloud Gateway (spring-cloud-starter-gateway): Proporciona las herramientas necesarias para construir un gateway de API con Spring Cloud.
- Spring Reactive Web (spring-boot-starter-webflux): Para construir aplicaciones web reactivas, necesario para el funcionamiento de Spring Cloud Gateway, por su naturaleza no bloqueante.
- Cloud Discovery Client (spring-cloud-starter-netflix-eureka-client): Para registrar el gateway en un servicio de descubrimiento como Eureka, permitiendo que otros servicios lo encuentren y se comuniquen con él.
- Cloud Bootstrap (spring-cloud-starter-bootstrap): Para cargar la configuración del gateway desde un servidor de configuración centralizado, como Spring Cloud Config Server, sirve para centralizar la configuración de la aplicación y facilitar su gestión.

### Proyecto Config Server
- Es un proyecto que actúa como un servidor de configuración centralizado para todas las aplicaciones en el ecosistema. Utiliza Spring Cloud Config Server para proporcionar una fuente central de configuración, permitiendo que las aplicaciones cliente obtengan su configuración de manera dinámica y centralizada.
### Dependencias típicas:
- Spring Cloud Config Server (spring-cloud-config-server): Proporciona las herramientas necesarias para construir un servidor de configuración centralizado con Spring Cloud.
