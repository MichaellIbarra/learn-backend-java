# Documentación de Código y Conceptos (Java & Spring Boot)

## 1. Java Core y Colecciones

### Map (Tipos e Instanciación)

**¿Qué es?**
Una estructura de datos que almacena elementos en pares clave-valor (`key`-`value`), donde cada clave es única. En Java existen varias implementaciones dependiendo de si se requiere orden, velocidad o inmutabilidad.

**Ejemplo de Código:**
```java
// Tipos comunes
Map<String, Object> hashMap = new HashMap<>();
Map<String, Object> linkedHashMap = new LinkedHashMap<>();
Map<String, Object> treeMap = new TreeMap<>();

// Mapas inmutables (Java 9+)
Map<String, Object> mapOf = Map.of("message", "Error", "status", HttpStatus.NOT_FOUND);
Map<String, Object> mapEntries = Map.ofEntries(
    Map.entry("message", "Error"),
    Map.entry("success", false)
);
```

**Explicación / Desglose:**
- **`HashMap`**: Implementación más usada y rápida. Uso general. No garantiza ningún orden interno ni de inserción.
- **`LinkedHashMap`**: Mantiene rigurosamente el orden de inserción de los elementos.
- **`TreeMap`**: Ordena las claves de forma natural (alfabética o numérica) o usando un comparador personalizado.
- **`Map.of` / `Map.ofEntries`**: Métodos estáticos de fábrica (Java 9+) para crear mapas inmutables de solo lectura (no aceptan `null`). Usar `Map.of` para mapas pequeños y `Map.ofEntries` para más de 10 pares.

**Notas rápidas (prácticas):**
- `Map` es muy útil para armar payloads rápidos (por ejemplo, errores), pero para APIs medianas/grandes suele ser mejor usar DTOs/records para mantener consistencia.
List<Grade> highGrades = grades.stream().filter(g -> g.getScore() > 8).toList();

// peek(): debugging/logging (evitar lógica de negocio aquí)
List<Grade> debug = grades.stream().peek(g -> logger.info("Grade: {}", g.getId())).toList();
```

**toList() vs collect(Collectors.toList())**
- `toList()` (Java 16+): retorna lista inmutable.
- `collect(Collectors.toList())` (Java 8+): suele retornar lista mutable.

**Casos de uso / ¿Cuándo elegirlo?**
- Reestructurar de forma limpia un dataset traído de BBDD sin usar decenas de bucles `for` y bloques `if`.

---

### Complejidad y Optimización ( O(n*m) VS O(n+m) )

**¿Qué es?**
Es la forma arquitectónica en la que programas una asignación entre dos listas grandes. Si tienes 1,000 *grades* (n) y haces un bucle que internamente usa `.stream().filter()` sobre una lista de 1,000 *hotels* (m), tu procesador hará multiplicidad exponencial (hasta 1,000,000 comparaciones), generando cuellos de botella `O(n*m)`.

**Ejemplo de Código:**
```java
// ALGORITMO OPTIMIZADO: O(n + m) empleando HashMaps -> O(1) Búsqueda
// 1. Construir un índice estructurado (Consume un solo pase -> iteraciones "m")
Map<String, Hotel> hotelMap = hotels.stream()
    .filter(h -> h.getId() != null)
    .collect(Collectors.toMap(Hotel::getId, Function.identity()));

// 2. Extraer limpiamente consultando la clave (Consume un pase secundario -> iteraciones "n")
List<Grade> gradeWithHotel = gradesList.stream()
    .map(g -> {
        g.setHotel(hotelMap.get(g.getHotelId())); // Esto cuesta tiempo constante rápido: O(1)
        return g;
    })
    .toList();
