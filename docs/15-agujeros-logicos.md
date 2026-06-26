# 15. Agujeros lógicos

[← Trucos y errores comunes](14-trucos-y-errores.md) · [Índice](README.md)

---

## ¿Qué es un agujero lógico?

Un **agujero lógico** es un error en tu **lógica** (no en la sintaxis) donde tu script no hace exactamente lo que querías porque falta una condición o no la pensaste bien. El código es válido y corre sin errores, pero el resultado no es el esperado.

Es como darle instrucciones confusas a alguien: la persona entiende perfectamente lo que le dices, pero hace lo que no querías.

---

## 🔑 La técnica clave: SIEMPRE cierra con `:` (sino)

**Regla de oro:** Aunque sea un solo `?`, siempre cierra la cadena con `:` (sino) **que imprima un mensaje de depuración**. Si nunca ves ese mensaje, tu lógica está correcta. Si lo ves, **encontraste el agujero**.

```
?condición
  hacer algo
:
  >DEPURACIÓN: condición falsa - LÍNEA 3
```

---

## El agujero lógico más común: cadenas incompletas

**El peor caso:**

```
?foe & foe.distance < 15
:?foe & foe.distance < 20
  ?foe.hp < 50
    activate potion
    // ¡AGUJERO! Si foe.hp >= 50, ¿qué pasa?
:?foe & foe.distance < 50
:
  acción por defecto
```

Si `foe.distance` está entre 15 y 20, pero `foe.hp >= 50`, el script **no entra al `:` final**. Tu personaje hace nada (agujero).

**Correcto:**

```
?foe & foe.distance < 15
:?foe & foe.distance < 20
  ?foe.hp < 50
    activate potion
  :
    >DEPURACIÓN: enemigo a 15-20 pero sano - LÍNEA 5
:?foe & foe.distance < 50
  equipR bash
:
  >DEPURACIÓN: sin enemigo o muy lejos - LÍNEA 9
  loadout 1
```

Ahora si ves "enemigo a 15-20 pero sano", sabes exactamente dónde está el problema.

---

## Técnica de depuración: paso a paso

### 1. Identifica todas tus cadenas `?` `:?` `:`

Marca dónde empiezan y terminan:

```
?foe & foe.distance < 10        // ← cadena 1 empieza
  ?foe.hp < 50                  // ← cadena 2 empieza (anidada)
    activate potion
  :
    defend
:?foe & foe.distance < 20       // ← cadena 1 continúa
  equipR bash
:                               // ← cadena 1 termina
  >DEPURACIÓN: sin enemigo o muy lejos - LÍNEA 9
```

### 2. Asegúrate de que CADA cadena termine con `:`

```
?...
:?...
:?...
:                    // ← obligatorio
  >DEPURACIÓN: mensaje
```

Incluso cadenas de una sola `?`:

```
?condición
  hacer algo
:                    // ← SÍ, aunque sea solo un if
  >DEPURACIÓN: mensaje
```

### 3. Añade depuración a cada `:` final

```
?hp < 20
  activate potion
:
  >DEPURACIÓN: vida >= 20 - LÍNEA 4
```

### 4. Prueba en el juego

Si ves mensajes de depuración en lugares inesperados, encontraste el agujero.

### 5. Cierra el agujero

Ajusta las condiciones o añade la lógica que falta.

---

## El segundo problema: Anidación profunda

Muchas condiciones anidadas = muchos agujeros potenciales.

**Malo (4 niveles de anidación):**

```
?foe
  ?foe.distance < 20
    ?foe.hp < 50
      ?foe.buffs.count = 0
        activate potion
        // ¿Qué si foe.buffs.count > 0?
```

Hay 4 puntos donde podrías olvidar un `:`.

**Mejor (condiciones compuestas):**

```
?foe & foe.distance < 20 & foe.hp < 50 & foe.buffs.count = 0
  activate potion
:
  >DEPURACIÓN: condición de poción no se cumplió - LÍNEA 3
```

Solo **1** punto de ramificación. Mucho más seguro.

**Mixto (lo ideal):**

```
?foe & foe.distance < 20
  ?foe.hp < 50 & foe.buffs.count = 0
    activate potion
  :
    >DEPURACIÓN: cerca pero sano o con buffs - LÍNEA 5
:
  >DEPURACIÓN: sin enemigo o muy lejos - LÍNEA 7
```

2 niveles máximo, depuración en todos los `:`.

---

## Ejemplo completo: Sistema de habilidades con cooldown (REAL)

**Versión con depuración (recomendada):**

