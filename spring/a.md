
La diferencia entre `@Configuration` y `@Component` radica en su propósito y uso específico:

## @Configuration
- **Propósito**: Marca una clase como fuente de definiciones de beans
- **Uso**: Contiene métodos anotados con `@Bean` 
- **Procesamiento**: Spring usa CGLIB para crear proxies y asegurar que los métodos `@Bean` se llamen una sola vez (singleton)
- **Contexto**: Para configuración de la aplicación

```java
@Configuration
public class PasswordEncoderConfig {
    @Bean  // Este método define un bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}
```

## @Component
- **Propósito**: Marca una clase como componente Spring para auto-detección
- **Uso**: La clase completa se convierte en un bean
- **Procesamiento**: Spring crea una instancia directa de la clase
- **Contexto**: Para servicios, utilidades, componentes de negocio

```java
@Component
public class JwtProvider {
    @Value("${jwt.secret}")
    private String secret;
    
    // La clase completa es un bean
}
```

## Diferencias clave:

| Aspecto | @Configuration | @Component |
|---------|----------------|------------|
| **Bean creation** | Métodos `@Bean` | Toda la clase |
| **Proxy** | Sí (CGLIB) | No |
| **Singleton enforcement** | Automático para `@Bean` | Manual si es necesario |
| **Uso típico** | Configuración | Servicios/Utilidades |

## Cuándo usar cada uno:

- **@Configuration**: Cuando necesitas definir múltiples beans o configuración compleja
- **@Component**: Cuando la clase completa representa un servicio o utilidad

En tu caso, `PasswordEncoderConfig` usa `@Configuration` correctamente porque define un bean, mientras que `JwtProvider` usa `@Component` porque toda la clase es el servicio JWT.

Basándome en tu código, te explico las utilidades específicas de cada anotación:

## @Configuration en `PasswordEncoderConfig`

**Utilidad específica:**
- Defines **cómo crear beans** que Spring necesita
- El método `passwordEncoder()` le dice a Spring: "cuando alguien necesite un `PasswordEncoder`, usa este `BCryptPasswordEncoder`"
- Spring puede **inyectar** este bean en otras clases

```java
// Otra clase puede usar el bean que definiste
@Service
public class UserService {
    @Autowired
    private PasswordEncoder passwordEncoder; // Spring inyecta el BCryptPasswordEncoder
    
    public void saveUser(String password) {
        String encoded = passwordEncoder.encode(password); // Usa tu configuración
    }
}
```

## @Component en `JwtProvider`

**Utilidad específica:**
- Convierte **toda la clase** en un bean de Spring
- Otras clases pueden usar `JwtProvider` directamente
- Spring maneja la **inyección de propiedades** (`@Value`)

```java
// Otras clases pueden usar JwtProvider
@RestController
public class AuthController {
    @Autowired
    private JwtProvider jwtProvider; // Spring inyecta tu JwtProvider
    
    public String login() {
        // Usa los métodos de jwtProvider
        return jwtProvider.generateToken();
    }
}
```

## Sin estas anotaciones:

**Problema sin @Configuration:**
```java
// Tendrías que crear manualmente en cada lugar:
PasswordEncoder encoder = new BCryptPasswordEncoder(); // Repetitivo y sin control
```

**Problema sin @Component:**
```java
// Tendrías que crear manualmente:
JwtProvider jwt = new JwtProvider(); // Sin inyección de propiedades automática
```

## Resumen de utilidades:

- **@Configuration**: Spring **fabrica beans** para ti y los inyecta donde los necesites
- **@Component**: Spring **maneja la clase** por ti (creación, inyección de propiedades, ciclo de vida)

Ambas permiten que Spring **gestione automáticamente** tus objetos en lugar de que los crees manualmente.
Necesitas **AMBOS** conjuntos de dependencias. Te explico por qué:

## Estructura de JJWT (modular desde 0.10.0):

JJWT se divide en 3 módulos principales:

### 1. **jjwt-api** (obligatorio)
- Contiene las **interfaces y clases públicas**
- Es lo que usas en tu código (`Jwts`, `Claims`, etc.)
- **Scope**: `compile` (por defecto)

### 2. **jjwt-impl** (obligatorio en runtime)
- Contiene la **implementación** de las interfaces
- Necesario para que funcione en ejecución
- **Scope**: `runtime`