```

**Explicación / Desglose:**
- **`Function.identity()`**: Un sintaxis nativa estática referenciando al mismo objeto `Hotel` capturado para registrarlo como el "Value" del Map.
- **`hotelMap.get(id)`**: Buscar en un `HashMap` pre-indexado demora una latencia mínima e idéntica independientemente del tamaño (Tiempo Constante `O(1)`).

**Por qué mejora tanto:**
- Enfoque malo: por cada `grade` filtras la lista completa de `hotels` → O(n*m).
- Enfoque bueno: creas un índice (mapa) una vez O(m) y luego haces búsquedas O(1) para cada `grade` → O(n+m).

**Variante segura (si hay ids duplicados):**
```java
Map<String, Hotel> hotelMap = hotels.stream()
  .filter(h -> h.getId() != null)
  .collect(Collectors.toMap(Hotel::getId, Function.identity(), (existing, replacement) -> existing));
```

**Casos de uso / ¿Cuándo elegirlo?**
- **Obligatorio** cuando necesitas "poblar", "asignar" o "cruzar" atributos de dos tablas distintas cargadas masivamente en memoria.

## 2. Spring Core y Configuración

### @Configuration y @Bean

**¿Qué es?**
Marcas utilizadas para que Spring IoC (Inversión de control) gestione la creación de un objeto particular desde que arranca la aplicación, para ser globalmente inyectado ("Singleton") cuando un controlador lo solicite.

**Ejemplo de Código:**
```java
@Configuration
public class RestTemplateConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

**Explicación / Desglose:**
- **`@Configuration`**: Advierte a Spring que escanee internamente la clase antes de levantar el servidor web buscando métodos que liberen Beans.
- **`@Bean`**: Al retornar un bloque fabricado (Ej: tu propio `RestTemplate` parametrizado con timeouts y balanceos), queda inmortalizado y se puede llamar globalmente vía `@Autowired` sin usar la palabra reservada `new`.

**Casos de uso / ¿Cuándo elegirlo?**
- Para inyectar herramientas que vienen de librerías externas que no traen anotación `@Component` nativa (ej. `RestTemplate`).
- Para **WebClient** (el estándar reactivo de interconexión recomendado para sustituir RestTemplate a futuro). 

**Por qué se hace así (práctico):**
- Centraliza configuración (timeouts, interceptores, converters).
- Spring gestiona el ciclo de vida (por defecto singleton) y facilita testing (puedes mockear/injectar fácilmente).

**Ejemplo (WebClient recomendado en proyectos nuevos):**
```java
@Configuration
public class WebClientConfig {
  @Bean
  public WebClient webClient(WebClient.Builder builder) {
    return builder.build();
  }
}
```

## 3. Spring Web y Controladores

### ResponseEntity

**¿Qué es?**
Clase central en Spring Web que empaqueta meticulosamente la respuesta HTTP resultante de una función Controladora Rest. Representa: un cuerpo JSON, cabeceras HTTP de envío y un código de estado (200, 400, 500...).

**Ejemplo de Código:**
```java
// 1. Constructor explícito
ResponseEntity<String> res1 = new ResponseEntity<>("Cuerpo", HttpStatus.OK);

// 2. Método estático ok()
ResponseEntity<String> res2 = ResponseEntity.ok("Cuerpo");

// 3. Método status() encadenando partes
ResponseEntity<String> res3 = ResponseEntity.status(HttpStatus.NOT_FOUND).body("No encontrado");

// 4. Métodos alternativos
ResponseEntity<String> res4 = ResponseEntity.badRequest().body("Error en solicitud"); // 400
ResponseEntity<Void> res5 = ResponseEntity.noContent().build(); // 204 Exitoso vacío
ResponseEntity<String> res6 = ResponseEntity.accepted().body("Procesando en hilo fondo"); // 202
```

**Explicación / Desglose:**
- **`ResponseEntity.ok("Cuerpo")`**: Ataljo directo que te arma un 200 SUCCESS e inyecta el JSON sin escribir un constructor.
- **`.build()`**: Finaliza el constructor estilo *Builder* cuando no hay objeto por transmitir de `.body()` de por medio.
- **`HttpStatus.NOT_FOUND`**: Enumeración integral de Java que contiene todos los estados universalmente admitidos.

