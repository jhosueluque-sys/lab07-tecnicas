# Bitacora de tecnicas avanzadas

Laboratorio 07: Tecnicas Avanzadas de Prompting.
Herramienta de IA usada: (escribe aqui cual usaste)

## Ejercicio 2: Zero-shot, one-shot y few-shot
| Tipo | Aciertos (de 5) | Formato de la respuesta | Todas con el mismo formato (Si/No) |
|------|-----------------|-------------------------|------------------------------------|
| Zero-shot | 5/5 | Tabla con columnas N.º, Comentario y Clasificación, con círculos de colores, más un resumen de totales (2 positivos, 1 neutral, 2 negativos) | Si (dentro de la tabla), pero es un formato libre que la IA eligió sola |
| One-shot | 5/5 | Lista numerada con el formato `comentario → etiqueta` | Si, pero no copió exactamente el ejemplo: agregó numeración, usó la flecha → en lugar de -> y quitó las comillas |
| Few-shot | 5/5 | Una línea por comentario con el formato exacto `"comentario" -> Etiqueta`, sin texto adicional | Si, idéntico al formato de los ejemplos |
## Ejercicio 3: Chain of Thought
### Prompts usados

Directo:
```text
Un producto cuesta S/ 120. La tienda aplica un descuento del 25 %
y luego suma el 18 % de IGV sobre el precio con descuento.
Un cliente compra 3 unidades. Cuanto paga en total?
Responde solo con el numero.
```

Paso a paso:
```text
Un producto cuesta S/ 120. La tienda aplica un descuento del 25 %
y luego suma el 18 % de IGV sobre el precio con descuento.
Un cliente compra 3 unidades. Cuanto paga en total?
Resuelvelo paso a paso: muestra cada calculo y comprueba el resultado
antes de dar la respuesta final.
```

### Verificación con calculadora

| Paso | Cálculo | Resultado |
|------|---------|-----------|
| 1. Precio con descuento | 120 × 0,75 | 90 |
| 2. Precio con IGV | 90 × 1,18 | 106,20 |
| 3. Total por 3 unidades | 106,20 × 3 | 318,60 |

### Resultados

| Pedido | Respuesta de la IA | Muestra los pasos (Si/No) | Correcta (Si/No) |
|--------|--------------------|---------------------------|------------------|
| Directo | 318.60 | No | Si |
| Paso a paso | S/ 318.60 (tras 6 pasos y una comprobación final) | Si | Si |

Aunque la respuesta directa fue correcta, solo mostró un número y no había forma de saber cómo se calculó. Con el razonamiento paso a paso puedo revisar cada cálculo y, si hubiera un error, ver exactamente en qué paso ocurrió.

## Ejercicio 4: Role prompting
### Prompts usados

A. Sin rol:
```text
Explica que es una variable en programacion.
```

B. Rol docente:
```text
Actua como profesor de programacion que explica a estudiantes que nunca han programado. Explica que es una variable en programacion.
```

C. Rol senior:
```text
Actua como desarrollador Java senior que explica a un companero de trabajo. Explica que es una variable en programacion.
```

### Resultados

| Version | Vocabulario (sencillo/tecnico) | Usa ejemplos o codigo | A quien le sirve mas |
|---------|--------------------------------|-----------------------|----------------------|
| A. Sin rol | Sencillo | Sí, código en Python (`edad = 20`) y la analogía de la caja con etiqueta | A un principiante en general, sin un público definido |
| B. Rol docente | Sencillo | Sí, código en JavaScript (`let edad = 18;`), la caja con nombre y una tabla de variables y valores | A estudiantes que nunca han programado |
| C. Rol senior | Técnico | Sí, código en Java con tipos (`int`, `String`, `double`, `boolean`) y `System.out.println` | A un compañero de trabajo que ya conoce programación |

## Ejercicio 5: Descomposición

### Prompts usados

Pedido de una sola vez:
```text
Crea un sistema de inventario para una tienda.
```

Pedido por pasos (todos en el mismo chat):

Paso 1:
```text
Voy a crear un sistema de inventario para una tienda pequena en Java. Lista los 5 requisitos principales del sistema.
```

Paso 2:
```text
Con esos requisitos, disena las clases necesarias. Para cada clase indica sus atributos con su tipo de dato.
```

Paso 3:
```text
Escribe el codigo Java de la clase Producto con sus atributos, un constructor y los metodos get y set.
```

Paso 4:
```text
Revisa el codigo de la clase Producto y propone 3 mejoras concretas.
```

### Resultados por paso

| Paso | Qué entregó la IA |
|------|-------------------|
| Una sola vez | Una lista de 6 funciones generales (registrar, mostrar, buscar, actualizar stock, eliminar y calcular valor del inventario), una tabla de ejemplo con 3 productos y el cálculo del valor total (S/ 2,595). No incluyó clases ni código. |
| 1 | Los 5 requisitos principales: registrar productos, consultar productos, actualizar inventario, buscar productos y controlar el stock. |
| 2 | Un diseño de 4 clases (Producto, Inventario, Venta y Tienda) con sus atributos y tipos de dato, la función de cada clase y un esquema de cómo se relacionan. |
| 3 | El código Java de la clase `Producto` con atributos privados (`codigo`, `nombre`, `precio`, `stock`), un constructor y los métodos get y set, coherente con el diseño del paso 2. |
| 4 | Tres mejoras concretas: validar que el precio no sea negativo, validar que el stock no sea negativo y validar que código y nombre no estén vacíos ni sean `null`, con un ejemplo de `setStock()` mejorado. |

### Comparación

El pedido de una sola vez dio una descripción general y sin código, mientras que el pedido por pasos produjo requisitos, un diseño de clases y un código de `Producto` coherentes entre sí, que además pude revisar y mejorar en cada etapa.

## Ejercicio 6: Prompt estructurado y autocrítica

### Prompts usados

Prompt básico:
```text
Dame casos de prueba para un login.
```

Prompt estructurado:
```text
<rol>Actua como analista de pruebas de software.</rol>

<contexto>Login web con correo y contrasena. La cuenta se bloquea
despues de 3 intentos fallidos.</contexto>

<tarea>Piensa paso a paso que puede fallar y escribe 6 casos de
prueba.</tarea>

<formato>Tabla con las columnas: ID, escenario, datos de entrada,
resultado esperado.</formato>
```

Autocrítica (en el mismo chat):
```text
Revisa tu tabla: faltan casos limite como campos vacios, correo sin @
o contrasena con espacios? Agrega los que falten e indica cuales
agregaste.
```

### Evaluación

| Qué revisar | Cumple (Sí / No) |
|-------------|------------------|
| ¿Tiene las 4 columnas pedidas? | Sí |
| ¿Incluye el bloqueo después de 3 intentos? | Sí (CP-03 y CP-04) |
| ¿Incluye casos con campos vacíos? | Sí (CP-07, CP-08 y CP-09) |
| ¿Indica qué casos agregó en la autocrítica? | Sí (CP-07 al CP-11) |
| ¿Hay algún caso repetido o que no tenga sentido? | No hay casos repetidos. Solo CP-11 (contraseña con espacios) tiene un resultado esperado ambiguo, porque depende de las reglas definidas |