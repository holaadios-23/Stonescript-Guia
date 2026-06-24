# 8. Bucles y arrays

[← Funciones](07-funciones.md) · [Índice](README.md) · [Siguiente: Imprimir y ASCII-art →](09-impresion-y-ascii.md)

---

## Parte A — Bucles (`for`)

Un **bucle** repite un bloque de código varias veces. Recuerda: tu script **ya** se repite
cada frame; un `for` repite algo **dentro de un mismo frame**.

### Forma básica: `for v = a..b`

La variable `v` empieza en `a` y aumenta de 1 en 1 hasta llegar a `b`.

```
for i = 1..5
  >`0,@i@,i = @i@
// imprime i = 1, i = 2, ... i = 5 en líneas distintas
```

- **No** declares `v` con `var` antes; el `for` la crea y existe solo dentro del bucle.
- Los límites `a` y `b` sí pueden ser variables.

```
var min = 1
var max = 4
var sum = 0
for j = min..max
  sum = sum + j     // 1+2+3+4
>sum = @sum@        // 10
```

### Hacia atrás y con negativos

```
var g = ""
for k = 5..-2
  g = g + k
>g = @g@           // 543210-1-2
```

### Bucles anidados

Un bucle dentro de otro, igual que las condiciones anidadas:

```
for x = 1..9
  for y = x/2 .. x/2 + 6
    >`@x@,@y@,*
```

### Salir antes de tiempo: `break` y cambiar la variable

```
for i = 1..5
  ?i = 3
    break        // sale del bucle inmediatamente
```

También puedes salir poniendo la variable fuera de rango:

```
var n = ""
for i = 1..5
  ?i = 3
    i = -1       // fuerza el fin del bucle
  n = n + i
>n = @n@
```

### Saltar una vuelta: `continue`

`continue` salta el resto de esa vuelta y pasa a la siguiente:

```
var n = ""
for i = 1..5
  ?i = 3
    continue     // se salta el i=3
  n = n + i
>n = @n@         // 1245
```

### Recorrer un array directamente

```
var a = [1, 2, 3]
var n = ""
for value : a       // "para cada value en a"
  n = n + value
>n = @n@            // 123
```

---

## Parte B — Arrays (listas)

Un **array** es una variable que guarda **varios valores** en orden, como una lista.

### Crear un array

```
var emptyCollection = []                      // vacío
var magicNumbers = [10, 3, 0, 15, -7]         // de números
var someStrings = ["apple", "banana", "cherry"] // de textos

// también en varias líneas
var frutas = [
  "manzana",
  "plátano",
  "cereza",
]
```

### Leer un elemento: `array[posición]`

Las posiciones empiezan en **0** (la primera es `[0]`, no `[1]`).

```
var myArray = [10, 3]
?myArray[1] = 3
  >Sí, el valor en [1] es 3
```

### Operaciones más comunes

| Operación | Qué hace |
|-----------|----------|
| `a.Add(valor)` | Añade al final |
| `a.Count()` | Cuántos elementos tiene |
| `a.Contains(valor)` | ¿Está ese valor? (true/false) |
| `a.IndexOf(valor)` | Posición del valor (o -1 si no está) |
| `a.Emplace(i, valor)` | Reemplaza el valor en la posición `i` |
| `a.Insert(i, valor)` | Inserta en la posición `i` (corre los demás) |
| `a.RemoveAt(i)` | Elimina el de la posición `i` y lo devuelve |
| `a.Clear()` | Vacía el array (más eficiente que volver a crearlo) |
| `a.Sort()` | Ordena de menor a mayor |

```
var lista = []
lista.Add(10)
lista.Add(3)
>Tengo @lista.Count()@ elementos
```

### Recorrer un array con índice

```
var frutas = ["Manzana", "Plátano", "Cereza"]
var count = frutas.Count()
?count > 0
  for i = 0 .. count - 1
    var value = frutas[i]
    >`0,@i@,@value@
```

> Patrón clave: para recorrer con índices vas de `0` a `Count() - 1` (porque empieza en 0).

### Arrays de varias dimensiones

```
var a = [[1,2], [3,4]]
var value = a[1][0]    // fila 1, columna 0 -> 3
>Valor @value@
```

### Consejo de rendimiento

Crear un array nuevo cada frame es malo para el rendimiento. Mejor créalo una vez y
límpialo:

```
var clearedEachFrame = []
clearedEachFrame.Clear()   // mejor para CPU y memoria que volver a poner = []
```

O límpialo solo al empezar la zona / loop:

```
var clearedEachLoop = []
?loc.begin | loc.loop
  clearedEachLoop.Clear()
```

---

## Entrada del jugador (`?key`) — un mini-adelanto

Los bucles y arrays suelen ir de la mano con leer teclas para hacer minijuegos. Puedes
preguntar por la entrada con `?key`:

```
var x = 0
var y = 0

?key = leftBegin      // se pulsó "izquierda"
  x--
?key = rightBegin
  x++
?key = upBegin
  y--
?key = downBegin
  y++

>`@x@,@y@,#ffffff,@   // dibuja un @ que se mueve por la pantalla
```

Tabla de códigos de teclas (presionado / mantenido / soltado):

| Mantenido | Presionado | Soltado | Tecla PC |
|-----------|------------|---------|----------|
| `left` | `leftBegin` | `leftEnd` | A o ← |
| `right` | `rightBegin` | `rightEnd` | D o → |
| `up` | `upBegin` | `upEnd` | W o ↑ |
| `down` | `downBegin` | `downEnd` | S o ↓ |
| `primary` | `primaryBegin` | `primaryEnd` | Clic izq, Enter |
| `back` | `backBegin` | `backEnd` | X |
| `ability1` | `ability1Begin` | `ability1End` | Shift |
| `ability2` | `ability2Begin` | `ability2End` | Control |
| `bumpL` | `bumpLBegin` | `bumpLEnd` | Z |
| `bumpR` | `bumpRBegin` | `bumpREnd` | C |

> `xBegin` = el momento exacto en que se presiona; `x` = mientras se mantiene;
> `xEnd` = el momento en que se suelta.

---

[← Funciones](07-funciones.md) · [Índice](README.md) · [Siguiente: Imprimir y ASCII-art →](09-impresion-y-ascii.md)
