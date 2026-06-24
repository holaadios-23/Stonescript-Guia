# 4. Comandos (las órdenes que das)

[← Estado del juego](03-estado-del-juego.md) · [Índice](README.md) · [Siguiente: Comparaciones y filtros →](05-comparaciones-y-filtros.md)

---

Si las condiciones (`?`) son las **preguntas**, los comandos son las **respuestas**: le
dicen al juego que **haga algo**. Casi siempre van con sangría debajo de una condición.

```
?hp < 10
  activate potion   // "activate potion" es un comando
```

---

## Equipar objetos

### `equip (texto)`
Equipa un objeto. Para armas a **dos manos** se usa siempre esta forma. Puedes dar hasta
7 criterios de búsqueda.

```
equip shovel
equip vigor crossbow *8 +5      // ballesta de vigor, 8 estrellas, +5 de encantamiento
```

### `equipL (texto)` y `equipR (texto)`
Equipan en la mano **izquierda** (`equipL`) o **derecha** (`equipR`) el objeto que mejor
encaje con los criterios.

```
equipL poison wand
equipR vigor shield
```

> Recordando tu ejemplo:
> ```
> ?foe.distance > 20
>   equipL triskel
>   equipR mask
> ```

### `equip @variable@`
Equipa según un texto guardado en una variable. Soporta criterios restando con `-`.

```
var weaponName = "poison sword *10 -big"
equipR @weaponName@      // espada de veneno 10★, pero NO la grande
```

### `loadout (n)`
Equipa un **loadout** (conjunto guardado) por su número.

```
?loc = caves
  loadout 1
?loc = deadwood
  loadout 2
```

> Si llamas a varios `equip` en el mismo frame, **gana el último** que se ejecute.

---

## Activar habilidades

### `activate (habilidad)`
Activa la habilidad de un objeto o poción. Valores posibles: `potion`, `P`, `left`, `L`,
`right`, `R`, o un ID de habilidad.

```
activate R         // activa el arma de la mano derecha (ej. el hacha)
activate potion    // bebe poción
```

### `brew (ingredientes)`
Rellena la poción con una combinación. **Solo funciona en el frame 0** (inicio de la run).
Ingredientes: `stone`, `wood`, `tar`, `bronze`, separados por `+`.

```
?loc.begin
  brew bronze + tar
```

---

## Mostrar texto en pantalla

### `> (texto)`
Imprime texto en la parte superior de la pantalla.

```
>Hola Mundo!
```

### `> @variable@`
Imprime texto insertando el valor de una o más variables (rodea el nombre con `@`).

```
var foeInfo
foeInfo = foe
>FOE = @foeInfo@
```

### Impresiones avanzadas (`>o`, `>` con backtick, `>c`, `>f`, `>h`)
Permiten elegir posición y color. Se explican en detalle en el
[capítulo 9](09-impresion-y-ascii.md). Vista rápida:

| Comando | Posición relativa a... |
|---------|------------------------|
| `>oX,Y,...` | la posición del jugador |
| `` >`X,Y,... `` | la esquina superior izquierda de la pantalla |
| `>cX,Y,...` | el centro de la pantalla |
| `>fX,Y,...` | la cabeza del enemigo objetivo |
| `>hX,Y,...` | la capa del Big Head (sombreros, accesorios) |

```
>o-6,3,#red,Vamos!        // texto rojo, a la izquierda y abajo del jugador
>`0,1,Vida = @hp@         // arriba a la izquierda de la pantalla
```

---

## Declarar variables y funciones

Solo una vista rápida; tienen su propio capítulo.

### `var (variable)`
Declara una variable (ver [capítulo 6](06-variables-y-matematicas.md)).

```
var message = Hola Mundo!
>@message@
```

### `func (función)`
Declara una función reutilizable (ver [capítulo 7](07-funciones.md)).

```
func Print(message)
  >@message@

Print(Hola Mundo!)
```

### `for v = a..b`
Crea un bucle (ver [capítulo 8](08-bucles-y-arrays.md)).

```
var a = 0
for i = 1..5
  a = a + i
>a = @a@
```

---

## Importar otros scripts

### `import (script)` y `new (script)`
Cargan un archivo externo (ver [capítulo 12](12-importar-scripts.md)).

```
import Fishing
var v = new Components/Vector
```

---

## Sonido y música

### `play (sonido) (tono)`
Reproduce un efecto de sonido. El tono es opcional (100 por defecto; más alto = más agudo).

```
?key = primary
  play buy
?key = up
  play buy 200
```

(La lista completa de sonidos está en el apéndice del manual; música y ambiente se
controlan con `music.Play()` y `ambient.Add()`, ver
[capítulo 11](11-funciones-nativas.md).)

---

## Encender / apagar elementos del juego (`disable` / `enable`)

Estos comandos ocultan o muestran partes de la interfaz y mecánicas. Útiles para crear
minijuegos o interfaces limpias.

| Comando | Efecto |
|---------|--------|
| `disable abilities` / `enable abilities` | Bloquea / restaura el uso de poción y habilidades |
| `disable banner` / `enable banner` | Oculta / muestra el cartel con el nombre de la zona |
| `disable hud (opts)` / `enable hud (opts)` | Oculta / muestra la interfaz de juego |
| `disable loadout input` / `enable loadout input` | Bloquea / permite guardar y recuperar loadouts con teclas |
| `disable loadout print` / `enable loadout print` | Oculta / muestra los mensajes de loadout |
| `disable npcDialog` / `enable npcDialog` | Oculta / muestra los diálogos de NPC |
| `disable pause` / `enable pause` | Oculta / muestra el botón de pausa (la tecla P sigue funcionando) |
| `disable player` / `enable player` | Oculta / muestra al jugador (solo visual) |

Las opciones de `disable hud`: `p` (vida del jugador), `f` (vida del enemigo),
`a` (botones de habilidad), `r` (recursos), `b` (cartel), `u` (cinturón de utilidad).

```
disable hud          // oculta TODA la interfaz
disable hud ru       // oculta solo recursos (r) y cinturón (u)
```

---

[← Estado del juego](03-estado-del-juego.md) · [Índice](README.md) · [Siguiente: Comparaciones y filtros →](05-comparaciones-y-filtros.md)
