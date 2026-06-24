# 12. Importar scripts externos (`import` y `new`)

[← Funciones nativas](11-funciones-nativas.md) · [Índice](README.md) · [Siguiente: Recetario de ejemplos →](13-recetario-ejemplos.md)

---

Tu script no tiene que estar todo en el Mind Stone. Puedes guardarlo en **archivos
separados** y cargarlos. Esto mantiene tu código ordenado y reutilizable.

> Los archivos externos deben estar en la carpeta de guardado, dentro de una carpeta
> `/Stonescript`, y terminar en `.txt`.

---

## `import` — cargar un script externo

`import` carga y ejecuta un archivo como si estuviera escrito directamente en tu Mind
Stone. Ideal para organizar scripts de farmeo:

```
import Rocky
import Deadwood
import Caves
import Forest
```

### Aislamiento de variables

Las variables de un script importado están **aisladas**: sus nombres **no chocan** con los
de otros scripts. Cada script tiene su propio "contenedor".

### Importar y usar lo que devuelve

Un script importado se devuelve como una **referencia**, así que puedes guardar esa
referencia en una variable y usar sus funciones:

```
// PrintUtil.txt
func LowerLeft(x, y, color, message)
  y = y + screen.h
  >`@x@,@y@,@color@,@message@
```
```
// Script principal en el Mind Stone
var print = import PrintUtil
disable hud
print.LowerLeft(0, -1, #ffffff, "Vida: " + hp)
```

### Subcarpetas

```
import Games/Blackjack
import Cosmetics/PetFrog
import Cosmetics/Hats
```

---

## `new` — crear copias independientes

`new` es como `import`, pero crea una **copia única e independiente** cada vez. Útil para
programación orientada a objetos / componentes.

```
// Vector.txt
var x = 0
var y = 0

func init(_x, _y)
  x = _x
  y = _y

func subtract(otherVect)
  x = x - otherVect.x
  y = y - otherVect.y
```
```
// Script principal
var vectFrom = new Components/Vector
var vectTo = new Components/Vector

vectFrom.init(5, 4)
vectTo.init(8, 2)
vectTo.subtract(vectFrom)

>x = @vectTo.x@, y = @vectTo.y@
```

---

## `import` vs. `new`: las dos diferencias clave

| | `import` | `new` |
|--|----------|-------|
| ¿Misma copia o nueva? | **La misma** objeto cada vez (compartido) | Una **copia nueva** cada vez |
| ¿Cuándo corre el cuerpo del script? | **Cada frame** (como tu Mind Stone) | **Una sola vez** al crearlo |

Resumen:

- Usa **`import`** para scripts que deben correr cada frame (lógica de combate, farmeo).
- Usa **`new`** para crear objetos/componentes (vectores, entidades) que solo necesitan
  inicializarse una vez.

---

## `ToString()` para imprimir objetos directamente

Si tu script externo define una función `ToString()`, puedes imprimir el objeto
directamente con `@...@`:

```
// Vector.txt
var x = 0
var y = 0

func init(_x, _y)
  x = _x
  y = _y

func ToString()
  return "(" + x + ", " + y + ")"
```
```
// Script principal
var v = import Components/Vector
v.init(3, 5)
>Vector = @v@         // imprime: Vector = (3, 5)
```

---

## ¿De dónde se cargan los scripts? (`sys`)

Por defecto, en PC se cargan de tu disco local; en móvil, del repositorio oficial. Puedes
cambiar el origen (avanzado):

| Propiedad / función | Qué hace |
|---------------------|----------|
| `sys.fileUrl` | Ruta actual desde donde se importan scripts (solo lectura) |
| `sys.SetFileUrl(str)` | Cambia el origen (`"local"`, `"remote"` o una URL). Solo en el Mind Stone |
| `sys.cacheRemoteFiles` | Si los archivos remotos se cachean entre runs (por defecto true) |
| `sys.isMobile` / `sys.isPC` | ¿El jugador está en móvil / PC? |
| `sys.os` | Sistema operativo del jugador |

```
sys.SetFileUrl("https://MiDominio.com/scripts/")
import MyCombatScript
```

---

[← Funciones nativas](11-funciones-nativas.md) · [Índice](README.md) · [Siguiente: Recetario de ejemplos →](13-recetario-ejemplos.md)