### 3. **jjwt-jackson** (recomendado)
- Serialización/deserialización JSON usando **Jackson**
- Alternativas: `jjwt-gson` o `jjwt-orgjson`
- **Scope**: `runtime`

## Configuración correcta para tu `pom.xml`:

```xml
<!-- API (compile time) -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.13.0</version>
</dependency>

<!-- Implementación (runtime) -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.13.0</version>
    <scope>runtime</scope>
</dependency>

<!-- Serialización JSON (runtime) -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.13.0</version>
    <scope>runtime</scope>
</dependency>
```

## ¿Qué pasa si solo usas jjwt-api?

❌ **Error en runtime**: `ClassNotFoundException` porque falta la implementación real.

## ¿Qué pasa si solo usas jjwt-impl y jjwt-jackson?

❌ **Error en compile time**: No puede compilar porque faltan las interfaces públicas que usas en tu código.

## Resumen:

- **jjwt-api**: Para **compilar** tu código
- **jjwt-impl**: Para **ejecutar** tu código  
- **jjwt-jackson**: Para **serializar** claims a JSON

**Respuesta directa**: Usa las **3 dependencias** como mostré arriba. Es la configuración estándar y recomendada por la documentación oficial de JJWT.
# Explicación detallada de `AuthFilter`

## `extends AbstractGatewayFilterFactory<AuthFilter.Config>`

- **`extends`**: Hereda de `AbstractGatewayFilterFactory` para crear filtros personalizados en Spring Cloud Gateway
- **`<AuthFilter.Config>`**: Es un **tipo genérico** (generics en Java). Indica que este filtro usa la clase `Config` para su configuración
- Los `<>` definen **parámetros de tipo**, permitiendo crear clases reutilizables con diferentes tipos

---

## Clase estática `Config`

```java
public static class Config {
}
```

- **`static`**: Puede existir sin una instancia de `AuthFilter`
- Se usa para definir propiedades de configuración del filtro (aunque aquí está vacía)
- Spring Cloud Gateway la utiliza para mapear configuraciones desde `application.yml`

---

## Constructor con `super(Config.class)`

```java
public AuthFilter(WebClient.Builder builder) {
    super(Config.class);
    this.webClient = builder;
}
```

- **`super(Config.class)`**: Llama al constructor de la clase padre (`AbstractGatewayFilterFactory`)
- Le indica qué clase usar para la configuración
- Es **obligatorio** para que Spring sepa qué tipo de configuración esperar

---

## Método `apply()`

```java
public GatewayFilter apply(AuthFilter.Config config)
```

- Es el método **principal** del filtro
- Spring lo llama automáticamente cuando una petición pasa por el gateway
- Retorna un `GatewayFilter` que contiene la lógica de filtrado

---

## `.map()` y `.flatMap()`

```java
.map(t -> {
    t.getToken();
    return exchange;
}).flatMap(chain::filter);
```

### **`map()`**
- Transforma el resultado de `TokenDto` 
- Aquí llama a `t.getToken()` (aunque no usa el resultado)
- Retorna el `exchange` original para continuar con la petición

### **`flatMap(chain::filter)`**
- **`flatMap`**: "Aplana" Monos anidados (evita `Mono<Mono<Void>>`)
- **`chain::filter`**: Continúa con el siguiente filtro en la cadena
- Es como decir: "validación exitosa, deja pasar la petición"

---

## ¿Por qué termina en `flatMap`?

Porque estamos trabajando con **programación reactiva** (WebFlux):

1. `webClient.post()` → Retorna `Mono<TokenDto>`
2. `.map()` → Retorna `Mono<ServerWebExchange>`
3. `chain.filter()` → Retorna `Mono<Void>`
4. **Sin `flatMap`** → Tendríamos `Mono<Mono<Void>>` ❌
5. **Con `flatMap`** → Obtenemos `Mono<Void>` ✅

---

## Flujo completo

1. Verifica si existe header `Authorization`
2. Valida formato `Bearer token`
3. Llama al servicio de autenticación para validar el token
4. Si es válido → continúa con la petición
5. Si falla → retorna error 400
# Explicación de `exchange` y `chain`

## `ServerWebExchange exchange`

Es el **contexto completo de la petición HTTP** en Spring WebFlux. Contiene:

