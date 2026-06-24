# 7. Funciones (tus bloques reutilizables)

[← Variables y matemáticas](06-variables-y-matematicas.md) · [Índice](README.md) · [Siguiente: Bucles y arrays →](08-bucles-y-arrays.md)

---

Una **función** es un bloque de código con nombre que puedes **reutilizar** llamándolo
cuando quieras. Sirven para dos cosas:

1. **No repetir** el mismo código una y otra vez.
2. **Ordenar** tu script para que sea fácil de leer cuando crece.

## Crear una función: `func`

```
func Print(message)    // declaro la función "Print" que recibe "message"
  >@message@           // su cuerpo (con sangría)

Print(Hola Mundo!)     // la LLAMO -> imprime "Hola Mundo!"
```

Puntos clave:

- `func` declara la función, pero **su cuerpo NO se ejecuta en ese momento**.
- El código de dentro solo corre **cuando la llamas** por su nombre, ej. `Print(...)`.
- Lo que va entre paréntesis son los **parámetros** (los datos que recibe).

---

## Llamar una función

Para usarla escribes su nombre y, entre paréntesis, los valores que necesita:

```
func ResetCounter()
  count = 0

?key = begin
  ResetCounter()      // se ejecuta solo cuando se pulsa la tecla
```

Una función sin parámetros igual lleva los paréntesis vacíos: `ResetCounter()`.

---

## Funciones que devuelven un valor: `return`

Con `return` la función te **entrega un resultado** que puedes guardar o imprimir.

```
func NonBossDuration()
  return totalTime - time     // devuelve la duración de la zona principal

var duration
duration = NonBossDuration()  // guardo el resultado
>Tiempo: @duration@
```

En cuanto se ejecuta `return`, la función **termina** y devuelve ese valor.

---

## Parámetros (recibir datos)

Las funciones pueden recibir **cualquier número** de parámetros. Así una misma función
sirve para muchos casos distintos:

```
func RandomRange(min, max)
  ?min >= max
    return min
  return min + rng % (max - min + 1)

var randomValue
randomValue = RandomRange(5, 10)   // un número al azar entre 5 y 10
>RNG: @randomValue@
```

Fíjate que dentro de la función puedes usar **condiciones** (`?`) y `return` normalmente.

---

## La palabra `this` (variable del script vs. de la función)

Si una función tiene un parámetro con el **mismo nombre** que una variable del script,
usa `this.` para referirte a la del script y diferenciarla.

```
var a = 1
func TestScope(a)               // este "a" es el parámetro
  >Script var = @this.a@, function var = @a@
  // this.a = 1 (la del script), a = 3 (la del parámetro)

TestScope(3)
```

`this` es **opcional**: si no hay choque de nombres, puedes acceder a las variables del
script directamente, sin prefijo.

---

## Ejemplo real (de tu repo `Funciones.txt`)

Tus propias funciones para crear interfaz son un ejemplo perfecto de reutilización: una
función que crea un botón y lo devuelve, para no repetir 8 líneas cada vez.

```
func CreateButton(text, x, y, w, h, a, d, action)
  var b = ui.AddButton()
  b.text = text
  b.x = x
  b.y = y
  b.w = w
  b.h = h
  b.anchor = a
  b.dock = d
  b.SetPressed(action)
  return b              // devuelve el botón ya configurado
```

Luego, crear un botón es una sola línea:

```
var miBoton = CreateButton("Jugar", 0, 1, 12, 5, center_center, center_center, OnPlay)
```

(Más sobre interfaz en el [capítulo 10](10-interfaz-ui.md).)

---

## Cuidado: el límite de la pila (stack)

Cuando una función llama a otra (o a sí misma), se crea una "pila" de llamadas. StoneScript
tiene un **límite de 215**. Si lo superas (por ejemplo, una función que se llama a sí misma
sin condición de salida), el juego lanza un error.

```
func Infinita()
  Infinita()    // ¡mal! se llama sin parar -> error de stack

// Correcto: siempre con una condición que detenga la recursión
func CuentaAtras(n)
  ?n <= 0
    return
  >@n@
  CuentaAtras(n - 1)
```

---

## Resumen

| Concepto | Para qué sirve |
|----------|----------------|
| `func Nombre(...)` | Declarar una función (no se ejecuta aún) |
| `Nombre(...)` | Llamarla (ahí sí se ejecuta) |
| parámetros | Pasarle datos para que trabaje con ellos |
| `return` | Devolver un resultado y terminar la función |
| `this.` | Distinguir la variable del script de un parámetro |

---

[← Variables y matemáticas](06-variables-y-matematicas.md) · [Índice](README.md) · [Siguiente: Bucles y arrays →](08-bucles-y-arrays.md)
