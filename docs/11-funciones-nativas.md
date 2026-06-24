# 11. Funciones nativas (herramientas que ya vienen incluidas)

[← Interfaz (UI)](10-interfaz-ui.md) · [Índice](README.md) · [Siguiente: Importar scripts →](12-importar-scripts.md)

---

Además de las funciones que tú creas, StoneScript trae muchas **funciones nativas** ya
listas, agrupadas por temas (espacios de nombres) como `math`, `string`, `storage`, etc.

> Úsalo como referencia. Aquí están las más útiles con ejemplos cortos.

---

## `math` — matemáticas

Trabaja con enteros y decimales. Recuerda: un número con punto (`2.0`) es decimal; sin
punto (`2`) es entero (ver [capítulo 6](06-variables-y-matematicas.md)).

| Función | Qué hace |
|---------|----------|
| `math.Abs(n)` | Valor absoluto |
| `math.Min(a, b)` / `math.Max(a, b)` | El menor / el mayor |
| `math.Clamp(n, min, max)` | Limita `n` al rango [min, max] |
| `math.Floor(n)` / `math.Ceil(n)` | Redondea abajo / arriba (devuelve float) |
| `math.FloorToInt(n)` / `math.CeilToInt(n)` | Igual, pero devuelve entero |
| `math.Round(n)` / `math.RoundToInt(n)` | Redondea al más cercano |
| `math.Sign(n)` | `-1` si es negativo, `1` si no |
| `math.Sqrt(n)` | Raíz cuadrada |
| `math.Pow(n, p)` | `n` elevado a `p` |
| `math.Log(n, base)` | Logaritmo |
| `math.Exp(n)` | `e` elevado a `n` |
| `math.Lerp(a, b, t)` | Interpolación lineal (de `a` a `b` al % `t`) |
| `math.Sin`, `Cos`, `Tan`, `Asin`, `Acos`, `Atan`, `Atan2(y,x)` | Trigonometría (radianes) |
| `math.ToDeg(n)` / `math.ToRad(n)` | Convierte radianes ↔ grados |
| `math.pi` / `math.e` | Constantes π y e |
| `math.BigNumber(...)` | Crea un número enorme (ver abajo) |

```
var n = math.Clamp(50, 0, 10)   // n = 10
var r = math.Sqrt(9)            // r = 3
```

### `BigNumber` — números gigantes

Para números que no caben en 32 bits. Se opera con métodos:

| Método | Equivale a |
|--------|-----------|
| `.Add(n)` `.Sub(n)` `.Mul(n)` `.Div(n)` | `+` `-` `*` `/` |
| `.Eq(n)` `.Gt(n)` `.Ge(n)` `.Lt(n)` `.Le(n)` | `=` `>` `>=` `<` `<=` |
| `.ToInt()` `.ToFloat()` | Convertir a entero / decimal |
| `.ToString()` | Texto exacto (para guardar) |
| `.ToUI()` | Texto corto para interfaces (ej. "123.5Qa") |

```
var bn = math.BigNumber("500")
bn.Add(500).Mul(1000).Mul(1000).Mul(1000)
>@bn@                 // 1000000000000
```

---

## `string` — texto

| Función | Qué hace |
|---------|----------|
| `string.Size(s)` | Longitud (número de glifos) |
| `string.Equals(a, b)` | Comparación **exacta** (true/false) |
| `string.Capitalize(s)` | Primera letra en mayúscula |
| `string.ToUpper(s)` / `string.ToLower(s)` | Todo mayúsculas / minúsculas |
| `string.Sub(s, inicio)` / `string.Sub(s, inicio, largo)` | Recorta una parte |
| `string.IndexOf(s, criterio[, desde])` | Posición de un texto (o -1) |
| `string.Split(s[, sep...][, bool])` | Divide en un array |
| `string.Join(sep, array[, i][, n])` | Une un array en un texto |
| `string.Break(s, ancho)` | Parte el texto en líneas de ancho máximo |
| `string.Format(plantilla, ...)` | Sustituye `{0}`, `{1}`... por valores |

```
?string.Equals(face, "^^")
  >Estoy feliz

var str = "Me llamo {0} y tengo {1} de poder"
var result = string.Format(str, player.name, totalgp)
>@result@
```

> Recuerda: en condiciones, `=` con texto significa "contiene"; para igualdad exacta usa
> `string.Equals` (ver [capítulo 5](05-comparaciones-y-filtros.md)).

---

## `storage` — guardar datos permanentes

A diferencia de las variables (que se pierden al cerrar el juego), `storage` **persiste**
entre sesiones. Perfecto para récords, monedas, estadísticas...

| Función | Qué hace |
|---------|----------|
| `storage.Set(clave, valor)` | Guarda un valor |
| `storage.Get(clave)` / `storage.Get(clave, defecto)` | Lee (con valor por defecto opcional) |
| `storage.Has(clave)` | ¿Existe esa clave? |
| `storage.Delete(clave)` | Borra una clave |
| `storage.Incr(clave)` / `storage.Incr(clave, n)` | Suma 1 (o `n`) y devuelve el nuevo valor |
| `storage.Keys()` | Array con todas las claves |

