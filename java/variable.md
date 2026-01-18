# Variables en Cada
- Son lo má simportantes de un lenguaje, son las entidades, contiene valores o datos,
- Una declaración de variable siempre contiene dos partes, el tipo de dato de la variable y el nombre de la variable.
    - TypeData nameVariable; : declaración básica.
    - TypeData nameVariable = value; : declaración con inicialización.
    - AccessModifier TypeData nameVariable = value; : declaración con modificador de acceso e inicialización, los modificadores de acceso comunes son:
    - public: la variable es accesible desde cualquier clase.
    - private: la variable es accesible solo dentro de la clase donde se declara.
    - protected: la variable es accesible dentro del mismo paquete y por las subclases.
    - static TypeDato nameVariable = value; : declaración con modificador de acceso estático e inicialización, su útilidad es para definir variables que pertenecen a la clase en lugar de a instancias específicas de la clase.
    - final TypeData nameVariable = value; : declaración con modificador de acceso final e inicialización, su útilidad es para definir constantes.
- El tipo de la variable determina los valores que la variable pueda contener y las operaciones que se pueden realizar con ella.
- El nombre de la variable es un identificador único que se utiliza para referirse a la variable en el código.
## Categorías de Datos
- Existen dos categorías principales de datos en Java:
### 1. Tipos de datos primitivos
- Los tipos prmitivos contienen un solo valor e incluyen los tipos como los números enteros, números de punto flotante, caracteres y valores booleanos, y no contienen métodos ni atributos.
#### Tipos de datos primitivos en Java
- byte: 8 bits, rango de -128 a 127
- short: 16 bits, rango de -32,768 a 32,767
- int: 32 bits, rango de -2,147,483,648 a 2,147,483,647
- long: 64 bits, rango de -9,223,372,036,854,775,808 a 9,223,372,036,854,775,807
- float: 32 bits, números de punto flotante de precisión simple
- double: 64 bits, números de punto flotante de precisión doble
- char: 16 bits, representa un solo carácter Unicode
- boolean: 1 bit, representa un valor verdadero o falso
### 2. Tipos de datos de referencia
- Los tipos de datos de referencia contienen una referencia a un objeto en memoria e incluyen tipos como cadenas, matrices y objetos personalizados, que contiene metodos y atributos.
### Reglas para definir nombres de variables
- Por convención, los nombre de las variables empiezan con una letra minúscula y utilizan la notación de camello (camelCase) para múltiples palabras.
- Debe ser un juego de caracteres comprendido en la codificación Unicode (A-Z, a-z, 0-9, _ y $).
- No puede ser el mismo que una palabra reservada del lenguaje.
- No deben tener el mismo nombre que otras variables cuyas declraciones aparezcan en el mismo ámbito.
- No pueden contener espacios en blanco ni caracteres especiales (como !, %, &, etc.).
- Pueden comenzar por una letra, un subrayadado o un símbolo de dólar siendo los siguiendes caracteres letras o dígitos.

# Términos comunes
( == ) : presedencia de operadores, operador de relación.
