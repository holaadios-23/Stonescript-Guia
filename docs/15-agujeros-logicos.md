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
?distancia.enemigo < 15
:?distancia.enemigo < 20
  ?vidaEnemigo < 50
    atacar
    // ¡AGUJERO! Si vidaEnemigo >= 50, ¿qué pasa?
:?distancia.enemigo < 50
:
  acción por defecto
```

Si `distancia.enemigo` está entre 15 y 20, pero `vidaEnemigo >= 50`, el script **no entra al `:` final**. Tu personaje hace nada (agujero).

**Correcto:**

```
?distancia.enemigo < 15
:?distancia.enemigo < 20
  ?vidaEnemigo < 50
    atacar
  :
    >DEPURACIÓN: enemigo a 15-20 pero sano - LÍNEA 5
:?distancia.enemigo < 50
  acercarse
:
  >DEPURACIÓN: enemigo muy lejos - LÍNEA 9
  movimiento aleatorio
```

Ahora si ves "enemigo a 15-20 pero sano", sabes exactamente dónde está el problema.

---

## Técnica de depuración: paso a paso

### 1. Identifica todas tus cadenas `?` `:?` `:`

Marca dónde empiezan y terminan:

```
?foe.distance < 10        // ← cadena 1 empieza
  ?foe.hp < 50            // ← cadena 2 empieza (anidada)
    activate potion
  :
    defend
:?foe.distance < 20       // ← cadena 1 continúa
  move closer
:                         // ← cadena 1 termina
  >DEPURACIÓN: enemigo muy lejos - LÍNEA 9
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
      ?buffs.count = 0
        activate potion
        // ¿Qué si buffs.count > 0?
```

Hay 4 puntos donde podrías olvidar un `:`.

**Mejor (condiciones compuestas):**

```
?foe & foe.distance < 20 & foe.hp < 50 & buffs.count = 0
  activate potion
:
  >DEPURACIÓN: condición de poción no se cumplió - LÍNEA 3
```

Solo **1** punto de ramificación. Mucho más seguro.

**Mixto (lo ideal):**

```
?foe & foe.distance < 20
  ?foe.hp < 50 & buffs.count = 0
    activate potion
  :
    >DEPURACIÓN: cerca pero sano o con buffs - LÍNEA 5
:
  >DEPURACIÓN: sin enemigo o muy lejos - LÍNEA 7
```

2 niveles máximo, depuración en todos los `:`.

---

## Ejemplo completo: Sistema de combate (REAL)

**Versión con depuración (recomendada):**

```
?foe & foe.distance < 50
  ?foe.hp < 30 & foe.distance < 15
    activate potion
  :
    >DEPURACIÓN: cerca pero enemigo sano - LÍNEA 5
:?foe & foe.distance >= 50
  move closer
  >DEPURACIÓN: enemigo muy lejos - LÍNEA 8
:
  >DEPURACIÓN: sin enemigo - LÍNEA 10
  idle
```

**¿Qué pasa?**

- Ves "cerca pero enemigo sano" → Hay enemigo cerca pero con buena vida
- Ves "enemigo muy lejos" → Hay enemigo pero está lejano
- Ves "sin enemigo" → No hay enemigos
- **No ves nada** → Lógica correcta, ataque normal

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

### 5. Variables que no se reinician

```
var hasUsedAbility = false

?item.CanActivate()
  activate R
  hasUsedAbility = true   // ← OBLIGATORIO
```

Sin reiniciar, se activa 30 veces por segundo.

---

### 6. Cadenas mal cerradas con condiciones reales

**El peor caso del juego:**

```
?loc = caves
:?loc = deadwood
  ?foe = bolesh
    equipR hammer
  // ¡AGUJERO! deadwood, no es bolesh, ¿qué?
:?loc = halls
:
  loadout 1
```

**Correcto:**

```
?loc = caves
  loadout 1
  >DEPURACIÓN: en cuevas - LÍNEA 3
:?loc = deadwood
  ?foe = bolesh
    equipR hammer
  :
    >DEPURACIÓN: deadwood pero no bolesh - LÍNEA 8
:?loc = halls
  equipL poison wand
  >DEPURACIÓN: en halls - LÍNEA 11
:
  >DEPURACIÓN: otra ubicación - LÍNEA 13
  loadout 1
```

---

## Checklist: tu lógica está lista cuando...

- [ ] CADA cadena `?` `:?` `:?` termina con `:`
- [ ] CADA `:` final tiene un mensaje de depuración
- [ ] No hay más de 2 niveles de anidación sin razón
- [ ] Probaste el script en el juego
- [ ] **No ves NINGÚN mensaje de depuración** (eso = lógica correcta)
- [ ] Probaste casos extremos (sin enemigos, vida 1, etc.)
- [ ] Otro jugador puede leer tu código y entender cada rama

---

## Resumen rápido

| Problema | Solución |
|----------|----------|
| "¿Qué pasa en este caso?" | Añade un `:` con `>DEPURACIÓN` |
| Condiciones anidadas profundas | Usa `&` y `\|` en una línea |
| "Mi script hace cosas raras" | Ejecuta con depuración y busca dónde aparece |
| "Algunas veces no hace nada" | Hay un agujero; cierra todas las cadenas con `:` |

---

[← Trucos y errores comunes](14-trucos-y-errores.md) · [Índice](README.md)
