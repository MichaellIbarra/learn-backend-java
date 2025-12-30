`ResponseEntity` en Spring Boot se puede instanciar de varias formas, dependiendo de lo que necesites devolver (cuerpo, cabeceras, estado). Aquí tienes las formas más comunes y su explicación:

1. **Constructor directo**  
Permite crear una respuesta con cuerpo, cabeceras y estado.
```java
ResponseEntity<String> response = new ResponseEntity<>("Cuerpo", HttpStatus.OK);
```
- **"Cuerpo"**: el contenido de la respuesta.
- **HttpStatus.OK**: el código de estado HTTP.

2. **Método estático `ok()`**  
Devuelve 200 OK, con o sin cuerpo.
```java
ResponseEntity<String> response = ResponseEntity.ok("Cuerpo");
```
- Solo puedes establecer el cuerpo, el estado es 200 OK.

3. **Método estático `status()`**  
Permite personalizar el estado y luego agregar cuerpo o cabeceras.
```java
ResponseEntity<String> response = ResponseEntity.status(HttpStatus.NOT_FOUND).body("No encontrado");
```
- Puedes encadenar `.body()` para el cuerpo y `.headers()` para cabeceras.

4. **Método estático `badRequest()`**  
Devuelve 400 Bad Request.
```java
ResponseEntity<String> response = ResponseEntity.badRequest().body("Error en la solicitud");
```
- Estado 400, puedes agregar cuerpo.

5. **Método estático `noContent()`**  
Devuelve 204 No Content, sin cuerpo.
```java
ResponseEntity<Void> response = ResponseEntity.noContent().build();
```
- Sin cuerpo, solo estado.

6. **Método estático `accepted()`**  
Devuelve 202 Accepted.
```java
ResponseEntity<String> response = ResponseEntity.accepted().body("Aceptado");
```
- Estado 202, puedes agregar cuerpo.

**Resumen:**  
- Usa el **constructor** para máxima personalización (cuerpo, cabeceras, estado).
- Usa **métodos estáticos** para respuestas comunes y más legibles.
- `.body()` agrega el cuerpo, `.headers()` agrega cabeceras, `.build()` finaliza la construcción.

Cada forma te permite controlar diferentes aspectos de la respuesta HTTP según lo que tu endpoint necesite devolver.

En Java, puedes instanciar un `Map` de varias formas. Aquí te explico las más comunes y por qué usar cada una:

1. **HashMap**  
   Es la implementación más usada. No garantiza orden.  
   ```java
   Map<String, Object> map = new HashMap<>();
   ```

2. **LinkedHashMap**  
   Mantiene el orden de inserción. Útil si necesitas que los elementos se mantengan en el orden en que se agregaron.  
   ```java
   Map<String, Object> map = new LinkedHashMap<>();
   ```

3. **TreeMap**  
   Ordena las claves de forma natural o por un comparador. Útil si necesitas un mapa ordenado por clave.  
   ```java
   Map<String, Object> map = new TreeMap<>();
   ```

4. **Map.of(...)** (Java 9+)  
   Para mapas inmutables y pequeños. No permite valores nulos.  
   ```java
   Map<String, Object> map = Map.of("message", message, "success", false, "status", HttpStatus.NOT_FOUND);
   ```

5. **Map.ofEntries(...)** (Java 9+)  
   Para mapas inmutables con más de 10 pares clave-valor.  
   ```java
   Map<String, Object> map = Map.ofEntries(
       Map.entry("message", message),
       Map.entry("success", false),
       Map.entry("status", HttpStatus.NOT_FOUND)
   );
   ```

**¿Por qué elegir cada uno?**  
- `HashMap`: General, rápido, sin orden.
- `LinkedHashMap`: Si necesitas mantener el orden de inserción.
- `TreeMap`: Si necesitas un mapa ordenado por clave.
- `Map.of`/`Map.ofEntries`: Para mapas inmutables y de solo lectura.

Cada uno contiene pares clave-valor (`key`, `value`). En tu ejemplo, las claves son `"message"`, `"success"` y `"status"`, y los valores son el mensaje, un booleano y un `HttpStatus`.

