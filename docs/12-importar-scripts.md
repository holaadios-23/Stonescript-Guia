# 12. Importar scripts externos (`import` y `new`)

[← Funciones nativas](11-funciones-nativas.md) · [Índice](README.md) · [Siguiente: Recetario de ejemplos →](13-recetario-ejemplos.md)

---

## ¿Por qué importar scripts?

Tu script no tiene que estar **todo** en el Mind Stone. Puedes guardarlo en **archivos separados** y cargarlos. Esto tiene ventajas:

- **Organización:** Tu código está dividido en módulos lógicos (combate, interfaz, farmeo).
- **Reutilización:** Creas una vez, usas mil veces en diferentes scripts.
- **Mantenimiento:** Si necesitas cambiar algo, lo haces en un solo lugar.
- **Trabajo en equipo:** Diferentes personas pueden trabajar en diferentes módulos.

> En PC, los archivos externos deben estar en tu carpeta de guardado, en una subcarpeta `/Stonescript`, y terminar en `.txt`.
>
> En móvil, puedes hacer lo mismo, pero también hay una forma especial para importar desde GitHub (ver abajo).

---

## `import` — cargar un script externo (ejecuta cada frame)

`import` carga un archivo y lo ejecuta como si estuviera escrito **directamente** en tu Mind Stone. Es ideal para scripts que deben correr continuamente (combate, farmeo):

```
import Rocky
import Deadwood
import Caves
import Forest
```

Esto carga 4 archivos (Rocky.txt, Deadwood.txt, etc.) y **todos ellos se ejecutan cada frame** junto a tu script principal.

---

## `new` — crear copias independientes (ejecuta una sola vez)

`new` es como `import`, pero crea una **copia única e independiente** cada vez que lo llamas. El script se ejecuta **una sola vez** al crearlo.

Útil para programación orientada a objetos (crear "objetos" con su propio estado):

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
// Script principal en el Mind Stone
var vectFrom = new Components/Vector
var vectTo = new Components/Vector

vectFrom.init(5, 4)
vectTo.init(8, 2)
vectTo.subtract(vectFrom)

>x = @vectTo.x@, y = @vectTo.y@     // imprime: x = 3, y = -2
```

---

## Diferencia clave: `import` vs. `new`

| | `import` | `new` |
|---|----------|-------|
| ¿Misma copia o nueva? | **La misma** cada vez (compartido) | Una **copia nueva** cada vez |
| ¿Cuándo se ejecuta el código? | **Cada frame** (como tu Mind Stone) | **Una sola vez** cuando la creas |
| Ideal para... | Scripts de lógica (combate, farmeo) | Objetos / componentes |

**Resumen:**

- Usa **`import`** para scripts que deben correr continuamente cada frame.
- Usa **`new`** para crear objetos que solo se inicializan una vez y luego guardan su estado.

---

## Aislamiento de variables

Las variables de un script importado están **aisladas**: sus nombres **no chocan** con los de otros scripts o del Mind Stone. Cada script tiene su propio "contenedor".

```
// Script A
var counter = 0
counter = counter + 1
import Scripts/B

// Script B
var counter = 0      // DIFERENTE de la de Script A
counter = counter + 10
```

Las dos variables `counter` son completamente independientes. No interfieren una con la otra.

---

## Acceder a variables y funciones de un script importado

Puedes **guardar la referencia** del script importado en una variable y luego acceder a sus funciones:

```
// PrintUtil.txt
func LowerLeft(x, y, color, message)
  y = y + screen.h
  >`@x@,@y@,@color@,@message@

func Center(color, message)
  >`center,@color@,@message@
```

```
// Script principal en el Mind Stone
var print = import PrintUtil

