# 3. Estado del juego (lo que puedes preguntar)

[← Condiciones](02-condiciones.md) · [Índice](README.md) · [Siguiente: Comandos →](04-comandos.md)

---

El "estado del juego" es **toda la información que puedes preguntar** con `?`: dónde estás,
quién es el enemigo, cuánta vida tienes, etc. Es la materia prima de tus condiciones.

> No tienes que memorizar todo esto. Usa esta página como **diccionario**: busca lo que
> necesites cuando lo necesites.

Todas estas "preguntas" se usan igual:

```
?<lo que preguntas> <comparación> <valor>
  ...acción...
```

Por ejemplo: `?foe.distance > 20`, `?hp < 10`, `?loc = caves`.

---

## Ubicación (`loc`)

| Pregunta | Qué te dice |
|----------|-------------|
| `loc` | La ubicación actual (ej. `?loc = caves`) |
| `loc.id` | El identificador único de la ubicación |
| `loc.name` | El nombre de la ubicación (traducido) |
| `loc.stars` | La dificultad (en estrellas) |
| `loc.gp` | El poder de equipo (gear power) usado en esta run |
| `loc.begin` | Verdadero **solo en el primer frame** de la ubicación. Ideal para reiniciar variables |
| `loc.loop` | Verdadero en el primer frame tras un loop de Ouroboros |
| `loc.isQuest` | Verdadero si es una ubicación especial de Leyenda o misión |
| `loc.averageTime` | Tiempo promedio de finalización |
| `loc.bestTime` | Tu mejor tiempo (récord) |

```
?loc = caves
  ?loc.stars = 4
    loadout 1
  ?loc.stars = 5
    loadout 2
```

`loc.begin` es muy útil para poner valores iniciales una sola vez por run:

```
var i
?loc.begin       // solo el primer frame de la ubicación
  i = 0
  ?loc = caves
    i = -100
```

---

## Enemigo objetivo (`foe`)

| Pregunta | Qué te dice |
|----------|-------------|
| `foe` | El enemigo al que apuntas (ej. `?foe = boss`) |
| `foe.id` | Su ID/tipo único |
| `foe.name` | Su nombre (traducido) |
| `foe.damage` | Daño por ataque del enemigo |
| `foe.distance` | Distancia entre tú y el enemigo |
| `foe.z` | Posición Z del enemigo |
| `foe.count` | Nº de enemigos a 46 unidades de ti |
| `foe.GetCount(int)` | Nº de enemigos dentro de X unidades |
| `foe.hp` / `foe.maxhp` | Vida actual / máxima del enemigo |
| `foe.armor` / `foe.maxarmor` | Armadura actual / máxima |
| `foe.state` | Número del estado actual del enemigo (0 = dormido, etc.) |
| `foe.time` | Frames transcurridos en el estado actual |
| `foe.level` | Nivel del enemigo |
| `foe.buffs.count` | Nº de efectos positivos en el enemigo |
| `foe.buffs.GetCount(str)` | Nº de un buff concreto |
| `foe.buffs.GetTime(str)` | Duración de un buff concreto |
| `foe.debuffs.count` | Nº de efectos negativos en el enemigo |
| `foe.debuffs.GetCount(str)` | Nº de un debuff concreto |
| `foe.debuffs.GetTime(str)` | Duración de un debuff concreto |

```
?foe.distance > 20
  equipL triskel
  equipR mask

?foe.count > 10
  equip shiny bardiche
```

---

## Tu personaje (vida, armadura, posición)

| Pregunta | Qué te dice |
|----------|-------------|
| `hp` / `maxhp` | Tu vida actual / máxima |
| `armor` | Tu armadura, redondeada hacia abajo |
| `armor.f` | La parte decimal de tu armadura |
| `maxarmor` | Tu armadura máxima |
| `buffs.count` / `buffs.string` | Tus efectos positivos (cantidad / resumen) |
| `buffs.GetCount(str)` / `buffs.GetTime(str)` | Un buff concreto: cantidad / duración |
| `buffs.oldest` | ID del buff más antiguo |
| `debuffs.count` / `debuffs.string` | Tus efectos negativos |
| `debuffs.GetCount(str)` / `debuffs.GetTime(str)` | Un debuff concreto |
| `debuffs.oldest` | ID del debuff más antiguo |
| `pos.x` / `pos.y` / `pos.z` | Tu posición en el mundo |
| `face` | Tu expresión facial actual |
| `bighead` | Verdadero si tienes Big Head (Moondial) |

```
?hp < 10
  activate potion

?hp = maxhp
  loadout 2     // vida llena -> usa un equipo más ofensivo
```

---

## Movimiento e IA