```
// Guardar un récord
var score = 1000
storage.Set("highscore", score)

// Leerlo con valor por defecto 0
var best = storage.Get("highscore", 0)
>Récord = @best@

// Contar partidas jugadas
?gameOver
  storage.Incr("stat_TimesPlayed")
```

---

## `color` — colores

| Función | Qué hace |
|---------|----------|
| `color.FromRGB(r, g, b)` | Crea color desde 3 números (0–255) |
| `color.ToRGB(s)` | Devuelve un array de 3 enteros |
| `color.Lerp(c1, c2, t)` | Interpola de un color a otro |
| `color.Random()` | Color al azar |

```
var c = color.Lerp("#ff4400", "#8888ff", 0.5)
>`0,1,@c@,██████
```

---

## `draw` — dibujar directo en pantalla

| Función | Qué hace |
|---------|----------|
| `draw.Bg(x, y, color[, w, h])` | Pinta el fondo de una posición/región |
| `draw.Box(x, y, w, h, color, style)` | Dibuja un rectángulo |
| `draw.GetSymbol(x, y)` | Lee el glifo en una posición |
| `draw.Clear()` | Limpia toda la pantalla |
| `draw.Player([x, y])` | Dibuja al jugador en ese punto |

```
draw.Bg(5, 4, #cyan, 10, 6)
draw.Box(10, 5, 30, 15, #333333, 1)
```

---

## `music` y `ambient` — audio

| Función | Qué hace |
|---------|----------|
| `music.Play(id)` | Pone una música (solo una a la vez) |
| `music.Stop()` | Para la música |
| `music` | Devuelve el ID de la música actual |
| `ambient.Add(id)` | Añade una capa de sonido ambiente (máx. 4) |
| `ambient.Stop()` | Quita todas las capas |
| `ambient` | Lista de capas activas |

```
?loc.begin | loc.loop
  music.Play(temple_0)
  ambient.Add(ambient_crypt)
```

(Los IDs de sonidos, músicas y ambientes están en los apéndices del manual original.)

---

## `item` — consultas de objetos

| Función | Qué hace |
|---------|----------|
| `item.CanActivate()` | ¿Se puede activar alguna habilidad ahora? |
| `item.CanActivate(str)` | ¿Se puede activar ese objeto concreto? |
| `item.GetCooldown(str)` | Frames de enfriamiento restantes (ver Apéndice A de IDs) |
| `item.GetCount(str)` | Cuántas copias de un objeto tienes |
| `item.GetLoadoutL(n)` / `item.GetLoadoutR(n)` | Objeto en un loadout |
| `item.GetTreasureCount()` / `item.GetTreasureLimit()` | Cofres actuales / máximos |

```
?foe = boss & item.GetCooldown("bardiche") <= 0
  equip bardiche
  activate R
```

---

## `screen` — coordenadas de pantalla

| Función | Qué hace |
|---------|----------|
| `screen.FromWorldX(n)` / `FromWorldZ(n)` | Mundo → pantalla |
| `screen.ToWorldX(n)` / `ToWorldZ(n)` | Pantalla → mundo |
| `screen.Next()` / `Previous()` / `ResetOffset()` | Mover la cámara en zonas multi-pantalla |

---

## `key` — personalizar controles

| Función | Qué hace |
|---------|----------|
| `key.Bind(act, k1[, k2])` | Asigna teclas a una acción |
| `key.GetActKey(act)` / `GetActKey2(act)` | Tecla(s) de una acción |
| `key.GetKeyAct(k)` | Acción ligada a una tecla |
| `key.ResetBinds()` | Restaura los controles por defecto |

```
?loc.begin
  key.Bind("Potion", "P")   // ahora P bebe poción en vez de pausar
```

---

## `time` (formato) y otros

| Función | Qué hace |
|---------|----------|
| `time.FormatCasual(frames[, bool])` | Texto tipo "1m 23s" |
| `time.FormatDigital(frames[, bool])` | Texto tipo "1:23" |
| `int.Parse(s)` | Convierte texto a entero |
| `Type(v)` | Tipo de una variable |
| `loc.Leave()` / `loc.Pause()` | Abandona / pausa la run |
| `player.ShowScaredFace(n)` | Cara asustada (con Big Head) |

```
>`0,0,Tiempo = @time.FormatDigital(totaltime, true)@
```

---

## Localización de idioma (`te`)

| Función | Qué hace |
|---------|----------|
| `te.language` | Código del idioma elegido (`EN`, `ES-LA`, `ES-EU`, ...) |
| `te.xt(s)` | Traduce un texto inglés al idioma del jugador |
| `te.ToEnglish(s)` | Traduce al inglés |
| `te.GetTID(s)` | Devuelve el identificador de texto |

---

[← Interfaz (UI)](10-interfaz-ui.md) · [Índice](README.md) · [Siguiente: Importar scripts →](12-importar-scripts.md)
