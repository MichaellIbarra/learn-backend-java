# Spring Cloud Introducción
- Es un conjunto de herramientas y frameworks desarrollados que facilita la construcción y gestión de aplicaciones distribuidas y microservicios en entornos de nube.
- Spring cloud proporciona varias herramientas o módulos para que desarrolladores construyan patrones de diseño comunes para resolver diferentes problemas de infraestructura en proyectos de microservicios y centrarse en su lógica de negocio.
- Patrones como: Configuración distribuida, Registro de servicios, enrutamiento, llamada de servicios, balanceo de carga, mensajería, monitorización, tolerancia a fallos, entre otros.


## Conocimientos previos
- Java SE 8 o superior
- Base de datos
- Spring Boot y Spring Framework
- Git

## Contenido del curso
- Conceptos básico de los microservicios.
- Registro de microservicios en Eureka Server.
- Manejo de Spring Cloud Gateway.
- Resiliencia y tolerancia de fallos con Spring Cloud Circuit Breaker.
- Manejjo de Spring Cloud Config Server.
- Comunicación de microservicios con Feign Client y Rest Template.
- Trazabilidad distribuida con Zipkin y Sleuth.
- Monitoreo con Prometheus y Grafana.
- Mensajería de microservicios con Apache Kafka.
- Mensajería de microservicios con RabbitMQ.
- Patrón CQRS en la arquitectura de microservicios.
- Seguridad en implementación OKTA, Keycloak y JWT.
- Implementación de protocolo gRPC para comunicación entre microservicios.
- Despliegue de microservicios usando Docker.

## Arquitectura Monolítica
- Las arquitecturas monolíticas se basan en una única aplicación que contiene todas las funcionalidades, en ella todo el código está interconectado, mezclado y cualquier modificación afecta al producto en su totalidad. Trabajando en monolito, los diferentes equipos de desarrollo están obligados a actuar sobre un mismo código, lo que dilata los tiempos y la dificultad.
    ### Características de la arquitectura monolítica
    - Código unificado: Toda la funcionalidad de la aplicación está contenida en un solo código base.
    - Despliegue único: La aplicación se despliega como una sola unidad.
    - Escalabilidad limitada: La escalabilidad se realiza a nivel de toda la aplicación, lo que puede ser ineficiente.
    - Mantenimiento complejo: Las actualizaciones y cambios pueden afectar a toda la aplicación, lo que dificulta el mantenimiento.
    - Desarrollo lento: Los equipos de desarrollo pueden enfrentarse a cuellos de botella debido a la dependencia del código compartido.

## Arquitectura SOA
- La arquitectura orientada a servicios (SOA) es un tipo de diseño de software que permite reutilizar sus elementos gracias a las interfaces de servicios que se comunican a través de protocolos de red con un lenguaje común. Cada servicio es una unidad funcional independiente que puede ser utilizada por diferentes aplicaciones, su diferencia con los microservicios radica en que estos son más pequeños, autónomos y están diseñados para cumplir una única función, su enfoque de soa es para toda la empresa mientras que los microservicios se centran en aplicaciones específicas.

## Arquitectura de Microservicios
- El concepto de microservicios lo definió Martin Fowler en 2014 como un enfoque técnico para desarrollar aplicaciones coomo un conjunto de pequeños servicios. Se trata de unidades funcionales independientes que se ejecutan de manera autónoma en su propio proceso y que se comunican entre sí a través de APIs ligeras, generalmente utilizando HTTP/REST o mensajería asíncrona. Cada microservicio está diseñado para cumplir una única función o responsabilidad dentro de la aplicación global, lo que facilita su desarrollo, despliegue y escalabilidad de manera independiente.
    ### Características de los microservicios
    - Independencia: Cada módulo es un ejecutable en sí mismo y puede ser desplegado sin afectar a los demás.
    - Granularidad: Cada microservicio se encarga de una única funcionalidad de negocio.
    - Aplicación modular: Posibilidad de amplicar o reducir en función de la necesidad.
    - Arquitectura distribuida: Cada microservicio dispone de su propia base de datos. Los modelos de software distribuido se han visto respaldados por la proliferación de proveedores Cloud, que allanan el camino a la puesta en marcha este tipo de arquitecturas.
    - Tecnología heterogénea: Cada microservicio puede ser desarrollado con diferentes lenguajes de programación, frameworks o bases de datos, según las necesidades específicas del servicio.