**Notas prácticas:**
- Puedes agregar headers con `.headers(...)` o `.header("X-...", "...")`.
- Para APIs consistentes, es común devolver un DTO/record en vez de `String`.

**Ejemplo (headers + status + body):**
```java
return ResponseEntity.status(HttpStatus.CREATED)
  .header("Location", "/api/v1/users/123")
  .body("Creado");
```

**Casos de uso / ¿Cuándo elegirlo?**
| Tipo de Respuesta | Elegir Cuándo |
|---|---|
| `.status(...)` | Cuando el estado debe ser adaptativo mediante lógica compleja de negocio. |
| `.ok()` | Para el 90% de llamados tipo `GET` o lecturas resolutivas limpias. |
| `.noContent()` | Ideal para `DELETE` y `PUT` (actualizaciones exitosas que evitan gastar ancho de banda retornando el mismo objeto modificado). |

## 4. Clientes HTTP (RestTemplate)

### RestTemplate Fetchers vs Class References

**¿Qué es?**
Un administrador síncrono capaz de consultar a otros APIS foráneos de forma bloqueante. Cuenta con varias formas de mapear un envío remoto.

**Ejemplo de Código:**
```java
// Modo simple: Regresa directo la Lista cruda
Grade[] array = restTemplate.getForObject(url, Grade[].class);

// Modo envoltorio: Inspecciona si hay tokens o ver cómo le fue de status a la comunicación
ResponseEntity<Hotel> res = restTemplate.getForEntity(url, Hotel.class);
Hotel hotel = res.getBody();

// Modo omnipotente: Construyes verbos y Headers
ResponseEntity<Grade[]> call = restTemplate.exchange(url, HttpMethod.GET, reqEntity, Grade[].class);
```

**Explicación / Desglose:**
- **`getForObject()`**: Deserializa el JSON bruto transformándolo al modelo directamente. Te abstraes de las cabeceras resultantes.
- **`getForEntity()`**: Obtienes información metadatos vitales (Ej: si devuelven un header para setearte una cookie HTTP-ONLY).
- **`exchange()`**: El rey supremo para mandar encabezados propios (Tokens Bearer) o ejecutar acciones atípicas (`PATCH/PUT`).
- **¿Por qué pides `Hotel.class` y no solo `Hotel`?**: Spring necesita el *Objeto de clase literal* en tiempo de ejecución. Puesto que usar reflection (introspección estructural) por Jackson requiere el plano mental de atributos para ensamblar el JSON que llegó a variables correctas.

**Métodos típicos adicionales:**
```java
Hotel created = restTemplate.postForObject(url, requestBody, Hotel.class);
restTemplate.put(url, requestBody);
restTemplate.delete(url);
```

**Regla rápida (muy común):**
- Si el servicio devuelve “muchos elementos”, normalmente usas `Grade[].class`.
- Si devuelve “un objeto”, usas `Hotel.class`.

## 5. Arquitectura y Patrones

### Composición y Ensamble de Datos: Backend vs. Frontend

**¿Qué es?**
El lugar físico en la infraestructura donde múltiples sub-recursos distribucionales (Microservicios) se orquestan y unifican para el usuario final.

**Ejemplo de Código:**
```java
// Patrón de Composición pura en Backend (El Backend arma todo un reporte masivo)
user.setGrades(gradesWithHotelLoadedByRest); 
return user;
```

**Explicación / Desglose:**
- **Composición Backend (Agregador)**: El backend recolecta a todas las dependencias internas y exporta un modelo JSON monstruoso y auto-suficiente al que consuma.
- **Composición Frontend (API Descentralizada)**: El frontend le pega a 5 endpoints livianos diferentes de forma segregada y dibuja en pantalla asíncronamente según lleguen.

**Trade-offs (rápido):**
- Backend: menos llamadas desde frontend y datos consistentes; mayor latencia y acoplamiento entre servicios.
- Frontend: más flexible y carga bajo demanda; más llamadas y coordinación en UI.

