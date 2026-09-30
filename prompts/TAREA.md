# Tarea: Mi prompt avanzado

## Tarea elegida

Generar casos de prueba para un formulario web de registro de usuarios (nombre, correo y contraseña). La contraseña debe tener mínimo 8 caracteres, una mayúscula y un número, y el correo no puede estar repetido.

## Version 1: prompt basico

```text
Dame casos de prueba para un registro de usuarios.
```

- **Técnica agregada:** ninguna, es el punto de partida.
- **Qué pasó:** la IA entregó más de 20 casos en una tabla con columnas ID, Caso de prueba, Datos de entrada y Resultado esperado.
- **Qué faltó:** las respuestas fueron genéricas y no usaron las reglas de mi formulario (por ejemplo, "Contraseña corta: 123"). Incluyó casos que no pedí, como confirmación de contraseña y botón Registrar, y varios resultados esperados eran ambiguos, como "Validar o rechazar según las reglas del sistema".


## Version 2

```text
<rol>Actua como analista de pruebas de software de una tienda en linea.</rol>

<contexto>Formulario web de registro con nombre, correo y contrasena.
La contrasena debe tener minimo 8 caracteres, una mayuscula y un numero.</contexto>

<tarea>Escribe 8 casos de prueba.</tarea>

<formato>Tabla con las columnas: ID, escenario, datos de entrada,
resultado esperado.</formato>
```

- **Técnicas agregadas:** role prompting y prompt estructurado.
- **Por qué:** la v1 era demasiado general y larga. El rol y el contexto la orientan al formulario real, y las etiquetas separan cada parte del pedido.
- **Qué mejoró:** entregó exactamente 8 casos con las 4 columnas pedidas. Los casos usan las reglas de la contraseña (menos de 8 caracteres, sin mayúscula, sin número) y los resultados esperados son concretos.
- **Qué faltó:** no incluyó el caso de correo repetido ni un caso límite de exactamente 8 caracteres, y no explicó su razonamiento.


## Version 3: prompt final

```text
<rol>Actua como analista de pruebas de software de una tienda en linea.</rol>

<contexto>Formulario web de registro con nombre, correo y contrasena.
La contrasena debe tener minimo 8 caracteres, una mayuscula y un numero.
El correo no puede estar repetido.</contexto>

<tarea>Piensa paso a paso que puede fallar en cada campo y escribe 8
casos de prueba: validos, invalidos y limite. Al final, revisa tu tabla,
agrega los casos que falten e indica cuales agregaste.</tarea>

<ejemplo>
CP-01 | Registro con datos validos | Nombre: Ana, Correo: ana@mail.com,
Contrasena: Clave1234 | El sistema crea la cuenta y muestra confirmacion
</ejemplo>

<formato>Tabla con las columnas: ID, escenario, datos de entrada,
resultado esperado. Responde en espanol.</formato>
```

- **Técnicas agregadas:** few-shot, chain of thought y autocrítica.
- **Por qué:** el ejemplo fija el estilo de los datos y los IDs, "piensa paso a paso" hace que cubra cada campo, y la autocrítica busca los casos que faltan.
- **Qué mejoró:** incluyó el caso límite de exactamente 8 caracteres (CP-02) y el correo ya registrado (CP-06). Los IDs y los datos siguen el formato del ejemplo (CP-01, "Nombre: Ana, Correo: ..."). Además revisó su tabla y propuso 3 casos nuevos: CP-09, CP-10 y CP-11.


## Tecnicas usadas en el prompt final

| Técnica | Parte del prompt final |
|---------|------------------------|
| Role prompting | `<rol>Actua como analista de pruebas de software de una tienda en linea.</rol>` |
| Prompt estructurado | Las etiquetas `<rol>`, `<contexto>`, `<tarea>`, `<ejemplo>` y `<formato>` |
| Chain of thought | "Piensa paso a paso que puede fallar en cada campo..." |
| Few-shot | `<ejemplo>` con el caso CP-01 |
| Autocrítica | "Al final, revisa tu tabla, agrega los casos que falten e indica cuales agregaste." |

## Evaluacion del resultado

| Criterio | Cumple (Sí / No) |
|----------|------------------|
| ¿Tiene las 4 columnas pedidas (ID, escenario, datos de entrada, resultado esperado)? | Sí |
| ¿Incluye casos válidos, inválidos y límite? | Sí |
| ¿Incluye el caso de correo ya registrado? | Sí (CP-06) |
| ¿Respeta el formato del ejemplo (IDs con guion y datos con Nombre/Correo/Contraseña)? | Sí |
| ¿Indica qué casos agregó en la autocrítica? | Sí (CP-09, CP-10 y CP-11) |
| ¿Integró los casos agregados dentro de la tabla? | No, solo los listó debajo de la tabla |
| ¿Está libre de casos repetidos? | No, CP-10 (7 caracteres) repite a CP-03 (Clave12 también tiene 7) |

## Por que elegi estas tecnicas

Elegí role prompting y prompt estructurado porque un caso de prueba depende de las reglas del formulario, y el rol con el contexto evita las respuestas genéricas de la versión 1. Usé few-shot porque necesitaba que todos los casos tuvieran el mismo formato, de modo que se puedan copiar a una hoja de cálculo sin corregirlos. Usé chain of thought para que la IA pensara qué puede fallar en cada campo antes de escribir, y autocrítica para detectar casos que faltaban. No usé descomposición porque la tarea era pequeña y cabía en un solo pedido, y no necesitaba revisar resultados intermedios como en el Ejercicio 5. Aun así, la IA se equivocó al revisarse (agregó un caso repetido), por lo que debo revisar yo la respuesta final.