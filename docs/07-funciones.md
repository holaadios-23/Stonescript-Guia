# 7. Funciones (tus bloques reutilizables)

[← Variables y matemáticas](06-variables-y-matematicas.md) · [Índice](README.md) · [Siguiente: Bucles y arrays →](08-bucles-y-arrays.md)

---

## ¿Qué es una función?

Una **función** es un bloque de código con nombre que puedes **reutilizar** llamándolo cuando quieras. Sirven para dos cosas principales:

1. **No repetir** el mismo código una y otra vez (evitas escribir 20 líneas 5 veces).
2. **Ordenar** tu script para que sea fácil de leer cuando crece (tu mente y la de otros pueden seguir la lógica).

**Imagina esto:** Tienes un script que equipa armas 10 veces en diferentes puntos del combate. En lugar de escribir las mismas 8 líneas 10 veces, creas una función `EquipWeapons()` y solo la llamas cuando la necesitas.

---

## Crear una función: `func`

La sintaxis básica es:

```
func NombreFuncion(parámetro1, parámetro2)
  // el cuerpo (con sangría)
  // todo lo que va aquí se ejecuta cuando llames la función
```

### Ejemplo simple: función sin parámetros

```
func Print(message)    // declaro la función "Print" que recibe "message"
  >@message@           // su cuerpo (con sangría)

Print("Hola Mundo!")   // la LLAMO -> imprime "Hola Mundo!"
```

**Puntos clave:**

- `func` **declara** la función, pero **su cuerpo NO se ejecuta en ese momento**.
- El código de dentro solo corre **cuando la llamas** por su nombre, ej. `Print(...)`.
- Lo que va entre paréntesis son los **parámetros** (los datos que recibe la función).
- Si una función no tiene parámetros, igual lleva los paréntesis vacíos: `miFunc()`.

---

## Llamar una función

Para usar una función escribes su nombre, luego entre paréntesis los **valores** que necesita:

```
func ResetCounter()
  count = 0

?key = begin
  ResetCounter()      // se ejecuta solo cuando se pulsa la tecla
```

En cada frame donde se cumple la condición, la función se ejecuta. Fuera de esa condición, **no ocurre nada**.

---

## Funciones que devuelven un valor: `return`

Con `return`, la función te **entrega un resultado** que puedes guardar, imprimir o usar en otra operación.

```
func NonBossDuration()
  return totalTime - time     // devuelve la duración de la zona principal

var duration
duration = NonBossDuration()  // guardo el resultado en una variable
>Tiempo: @duration@           // lo imprimo
```

**Punto importante:** En cuanto se ejecuta `return`, la función **termina inmediatamente** y devuelve ese valor. Nada de lo que va después del `return` se ejecuta.

---

## Parámetros (pasar datos a la función)

Las funciones pueden recibir **cualquier número** de parámetros. Así una misma función sirve para muchos casos distintos sin escribir código repetido.

```
func RandomRange(min, max)
  ?min >= max
    return min
  return min + rng % (max - min + 1)

var randomValue
randomValue = RandomRange(5, 10)   // un número al azar entre 5 y 10
>RNG: @randomValue@
```

**Cómo funcionan los parámetros:**

1. Defines la función con nombres de parámetros: `func DoSomething(a, b, c)`
2. Cuando llamas la función, pasas los **valores reales**: `DoSomething(1, 2, 3)`
3. Dentro de la función, `a=1`, `b=2`, `c=3` (aunque sea temporalmente).

**Otro ejemplo más práctico:**

```
func Equip(mano, item)
  ?mano = left
    equipL @item@
  :
    equipR @item@

Equip(left, shield)     // equipa escudo en la mano izquierda
Equip(right, sword)     // equipa espada en la mano derecha
```

Sin funciones, tendrías que escribir la lógica `?` dos veces.

---

## Variables locales vs. globales (el alcance)

Dentro de una función puedes crear variables que solo existen **dentro** de esa función. Cuando la función termina, desaparecen. Eso se llama **variable local**.

```
var globalCount = 10    // global: existe en todo el script

func MiFunc()
  var localCount = 5    // local: solo existe aquí dentro
  globalCount = 20      // puedo cambiar la global
  
MiFunc()
>@globalCount@          // imprime 20
>@localCount@           // ERROR: localCount no existe aquí
```

**Resumen:**

- **Variables globales**: Declaradas fuera de funciones. Todos pueden acceder.
- **Variables locales**: Declaradas dentro de funciones. Solo existen dentro.

---

## La palabra `this` (diferencia entre variables)

Si una función tiene un **parámetro** con el mismo nombre que una variable del script, usa `this.` para referirte a la variable del script y diferenciarla.