- `\@Configuration`: indica a Spring que la clase contiene definiciones de beans que debe crear y gestionar.
- `\@Bean`: marca el método que devuelve un objeto que Spring registrará como bean en el contexto (por defecto como *singleton*).
- Por qué instanciar así:
  - Permite que Spring gestione el ciclo de vida (una única instancia compartida, gestión de scope).
  - Centraliza la configuración (timeouts, interceptores, conversores) en un solo lugar.
  - Facilita la inyección de dependencias (`\@Autowired` o inyección por constructor`)` y hace el código más testeable.
  - Evita crear varias instancias dispersas y duplicar configuración.
- Nota: `RestTemplate` no se registra automáticamente, por eso se crea como `\@Bean`. En proyectos nuevos se recomienda considerar `WebClient` si se usa programación reactiva.

`getForObject` en `RestTemplate` hace una petición GET y convierte la respuesta en un objeto Java.

Otra forma de instanciar `RestTemplate` es con `@Bean` en una clase de configuración, así puedes inyectarlo donde lo necesites. Ejemplo:

```java
@Bean
public RestTemplate restTemplate() {
    return new RestTemplate();
}
```

Esto centraliza la configuración y facilita el mantenimiento.

Otros métodos de `RestTemplate`:
- `getForEntity`: GET, devuelve `ResponseEntity` (incluye headers y status).
- `postForObject`: POST, devuelve objeto.
- `put`: actualiza recurso.
- `delete`: elimina recurso.
- `exchange`: permite cualquier método HTTP y personalización.

Se usa `Hotel.class` porque `getForEntity()` necesita una referencia a la clase Java en tiempo de ejecución para deserializar correctamente la respuesta JSON.

**¿Por qué `Hotel.class` y no `Hotel`?**

- `Hotel` es el tipo de dato (clase)
- `Hotel.class` es el **objeto de clase** (una instancia de `Class<Hotel>`)

Spring necesita el objeto `Class` para:
1. Saber qué tipo de objeto crear
2. Mapear los campos JSON a los atributos de la clase
3. Realizar la serialización/deserialización automáticamente

Es similar a cuando usas `JsonProperty` o Jackson - necesita el `Class` literal para introspección.
Aquí está la explicación extendida con ejemplos simples en una línea:

**Array vs Lista en Java:**

Un **array** tiene tamaño fijo:
```java
Grade[] grades = new Grade[5]; // Siempre 5 elementos
```

Una **lista** es dinámica:
```java
List<Grade> grades = new ArrayList<>(); // Puede crecer sin límite
grades.add(newGrade); // Agregar elementos
```

**En tu caso:**
- `Grade[]` viene del servicio externo (tamaño fijo)
- Lo conviertes a `List<Grade>` para poder trabajar con streams y filtros
- Las listas son más flexibles para manipular datos
**Formas de instanciar con RestTemplate:**

```java
// 1. getForObject() - retorna directamente el objeto
Grade[] grades = restTemplate.getForObject(url, Grade[].class);

// 2. getForEntity() - retorna ResponseEntity con status, headers, body
ResponseEntity<Hotel> response = restTemplate.getForEntity(url, Hotel.class);
Hotel hotel = response.getBody();

// 3. getForObject() con clase simple
Hotel hotel = restTemplate.getForObject(url, Hotel.class);

// 4. exchange() - máximo control
ResponseEntity<Grade[]> response = restTemplate.exchange(url, HttpMethod.GET, null, Grade[].class);
```

**¿Por qué `Grade[]` y no `Grade`?**

- `Grade[]` porque el servicio retorna un **array de calificaciones** (múltiples registros)
- `Hotel.class` porque retorna un **único objeto** (un hotel específico)

**Diferencias principales:**

| Método | Retorna | Uso |
|--------|---------|-----|
| `getForObject()` | El objeto directo | Simple, sin status |
| `getForEntity()` | ResponseEntity | Necesitas status/headers |
| `exchange()` | ResponseEntity | Control total, cualquier método HTTP |

En tu caso: usa `getForObject()` para arrays y `getForEntity()` cuando necesites verificar el estado HTTP.

**¿Qué es Stream en Java?**

Un `Stream` es una secuencia de elementos que permite procesar datos de forma funcional y declarativa.

```java
List<Grade> grades = Arrays.asList(grade1, grade2, grade3);
Stream<Grade> stream = grades.stream(); // Convierte lista a stream
```

**Métodos principales de Stream:**

```java
// map() - transforma cada elemento
List<Hotel> hotels = grades.stream().map(g -> g.getHotel()).collect(Collectors.toList());

// filter() - filtra elementos según condición
List<Grade> highGrades = grades.stream().filter(g -> g.getScore() > 8).collect(Collectors.toList());

// peek() - ejecuta acción sin transformar (para debugging/logging)
grades.stream().peek(g -> logger.info("Grade: {}", g.getId())).collect(Collectors.toList());

// collect() - convierte stream en colección
List<Grade> result = grades.stream().collect(Collectors.toList());

// forEach() - itera sobre elementos
grades.stream().forEach(g -> System.out.println(g.getId()));

// toList() - convierte a lista (Java 16+)
List<Grade> result = grades.stream().toList();
```
¿Qué es peek() en Stream?
peek() ejecuta una acción en cada elemento sin modificarlo (útil para debugging/logging):
**`toList()` vs `collect(Collectors.toList())`**

Ambos convierten un `Stream` a `List`, pero tienen diferencias:

```java
// toList() - Java 16+ (recomendado, más simple)
List<Grade> result = grades.stream().map(...).toList();

// collect(Collectors.toList()) - Java 8+ (más flexible)
List<Grade> result = grades.stream().map(...).collect(Collectors.toList());

// Otras opciones con collect()
List<Grade> arrayList = grades.stream().collect(Collectors.toCollection(ArrayList::new));
Set<Grade> set = grades.stream().collect(Collectors.toSet());
```

**Diferencias principales:**

| Método | Retorna | Mutable | Java |
|--------|---------|---------|------|
| `toList()` | `List` inmutable | No | 16+ |
| `collect(Collectors.toList())` | `ArrayList` mutable | Sí | 8+ |

**En tu código:**

```java
List<Grade> gradesWithHotel = gradesList.stream()
    .map(grade -> {
        // ... lógica
        return grade;
    })
    .toList(); // ✓ Correcto - convierte stream a List
```

**¿Por qué usarlos?**

- `toList()`: Termina el stream y obtiene el resultado final
- Sin ellos, `stream()` no retorna nada usable (es lazy)
**¿Está bien llamarlo así?**

Sí, tu enfoque es **válido** pero hay mejores alternativas según el caso de uso.

**Cuándo usar tu enfoque (composición en backend):**

```java
// ✓ Bueno cuando frontend necesita todos los datos juntos siempre
user.setGrades(gradesWithHotel); // Backend hace todas las llamadas
```

**Cuándo usar composición en frontend:**

```java
// ✓ Bueno cuando frontend necesita flexibilidad
// GET /users/{id} - Solo datos de usuario
// GET /grades/users/{id} - Solo calificaciones  
// GET /hotels/{id} - Solo hotel específico
```

**Casos reales de ambos enfoques:**

| Escenario | Backend (tu caso) | Frontend |
|-----------|-------------------|----------|
| **E-commerce** | Producto + reviews + vendedor | Producto básico, luego reviews bajo demanda |
| **Social Media** | Post + comentarios + usuarios | Posts, comentarios se cargan al hacer scroll |
| **Dashboard** | Datos completos para reportes | Widgets independientes |

**Tu JSON es correcto porque:**

- Reduces llamadas HTTP del frontend (1 vs múltiples)
- Datos consistentes en una transacción
- Menos complejidad en frontend

**Desventajas:**

- Mayor tiempo de respuesta
- Más acoplamiento entre servicios
- Harder caching

**Recomendación:** Tu enfoque está bien para **casos donde siempre necesitas los datos relacionados**. Para mayor flexibilidad, usa endpoints separados + composición en frontend.
**Ejemplos prácticos de cuándo usar cada enfoque:**

## **Sistema de Pagos**

**Backend composición (tu enfoque):**
```java
// Reporte financiero completo
PaymentReport report = paymentService.getFullReport(userId);
// Incluye: transacciones + detalles bancarios + estado cuentas
```
**¿Cuándo?** Reportes contables, auditorías, estados de cuenta mensuales.

**Frontend composición:**
```java
// Dashboard de pagos en tiempo real
GET /payments - Lista básica
GET /payments/{id}/details - Detalles bajo demanda  
GET /bank-accounts/{id} - Solo si usuario hace click
```
**¿Cuándo?** Dashboard interactivo, notificaciones push.

## **Sistema de Restaurante**

**Backend composición:**
```java
// Orden completa para cocina
Order orderWithDetails = orderService.getCompleteOrder(orderId);
// Incluye: platos + ingredientes + alergenos + tiempo prep
```
**¿Cuándo?** Ticket de cocina, facturación, inventario.

**Frontend composición:**
```java
// App de delivery
GET /restaurants - Lista restaurantes
GET /menu/{restaurantId} - Menu cuando usuario selecciona
GET /dishes/{id}/reviews - Reviews solo si hace click
```
**¿Cuándo?** Apps móviles, búsqueda de restaurantes.

## **E-commerce**

**Backend composición:**
```java
// Checkout completo
CheckoutData data = checkoutService.getFullCheckoutData(cartId);
// Incluye: productos + stock + precios + shipping + taxes
```
**¿Cuándo?** Proceso de pago, confirmación orden.

**Frontend composición:**
```java
// Catálogo de productos
GET /products - Grid de productos
GET /products/{id}/details - Solo al hacer click
GET /reviews/{productId} - Solo si baja a reviews
```
**¿Cuándo?** Navegación de catálogo, búsqueda.

**Tu enfoque es perfecto para operaciones críticas donde necesitas consistencia de datos**.
**¿Qué es el Balanceo de Carga (Load Balancing)?**

Es distribuir las peticiones entre múltiples instancias de un servicio para mejorar rendimiento y disponibilidad.

**En tu código usas:**

```java
@LoadBalanced // ✓ Anotación que habilita balanceo de carga
public RestTemplate restTemplate() {
    return new RestTemplate();
}
```

**¿Cómo funciona con Eureka?**

```java
// En lugar de URL fija:
// restTemplate.getForObject("http://localhost:8082/api/v1/grades", Grade[].class);

// Usas nombre del servicio:
restTemplate.getForObject("http://msvc-grades/api/v1/grades/users/"+ user.getId(), Grade[].class);
// ↑ Eureka resuelve "msvc-grades" a una instancia disponible
```

**Algoritmos de balanceo que Eureka/Ribbon usa:**

| Algoritmo | Descripción |
|-----------|-------------|
| **Round Robin** (default) | Rota entre instancias: A → B → C → A |
| **Random** | Selecciona instancia aleatoria |
| **Least Connections** | Va a la que tiene menos conexiones |

**¿Por qué es útil?**

```yaml
# Si tienes múltiples instancias:
# msvc-grades:8082 (Instancia 1)
# msvc-grades:8083 (Instancia 2) 
# msvc-grades:8084 (Instancia 3)

# Eureka distribuye automáticamente:
# Petición 1 → 8082
# Petición 2 → 8083  
# Petición 3 → 8084
# Petición 4 → 8082 (vuelve al inicio)
```

**En tu configuración:**

```yaml
eureka:
  client:
    fetch-registry: true # ✓ Obtiene lista de servicios disponibles
    register-with-eureka: true # ✓ Se registra como disponible
```

**Ventajas:**
- Alta disponibilidad (si una instancia falla, usa otra)
- Mejor rendimiento (distribuye carga)
- Transparente para el código

Tu `@LoadBalanced` + Eureka hace que `"http://msvc-grades"` se resuelva automáticamente a una instancia saludable.
**Formas refactorizadas para mejorar ese fragmento:**

## **1. Eliminar variable intermedia (más directo)**

```java
List<Grade> gradesList = Arrays.stream(
    restTemplate.getForObject("http://msvc-grades/api/v1/grades/users/" + user.getId(), Grade[].class)
).toList();

List<Grade> gradesWithHotel = gradesList.stream()...
```

## **2. Una sola línea (más conciso)**

```java
List<Grade> gradesWithHotel = Arrays.stream(
    restTemplate.getForObject("http://msvc-grades/api/v1/grades/users/" + user.getId(), Grade[].class)
).map(grade -> {
    // tu lógica aquí
}).toList();
```

## **3. Usar ParameterizedTypeReference (mejor tipo)**

```java
List<Grade> gradesList = restTemplate.exchange(
    "http://msvc-grades/api/v1/grades/users/" + user.getId(),
    HttpMethod.GET,
    null,
    new ParameterizedTypeReference<List<Grade>>() {}
).getBody();

List<Grade> gradesWithHotel = gradesList.stream()...
```

## **4. Extraer URL como constante (más mantenible)**

```java
private static final String GRADES_URL = "http://msvc-grades/api/v1/grades/users/";

List<Grade> gradesList = Arrays.stream(
    restTemplate.getForObject(GRADES_URL + user.getId(), Grade[].class)
).toList();
```

## **5. Método separado (más legible)**

```java
private List<Grade> getUserGrades(String userId) {
    return Arrays.stream(
        restTemplate.getForObject("http://msvc-grades/api/v1/grades/users/" + userId, Grade[].class)
    ).toList();
}

// En tu método principal:
List<Grade> gradesWithHotel = getUserGrades(user.getId()).stream()...
```
Arrays.stream
    es una forma eficiente de convertir un array en un Stream en Java. Esto te permite aprovechar las operaciones funcionales de Streams, como `map()`, `filter()`, y `collect()`, para procesar los datos de manera más declarativa y concisa.
**La opción más limpia sería la #2 o #3, eliminando variables temporales innecesarias.**

Definiciones rápidas y por qué la versión con *Map* es mejor:

- O(n\*m): crecimiento cuadrático por bucles anidados. Ej.: por cada *grade* (n) filtras la lista de *hotels* (m) → n\*m comparaciones. Mal para n y m grandes.
- O(1): tiempo constante. Ej.: acceso a un elemento en un `HashMap` por clave.
- O(n): tiempo lineal.
- O(log n): tiempo logarítmico (búsqueda binaria, árboles equilibrados).
- O(n log n): típico de algoritmos de ordenación eficientes.
- O(n^2), O(2^n), O(n!): escalables muy mal; evitar si es posible.

Por qué usar `Map` (mi idea) es mejor que filtrar la lista dentro del stream:
- Complejidad: pasa de O(n\*m) a O(n + m) — primero construyes el mapa en O(m) y luego haces n búsquedas O(1).
- Latencia: menos CPU y comparaciones; si m y n crecen reduce mucho tiempo.
- Trade\-off: usa más memoria (almacenar el mapa) pero suele ser aceptable para listas de tamaño razonable.

Otras alternativas prácticas (cuando aplican):
- Endpoint batch (`GET /hotels?ids=1,2,3`): menos datos y menos parsing.
- Cache (Spring Cache / Caffeine): evita llamadas repetidas entre peticiones.
- Llamadas concurrentes no bloqueantes (WebClient / Reactor): útil para latencias I/O altas.
- `CompletableFuture` / executor: paralelizar sin reescribir a WebClient.

Fragmento de código (reemplaza sólo la parte que asigna hoteles a cada `Grade`):


```java
// java
// Construir lista segura de arrays (manejo nulos omitido en tu snippet original)
Grade[] gradesArray = restTemplate.getForObject("http://msvc-grades/api/v1/grades/users/" + user.getId(), Grade[].class);
List<Grade> gradesList = gradesArray == null ? List.of() : Arrays.asList(gradesArray);

Hotel[] hotelsArray = restTemplate.getForObject("http://msvc-hotel/api/v1/hotels", Hotel[].class);
List<Hotel> hotels = hotelsArray == null ? List.of() : Arrays.asList(hotelsArray);

// Crear mapa id -> Hotel para búsqueda O(1)
Map<String, Hotel> hotelMap = hotels.stream()
    .filter(h -> h.getId() != null)
    .collect(Collectors.toMap(Hotel::getId, Function.identity()));

// Asignar hotel a cada grade en O(n)
List<Grade> gradeWithHotel = gradesList.stream()
    .map(g -> {
        Hotel hotel = hotelMap.get(g.getHotelId());
        g.setHotel(hotel);
        logger.info("Grade Id: {}, Hotel found: {}", g.getId(), hotel != null);
        return g;
    })
    .toList();

user.setGrades(gradeWithHotel);
```

Explicación breve del código: primero se crea el `hotelMap` en O(m), luego se recorren las calificaciones y se asigna cada hotel con una búsqueda O(1), resultando en O(n + m) en lugar de O(n\*m).

O(n\*m) significa tiempo proporcional al producto de dos tamaños: n (por ejemplo, número de *grades*) y m (por ejemplo, número de *hotels*). Aparece cuando, por cada elemento de una colección, recorres otra colección completa (bucle anidado). Esto escala mal: si n=1000 y m=1000, son ~1.000.000 comparaciones.

Mejor: construir un mapa de hoteles por id y luego buscar en O(1) para cada grade → complejidad O(n + m).

Ejemplo corto (pequeña explicación antes del código): el primer método muestra la versión O(n\*m) que filtra la lista de hotels por cada grade; el segundo construye un Map y hace búsquedas O(1).

```java
// java
import java.util.*;
import java.util.stream.Collectors;

class Example {
    static class Grade { String id; String hotelId; Hotel hotel; /* getters/setters omitted */ }
    static class Hotel  { String id; /* ... */ }

    // O(n * m): por cada grade se recorre la lista de hotels
    static void badAssign(List<Grade> grades, List<Hotel> hotels) {
        for (Grade g : grades) {
            Hotel found = hotels.stream()
                    .filter(h -> h.id.equals(g.hotelId))
                    .findFirst()
                    .orElse(null);
            g.hotel = found;
        }
    }

    // O(n + m): construir mapa en O(m) y luego n búsquedas O(1)
    static void goodAssign(List<Grade> grades, List<Hotel> hotels) {
        Map<String, Hotel> hotelMap = hotels.stream()
                .filter(h -> h.id != null)
                .collect(Collectors.toMap(h -> h.id, h -> h));
        for (Grade g : grades) {
            g.hotel = hotelMap.get(g.hotelId);
        }
    }
}
```

Explicación breve:
- El `Map<String, Hotel>` creado convierte la lista de `hotels` en un mapa donde la clave es `hotel.getId()` y el valor es el propio `Hotel`. Esto permite búsquedas por id en tiempo O(1) en lugar de recorrer la lista cada vez (evita O(n*m)).
- Paso a paso:
  - `hotels.stream()` crea un flujo de `Hotel`.
  - `.filter(h -> h.getId() != null)` elimina hoteles sin id para evitar `null` keys.
  - `Collectors.toMap(Hotel::getId, Function.identity())` construye el mapa: `Hotel::getId` es la función que produce la clave; `Function.identity()` devuelve el objeto `Hotel` como valor.
- Ventajas: complejidad O(m) para construir el mapa + O(1) por búsqueda → O(n + m). Trade‑off: usa más memoria y puede lanzar `IllegalStateException` si hay claves duplicadas; para eso se puede pasar una función de fusión.

Código (alternativa segura con manejo de duplicados):

```java
// java
import java.util.Map;
import java.util.function.Function;
import java.util.stream.Collectors;

Map<String, Hotel> hotelMap = hotels.stream()
    .filter(h -> h.getId() != null)
    // en caso de ids duplicados, mantener el primero (cambiar la fusión si se desea otra política)
    .collect(Collectors.toMap(Hotel::getId, Function.identity(), (existing, replacement) -> existing));

// uso:
Hotel hotel = hotelMap.get(grade.getHotelId());
```

Alternativas breves: usar `Collectors.groupingBy` si quieres listas por id, o construir el `Map` manualmente con un bucle `for` si prefieres control total sobre duplicados y memoria.

Para microservicios con Spring Cloud Config, es recomendable separar las configuraciones de la siguiente manera:

## En `application.yml` local (del servicio):
Mantener solo configuración básica y de conexión al Config Server:

```yaml
spring:
  application:
    name: service-hotel
  config:
    import: optional:configserver:http://localhost:8085
  profiles:
    active: dev
```

## En el repositorio del Config Server (`service-hotel.yml`):
Colocar toda la configuración específica del entorno:

```yaml
# service-hotel.yml (en el repo Git del Config Server)
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/hotels_db
    username: postgres
    password: ${DB_PASSWORD:matichelo17}
    driver-class-name: org.postgresql.Driver
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    properties:
      hibernate:
        dialect: org.hibernate.dialect.PostgreSQLDialect

server:
  port: 8082

eureka:
  instance:
    prefer-ip-address: true
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:8761/eureka

# Configuraciones específicas del negocio
service:
  grade:
    name: service-grade
  hotel:
    name: service-hotel
```

## Para diferentes entornos (`service-hotel-prod.yml`):

```yaml
spring:
  datasource:
    url: jdbc:postgresql://prod-db:5432/hotels_db
    username: ${DB_USER}
    password: ${DB_PASSWORD}

server:
  port: 80

eureka:
  client:
    service-url:
      defaultZone: http://eureka-prod:8761/eureka
```

**Ventajas**: Configuración centralizada, fácil cambio entre entornos, y secrets manejados externamente.
Te explico **paso a paso** cómo funciona cada atributo del circuit breaker cuando se ejecuta:

## **1. register-health-indicator: true**
- **Cuándo actúa**: En el arranque de la aplicación
- **Qué hace**: Registra el circuit breaker en Spring Boot Actuator para que puedas consultar su estado en `/actuator/health`

## **2. event-consumer-buffer-size: 10**
- **Cuándo actúa**: En **cada llamada** al servicio
- **Por qué se necesita**: Para **almacenar eventos** (éxitos, fallos, timeouts) sin afectar performance
- **Ejemplo**: Si haces 100 llamadas rápidas, solo guarda los últimos 10 eventos en memoria

## **3. failure-rate-threshold: 50**
- **Cuándo actúa**: Después de `minimum-number-of-calls`
- **Qué evalúa**: Si ≥ 50% de llamadas fallan → **ABRE** el circuito
- **Ejemplo**: De 10 llamadas, si 5 o más fallan = circuito OPEN

## **4. minimum-number-of-calls: 5**
- **Cuándo actúa**: Es el **prerequisito** para evaluar
- **Por qué**: Evita que 1 fallo de 1 llamada (100%) abra el circuito
- **Ejemplo**: Necesitas mínimo 5 llamadas antes de calcular el porcentaje de fallos

## **5. automatic-transition-from-open-to-half-open-enabled: true**
- **Cuándo actúa**: Después de `wait-duration-in-open-state`
- **Qué hace**: Automáticamente pasa de OPEN → HALF-OPEN para **probar** si el servicio se recuperó

## **6. wait-duration-in-open-state: 6000**
- **Cuándo actúa**: Cuando el circuito está **OPEN**
- **Qué hace**: Espera 6 segundos antes de intentar reconectar
- **Durante este tiempo**: Todas las llamadas fallan inmediatamente con `CallNotPermittedException`

## **7. permitted-number-of-calls-in-half-open-state: 3**
- **Cuándo actúa**: En estado **HALF-OPEN**
- **Qué hace**: Permite solo 3 llamadas de prueba
- **Si las 3 tienen éxito**: Circuito → CLOSED (normal)
- **Si alguna falla**: Circuito → OPEN (esperar otros 6 segundos)

## **8. sliding-window-size: 10**
- **Cuándo actúa**: Continuamente en estado **CLOSED**
- **Qué hace**: Solo considera las **últimas 10 llamadas** para calcular la tasa de fallos
- **Ejemplo**: Si tienes 100 llamadas exitosas y luego 5 fallos de 5 llamadas = 100% fallos (solo mira las últimas 10)

## **9. sliding-window-type: COUNT_BASED**
- **Qué significa**: La ventana se basa en **número de llamadas** (no tiempo)
- **Alternativa**: `TIME_BASED` (basada en segundos)

## **Flujo Completo de Ejecución:**
1. Servicio normal → **CLOSED**
2. Fallan 5 de 10 llamadas → **OPEN** (bloquea todo)
3. Espera 6 segundos → **HALF-OPEN** 
4. Prueba 3 llamadas → Si OK: **CLOSED**, Si falla: **OPEN**
Excelente pregunta. Te explico las **diferencias clave** y por qué **ambos son necesarios**:

## **Exception Handling vs Circuit Breaker**

### **Exception Handling (GlobalExceptionController)**
- **Propósito**: Maneja errores **después** de que ocurren
- **Cuándo actúa**: Cuando ya se ejecutó la llamada y falló
- **Alcance**: Error individual por request

### **Circuit Breaker**
- **Propósito**: **Previene** errores futuros basado en patrones
- **Cuándo actúa**: **Antes** de hacer la llamada si detecta problemas
- **Alcance**: Protección sistemática del servicio

## **Ejemplo Práctico - Por qué necesitas ambos:**

### **Sin Circuit Breaker (solo exceptions):**
```java
// Cada request sigue intentando conectar al servicio caído
Request 1: intenta → falla → exception → 5 segundos perdidos
Request 2: intenta → falla → exception → 5 segundos perdidos  
Request 3: intenta → falla → exception → 5 segundos perdidos
// Tu API se vuelve LENTA porque cada request espera timeout
```

### **Con Circuit Breaker:**
```java
Request 1-5: intentan → fallan → circuit se ABRE
Request 6-100: fallback INMEDIATO (sin intentar conectar)
// Tu API sigue siendo RÁPIDA
```

## **Ventajas del Circuit Breaker:**

### **1. Performance**
- **Sin CB**: Cada request espera timeout (5-30 segundos)
- **Con CB**: Respuesta inmediata del fallback (milisegundos)

### **2. Protección en Cascada**
- Evita que un servicio caído **tumbe** toda tu aplicación
- Protege recursos (conexiones, threads, memoria)

### **3. Auto-recuperación**
- Detecta automáticamente cuando el servicio se recupera
- No necesitas intervención manual

### **4. Monitoreo**
- Métricas en tiempo real del estado de servicios
- Alertas proactivas

## **Cuándo usar cada uno:**

### **Exception Handling para:**
- Errores de validación
- Errores de base de datos
- Errores de lógica de negocio
- Errores inesperados

### **Circuit Breaker para:**
- Llamadas a servicios externos
- APIs de terceros
- Microservicios
- Cualquier dependencia que pueda fallar

## **En tu código actual:**
```java
// Exception handling: maneja errores individuales
@ExceptionHandler(ResourceNotFoundException.class)

// Circuit Breaker: protege contra fallos sistemáticos del servicio hotel/grade
@CircuitBreaker(name = "ratingHotelBreaker", fallbackMethod = "ratingHotelFallback")
```

**Ambos se complementan** para crear una aplicación **resiliente** y **rápida**.
La configuración de `management` define los endpoints de monitoreo de Spring Boot Actuator. Cada atributo tiene las siguientes funciones:

## `management.health.circuitbreakers.enabled: true`
- **Qué activa**: Habilita el indicador de salud específico para circuit breakers
- **Cuándo se ejecuta**: Al realizar una petición GET a `/actuator/health`
- **Por qué se ejecuta**: Para monitorear el estado de los circuit breakers configurados
- **Efecto**: Incluye información del estado de los circuit breakers en la respuesta de salud

## `management.endpoints.web.exposure.include: health`
- **Qué activa**: Expone el endpoint de salud a través de HTTP
- **Cuándo se ejecuta**: Al iniciar la aplicación, Spring registra la ruta `/actuator/health`
- **Por qué se ejecuta**: Para permitir acceso externo al endpoint de monitoreo
- **Efecto**: Hace disponible la URL `http://localhost:puerto/actuator/health`

## `management.endpoint.health.show-details: always`
- **Qué activa**: Muestra detalles completos del estado de salud
- **Cuándo se ejecuta**: En cada petición al endpoint `/actuator/health`
- **Por qué se ejecuta**: Para proporcionar información detallada sobre cada componente
- **Efecto**: La respuesta incluye detalles de base de datos, circuit breakers, disco, etc., en lugar de solo mostrar "UP" o "DOWN"

Con esta configuración, al acceder a `/actuator/health` obtienes información detallada del estado de todos los componentes, incluyendo el estado específico de tus circuit breakers configurados con Resilience4j.

El @Retry funciona de manera diferente al Circuit Breaker. Te explico cada configuración y cómo afecta el comportamiento:
Configuración de Retry explicada:
max-attempts: 3
Qué hace: Intenta la operación hasta 3 veces en total (1 llamada original + 2 reintentos)
Cuándo se ejecuta: En cada petición HTTP individual
Efecto: Si la primera llamada falla, automáticamente reintenta 2 veces más antes de ejecutar el fallback
wait-duration: 2000ms
Qué hace: Espera 2 segundos entre cada reintento
Cuándo: Entre cada uno de los 3 intentos
Efecto: Si falla el intento 1, espera 2 segundos → intento 2, si falla espera 2 segundos → intento 3
retry-exceptions
Qué hace: Solo reintenta cuando ocurren estas excepciones específicas
Por qué es importante: No todos los errores deben reintentar (ej: errores 400 Bad Request no tienen sentido reintentar)
Efecto: Solo reintenta en IOException y TimeoutException

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