## Términos comunes
- asíncrono: que no ocurre al mismo proceso o tiempo.
- síncrono: que ocurre al mismo proceso o tiempo.
- escalabilidad: capacidad de un sistema para adaptarse a un aumento de carga.
- allanan: facilitar o hacer más sencillo un proceso.
- granularidad: nivel de detalle o especificidad en la división de un sistema en partes más pequeñas.
- modular: que está compuesto por módulos o partes independientes.
- aplicación distribuida: sistema en el que los componentes están ubicados en diferentes nodos de una red y se comunican entre sí para lograr un objetivo común.
- lombok: biblioteca de Java que reduce el código repetitivo mediante anotaciones, enriqueciendo las clases con funcionalidades como getters, setters, constructores, entre otros.
- spring data jpa: módulo de Spring que facilita la implementación de la capa de acceso a datos utilizando JPA (Java Persistence API) para interactuar con bases de datos relacionales.
- hibernate: framework de mapeo objeto-relacional (ORM) para Java que simplifica la interacción entre aplicaciones Java y bases de datos relacionales al mapear objetos Java a tablas de bases de datos.
- ddl: Data Definition Language, es un subconjunto de SQL utilizado para definir y gestionar la estructura de las bases de datos, incluyendo la creación, modificación y eliminación de tablas y otros objetos.
- dialect: en el contexto de Hibernate, un dialecto es una configuración específica que adapta las consultas SQL generadas por Hibernate a las particularidades de un sistema de gestión de bases de datos (DBMS) específico.
- mysql driver: controlador JDBC que permite la conexión y comunicación entre aplicaciones Java y bases de datos MySQL.
- jdbc: Java Database Connectivity, es una API de Java que permite la conexión y ejecución de consultas en bases de datos relacionales.
- spring boot: framework de desarrollo rápido para aplicaciones Java que simplifica la configuración y el despliegue mediante convenciones y herramientas integradas.
- eureka server: servicio de descubrimiento de microservicios que permite registrar y localizar servicios en una arquitectura distribuida.
- error throw: mecanismo en programación para indicar que ha ocurrido una condición excepcional o error durante la ejecución de un programa.
- restcontroller: anotación en Spring que combina @Controller y @ResponseBody, utilizada para definir controladores que manejan solicitudes RESTful y devuelven datos directamente en el cuerpo de la respuesta.
- responseentity: clase en Spring que representa una respuesta HTTP completa, incluyendo el cuerpo, los encabezados y el estado.
- restcontrolleradvice: anotación en Spring que permite manejar excepciones de manera global para todos los controladores REST, proporcionando una forma centralizada de gestionar errores y respuestas.
- @PostMapping, @GetMapping : anotación en Spring que se utiliza para mapear solicitudes HTTP POST a métodos específicos en un controlador.
- GET, POST, PUT, DELETE: son métodos HTTP utilizados para realizar diferentes operaciones en recursos web. GET recupera datos, POST crea nuevos recursos, PUT actualiza recursos existentes y DELETE elimina recursos.
- @PathVariable: anotación que se utiliza en Spring para extraer valores de variables de ruta en las URL y pasarlos como parámetros a los métodos del controlador.
- @RequestParam: anotación en Spring que se utiliza para extraer parámetros de consulta de una solicitud HTTP y pasarlos como argumentos a los métodos del controlador.
- http: protocolo de transferencia de hipertexto, es el protocolo subyacente utilizado para la comunicación en la web.
- @Service, @Component, @Repository: anotaciones en Spring que se utilizan para marcar clases como componentes gestionados por el contenedor de Spring, representando servicios, componentes genéricos y repositorios de datos respectivamente.
- subyacente: que está debajo o sirve de base a algo.
- api: Interfaz de Programación de Aplicaciones, es un conjunto de reglas y protocolos que permiten la comunicación entre diferentes aplicaciones o sistemas.
- rest: Representational State Transfer, es un estilo arquitectónico para diseñar servicios web que utilizan los métodos HTTP y se basan en recursos identificados por URL.
- api rest y restful: API REST es una interfaz que sigue los principios de REST para permitir la comunicación entre sistemas. RESTful se refiere a la implementación de servicios web que cumplen con los principios REST, utilizando métodos HTTP y recursos identificados por URL.
- diferencia entre extends y implements: "extends" se utiliza para heredar de una clase base, mientras que "implements" se utiliza para implementar una interfaz en una clase para definir su comportamiento en sus métodos.
- map: estructura de datos en Java que almacena pares clave-valor, permitiendo la asociación y recuperación eficiente de valores basados en sus claves.
- list: estructura de datos en Java que alamacena una colección ordenada de elementos, permitiendo duplicados y acceso secuencial a través de índices.
- arraylist: implementación de la interfaz List en Java que utiliza un array dinámico para almacenar elementos, permitiendo acceso rápido y eficiente a los mismos.
- HashMap, Hashtable: ambas son implementaciones de la interfaz Map en Java que almacenan pares clave-valor. La principal diferencia es que HashMap permite claves y valores nulos y no es sincronizado, mientras que Hashtable no permite nulos y es sincronizado, lo que lo hace seguro para el acceso concurrente.