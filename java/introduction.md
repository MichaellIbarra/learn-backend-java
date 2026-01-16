# Introdución a Java
- Java es un lenguaje de programación de propósito general, concurrente, orientado a objetos y basado en clases.
- Fue desarrollado por Sun Microsystems (ahora propiedad de Oracle) y lanzado en 1995.
- Java está diseñado para tener la menor cantidad de dependencias de implementación posibles, lo que permite que las aplicaciones Java se ejecuten en cualquier sistema que tenga una Máquina Virtual Java (JVM).
- La sintaxis de Java se basa en gran medida en C y C++, lo que facilita a los programadores familiarizados con esos lenguajes aprender Java.

## Versiones de Java
Java ha evolucionado significativamente desde su lanzamiento inicial. Aquí se presenta una descripción detallada de cada versión y su impacto en la evolución del lenguaje:

### Era Temprana (1996-2003)
- **Java 1.0 (1996)**: La primera versión oficial de Java. Estableció los fundamentos del lenguaje con características básicas como orientación a objetos, "write once, run anywhere" y la JVM.
- **Java 1.1 (1997)**: Introdujo eventos AWT, clases internas, JavaBeans, JDBC, RMI y reflexión. Marcó el primer gran refinamiento del lenguaje.
- **Java 1.2 (1998)** - **Java 2**: Gran hito que introdujo el concepto de "ediciones" (J2SE, J2EE, J2ME). Agregó Swing GUI, Collections Framework, y JIT compiler como estándar.
- **Java 1.3 (2000)**: Introdujo HotSpot JVM, JavaSound API, JNDI incluido en el core, y mejoras significativas en el rendimiento.
- **Java 1.4 (2002)**: Agregó assertions, expresiones regulares, NIO (New I/O), logging API, XML parsing, y la criptografía integrada.

### Era de Maduración (2004-2013)
- **Java 5 (2004)** - **Java 1.5**: Revolución del lenguaje con genéricos, anotaciones, autoboxing/unboxing, enumeraciones, bucles for-each (enhanced for), varargs, y concurrencia mejorada.
- **Java 6 (2006)**: Enfoque en rendimiento y usabilidad. Introdujo scripting support, Compiler API, JAXB, mejoras en GUI con SystemTray, y Desktop API.
- **Java 7 (2011)**: Diamond operator, switch con strings, try-with-resources, multi-catch exceptions, ForkJoinPool, y NIO.2 para mejor manejo de archivos.
- **Java 8 (2014)**: Transformación mayor con expresiones lambda, Stream API, nueva API de fecha/hora (java.time), default methods en interfaces, y Nashorn JavaScript engine.

### Era Moderna - Ciclo de 6 Meses (2017-presente)
- **Java 9 (2017)**: Sistema de módulos (Project Jigsaw), JShell (REPL), API de Process mejorada, y Collection factory methods.
- **Java 10 (2018)**: Inferencia de tipos con `var`, garbage collector G1 paralelo, y Application Class-Data Sharing.
- **Java 11 (2018) - LTS**: HTTP Client estándar, eliminación de Java EE y CORBA modules, nuevos métodos en String, y Flight Recorder open source.
- **Java 12 (2019)**: Switch expressions (preview), Shenandoah GC experimental, y JVM Constants API.
- **Java 13 (2019)**: Text blocks (preview), switch expressions mejoradas, y reimplement legacy Socket API.
- **Java 14 (2020)**: Records (preview), pattern matching para instanceof (preview), packaging tool incubator, y Text Blocks estándar.
- **Java 15 (2020)**: Text Blocks finalizados, Sealed Classes (preview), Hidden Classes, y ZGC y Shenandoah GC en producción.
- **Java 16 (2021)**: Records finalizados, Pattern Matching para instanceof finalizado, Vector API (incubator), y Strong Encapsulation por defecto.
- **Java 17 (2021) - LTS**: Sealed Classes finalizadas, switch pattern matching (preview), eliminación de AOT y JIT compiler experimentales, y nuevas APIs de macOS rendering.
- **Java 18 (2022)**: UTF-8 como charset por defecto, Simple Web Server, Code Snippets en JavaDoc, y Vector API (segunda incubación).
- **Java 19 (2022)**: Virtual Threads (preview), Structured Concurrency (incubator), Pattern Matching para switch (tercera preview), y Foreign Function & Memory API (preview).
- **Java 20 (2023)**: Scoped Values (incubator), Record Patterns (segunda preview), Pattern Matching para switch (cuarta preview), y Foreign Function & Memory API (segunda preview).

