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