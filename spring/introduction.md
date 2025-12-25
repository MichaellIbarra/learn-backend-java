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