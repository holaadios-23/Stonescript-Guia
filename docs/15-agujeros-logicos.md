# 15. Agujeros lógicos

[← Trucos y errores comunes](14-trucos-y-errores.md) · [Índice](README.md)

---

## ¿Qué es un agujero lógico?

Un **agujero lógico** es un error en tu **lógica** (no en la sintaxis) donde tu script no hace exactamente lo que querías porque falta una condición o no la pensaste bien. El código es válido y corre sin errores, pero el resultado no es el esperado.

Es como darle instrucciones confusas a alguien: la persona entiende perfectamente lo que le dices, pero hace lo que no querías.

---

## 🔑 La técnica clave: SIEMPRE cierra con `:` (else)

**Regla de oro:** Aunque sea un solo `if`, siempre cierra la cadena con `:` que imprima un mensaje de debug. Si nunca ves ese mensaje, tu lógica está correcta. Si lo ves, **encontraste el agujero**.

```
?condition
  do thing
:
  >DEBUG: condition false - LINE 3
```

---

## El agujero lógico más común: cadenas incompletas

**El peor caso:**

```
?condition
:?condition2
  ?innerThing
    do thing
    // ¡AGUJERO! Si innerThing es false, ¿qué pasa?
:?condition3
:
  default
```

Si `condition2` es **true** pero `innerThing` es **false**, el script **no entra al `:` final**. Tu personaje hace nada (agujero).

**Correcto:**

```
?condition
:?condition2
  ?innerThing
    do thing
  :
    >DEBUG: condition2 true but innerThing false - LINE 5
:?condition3
  do thing3
:
  >DEBUG: no condition matched - LINE 9
  default
```

Ahora si ves "condition2 true but innerThing false", sabes exactamente dónde está el problema.

---

## Técnica de debugging: paso a paso

### 1. Identifica todas tus cadenas `?` `:?` `:`

Marca dónde empiezan y terminan:

```
?foe.distance < 10           // ← cadena 1 empieza
  ?foe.hp < 50               // ← cadena 2 empieza (anidada)
    attack
  :
    defend
:?foe.distance < 20          // ← cadena 1 continúa
  move closer
:                            // ← cadena 1 termina
  >DEBUG: too far - LINE 9
```

### 2. Asegúrate de que CADA cadena termine con `:`

```
?...
:?...
:?...
:                    // ← obligatorio
  >DEBUG: message
```

Incluso cadenas de una sola `?`:

```
?condition
  do thing
:                    // ← SÍ, aunque sea solo un if
  >DEBUG: message
```

### 3. Añade debug a cada `:` final

```
?hp < 20
  activate potion
:
  >DEBUG: hp >= 20 - LINE 4
```

### 4. Prueba en el juego

Si ves mensajes de debug en lugares inesperados, encontraste el agujero.

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
      ?!status.shield
        attack
        // ¿Qué si shield está activo?
```

Hay 4 puntos donde podrías olvidar un `:`.

**Mejor (condiciones compuestas):**

```
?foe & foe.distance < 20 & foe.hp < 50 & !status.shield
  attack
:
  >DEBUG: attack condition not met - LINE 3
```

Solo **1** punto de ramificación. Mucho más seguro.

**Mixto (lo ideal):**

```
?foe & foe.distance < 20
  ?foe.hp < 50 & !status.shield
    attack
  :
    >DEBUG: close but defended or healthy - LINE 5
:
  >DEBUG: enemy too far or not present - LINE 7
```

2 niveles máximo, debug en todos los `:`.

---

## Ejemplo completo: Sistema de combate

**Versión con debugging (recomendada):**

```
?foe & foe.distance < 50
  ?foe.hp < 30 & foe.distance < 15
    attack aggressive
  :
    >DEBUG: close but foe healthy - LINE 5
:?foe & foe.distance >= 50
  move closer
  >DEBUG: foe too far - LINE 8
:
  >DEBUG: no foe present - LINE 10
  idle
```

**¿Qué pasa?**

- Ves "close but foe healthy" → enemigo cerca pero vivo
- Ves "foe too far" → hay enemigo pero está lejos
- Ves "no foe present" → sin enemigos
- **No ves nada** → ataque normal (lógica correcta)

---

## Ejemplos de agujeros comunes

### 1. Olvidar el `:` final

```
?condition1
  do thing
?condition2           // ¡MAL! Esto corre independiente, no es parte del if
  do thing2
```

**Correcto:**

```
?condition1
  do thing
:?condition2
  do thing2
:
  >DEBUG: no condition - LINE 7
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

### 3. Confundir `&` (AND) con `|` (OR)

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

---

### 4. Variables que no se reinician

```
var hasUsedUlimate = false

?key = spacebar & !hasUsedUlimate
  activate ultimate
  hasUsedUlimate = true   // ← OBLIGATORIO
```

Sin reiniciar, se activa 30 veces por segundo.

---

### 5. Counter mal estructurado

```
var counter = 0
counter = counter + 1
?counter = 10
  activate ability
```

`counter` nunca llega a 10.

**Correcto:**

```
var counter = 0

counter = counter + 1
?counter = 10
  activate ability
  counter = 0         // reiniciar
```

---

## Checklist: tu lógica está lista cuando...

- [ ] CADA cadena `?` `:?` `:?` termina con `:` con debug
- [ ] No hay más de 2 niveles de anidación sin razón
- [ ] Probaste el script en el juego
- [ ] **No ves NINGÚN mensaje de debug** (eso significa que está correcto)
- [ ] Probaste casos extremos (sin enemigos, vida 0, etc.)
- [ ] Otro programador puede leer tu código y entender cada rama

---

## Resumen rápido

| Problema | Solución |
|----------|----------|
| "¿Qué pasa en este caso?" | Añade un `:` con `>DEBUG` |
| Condiciones anidadas profundas | Usa `&` y `|` en una línea |
| "Mi script hace cosas raras" | Ejecuta con debug y busca dónde aparece |
| "Algunas veces no hace nada" | Hay un agujero; cierra las cadenas con `:` |

---

[← Trucos y errores comunes](14-trucos-y-errores.md) · [Índice](README.md)
