# 2. Condiciones: `?` `:?` `:` (el corazón de StoneScript)

[← Introducción](01-introduccion.md) · [Índice](README.md) · [Siguiente: Estado del juego →](03-estado-del-juego.md)

---

Este es **el capítulo más importante**. Si lo dominas, ya puedes escribir el 90% de los
scripts. La idea, otra vez, es **preguntas y respuestas**.

## `?` = "si..." (if)

`?` hace una pregunta. Si la respuesta es **verdadera**, se ejecuta lo que está con
sangría debajo.

```
?loc = caves        // ¿Estoy en las cuevas?
  loadout 1         // sí -> usa el equipo 1
```

Si la pregunta es falsa, simplemente **se salta** ese bloque y sigue con el resto.

---

## `:` = "si no..." (else)

`:` es el plan B. Se ejecuta **solo cuando la pregunta anterior fue falsa**.

```
?loc = caves        // ¿Estoy en las cuevas?
  loadout 1         // sí -> equipo 1
:                   // si no...
  loadout 2         //   -> equipo 2
```

Léelo en voz alta: *"Si estoy en cuevas usa el equipo 1, si no, usa el equipo 2."*

---

## `:?` = "si no, pero si..." (else-if)

`:?` añade **otra pregunta** que solo se revisa si la anterior fue falsa. Puedes encadenar
todas las que quieras.

```
?loc = caves         // ¿cuevas?
  loadout 1          //   sí -> equipo 1
:?loc = deadwood     // si no, ¿deadwood?
  loadout 2          //   sí -> equipo 2
:                    // si no es ninguna de las anteriores...
  loadout 3          //   -> equipo 3
```

Importante: en una cadena `?` / `:?` / `:`, **solo se ejecuta UN bloque**: el primero que
sea verdadero. Una vez que uno entra, los demás se ignoran.

### Tu ejemplo, explicado

Volvamos al ejemplo del inicio:

```
?foe.distance > 20   // ¿el enemigo está a más de 20 de distancia?
  equipL triskel     //   sí -> triskel en la mano izquierda
  equipR mask        //       y máscara en la derecha
```

Y si quisieras hacer algo distinto cuando está cerca:

```
?foe.distance > 20   // ¿lejos?
  equipL triskel
  equipR mask
:                    // si no (está cerca)...
  equipL sword
  equipR shield
```

---

## Condiciones DENTRO de otras condiciones (anidadas)

Aquí está el concepto que pediste explicar bien. Una condición **anidada** es una pregunta
que vive **dentro** de otra, gracias a tener **más sangría**.

Significa: *"esto solo se revisa si la pregunta de afuera ya fue verdadera"*.

```
?loc = caves          // PRIMERA pregunta (nivel 0)
  equipL sword        // se equipa si estoy en cuevas
  ?foe = boss         // SEGUNDA pregunta, DENTRO de la primera (nivel 1)
    equip crossbow    // solo si estoy en cuevas Y el enemigo es el jefe
```

Lee la sangría como "niveles":

- Nivel 0: `?loc = caves`
- Nivel 1 (2 espacios): `?foe = boss` → solo se evalúa si el nivel 0 fue cierto
- Nivel 2 (4 espacios): `equip crossbow` → solo si ambos fueron ciertos

Puedes anidar tantos niveles como necesites:

```
?loc = caves
  ?loc.stars >= 5          // ¿cuevas difíciles?
    ?foe = boss            // ¿y es el jefe?
      ?hp < 15             // ¿y tengo poca vida?
        activate potion    // entonces toma poción
```

> Cada nivel añade **2 espacios más**. Si te equivocas con la sangría, el bloque se
> "engancha" a otra pregunta y el comportamiento cambia. Es el error #1 de los novatos.

### Anidado con `:` y `:?`

El plan B (`:`) y el "si no, pero si" (`:?`) también funcionan dentro de un bloque
anidado. Solo deben tener la **misma sangría** que su `?` correspondiente.

```
?loc = caves
  ?foe = boss          // dentro de cuevas: ¿es el jefe?
    equip crossbow     //   sí -> ballesta
  :                    //   si no (enemigo normal, pero seguimos en cuevas)
    loadout 1          //   -> equipo 1
```

---

## ¿Anidar o usar `&`? (dos formas de decir "Y")

A veces "una condición dentro de otra" se puede escribir más corto con el operador `&`
("y"). Estas dos versiones hacen **lo mismo**:

**Versión anidada:**
```
?loc = caves
  ?foe = boss
    equip crossbow
```

**Versión con `&`:**
```
?loc = caves & foe = boss
  equip crossbow
```

¿Cuándo usar cada una?

- Usa **`&`** cuando solo necesitas una acción y todo cabe en una línea: más corto y claro.
- Usa **anidado** cuando dentro de la primera condición haces **varias cosas** o necesitas
  un `:` (else) para la condición interna.

```
?loc = caves
  loadout 1            // esto pasa en cuevas siempre
  ?foe = boss          // y además, si es el jefe...
    equip crossbow     //   cámbiate a la ballesta
  :                    // si no es el jefe...
    >Enemigo normal     //   muestra un mensaje
```

Esto **no** se puede comprimir bien con `&` porque hay un `loadout 1` que debe pasar
siempre que estés en cuevas, y un `else` para la pregunta interna.

(Verás los operadores `&`, `|`, `!`, etc. en detalle en el
[capítulo 5](05-comparaciones-y-filtros.md).)

---

## Errores típicos con condiciones

1. **Sangría inconsistente.** No mezcles 2 y 4 espacios al azar; elige 2 por nivel.
2. **Olvidar que solo entra un bloque** en una cadena `?`/`:?`/`:`.
3. **Poner `:` con sangría distinta a su `?`.** Deben alinearse.
4. **Creer que el script corre una sola vez.** Corre cada frame; ver
   [capítulo 1](01-introduccion.md).

---

## Resumen

| Símbolo | Significado | En programación |
|---------|-------------|-----------------|
| `?` | "si..." | if |
| `:?` | "si no, pero si..." | else if |
| `:` | "si no..." | else |
| sangría | qué pasa dentro de la condición | scope / bloque |

---

[← Introducción](01-introduccion.md) · [Índice](README.md) · [Siguiente: Estado del juego →](03-estado-del-juego.md)
