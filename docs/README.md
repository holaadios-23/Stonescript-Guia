# Guía de StoneScript en español (fácil y entendible)

StoneScript es el lenguaje con el que programas a tu personaje en **Stone Story RPG**.
La idea clave es muy simple: **el juego te hace preguntas y tú respondes qué hacer**.

> Si la distancia del enemigo es mayor a 20 → equipa la máscara en la mano derecha y la
> triskel en la izquierda.

En StoneScript eso se escribe así:

```
?foe.distance > 20
  equipL triskel
  equipR mask
```

- `?` significa **"si..."** (en programación: *if*).
- `:?` significa **"si no, pero si..."** (*else-if*).
- `:` significa **"si no..."** (*else*).
- La **sangría** (los espacios al inicio de la línea) indica qué pasa **dentro** de cada pregunta.

Si entiendes esas 4 cosas, ya entiendes el 80% de StoneScript. El resto de esta guía
explica todo lo demás paso a paso, con ejemplos sencillos.

---

## ¿Por dónde empiezo?

Lee los capítulos **en orden** si eres nuevo. Si ya sabes lo básico, salta al que necesites.

| # | Capítulo | ¿Qué aprenderás? |
|---|----------|------------------|
| 1 | [Introducción: cómo funciona](01-introduccion.md) | Qué es el Mind Stone, frames, sangría y comentarios |
| 2 | [Condiciones: `?` `:?` `:`](02-condiciones.md) | El corazón del lenguaje: preguntas, respuestas y **condiciones dentro de otras** |
| 3 | [Estado del juego](03-estado-del-juego.md) | Todo lo que puedes preguntar: enemigo, vida, ubicación, etc. |
| 4 | [Comandos](04-comandos.md) | Las órdenes: `equip`, `activate`, `loadout`, `>`... |
| 5 | [Comparaciones y filtros](05-comparaciones-y-filtros.md) | `=` `!` `&` `|` `>` `<` y los filtros de búsqueda |
| 6 | [Variables y matemáticas](06-variables-y-matematicas.md) | Guardar datos y hacer cuentas |
| 7 | [Funciones](07-funciones.md) | Crear tus propios "bloques" reutilizables |
| 8 | [Bucles y arrays](08-bucles-y-arrays.md) | Repetir cosas y listas de valores |
| 9 | [Imprimir texto y ASCII-art](09-impresion-y-ascii.md) | Mostrar mensajes y dibujos en pantalla |
| 10 | [Interfaz de usuario (UI)](10-interfaz-ui.md) | Botones, paneles y texto personalizado |
| 11 | [Funciones nativas](11-funciones-nativas.md) | Herramientas listas: `math`, `string`, `storage`, `color`... |
| 12 | [Importar scripts externos](12-importar-scripts.md) | Dividir tu código en archivos `import` / `new` |
| 13 | [Recetario de ejemplos](13-recetario-ejemplos.md) | Scripts completos y útiles, listos para copiar |
| 14 | [Trucos y errores comunes](14-trucos-y-errores.md) | Consejos prácticos y cómo depurar |
| 15 | [Agujeros lógicos](15-agujeros-logicos.md) | Errores de lógica sin sintaxis: por qué tu código funciona pero no hace lo que quieres |

---

## La regla de oro: sangría = scope

En StoneScript **los espacios importan**. Lo que escribes con sangría debajo de un `?`
solo se ejecuta **si esa pregunta es verdadera**.

```
?hp < 10
  activate potion   <- esto SOLO pasa si la vida es menor a 10
```

Si quitas la sangría, la línea ya **no** pertenece a la pregunta:

```
?hp < 10
activate potion   <- esto pasa SIEMPRE (mal, casi nunca quieres esto)
```

Usa siempre **2 espacios** por nivel y sé consistente. En este manual lo verás así
en todos los ejemplos.

---

## Recuerda

- El script corre **30 veces por segundo** (1 vez por *frame*). Es como un bucle infinito.
- Puedes editar el script en pleno combate pulsando **M** (Mind Stone).
- Mantén pulsado **Tab** en el juego para ver el estado y los últimos errores.

¿Listo? Empieza por la **[Introducción](01-introduccion.md)**.