**Casos de uso / ¿Cuándo elegirlo?**
| Escenario / Patrón | Casos Reales | Descripción de Elección |
|---|---|---|
| **Backend Agregador** | Facturación, Dashboards Financieros, Checkout (Carro de Compras). | Bueno cuando la transacción entera es **vitalmente inquebrantable**. Si un servicio local falla, frena antes de presentar basura. Hay más latencia y acoplamiento severo. |
| **Composición Frontend** | Catálogos (Netflix/Amazon), Reviews en Social Media / Posts. | Otorga rendimiento modular dinámico. El front no retrasa la imagen de un vídeo si la URL de carga de comentarios se congeló. |

## 6. Spring Cloud y Microservicios

### Eureka Balanceo de Redes (@LoadBalanced)

**¿Qué es?**
Anotación central que configura interceptores en las peticiones salientes HTTP de Spring para que deleguen en el Servidor Eureka para buscar la instacia física viva mediante un identificador virtual.

**Ejemplo de Código:**
```java
@Bean
@LoadBalanced
public RestTemplate restTemplate() { return new RestTemplate(); }

// Se llama con nombre ficticio de Eureka:
restTemplate.getForObject("http://msvc-grades/api/v1/...", Grade[].class);
```

**Explicación / Desglose:**
- **`msvc-grades`**: El `RestTemplate` bloquea temporalmente la salida a la red para resolver que `msvc-grades` se traduce iteradamente de forma oculta a las IP físicas: `localhost:8082`, `8083` y `8084` evitando harcordearlas.
- **`Round-Robin / Least Connections (Algoritmos)`**: Eureka enviará un request a la A, otro request le pasará al equipo B y otro al C por rotación. O a aquel proceso que tenga menos conexiones activas consumiendo.

**Idea clave:** llamas por nombre lógico y el discovery + load balancer elige una instancia saludable.

### Spring Cloud Config & Profiles

**¿Qué es?**
Repositorio o Servidor central que gestiona configuracion ambiental transversal (archivos `**.yml`).

**Ejemplo de Código:**
```yaml
# En microservicio propio solo conectamos al server:
spring:
  config:
    import: optional:configserver:http://localhost:8085

# En repositorio global separado /service-hotel.yml
spring:
  datasource:
    url: jdbc:postgresql://prod-db:5432/db
    password: ${DB_PASSWORD}
```

**Recomendación práctica (separación):**
- En el microservicio deja solo `spring.application.name`, `spring.config.import` y el perfil.
- En el repo del Config Server vive todo lo demás (DB, puertos, eureka, propiedades de negocio).

**Casos de uso / ¿Cuándo elegirlo?**
- Previene tener que empacar, compilar y desplegar nuevamente toda tu imagen Docker porque simplemente haya mutado un simple puerto numérico o password en la nube, abstraes la configuración sacándola del frágil `.jar`.

## 7. Resiliencia, Tolerancia a Fallos y Monitoreo

### Concepto: Circuit Breaker VS. Exception Handling

**¿Qué es?**
Resilience4j plantea mecanismos predictivos contra APIs en mal estado previniendo fallas en cascada y ahorrando el TimeOut interminable de conexiones nulas de un bloque entero.

- **Diferencias Claves**: El *Exception Handler* reacciona póstumamente de que suceda una falla, castiga tu procesador y tiempo de request local (El request va a esperar hasta caducar por cada usuario intentando comprar). Por su parte, el **Circuit Breaker** se adelanta estadísticamente a bloquear la tubería completa para toda posterior demanda antes de ni siquiera cursar la llamada original.

**Ejemplo de Código (YML Completo y exhaustivo):**
```yaml
resilience4j:
  circuitbreaker:
    instances:
      ratingHotelBreaker:
        register-health-indicator: true
        event-consumer-buffer-size: 10
        minimum-number-of-calls: 5 
        failure-rate-threshold: 50 
        wait-duration-in-open-state: 6000 
        automatic-transition-from-open-to-half-open-enabled: true 
        permitted-number-of-calls-in-half-open-state: 3
        sliding-window-size: 10
        sliding-window-type: COUNT_BASED
```