### **Request (Petición)**
```java
exchange.getRequest()  // Accede a la petición HTTP
    .getHeaders()      // Headers HTTP (Authorization, Content-Type, etc.)
    .getPath()         // Ruta (/api/users)
    .getMethod()       // Método HTTP (GET, POST, etc.)
    .getBody()         // Cuerpo de la petición
```

### **Response (Respuesta)**
```java
exchange.getResponse()      // Accede a la respuesta HTTP
    .setStatusCode()        // Define código de estado (200, 400, 500)
    .getHeaders()           // Modifica headers de respuesta
    .writeWith()            // Escribe el cuerpo de la respuesta
```

### **Atributos**
```java
exchange.getAttributes()    // Almacena datos entre filtros
```

**Es como el `HttpServletRequest` y `HttpServletResponse` combinados en programación reactiva.**

---

## `GatewayFilterChain chain`

Es la **cadena de filtros** del Gateway. Representa todos los filtros que se ejecutan en orden.

### **¿Qué hace `chain.filter(exchange)`?**

```java
chain.filter(exchange)  // Pasa al SIGUIENTE filtro
```

1. Ejecuta el **siguiente filtro** en la cadena
2. Si no hay más filtros, **envía la petición al servicio destino**
3. Retorna un `Mono<Void>` (promesa de que terminará)

---

## Ejemplo práctico

```java
return ((exchange, chain) -> {
    // 1️⃣ Modificas la petición (lees headers, validas token)
    String token = exchange.getRequest().getHeaders().get("Authorization");
    
    // 2️⃣ Si hay error, terminas aquí
    if (token == null) {
        return onError(exchange, HttpStatus.BAD_REQUEST);
    }
    
    // 3️⃣ Si todo está bien, PASAS al siguiente filtro
    return chain.filter(exchange);  // ← Continúa el flujo
});
```

---

## Flujo completo en tu código

```java
// Validar token con servicio externo
return webClient.build()
    .post()
    .uri("http://auth-service/validate")
    .retrieve()
    .bodyToMono(TokenDto.class)
    .map(t -> exchange)           // ← Token válido, devuelve exchange
    .flatMap(chain::filter);      // ← Pasa al SIGUIENTE filtro o servicio
```

### Sin `chain.filter()` → La petición **NO llegaría** al servicio destino

---

## Resumen

| Componente | Propósito |
|------------|-----------|
| `exchange` | Contiene la petición y respuesta HTTP completa |
| `chain` | Permite continuar con el siguiente filtro/servicio |
| `chain.filter(exchange)` | "Deja pasar" la petición al siguiente paso |

**Piensa en `chain` como una cinta transportadora: si no llamas a `chain.filter()`, la petición se detiene en tu filtro.** 
La clase `Config` estática es parte del patrón de diseño de Spring Cloud Gateway para filtros personalizados.

## ¿Por qué existe?

`AbstractGatewayFilterFactory<T>` es una clase genérica que espera un tipo de configuración. Aunque no la uses ahora, está ahí por estas razones:

1. **Requisito del framework**: `AbstractGatewayFilterFactory` necesita un tipo genérico para su configuración
2. **Extensibilidad futura**: Si necesitas agregar parámetros configurables al filtro (por ejemplo, rutas excluidas, timeout, etc.)
3. **Convención de Spring**: Todos los filtros siguen este patrón uniforme

## Ejemplo de uso si necesitaras configuración:

```java
public static class Config {
    private String authServiceUrl;
    private List<String> excludedPaths;
    
    // getters y setters
}
```

Y en `application.yml`:
```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: my-route
          uri: http://my-service
          filters:
            - name: AuthFilter
              args:
                authServiceUrl: http://service-auth
                excludedPaths: /public,/health
```

**En resumen**: Es una clase placeholder requerida por el framework. Puedes dejarla vacía si no necesitas configuración externa, pero debe existir para cumplir con la arquitectura de Spring Cloud Gateway.
## ¿Cómo funciona `@ConfigurationProperties`?

Spring Boot usa **convención sobre configuración** para mapear automáticamente el YAML a clases Java:

### Mapeo automático

1. **Prefix `protected-paths`** → Busca la raíz en el YAML
2. **Campo `paths`** → Busca la propiedad `paths` bajo `protected-paths`
3. **Tipo `List<PathConfig>`** → Convierte cada elemento del array YAML a objetos `PathConfig`

### Proceso de binding

