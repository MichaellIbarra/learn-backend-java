# learn-backend-java

## Aquitectura de software 


### Estilos arquitectónicos
1. ESTILOS ARQUITECTÓNICOS (El nivel MACRO o de Despliegue)
Define cómo se distribuye y despliega el sistema a gran escala. Responde a la pregunta: "¿El sistema es una sola pieza o varias piezas comunicándose?"

Monolítica: Toda la aplicación (lógica, acceso a datos, interfaz) se compila y despliega como un solo bloque. Si algo falla, puede caer todo el sistema.
Microservicios: La aplicación se divide en servicios pequeños, independientes y delimitados por contexto de negocio. Cada uno tiene su propia base de datos y se comunican por red (APIs, eventos).
SOA (Arquitectura Orientada a Servicios): Similar a los microservicios pero más tradicional/enpresarial ("Service by Service"). Los servicios suelen ser más grandes y se comunican a través de un bus central (ESB).
Serverless (Sin Servidor): El código se ejecuta en funciones temporales gestionadas por la nube (ej. AWS Lambda) que solo se activan cuando hay una petición.
2. ARQUITECTURA DE APLICACIÓN (El nivel MICRO o Estructura Interna)
Define cómo se organiza el código dentro de un proyecto. Responde a la pregunta: "¿Cómo separo las responsabilidades en mi código para que sea mantenible?"
(Nota: Un Monolito o un Microservicio individual pueden usar internamente cualquiera de estas arquitecturas).

Arquitectura en Capas (N-Tier): El código se organiza de forma horizontal (Controlador -> Servicio -> Repositorio). Es la más tradicional.
Arquitectura Hexagonal (Puertos y Adaptadores): El "Core" (la lógica de negocio) está en el centro, aislado totalmente de la base de datos o de la web. Se usan interfaces (puertos) e implementaciones (adaptadores) para conectarse con el exterior.
Arquitectura Limpia (Clean Architecture / Onion): Muy similar a la hexagonal. Define reglas estrictas donde las dependencias siempre apuntan hacia adentro, protegiendo las "Entidades" y "Casos de Uso" del negocio.

1. Patrones de Diseño (Los tradicionales, ej. GoF - Gang of Four)
Alcance: Micro (Nivel de clases y objetos).
Problema que resuelven: Cómo se crean los objetos, cómo se estructuran las clases para evitar acoplamiento, cómo se envían mensajes entre un pequeño grupo de clases.
Ejemplos: Singleton, Factory, Observer, Strategy, Decorator.
Dónde viven: Dentro de un servicio, repositorio o controlador. Son detalles de implementación de código.
2. Patrones Arquitectónicos (El nivel intermedio/macro)
Alcance: Macro (Nivel de componentes, servicios, bases de datos, APIs).
Problema que resuelven: Cómo se comunican grandes bloques de la aplicación entre sí, cómo fluye la información a nivel de sistema, cómo escalan los componentes. Afectan la infraestructura y todo el flujo de la aplicación.
Ejemplos: CQRS, Event-Driven, Event Sourcing, BFF.
Dónde viven: Dictan cómo se relacionan los servicios y bases de datos enteros.