**Explicación / Desglose de cada atributo al detonar:**
1. Se opera en la modalidad `sliding-window-size: 10` que analiza solo las 10 transacciones más recientes bajo un tipo enumerado `COUNT_BASED` (no temporal, sino número de llamadas). Se registrarán en `event-consumer-buffer-size: 10` (su memoria búfer flotante).
2. Primero necesita un requisito habilitante de `minimum-number-of-calls: 5`. Evita que si casualmente la primerísima petición post despliegue falla, acabe marcando un rotundo y sesgado `%100 de fracasos` para trancar el sistema.
3. Si el estado detecta que pasaste el `failure-rate-threshold: 50` (la rotunda mitad falla); pasa el interruptor a **ESTADO OPEN** (corta circuitos en la pared). Bloquea accesos y todo recurso caerá instantáneamente hacia un atajo fallback preventivo (`ratingHotelFallback()`).
4. Entraría una pausa letárgica predefinida por `wait-duration-in-open-state: 6000` (El servicio ignorará todo durante 6000ms o 6 segundos exactos). No se quema red ni hilo local en procesarlo durante la vigilia.
5. Mediante la habilitación manual de `automatic-transition: true` entrará a **ESTADO HALF-OPEN** y filtrará con cautela un total testeable de solicitudes dictadas en `permitted-number...: 3`. Si ellas retornan exitosas cierra el sistema y lo vuelve **CLOSED**; de volver a fallar regresará dramáticamente al encierro abierto.

### @Retry

**¿Qué es?**
Un paliativo temporal a bajo nivel donde en vez de cancelar un request por un micro-corte (o alimentar en vano métricamente un Circuit Breaker), intenta repetirla calladamente cierta cantidad de veces.

**Ejemplo de Código:**
```yaml
resilience4j:
  retry:
    instances:
      retryId:
        max-attempts: 3
        wait-duration: 2000ms
        retry-exceptions:
          - java.io.IOException
          - java.util.concurrent.TimeoutException
```

**Explicación / Desglose:**
- **`max-attempts: 3`**: Dicta 1 intento original fallido orgánico + 2 reintentos fantasma automáticos. 
- **`wait-duration: 2000ms`**: Separa la distancia de agresión iterativa para darle aire de recuperación a un destino sobrecargado.
- **`retry-exceptions`**: Permite discriminar que anomalías perdonar. *Error 400 - Validaciones Clientes Erróneas*, nunca prosperará repitiéndolo por lo que omitirlo impedirá una re-ejecución ridícula.

### Spring Boot Actuator

**¿Qué es?**
Agrega un set prefabricado e invaluable de utilidades de auditoría en caliente HTTP exponiendo el intestino de la memoria RAM del container, estado del puerto, conexiones base, etc.

**Ejemplo de Código:**
```yaml
management:
  health:
    circuitbreakers:
      enabled: true
  endpoints:
    web:
      exposure:
        include: health
  endpoint:
    health:
      show-details: always
```

**Explicación / Desglose:**
- **`management.health.circuitbreakers.enabled`**: Prende y compila el bean encargado de imprimir el panel de lecturas (OPEN/CLOSED) de los microcortes.
- **`include: health`**: Permite la exposición HTTP por web externa vía `/actuator/health`.
- **`show-details: always`**: Modula en profundidad extrema métricas crudas en lugar de dictaminar un solitario Status Boolean textual `"UP"`.

**Casos de uso / ¿Cuándo elegirlo?**
- En Kubernetes o infraestructuras nativas de orquestación donde el clúster requiere *Health Checks / Pings* que informen robóticamente si el contenedor es apto, y decidir en base a sus diagnósticos su eliminación o su redirección de tráfico mediante load balancers.