## Ediciones de Java (SE, EE, ME)
Java se divide en diferentes ediciones, cada una diseñada para tipos específicos de aplicaciones y entornos:
### Java SE (Standard Edition)
**Descripción**: La plataforma core de Java que proporciona las APIs fundamentales del lenguaje.
- **Componentes principales**: 
  - APIs básicas (Collections, I/O, Networking, Concurrency)
  - Swing y AWT para interfaces gráficas
  - JDBC para conectividad con bases de datos
  - Security APIs y Cryptography
  - XML processing y JAXP
- **Uso típico**: Aplicaciones de escritorio, aplicaciones cliente-servidor básicas, herramientas de línea de comandos
- **JVM incluida**: Sí, viene con implementación completa de la JVM
- **Target**: Desarrolladores que crean aplicaciones estándar y bibliotecas base
### Java EE (Enterprise Edition) / Jakarta EE
**Descripción**: Extensión de Java SE enfocada en desarrollo de aplicaciones empresariales escalables y distribuidas.
- **Componentes principales**:
  - **Servlets y JSP**: Para aplicaciones web
  - **EJB (Enterprise JavaBeans)**: Para lógica de negocio distribuida
  - **JPA (Java Persistence API)**: Para mapeo objeto-relacional
  - **JAX-RS y JAX-WS**: Para servicios web REST y SOAP
  - **JMS (Java Message Service)**: Para messaging asíncrono
  - **CDI (Contexts and Dependency Injection)**: Para inyección de dependencias
  - **JSF (JavaServer Faces)**: Para interfaces web basadas en componentes
- **Historia**: Oracle transfirió Java EE a Eclipse Foundation en 2017, donde se renombró como **Jakarta EE**
- **Uso típico**: Aplicaciones web empresariales, microservicios, sistemas distribuidos de gran escala
- **Servidores de aplicación**: WildFly, GlassFish, WebLogic, WebSphere, Tomcat (parcial)
### Java ME (Micro Edition)
**Descripción**: Versión reducida de Java diseñada para dispositivos con recursos limitados.
- **Características**:
  - **Footprint mínimo**: APIs reducidas para conservar memoria
  - **Optimización de rendimiento**: Especialmente para procesadores limitados
  - **Perfiles específicos**: MIDP (Mobile Information Device Profile), Foundation Profile
  - **Configuraciones**: CLDC (Connected Limited Device Configuration), CDC (Connected Device Configuration)
- **Evolución**: 
  - Muy popular en teléfonos móviles pre-smartphone (2000-2010)
  - Ahora usado principalmente en IoT, sistemas embebidos, y dispositivos industriales
  - Competencia de Android (Java-based) y iOS redujo su relevancia en móviles
- **Uso actual**: Dispositivos IoT, sistemas embebidos, smart cards, televisores inteligentes, electrodomésticos conectados

### Comparación y Relación entre Ediciones
- **Java SE** es la base: tanto EE como ME extienden o subconjunto de SE
- **Compatibilidad**: Las aplicaciones Java EE pueden usar APIs de Java SE, pero no viceversa
- **Deployment**: SE en JVMs estándar, EE en servidores de aplicación, ME en dispositivos específicos
- **Licenciamiento**: SE es gratuito, EE depende del servidor de aplicación, ME varía según implementación

## Distribuciones y Licencias de JDK
Jdk es el kit de desarrollo de Java que incluye el JRE (Java Runtime Environment) y herramientas para desarrollar aplicaciones Java.
Java se distribuye a través de múltiples proveedores, cada uno con diferentes modelos de licencia y características:
### OpenJDK (Open Source)
**Descripción**: Implementación de referencia gratuita y de código abierto de Java SE.
- **Licencia**: GPL v2 con Classpath Exception - completamente gratuito
- **Beneficios**:
  - **Gratuito para uso comercial**: Sin restricciones de licencia para producción
  - **Código abierto**: Transparencia total, posibilidad de contribuir y modificar
  - **Base de otras distribuciones**: Fundamento para la mayoría de JDKs comerciales
  - **Actualizaciones regulares**: Nuevas versiones cada 6 meses
  - **Sin vendor lock-in**: No dependes de un proveedor específico
  - **Comunidad activa**: Respaldo de múltiples organizaciones y desarrolladores
- **Desventajas**:
  - **Soporte limitado**: No incluye soporte técnico comercial directo
  - **Responsabilidad propia**: Debes gestionar actualizaciones y seguridad
  - **Menos herramientas**: No incluye herramientas comerciales adicionales
  - **Documentación básica**: Menos recursos de enterprise comparado con versiones comerciales