disable hud
print.LowerLeft(0, -1, #ffffff, "Vida: " + hp)
print.Center(#ff0000, "¡PELIGRO!")
```

Aquí `print` es una referencia al script, y accedes a sus funciones con punto (`.`).

---

## Subcarpetas

Puedes organizar tus scripts en subcarpetas:

```
import Games/Blackjack
import Cosmetics/PetFrog
import Cosmetics/Hats
import Combat/Strategies/Heavy
```

Los nombres de las carpetas se separan con `/`.

---

## `ToString()` — imprimir objetos directamente

Si tu script externo define una función `ToString()`, puedes imprimir el objeto directamente con `@...@`:

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
var v = new Components/Vector
v.init(3, 5)
>Vector = @v@         // imprime: Vector = (3, 5)
```

---

## Cargar scripts desde archivos personalizados en móvil

En móvil, los scripts no están en tu disco local, así que Stone Story carga del repositorio oficial por defecto. Pero **puedes cambiar eso** usando `sys.SetFileUrl()` para cargar tus propios scripts desde GitHub.

### Paso 1: Crear un repositorio en GitHub

1. Ve a **Github.com** en tu navegador y crea una cuenta (si no la tienes).
2. Haz clic en el botón **"New"** en la parte superior izquierda.
3. Completa el formulario y crea el repositorio (es básicamente un directorio de archivos en la nube).

### Paso 2: Crear archivos en el repositorio

1. Se te abrirá una pantalla nueva. Busca un botón que diga **"creating a new file"** (cerca al medio de la pantalla).
2. Ponle un nombre al archivo terminado en `.txt` (ej: `CombatScript.txt`).
3. Pega tu código en el archivo.
4. Haz clic en el botón verde **"commit changes"** (a la derecha de la pantalla). ¡Tu archivo está guardado!

### Paso 3: Obtener la URL del repositorio

Copia el link de tu repositorio que siga este formato:

```
https://raw.githubusercontent.com/"nombre-de-usuario"/"nombre-del-repositorio"/refs/heads/main
```

**Reemplaza** lo que está entre comillas:
- `"nombre-de-usuario"`: Tu usuario de GitHub (ej: `holaadios-23`)
- `"nombre-del-repositorio"`: El nombre del repo que creaste (ej: `MyStoneScripts`)

**Ejemplo real:**

```
https://raw.githubusercontent.com/Javascript-pixel/Snoopy/refs/heads/main
```

(En este ejemplo el usuario es `Javascript-pixel` y el repo es `Snoopy`.)

### Paso 4: Conectar tu mente (Mind Stone) al repositorio

Usa `sys.SetFileUrl()` **en el Mind Stone** para establecer la conexión:

```
sys.SetFileUrl(https://raw.githubusercontent.com/Javascript-pixel/Snoopy/refs/heads/main)
```

**Punto importante:** Solo se puede usar `sys.SetFileUrl()` en el Mind Stone, no en scripts importados.

### Paso 5: Importar tus archivos

Ahora puedes importar tus archivos como si estuvieran en el juego:

```
import UI/DreamOn
import UI/Timer
// DreamOn y Timer son archivos dentro de la carpeta UI
```

---

## Depuración: Cómo validar que los imports funcionen en móvil

Si los imports no funcionan, necesitas hacer esto (**"debuggear conexión"**):

### El proceso paso a paso:

1. **Comenta TODO tu código** (añade `//` al inicio de cada línea o rodea con `/* */`)
2. **Ve a una ubicación cualquiera del juego.** Espera unos segundos.
3. **Sal de esa ubicación.** Esto le da tiempo al juego para cargar los archivos.
4. **Descomenta el código** (elimina los comentarios).
5. **Vuelve a entrar a la ubicación.** Si todo está bien, ¡disfruta!

### ¿Por qué funciona esto?

- Comentar el código evita que errores de parsing rompan el sistema.
- Salir y entrar fuerza al juego a recargar los scripts desde GitHub.
- Descomentar después permite que el juego valide que todo se cargó correctamente.

### Ejemplo:

```
// Antes (con comentarios para debuggear):
/*
sys.SetFileUrl(https://raw.githubusercontent.com/miusuario/miscrips/refs/heads/main)
import UI/DreamOn
import UI/Timer
*/

// (Salir y entrar del juego)

// Después (comentarios quitados):
sys.SetFileUrl(https://raw.githubusercontent.com/miusuario/miscrips/refs/heads/main)
import UI/DreamOn
import UI/Timer
```

---

## Tabla de funciones de sistema (`sys`)

| Propiedad / Función | Qué hace |
|---------------------|----------|
| `sys.fileUrl` | Muestra la URL actual desde donde se importan scripts (solo lectura) |
| `sys.SetFileUrl(str)` | Cambia el origen. Puede ser `"local"`, `"remote"` (por defecto) o una URL personalizada. **Solo en el Mind Stone** |
| `sys.cacheRemoteFiles` | Si los archivos remotos se cachean entre runs (por defecto `true`) |
| `sys.isMobile` | ¿El jugador está en móvil? (`true` o `false`) |
| `sys.isPC` | ¿El jugador está en PC? (`true` o `false`) |
| `sys.os` | Sistema operativo del jugador (ej: `"Windows"`, `"Android"`) |

### Ejemplo: Script diferente según si es móvil o PC

```
?sys.isMobile
  sys.SetFileUrl(https://raw.githubusercontent.com/miusuario/miscrips/refs/heads/main)
  import MobileScripts/Combat
:
  import PCScripts/Combat
```

---

## Errores comunes y cómo arreglarlo

| Error | Probable causa | Solución |
|-------|----------------|----|  
| "Script no encontrado" | El archivo no existe o la ruta es incorrecta | Verifica que el archivo exista y que el nombre sea exacto (con mayúsculas/minúsculas correctas) |
| "Error al cargar desde URL" | La URL está mal formada o no es accesible | Verifica que la URL sea válida y empiece con `https://` |
| Los imports no funcionan en móvil | No definiste `sys.SetFileUrl()` | Asegúrate de poner `sys.SetFileUrl()` en el Mind Stone antes de importar |
| Los cambios no se reflejan | El archivo se ha cacheado | Cambia el nombre del archivo o borra la caché (depende del juego) |
| Variable/función no existe | Importaste pero no está disponible | Verifica que la función/variable esté definida en el archivo y que no sea local |

---

## Checklist: tus imports están listos cuando...

- [ ] Todos tus archivos terminen en `.txt`
- [ ] Estén en la carpeta `/Stonescript` (en PC) o en un repositorio de GitHub (en móvil)
- [ ] Los nombres sean exactos (respetando mayúsculas/minúsculas)
- [ ] La URL del repositorio sea válida (empiece con `https://raw.githubusercontent.com/...`)
- [ ] Hayas definido `sys.SetFileUrl()` si usas repositorio remoto
- [ ] Los scripts importados funcionen independientemente (pruébalos primero)

---

[← Funciones nativas](11-funciones-nativas.md) · [Índice](README.md) · [Siguiente: Recetario de ejemplos →](13-recetario-ejemplos.md)