```yaml
protected-paths:        # ← prefix del @ConfigurationProperties
  paths:                # ← nombre del campo en la clase
    - uri: "..."        # ← propiedad de PathConfig
      methods: [...]    # ← propiedad de PathConfig
      roles: [...]      # ← propiedad de PathConfig
```

Spring Boot:
1. **Detecta** `@ConfigurationProperties(prefix = "protected-paths")`
2. **Busca** en el YAML la clave `protected-paths`
3. **Mapea** `protected-paths.paths` al campo `private List<PathConfig> paths`
4. **Crea objetos** `PathConfig` por cada elemento del array
5. **Asigna valores** usando los setters de `PathConfig` (por eso necesita `@Data` o `@Setter`)

### Requisitos para que funcione

- `@Component` o `@Configuration` en la clase
- `@ConfigurationProperties` con el prefix correcto
- **Getters y setters** (`@Data`, `@Getter`, `@Setter`)
- **Nombres exactos** entre el YAML y los campos Java
- Dependencia `spring-boot-configuration-processor` (opcional pero recomendada)

Es **"magia" de Spring Boot** que usa reflexión para mapear propiedades automáticamente. 🪄
## ¿Es perjudicial usar `(/.*)?" en el patrón?

**No es perjudicial**, pero debes entender qué estás permitiendo:

### ✅ Ventajas

- **Flexibilidad**: Coincide con rutas con o sin parámetros
- **Mantenimiento**: Un solo patrón para múltiples casos
- **Práctico**: Cubre casos comunes sin duplicar configuración

### ⚠️ Consideraciones importantes

```yaml
- uri: "/api/v1/users(/.*)?"
  methods:
    - POST
  roles:
    - ADMIN
```

Este patrón coincide con:
- ✅ `/api/v1/users` (crear usuario)
- ✅ `/api/v1/users/123` (actualizar usuario específico)
- ✅ `/api/v1/users/123/profile` (ruta anidada)
- ✅ `/api/v1/users/cualquier-cosa` (todas las sub-rutas)

### 🎯 Recomendación

**Si quieres control granular**, separa los patrones:

```yaml
protected-paths:
  paths:
    # Crear usuario (POST /api/v1/users)
    - uri: "/api/v1/users"
      methods:
        - POST
      roles:
        - ADMIN
    
    # Operaciones sobre usuarios específicos (PUT/DELETE /api/v1/users/123)
    - uri: "/api/v1/users/[0-9]+"
      methods:
        - PUT
        - DELETE
        - PATCH
      roles:
        - ADMIN
    
    # Obtener usuarios
    - uri: "/api/v1/users(/.*)?"
      methods:
        - GET
      roles:
        - ADMIN
        - USER
```

### 📋 Conclusión

El patrón `(/.*)?"` **NO es perjudicial** si todas las sub-rutas deben tener la misma restricción de seguridad. Si necesitas permisos diferentes para rutas específicas, usa patrones más específicos. 🔐
EXPRESIONES REGULARES
## ¿Qué pasa si NO usas `optional:`?

Si **no usas** `optional:configserver:http://localhost:8085`, y el **Config Server no está disponible**, tu microservicio **FALLARÁ al iniciar** con un error similar a:

```
Could not locate PropertySource and the fail fast property is set, failing
```

## ¿Qué hace `optional:`?

Permite que tu aplicación **inicie INCLUSO SI el Config Server no está disponible**:

```yaml
spring:
  config:
    import: optional:configserver:http://localhost:8085
```

## Comparación:

| Sin `optional:` | Con `optional:` |
|----------------|----------------|
| ❌ Falla si Config Server está caído | ✅ Inicia con configuración local |
| ❌ No puede funcionar sin Config Server | ✅ Usa `application.yml` local como fallback |
| ✅ Asegura que siempre use config centralizada | ⚠️ Puede funcionar con config desactualizada |

## ¿Cuándo usar cada uno?

### **Sin `optional:`** (Recomendado para producción)
```yaml
spring:
  config:
    import: configserver:http://localhost:8085
```
- ✅ Garantiza que siempre use la configuración centralizada
- ✅ Evita inconsistencias entre microservicios
- ❌ Requiere que Config Server esté siempre disponible

### **Con `optional:`** (Útil para desarrollo local)
```yaml
spring:
  config:
    import: optional:configserver:http://localhost:8085
```
- ✅ Permite desarrollar sin levantar Config Server
- ✅ Usa configuración local si Config Server no responde
- ⚠️ Puede causar comportamiento inconsistente en producción