### Oracle JDK (Comercial)
**Descripción**: Distribución comercial de Oracle con soporte enterprise y herramientas adicionales.
- **Licencia**: Oracle Technology Network License Agreement (requiere licencia comercial para uso en producción)
- **Beneficios**:
  - **Soporte comercial**: Acceso directo al soporte técnico de Oracle
  - **Actualizaciones de seguridad extendidas**: Para versiones LTS más allá del ciclo público
  - **Herramientas adicionales**: Java Mission Control, Flight Recorder comercial
  - **Certificaciones**: Ampliamente certificado para aplicaciones empresariales
  - **Garantías**: SLA y garantías de rendimiento para entornos críticos
- **Desventajas**:
  - **Costo**: Requiere licencia de pago para uso comercial en producción
  - **Restricciones de licencia**: Limitaciones en redistribución y uso
  - **Vendor lock-in**: Dependencia de Oracle para soporte y actualizaciones
### Amazon Corretto (Gratuito)
**Descripción**: Distribución gratuita de OpenJDK con soporte a largo plazo de Amazon.
- **Licencia**: GPL v2 con Classpath Exception - gratuito para todos los usos
- **Beneficios**:
  - **Completamente gratuito**: Incluye soporte de seguridad sin costo
  - **LTS extendido**: Amazon proporciona actualizaciones de seguridad por años adicionales
  - **Optimizado para AWS**: Rendimiento mejorado en servicios de Amazon
  - **Ampliamente probado**: Usado internamente por Amazon en gran escala
  - **Soporte multi-plataforma**: Disponible para Linux, Windows, macOS
- **Características especiales**:
  - Actualizaciones de seguridad trimestrales garantizadas
  - Optimizaciones específicas para cargas de trabajo en la nube
  - Compatible con OpenJDK estándar

### Recomendaciones de Uso
#### Para Desarrollo y Aprendizaje:
- **OpenJDK**: Ideal para proyectos personales, aprendizaje y desarrollo
- **Amazon Corretto**: Excelente opción gratuita con soporte extendido
#### Para Producción Empresarial:
- **Amazon Corretto**: Para aplicaciones en la nube, especialmente AWS
- **Oracle JDK**: Para entornos que requieren soporte comercial garantizado
- **Azul Zulu Enterprise**: Para necesidades específicas de rendimiento y soporte

## Maven
Maven **NO es una distribución de JDK**, es una herramienta de gestión de dependencias y construcción de proyectos Java.

**¿Qué es Maven?**
- **Herramienta de automatización**: Para compilar, empaquetar y gestionar proyectos Java
- **Gestor de dependencias**: Descarga automáticamente librerías externas que tu proyecto necesita
- **Estándar de estructura**: Define una estructura estándar de directorios para proyectos
- **Sistema de plugins**: Extensible mediante plugins para diferentes tareas

**Características principales:**
- **POM (Project Object Model)**: Archivo XML (`pom.xml`) que describe el proyecto
- **Repositorios**: Central, local y remotos para almacenar y descargar dependencias
- **Ciclo de vida**: Fases predefinidas (compile, test, package, install, deploy)
- **Arquetipos**: Plantillas para crear diferentes tipos de proyectos

**Relación con JDK:**
- Maven **requiere** un JDK instalado para funcionar
- Puedes configurar qué versión de Java usar en el `pom.xml`
- Es independiente de la distribución de JDK que elijas (OpenJDK, Oracle JDK, Corretto, etc.)

## Independiente de la Plataforma
- La idea de Gosling era que el lenguaje java fuera multiplataforma y su promesa era: "Write Once, Run Anywhere" (Escribir una vez, ejecutar en cualquier lugar).
- Un lenguaje independiente del sistema operativo con un entorno de ejecución llamado JVM (Java Virtual Machine).
### El Bytecode
- Todo el código java se compila en un lenguaje intermedio que no es 100% binario, esta justo entremedio de la maquina y el código fuente y se lo conoce como Bytecode.
- Código Fuentes (.java) --> Java Compiler (javac) -> Bytecode (.class) --> JVM --> Código Máquina (binario) -> sistema operativo


## Términos comunes
- api: Interfaz de programación de aplicaciones, un conjunto de herramientas y protocolos para construir software.
- binario: Código que una computadora puede entender directamente.
- lenguaje tipado: Un lenguaje de programación que requiere que las variables tengan un tipo específico (como entero, cadena, etc.).