```
var a = 1               // variable global

func TestScope(a)       // parámetro con el MISMO nombre
  >Script var = @this.a@, function var = @a@
  // this.a = 1 (la global), a = 3 (el parámetro)

TestScope(3)
```

**Punto:** `this` es **opcional**. Si no hay choque de nombres, puedes acceder a variables globales directamente sin prefijo. Pero cuando hay ambigüedad, `this` es tu salvavidas.

---

## Recursión (una función que se llama a sí misma)

Una función puede llamarse a sí misma. Pero **siempre necesita una condición de salida**, o entraría en un bucle infinito.

```
func CuentaAtras(n)
  ?n <= 0              // condición de salida: si llegó a 0, termina
    return
  >@n@                 // imprime el número
  CuentaAtras(n - 1)   // se llama a sí misma con n-1
```

Llámalo así: `CuentaAtras(5)` y imprimirá 5, 4, 3, 2, 1.

**Cuidado:** El stack tiene un límite de **215 llamadas anidadas**. Si tu función recursiva es demasiado profunda, el juego lanza un error.

---

## El límite de la pila (stack overflow)

Cuando una función llama a otra (o a sí misma), se crea una "pila" de llamadas. StoneScript tiene un límite de **215**. Si lo superas (por ejemplo, una función que se llama sin parar), el juego lanza un error:

```
func Infinita()
  Infinita()    // ¡mal! se llama sin parar -> error de stack overflow
```

**Mal:**

```
func recursion(n)
  recursion(n + 1)    // nunca termina
```

**Bien:**

```
func recursion(n)
  ?n > 100            // condición para parar
    return
  recursion(n + 1)    // solo llamada si no hemos llegado a 100
```

---

## Ejemplo real: Función para crear botones

Un buen ejemplo de por qué las funciones son útiles. En lugar de escribir 8 líneas cada vez que creas un botón, haces una función:

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

Luego, crear un botón es una **sola línea**:

```
var miBoton = CreateButton("Jugar", 0, 1, 12, 5, center_center, center_center, OnPlay)
var otroBoton = CreateButton("Salir", 0, 2, 12, 5, center_center, center_center, OnQuit)
```

Sin la función, serían **16 líneas** (8 × 2). Con la función, son **2 líneas**. Mucho más limpio.

---

## Ejemplo real: Función con lógica compleja

```
func DamageMultiplier(baseDamage, isCritical, buff)
  var total = baseDamage
  
  ?isCritical
    total = total * 2
  
  ?buff = strength
    total = total + 50
  :? buff = weakness
    total = total / 2
  
  return total

var myDamage = DamageMultiplier(25, true, strength)
>Daño final: @myDamage@
```

Esta función recibe datos, hace cálculos con lógica `?`, y devuelve el resultado. Reutilizable en cualquier lugar de tu script.

---

## Errores comunes con funciones

| Error | Solución |
|-------|----------|
| "Función no definida" | Asegúrate de haber escrito `func` antes de usarla |
| Función no se ejecuta | Recuerda que las funciones solo corren cuando las **llamas** con `()` |
| Variable no existe dentro de la función | Usa variables globales o parámetros para pasarle datos |
| "Stack overflow" | Tu función se llama infinitamente; añade una condición de salida |
| `return` en medio de la función no termina | Sí lo hace; todo lo después del `return` se ignora |

---

## Resumen: conceptos clave de funciones

| Concepto | Significa |
|----------|----------|
| `func Nombre(...)` | Declarar una función (aún no se ejecuta) |
| `Nombre(...)` | Llamarla (ahora sí se ejecuta) |
| **parámetros** | Datos que la función recibe: `func Saludar(nombre)` |
| **return** | Devolver un resultado y terminar la función |
| **`this.`** | Distinguir la variable del script de un parámetro |
| **variable local** | Variable que solo existe dentro de la función |
| **recursión** | Una función que se llama a sí misma (con condición de salida) |
| **stack** | La "pila" de llamadas; máximo 215 |

---

## Checklist: tu función está lista cuando...

- [ ] Tiene un nombre descriptivo (no `func f()`)
- [ ] Recibe solo los parámetros que necesita
- [ ] Devuelve algo con `return` (o no devuelve nada si no es necesario)
- [ ] Tiene una condición de salida si es recursiva
- [ ] No "accidentalmente" modifica variables globales (o lo hace intencionadamente)
- [ ] Funciona igual cada vez que la llamas con los mismos parámetros

---

[← Variables y matemáticas](06-variables-y-matematicas.md) · [Índice](README.md) · [Siguiente: Bucles y arrays →](08-bucles-y-arrays.md)