## Recomendación:

Para **desarrollo local**, usa `optional:` para poder trabajar sin depender del Config Server:

```yaml
spring:
  config:
    import: optional:configserver:http://localhost:8085
```

Para **producción**, quita `optional:` para asegurar que siempre use la configuración centralizada.
## Respuesta: NO es recomendado registrar Config Server en Eureka

**Config Server debe mantenerse estático** por las siguientes razones:

### ❌ Problemas de registrar Config Server en Eureka:

1. **Dependencia circular**: Los servicios necesitan Config Server para obtener su configuración, incluyendo la configuración de Eureka
2. **Bootstrap problem**: Config Server debe estar disponible ANTES que Eureka para que los servicios obtengan su configuración
3. **Complejidad innecesaria**: Config Server es infrastructure, no business logic

### ✅ Configuración correcta actual:

Tu configuración actual es la **práctica recomendada**:

````yaml
spring:
  application:
    name: service-user
  config:
    import: optional:configserver:http://localhost:8085  # ✅ Correcto: URL estática
````

### 🐳 Para Docker, necesitas cambiar solo para containers:

````yaml
spring:
  application:
    name: service-user
  config:
    import: optional:configserver:http://server-config-server:8085  # Nombre del container
  datasource:
    url: jdbc:h2:mem:userdb
    driver-class-name: org.h2.Driver
    username: sa
    password: password

eureka:
  client:
    service-url:
      defaultZone: http://server-discovery:8761/eureka

server:
  port: 8080
````

### 📝 Configuraciones Docker necesarias:

````yaml
spring:
  application:
    name: server-config-server
  cloud:
    config:
      server:
        git:
          uri: https://github.com/MichaellIbarra/spring-cloud-hotel-microservices-mvc.git
          clone-on-start: true
          default-label: main
          search-paths: hotel-config-repo

server:
  port: 8085
````

### 🔧 Docker Compose actualizado:

````yaml
version: '3.8'

services:
  server-config-server:
    image: server-config-server:latest
    container_name: server-config-server
    ports:
      - "8085:8085"
    networks:
      - hotel-network
    environment:
      - SPRING_PROFILES_ACTIVE=docker

  server-discovery:
    image: server-discovery:latest
    container_name: server-discovery
    ports:
      - "8761:8761"
    networks:
      - hotel-network
    depends_on:
      - server-config-server
    environment:
      - SPRING_PROFILES_ACTIVE=docker

  service-user:
    image: service-user:latest
    container_name: service-user
    networks:
      - hotel-network
    depends_on:
      - server-config-server
      - server-discovery
    environment:
      - SPRING_PROFILES_ACTIVE=docker

networks:
  hotel-network:
    driver: bridge
````

**Resumen**: Mantén Config Server con URL estática. Solo cambia `localhost` por el nombre del container en Docker. NO registres Config Server en Eureka.
✅ 2. Forma moderna (manejo global centralizado)

Se usa un manejador global de excepciones para toda la aplicación.

👉 Se define una clase con @ControllerAdvice
👉 Permite capturar errores de todos los controladores

📌 Paso 1 — Crear excepción personalizada
public class RecursoNoEncontradoException extends RuntimeException {
    public RecursoNoEncontradoException(String mensaje) {
        super(mensaje);
    }
}
📌 Paso 2 — Lanzar la excepción en el servicio
public Usuario buscarPorId(Long id) {
    return usuarioRepository.findById(id)
            .orElseThrow(() -> new RecursoNoEncontradoException("Usuario no existe"));
}
📌 Paso 3 — Crear manejador global
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(RecursoNoEncontradoException.class)
    public ResponseEntity<?> manejarRecursoNoEncontrado(RecursoNoEncontradoException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<?> manejarErroresGenerales(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body("Error interno del servidor");
    }
}
📌 Paso 4 — Controlador limpio (sin try-catch)
@GetMapping("/usuario/{id}")
public ResponseEntity<Usuario> obtenerUsuario(@PathVariable Long id) {
    Usuario usuario = usuarioService.buscarPorId(id);
    return ResponseEntity.ok(usuario);
}
🎯 Ventajas del manejo centralizado

✅ Código más limpio
✅ Reutilizable
✅ Fácil mantenimiento
✅ Manejo consistente de errores
✅ Separación de responsabilidades

