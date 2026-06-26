# 15. Agujeros lógicos

[← Trucos y errores comunes](14-trucos-y-errores.md) · [Índice](README.md)

---

## ¿Qué es un agujero lógico?

Un **agujero lógico** es un error en tu **lógica** (no en la sintaxis) donde tu script no hace exactamente lo que querías porque falta una condición o no la pensaste bien. El código es válido y corre sin errores, pero el resultado no es el esperado.

Es como darle instrucciones confusas a alguien: la persona entiende perfectamente lo que le dices, pero hace lo que no querías.

---

## Ejemplos comunes de agujeros lógicos

### 1. Olvidar un `else` (`:`)

Querías: *Si mi vida es baja, toma poción. Si no, ataca.*

```
?hp < 20
  activate potion
attack                  // ¡MAL! Esto ocurre SIEMPRE, incluso tomando poción
```

**Correcto:**

```
?hp < 20
  activate potion
:
  attack                // Solo atacas si NO tienes vida baja
```

Sin el `:`, ambas cosas se ejecutan en el mismo frame (tomas poción Y atacas).

---

### 2. Invertir una condición sin querer

```
?hp > 80              // si vida es MAYOR a 80
  activate potion     // toma poción (¡al revés!)
```

Querías tomar poción cuando tienes **poca** vida, no mucha.

**Correcto:**

```
?hp < 20
  activate potion
```

Siempre **verifica el operador** (`>`, `<`, `=`, etc.).

---

### 3. Confundir `&` (AND) con `|` (OR)

```
?hp < 10 & foe.distance > 15    // si vida es baja Y enemigo lejano
  activate potion
```

Esto significa: *toma poción solo si tienes poca vida* **Y** *el enemigo está lejos*. Si el enemigo está cerca pero tienes poca vida, ¡no tomas poción!

Querías tomar poción si tienes poca vida, **sin importar** dónde esté el enemigo:

**Correcto:**

```
?hp < 10
  activate potion
```

O si querías una condición más compleja:

```
?hp < 10 | status.poison     // si vida es baja O tienes veneno
  activate potion
```

---

### 4. Olvidar que el script corre 30 veces por segundo

```
var counter = 0
counter = counter + 1
?counter = 10
  activate ability
```

Querías: *espera 10 frames y activa*.

Pero `counter` **nunca llega a 10** porque se reinicia a 0 cada vez que das la vuelta (si no lo guardas en una variable de verdad, o si hay más lógica).

**Correcto:**

```
var counter = 0           // declarar fuera del script principal

counter = counter + 1
?counter = 10
  activate ability
  counter = 0             // reiniciar después de usar
```

O usa `time` del juego:

```
var lastAbilityTime = 0
?time - lastAbilityTime > 300   // han pasado más de 10 segundos (300 frames)
  activate ability
  lastAbilityTime = time
```

---

### 5. Condiciones anidadas mal pensadas

```
?foe.distance < 10        // si enemigo está cerca
  ?foe.hp > 100           // y enemigo tiene mucha vida
    attack enemy          // solo ataca si AMBAS son ciertas
:
  move up                 // si el enemigo está lejos, mueve
```

Pero hay un **agujero**: ¿Qué pasa si el enemigo está cerca pero tiene **poca** vida? **No hace nada.**

**Correcto:**

```
?foe.distance < 10        // si enemigo está cerca
  ?foe.hp > 100
    attack enemy
  :
    use strong_ability    // si enemigo está cerca pero débil, usa habilidad fuerte
:
  move up                 // si está lejos, mueve
```

---

### 6. Variables que no se reinician

```
var hasUsedUlimate = false

?key = spacebar & !hasUsedUlimate
  activate ultimate
  hasUsedUlimate = true   // marcar que ya lo usaste
```

Si **olvidas marcar** `hasUsedUlimate = true`, la habilidad se activa **30 veces por segundo** (el script corre cada frame). El personaje intenta usar ultimate constantemente.

---

### 7. Filtros mal aplicados

```
?foe.status = poison & foe.distance < 20
  attack foe
```

Significa: *ataca si el enemigo tiene veneno Y está a menos de 20 de distancia*.

Si el enemigo NO tiene veneno, no ataca aunque esté cerca. ¿Era lo que querías?

Probablemente quisiste:

```
?foe.distance < 20        // si enemigo está cerca
  ?foe.status = poison    // y tiene veneno
    attack foe
  :
    defense               // si está cerca pero sin veneno, defiéndete
```

O simplemente:

```
?foe.distance < 20
  attack foe              // ataca si está cerca, sin importar el estado
```

---

### 8. Usar `=` en lugar de `==` (o no estar seguro qué operador usar)

StoneScript usa **`=` para comparar** (no `==` como otros lenguajes). Si usas mal el operador:

```
?foe.hp = 50   // ¡ASIGNA! (mala idea)
```

Esto **asigna** 50 a foe.hp en lugar de comparar. Lo correcto es:

```
?foe.hp > 50   // compara si hp es mayor a 50
```

---

## ¿Cómo evitar agujeros lógicos?

1. **Dibuja un diagrama mental** de qué debe pasar en cada caso.
2. **Lee tu código en voz alta**: "Si esto, entonces aquello. Si no, esto otro."
3. **Pregúntate: ¿hay casos que no cubrí?** Por ejemplo, si solo cubres "enemigo cerca" y "enemigo lejos", ¿qué pasa en la distancia exacta del límite?
4. **Prueba casos extremos**: ¿Y si la vida es exactamente 10? ¿Y si el contador es 0? ¿Y si no hay enemigos?
5. **Usa comentarios** para explicar tu lógica mientras escribes. Si el comentario tiene sentido pero el código no, encontraste un agujero.

---

## Ejemplo real: Sistema de defensa mejorado

**Versión con agujeros:**

```
?foe
  ?foe.distance < 15
    attack
  :
    move
```

Agujero: ¿Y si no hay enemigo? El script no hace nada.

**Versión sin agujeros:**

```
?foe                      // si hay enemigo
  ?foe.distance < 15
    ?foe.hp < 50          // y tiene poca vida
      attack enemy
    :
      attack enemy        // ambos atacan (redundante, simplifica)
  :
    ?foe.distance < 50    // si está lejos pero visible
      move closer
    :
      move random         // si está muy lejos, muévete al azar
:
  idle                    // si no hay enemigo, descansa
```

---

## Resumen: cómo detectar agujeros

| Síntoma | Probable causa |
|---------|----------------|
| El personaje no hace nada en algunos casos | Falta un `:` (else) o una rama |
| Hace más cosas de las que debería cada frame | No reinicia variables o faltan condiciones |
| El resultado es al revés | Invertiste `<` y `>` |
| Solo reacciona en **algunos** casos aunque debería en más | Confundiste `&` (AND) con `\|` (OR) |
| Bugs aleatorios o inconsistentes | Variable local que se reinicia sin querer |
| El timing es incorrecto | No contaste bien los frames o `time` |

---

[← Trucos y errores comunes](14-trucos-y-errores.md) · [Índice](README.md)
