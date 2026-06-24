# 9. Imprimir texto y ASCII-art

[← Bucles y arrays](08-bucles-y-arrays.md) · [Índice](README.md) · [Siguiente: Interfaz (UI) →](10-interfaz-ui.md)

---

Mostrar información en pantalla es una de las cosas más útiles: contadores, avisos,
dibujos, animaciones... Todo empieza con el comando `>`.

## Impresión simple: `>`

```
>Hola Mundo!
```

Como el script corre cada frame, el texto se mantiene en pantalla mientras la línea se
siga ejecutando.

### Insertar variables con `@...@`

Rodea el nombre de la variable con `@` para mostrar su valor:

```
var hp_actual = hp
>Mi vida es @hp_actual@
```

Puedes insertar varias en el mismo print:

```
>Vida @hp@/@maxhp@
```

### Salto de línea con `\n`

```
>Línea 1\nLínea 2
```

> `\n` es "caro" para dibujos grandes. Para arte ASCII grande usa el bloque
> `ascii ... asciiend` (más abajo).

---

## Impresiones avanzadas (posición + color)

Las versiones avanzadas dejan elegir **dónde** y de **qué color** imprimir. Formato
general:

```
>X,Y,[#color,]texto
```

El color es **opcional**. La X y la Y son desplazamientos en la rejilla.

| Comando | Posición relativa a... |
|---------|------------------------|
| `>oX,Y,...` | la **posición del jugador** |
| `` >`X,Y,... `` | la **esquina superior izquierda** de la pantalla |
| `>cX,Y,...` | el **centro** de la pantalla |
| `>fX,Y,...` | la **cabeza del enemigo** objetivo |
| `>hX,Y,...` | la **capa del Big Head** (ideal para sombreros/accesorios) |

```
// "Vamos!" en rojo, 6 a la izquierda y 3 abajo del jugador
>o-6,3,#red,Vamos!

// Arriba a la izquierda de la pantalla, usando variables
var posX = 10
var posY = 5
>`@posX@,@posY@,Hola Mundo!

// En el centro (color opcional -> blanco por defecto)
>c0,0,Hola Mundo!
```

### Colores disponibles

- En hexadecimal: `#ff0000`, `#00aa00`, etc.
- Constantes: `#white`, `#cyan`, `#yellow`, `#green`, `#blue`, `#red`.
- Arcoíris: `#rainFF`, donde los 2 últimos caracteres son el brillo (`#rainE1`, etc.).

```
>`0,1,#magenta, ♦ @res.crystals@
```

---

## ASCII-art (dibujos)

Puedes incrustar dibujos hechos con caracteres y mostrarlos en pantalla.

Glifos con comportamiento especial:

- `#` → **espacio transparente** (no dibuja nada).
- `\n` → salto de línea (caro; mejor usa bloques `ascii`).

### Método 1 — directo en un print

```
>`1,0,#green,ascii
#.-.
(   )
#`-´
asciiend
```

### Método 2 — guardar el arte en una variable

```
var fishSprite
fishSprite = ascii
###(°_##
#_/_ o\#
#´  `'"#
asciiend

>`0,3,#red,@fishSprite@
```

### Dibujos sobre el enemigo o el jugador

```
// Mira (crosshair) roja sobre el enemigo
>f-2,0,#ff0000,ascii
##!
-#·#-
##¡
asciiend

// Sombrero amarillo sobre el jugador (mejor con Big Head)
>h-2,-3,#yellow,ascii
##_
#| |
_|_|_
asciiend
```

---

## Animaciones sencillas con `time`

Combinando `%` (capítulo 6) con prints puedes animar:

```
?time % 8 < 4
  > \O)
?time % 8 > 3
  > (O/
```

Esto alterna entre dos "fotogramas" varias veces por segundo.

Para arte que cambia, también puedes guardar varios fotogramas en un **array** (capítulo 8)
y elegir uno según el tiempo:

```
var frames = [ascii
───┼
  O/
 /|
 / \
asciiend
^,ascii
   ---.
  O    \
 /|\┼───
 / \
asciiend
^]

var idx = time / 8 % frames.Count()
>`10,5,@frames[idx]@
```

---

## Expresiones faciales (Big Head)

Con Big Head activo puedes cambiar la cara del jugador:

```
>( OwO
```

---

[← Bucles y arrays](08-bucles-y-arrays.md) · [Índice](README.md) · [Siguiente: Interfaz (UI) →](10-interfaz-ui.md)