| Pregunta | Qué te dice |
|----------|-------------|
| `ai.enabled` | La IA está encendida (falso en cinemáticas) |
| `ai.paused` | La IA está suspendida temporalmente |
| `ai.idle` | El jugador está quieto esperando algo |
| `ai.walking` | El jugador se está moviendo |
| `player.direction` | Dirección: `1` derecha, `-1` izquierda |
| `player.framesPerMove` | Frames que tarda en avanzar una posición |
| `player.moveX` / `moveZ` | Velocidad de movimiento |
| `player.name` | El nombre del jugador |

```
?player.direction = 1
  >`0,0,Caminando a la derecha
:
  >`0,0,Caminando a la izquierda
```

---

## Objetos equipados (`item`)

| Pregunta | Qué te dice |
|----------|-------------|
| `item.left` / `item.right` | El objeto en la mano izquierda / derecha |
| `item.left.id` / `item.right.id` | El ID del objeto equipado |
| `item.left.gp` / `item.right.gp` | El gear power del objeto |
| `item.left.state` / `item.left.time` | Estado y frames del arma izquierda |
| `item.right.state` / `item.right.time` | Estado y frames del arma derecha |
| `item.potion` | La poción preparada (incluye "auto" si hay auto-relleno) |

```
?item.right = quarterstaff
  activate R

?item.potion ! empty & item.potion = auto
  activate potion
```

---

## Recursos, cosechables y pickups

| Pregunta | Qué te dice |
|----------|-------------|
| `res.stone`, `res.wood`, `res.tar`, `res.ki`, `res.bronze`, `res.crystals` | Tus recursos |
| `harvest` | El próximo objeto cosechable (árbol, roca...) |
| `harvest.distance` | Distancia al cosechable más cercano |
| `harvest.z` | Posición Z del cosechable |
| `pickup` | El objeto recolectable al que apuntas |
| `pickup.distance` | Distancia a ese recolectable |
| `pickup.z` | Posición Z del recolectable |

```
?harvest = Boulder
  equip shovel

?loc = Rocky & harvest.distance < 7
  equip shovel
```

---

## Invocaciones (`summon`)

| Pregunta | Qué te dice |
|----------|-------------|
| `summon.count` | Nº de aliados invocados |
| `summon.GetId(index=0)` | ID de la invocación en ese índice |
| `summon.GetName(index=0)` | Nombre de la invocación |
| `summon.GetState(index=0)` | Estado de la invocación |
| `summon.GetTime(index=0)` | Frames en el estado actual |
| `summon.GetVar(varName, index=0)` | Variable personalizada de la invocación |

```
?summon.count = 0 &
^ item.CanActivate()
  equipL talisman
  activate L
```

---

## Tiempo, pantalla y aleatoriedad

| Pregunta | Qué te dice |
|----------|-------------|
| `time` | Frame actual de la ubicación |
| `totaltime` | Frame actual acumulado (cuenta sub-zonas de jefe) |
| `time.year`, `time.month`, `time.day`, `time.hour`, `time.minute`, `time.second` | Hora local del sistema |
| `utc.year` ... `utc.second` | Hora UTC |
| `time.msbn` | Tiempo Unix en milisegundos (BigNumber) |
| `screen.i` / `screen.x` | Posición de la pantalla en el juego |
| `screen.w` / `screen.h` | Ancho / alto de la rejilla ASCII |
| `input.x` / `input.y` | Posición del ratón/toque en la rejilla |
| `key` | Estado de la entrada personalizada (ver [cap. 8 y recetario]) |
| `rng` | Entero aleatorio entre 0 y 9999 |
| `rngf` | Decimal aleatorio entre 0 y 1 |
| `totalgp` | Poder total de tu inventario |
| `encounter.isElite` | ¿El encuentro actual es élite? |
| `encounter.eliteMod` | El modificador especial del encuentro |

```
?time % 300 = 0
  >Verás este mensaje cada 10 segundos

?rng < 5000
  >Cara!
:
  >Cruz!
```

> **Truco con `time` y `%`:** como el script corre 30 veces por segundo,
> `?time % 30 = 0` es verdadero una vez por segundo, `?time % 300 = 0` cada 10 segundos,
> etc. Sirve para hacer cosas "de vez en cuando".

---

## Cómo leer esta tabla en tus condiciones

Cualquier valor de arriba se combina con una comparación (capítulo 5) y una acción
(capítulo 4):

```
?foe.hp < foe.maxhp / 2 & foe = boss   // jefe con menos de la mitad de vida
  equip crossbow
  activate R
```

---

[← Condiciones](02-condiciones.md) · [Índice](README.md) · [Siguiente: Comandos →](04-comandos.md)