¡Claro que sí! Vamos a desglosar este código paso a paso para que entiendas exactamente qué hace cada parte y por qué se utiliza esta estructura en Spring Boot.

### 1. Las Anotaciones: `@RestControllerAdvice` y `@ExceptionHandler`

**`@RestControllerAdvice`**
Aunque mencionas `@RestController`, en tu código usas `@RestControllerAdvice`. Están muy relacionados:
*   Un `@RestController` se usa para definir tus endpoints (APIs) y le dice a Spring que las respuestas deben convertirse automáticamente a JSON.
*   **`@RestControllerAdvice`** es un "escudo" global para todos tus `@RestController`. Su trabajo es interceptar cualquier excepción (error) que ocurra en *cualquier* controlador de tu aplicación. Al tener la palabra "Rest", también garantiza que la respuesta de error que devuelvas se convierta automáticamente a JSON.

**`@ExceptionHandler(ResourceNotFoundException.class)`**
Esta anotación se coloca sobre un método dentro de tu clase `@RestControllerAdvice`. Le dice a Spring: *"Oye, si en cualquier parte de la aplicación alguien lanza un error del tipo `ResourceNotFoundException`, detén todo y ejecuta este método para manejarlo"*.

### 2. El Controlador Global (`GlobalExceptionController`)

```java
public class GlobalExceptionController {
    // ...
}
```
Esta clase centraliza el manejo de errores. En lugar de llenar todos tus controladores (users, hotels, etc.) con bloques `try-catch`, dejas que el error suba y esta clase lo atrapa. 

**El método `handlerResourceNotFoundException`:**
1. **Recibe la excepción:** Toma el `ResourceNotFoundException` que se lanzó, lo que te permite acceder a su mensaje original.
2. **Construye la respuesta (`ApiResponse`):** Usa el patrón *Builder* para crear un objeto que tiene un formato estándar para tus respuestas de error (mensaje, éxito: falso, status: 404).
3. **Retorna `ResponseEntity`:** Devuelve ese objeto envuelto en un `ResponseEntity` con el código de estado HTTP 404 (`HttpStatus.NOT_FOUND`), lo cual es correcto semánticamente cuando algo no se encuentra.

### 3. La Excepción Personalizada (`ResourceNotFoundException`)

```java
public class ResourceNotFoundException extends RuntimeException {
    // ...
}
```
Aquí estás creando tu propia clase de error que hereda de `RuntimeException`. Heredar de `RuntimeException` (y no de `Exception`) es una buena práctica en Spring porque crea una **excepción no comprobada (unchecked)**. Esto significa que no estás obligado a poner `throws ResourceNotFoundException` en la firma de todos tus métodos.

### 4. ¿Qué es `super()` y por qué hay dos constructores?

`super(...)` es una palabra clave en Java que sirve para **llamar al constructor de la clase padre**. En este caso, la clase padre es `RuntimeException`.

Tienes dos constructores para darte flexibilidad al usarlos:

1. **Sin parámetros:** 
   ```java
   public ResourceNotFoundException() {
       super("Resource not found"); // Llama al padre con un mensaje por defecto
   }
   ```
   *Uso:* `throw new ResourceNotFoundException();` (El mensaje será "Resource not found").

2. **Con parámetro `String message`:**
   ```java
   public ResourceNotFoundException(String message) {
       super(message); // Llama al padre pasándole el mensaje que tú decidas
   }
   ```
   *Uso:* `throw new ResourceNotFoundException("El usuario con ID 5 no existe");`

En ambos casos, pasarle el texto a `super()` hace que la clase original de Java guarde ese mensaje. Luego, en tu controlador global, puedes recuperarlo usando `resourceNotFoundException.getMessage()`.

### 5. Beneficios de crear excepciones personalizadas

*   **Claridad:** Leer `throw new ResourceNotFoundException()` en tu código es mucho más claro y describe la intención lógica de negocio mejor que un genérico `throw new RuntimeException()`.
*   **Manejo Específico:** Como creaste una clase propia, puedes capturarla exactamente a ella con `@ExceptionHandler(ResourceNotFoundException.class)`. Si usaras errores genéricos, no podrías separarlos fácilmente: un 404 no es lo mismo que un error interno 500.
*   **Extensibilidad:** Si en el futuro deseas que el error no solo tenga un mensaje, sino también el nombre del campo que falló (ej: `String resourceName`, `String fieldName`), puedes añadir esos campos a tu clase personalizada.