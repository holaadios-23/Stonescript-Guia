# 6. Variables y matemáticas

[← Comparaciones y filtros](05-comparaciones-y-filtros.md) · [Índice](README.md) · [Siguiente: Funciones →](07-funciones.md)

---

Una **variable** es una "caja" con nombre donde guardas un valor para usarlo después.
Como el script corre 30 veces por segundo, las variables son la forma de **recordar**
cosas entre frames.

## Crear y usar variables: `var`

```
var myVar = 10        // creo la caja "myVar" con el valor 10

myVar = myVar + 5     // ahora vale 15

var secondVar = 3
myVar = myVar - secondVar   // ahora vale 12
```

- Usa `var` **solo la primera vez** para crear la variable.
- Después la cambias **sin** `var`, solo con `nombre = valor`.

---

## ⚠️ Lo más importante de las variables en StoneScript

> Una variable se **inicializa una sola vez**, la primera vez que se ejecuta su línea `var`.

Aunque el script corra cada frame, la línea `var i = 0` **no** vuelve a poner `i` en 0 cada
frame. Por eso esto cuenta hacia arriba sin parar:

```
var i = 0     // se pone en 0 UNA sola vez
i++           // cada frame suma 1
>i = @i@      // 0, 1, 2, 3, ...
```

Las variables **solo se reinician** cuando sales de una ubicación y empiezas una run nueva
manualmente. **No** se reinician cuando Ouroboros hace loop ni al abrir/cerrar el Mind
Stone.

¿Quieres reiniciar a propósito al empezar la zona? Usa `loc.begin`:

```
var i = 0
?loc.begin     // primer frame de la ubicación
  i = 0        // reinicio manual
i++
>i = @i@
```

---

## Variables de texto (strings)

Un string es texto. Puedes usar comillas para incluir símbolos especiales o espacios al
final.

```
var nombre = "Aventurero"
>Hola @nombre@
```

### Unir textos con `+`

El operador `+` también **junta** textos (y números convertidos a texto):

```
var a = 10
var b = 5
var myVar = a + " x " + b + " = " + (a * b)
>@myVar@        // imprime: 10 x 5 = 50
```

---

## Operaciones matemáticas

| Operador | Qué hace | Ejemplo | Resultado |
|----------|----------|---------|-----------|
| `+` | Suma | `2 + 3` | `5` |
| `-` | Resta | `10 - 4` | `6` |
| `*` | Multiplica | `2 * 5` | `10` |
| `/` | Divide | `8 / 4` | `2` |
| `%` | Resto (módulo) | `5 % 4` | `1` |
| `++` | Suma 1 a la variable | `a++` | `a` aumenta en 1 |
| `--` | Resta 1 a la variable | `a--` | `a` disminuye en 1 |
| `( )` | Prioriza operaciones | `2 * (3 - 1)` | `4` |
| `!` | Niega un booleano | `!ai.enabled` | invierte verdadero/falso |

### Cuidado con la división de enteros

Si divides **enteros**, el resultado se **redondea hacia abajo**:

```
var a = 8
a = a / 4     // a = 2

var b = 5
b = b / 2     // b = 2  (¡no 2.5!, porque son enteros)
```

Para obtener decimales, usa un número con punto (un *float*):

```
var c = 5.0
c = c / 2     // c = 2.5
```

### El módulo `%` y los ciclos de tiempo

`%` da el **resto** de una división. Combinado con `time` es perfecto para hacer cosas
periódicas o animaciones:

```
?time % 8 < 4
  > \O)
?time % 8 > 3
  > (O/
// alterna entre dos "fotogramas" -> emoji animado
```

---

## Enteros vs. decimales (int vs. float)

- Si escribes un número **sin punto** (`2`, `100`), es un **entero** (int).
- Si escribes un número **con punto** (`0.5`, `2.0`), es un **decimal** (float).

Esto afecta a la división (como viste arriba) y a varias funciones de `math`
(ver [capítulo 11](11-funciones-nativas.md)).

---

## Números aleatorios

```
?rng < 5000      // rng: entero al azar 0..9999
  >Cara!
:
  >Cruz!
```

Generar un entero en un rango (por ejemplo 5 a 24):

```
var min = 5
var max = 24
var n = min + rng % (max - min + 1)
```

Generar un decimal en un rango (5.0 a 24.0):

```
var min = 5.0
var max = 24.0
var n = min + (max - min) * rngf
```

---

## El tipo de una variable: `Type()`

`Type(variable)` devuelve un texto con el tipo: `string`, `int`, `bool`, `function`,
`object` o `null`.

```
var a = 0
?Type(a) = int
  >La variable 'a' es un entero.
```

---

## Ejemplo real (de tu propio repo)

Este patrón de "llevar la cuenta" entre frames es justo lo que hace tu
`CruzRojaCounter.txt`:

```
var last_hp = 0
?loc.begin
  last_hp = hp          // guardo la vida inicial una vez por run

var current_hp = hp
?current_hp < last_hp   // ¿perdí vida desde el frame anterior?
  >Recibí daño
last_hp = current_hp    // actualizo para el próximo frame
```

---

[← Comparaciones y filtros](05-comparaciones-y-filtros.md) · [Índice](README.md) · [Siguiente: Funciones →](07-funciones.md)
