# 5. Comparaciones y filtros de búsqueda

[← Comandos](04-comandos.md) · [Índice](README.md) · [Siguiente: Variables y matemáticas →](06-variables-y-matematicas.md)

---

Las **comparaciones** son el "pegamento" entre una pregunta y un valor. Te dejan decir
cosas como *"si la vida es menor que 10"* o *"si el enemigo es un jefe Y está cerca"*.

## Operadores de comparación

| Operador | Significado | Ejemplo |
|----------|-------------|---------|
| `=` | Igual a (o "el texto contiene") | `?hp = maxhp` |
| `!` | Distinto de (o "el texto no contiene") | `?foe ! poison` |
| `>` | Mayor que | `?foe.count > 10` |
| `<` | Menor que | `?hp < 6` |
| `>=` | Mayor o igual que | `?loc.stars >= 6` |
| `<=` | Menor o igual que | `?hp <= 6` |

```
?hp = maxhp        // vida llena
  loadout 2

?foe ! poison      // el enemigo NO es de tipo veneno
  equipL sword

?hp < 6            // poca vida
  activate potion
```

> Ojo: con textos, `=` significa **"contiene"** y `!` significa **"no contiene"**. Para
> comparar textos de forma **exacta** usa `string.Equals(a, b)`
> (ver [capítulo 11](11-funciones-nativas.md)).

`>=` y `<=` son combinaciones. Estas dos condiciones son equivalentes:

```
?loc.stars >= 6
  equipR vigor shield
```
```
?loc.stars > 6 | loc.stars = 6
  equipR vigor shield
```

---

## Operadores lógicos: `&` (Y), `|` (O), `!` (NO)

### `&` = "Y" (las dos cosas a la vez)

```
?loc = caves & foe = boss     // estoy en cuevas Y el enemigo es el jefe
  equip crossbow
```

### `|` = "O" (al menos una)

```
?foe = slow | foe.count > 3   // el enemigo es lento O hay más de 3 enemigos
  activate potion
```

### `!` = "NO" (negación)

Invierte el valor de una condición.

```
? !ai.enabled                 // si la IA NO está activa
  >La IA no está habilitada.
```

### Regla de precedencia: `&` antes que `|`

Si mezclas `&` y `|` en la misma línea, **primero se evalúan todos los `&`** y luego los
`|`. En otras palabras, `a & b | c & d` se lee como `(a & b) | (c & d)`.

```
?loc = caves & foe = boss | foe.count > 5
// se entiende como: (caves Y boss) O (más de 5 enemigos)
```

Si quieres otro orden, **divídelo en condiciones anidadas** (capítulo 2) para que quede
claro.

---

## `&` vs. condiciones anidadas

Como viste en el [capítulo 2](02-condiciones.md), `?a & b` equivale a anidar `?a` y `?b`.
Usa `&` para líneas simples y anida cuando dentro haya varias acciones o un `:` (else).

---

## Filtros de búsqueda (para `foe`, `loc` e `item`)

Cuando preguntas por un enemigo, ubicación u objeto, puedes usar **filtros** que describen
características. Por ejemplo, `?foe = boss` o `equip vigor crossbow *8`.

### Tipos y propiedades de enemigos

Estos se usan como palabras clave de búsqueda:

```
poison      vigor       aether      fire        air
ice         arachnid    serpent     insect      machine
humanoid    elemental   boss        phase1      phase2
phase3      spawner     flying      slow        ranged
explode     swarm       unpushable  undamageable
magic_resist            magic_vulnerability
immune_to_stun          immune_to_ranged
immune_to_debuff_damage immune_to_physical
```

Ejemplo, combinando filtros con `|`:

```
?foe = insect | foe = poison
  loadout 3
```

### Estrellas y encantamiento

| Filtro | Significado |
|--------|-------------|
| `*[número]` | Nivel de estrellas (de una ubicación o un objeto) |
| `+[número]` | Bonus de encantamiento (solo objetos) |

```
equip vigor crossbow *8 +5    // 8 estrellas, +5 de encantamiento
```

### Criterios que restan (`-`)

En los comandos de equipar puedes **excluir** características con `-`:

```
var weaponName = "poison sword *10 -big -socket"
equipR @weaponName@   // espada de veneno 10★, pero NO la grande ni con engaste
```

---

## Ejemplos combinados

```
// Jefe a media vida y cerca: cambia a la ballesta y dispara
?foe = boss & foe.hp < foe.maxhp / 2 & foe.distance < 15
  equip crossbow
  activate R

// Muchos enemigos voladores o un enjambre: usa área
?foe = flying & foe.count > 4 | foe = swarm
  loadout 3
```

---

[← Comandos](04-comandos.md) · [Índice](README.md) · [Siguiente: Variables y matemáticas →](06-variables-y-matematicas.md)