```
var igc = item.GetCooldown

?igc("bash") = 0
  equipR bash
  activate R
  >DEPURACIÓN: bash activado - LÍNEA 4
:?igc("dash") = 0
  equipR dash
  activate R
  >DEPURACIÓN: dash activado - LÍNEA 8
:?igc("potion") = 0
  activate potion
  >DEPURACIÓN: potion activado - LÍNEA 11
:
  >DEPURACIÓN: todas las habilidades en cooldown - LÍNEA 13
  equipR shield
```

**¿Qué pasa?**

- Ves "bash activado" → bash está listo (cooldown = 0)
- Ves "dash activado" → bash está en cooldown, pero dash está listo
- Ves "potion activado" → bash y dash en cooldown, potion listo
- Ves "todas las habilidades en cooldown" → Nada disponible
- **No ves nada** → Lógica funcionando correctamente

---

## Ejemplos de agujeros comunes (REALES)

### 1. Olvidar el `:` final

```
?loc = caves
  loadout 1
?loc = deadwood        // ¡MAL! Esto corre independiente
  loadout 2
```

**Correcto:**

```
?loc = caves
  loadout 1
:?loc = deadwood
  loadout 2
:
  >DEPURACIÓN: otra ubicación - LÍNEA 6
  loadout 3
```

---

### 2. Invertir operadores

```
?hp > 80              // vida ALTA
  activate potion     // pero querías vida BAJA
```

**Correcto:**

```
?hp < 20
  activate potion
```

---

### 3. Confundir `&` (Y) con `|` (O)

```
?hp < 10 & foe.distance > 15    // solo si AMBAS son true
  activate potion
```

Si enemigo está cerca pero tienes poca vida, ¡no funciona!

**Correcto:**

```
?hp < 10              // solo si vida baja
  activate potion
```

O si querías verificar ambas (aunque sea raro):

```
?hp < 10 | foe.distance > 50    // si vida baja O enemigo muy lejos
  activate potion
```

---

### 4. Confundir `!` (NO)

```
?foe.armor = 0        // armor exactamente 0
```

**Si querías "sin armadura":**

```
?foe.armor ! 0        // armor NO es 0
```

O:

```
?!foe               // NO hay enemigo
  loadout 1
:
  loadout 2
```

---

### 5. Olvidar que el cooldown devuelve números

```
var igc = item.GetCooldown

?igc("bash")          // ¡AGUJERO! Esto siempre es true si > 0
  equipR bash
```

`item.GetCooldown()` devuelve un **número** (frames restantes). Si es > 0, el cooldown aún activo. Si es 0, está listo.

**Correcto:**

```
var igc = item.GetCooldown

?igc("bash") = 0      // = 0 significa que está LISTO
  equipR bash
  activate R
:
  >DEPURACIÓN: bash en cooldown - LÍNEA 7
```

---

### 6. Cadenas mal cerradas con cooldowns

**El peor caso:**

```
var igc = item.GetCooldown

?igc("bash") = 0
  equipR bash
:?igc("dash") = 0
  equipR dash
  // ¡AGUJERO! Si ambos en cooldown, ¿qué?
```

**Correcto:**

```
var igc = item.GetCooldown

?igc("bash") = 0
  equipR bash
  activate R
  >DEPURACIÓN: bash listo - LÍNEA 6
:?igc("dash") = 0
  equipR dash
  activate R
  >DEPURACIÓN: dash listo - LÍNEA 10
:
  >DEPURACIÓN: ambos en cooldown - LÍNEA 12
  equipR shield
```

---

### 7. Variables que no se reinician

```
var hasUsedAbility = false

?item.CanActivate()
  activate R
  hasUsedAbility = true   // ← OBLIGATORIO
```

Sin reiniciar, se activa 30 veces por segundo.

---

## Checklist: tu lógica está lista cuando...

- [ ] CADA cadena `?` `:?` `:?` termina con `:`
- [ ] CADA `:` final tiene un mensaje de depuración
- [ ] No hay más de 2 niveles de anidación sin razón
- [ ] Probaste el script en el juego
- [ ] **No ves NINGÚN mensaje de depuración** (eso = lógica correcta)
- [ ] Probaste casos extremos (todos los cooldowns activos, sin enemigos, etc.)
- [ ] Otro jugador puede leer tu código y entender cada rama

---

## Resumen rápido

| Problema | Solución |
|----------|----------|
| "¿Qué pasa en este caso?" | Añade un `:` con `>DEPURACIÓN` |
| Condiciones anidadas profundas | Usa `&` y `\|` en una línea |
| "Mi script hace cosas raras" | Ejecuta con depuración y busca dónde aparece |
| "Algunas veces no hace nada" | Hay un agujero; cierra todas las cadenas con `:` |
| "El cooldown nunca funciona" | Recuerda: `igc() = 0` es listo, `igc() > 0` es en cooldown |

---

[← Trucos y errores comunes](14-trucos-y-errores.md) · [Índice](README.md)
