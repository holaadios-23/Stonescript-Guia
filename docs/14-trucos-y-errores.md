# 14. Trucos y errores comunes

[← Recetario de ejemplos](13-recetario-ejemplos.md) · [Índice](README.md)

---

## Trucos oficiales (del manual)

- **La sangría define el scope.** Los espacios al inicio de línea determinan qué pasa
  dentro de cada `?`. Es lo más importante del lenguaje.
- **Puedes editar el script en pleno combate** pulsando **M**.
- **El botón de encendido** (arriba a la derecha del Mind Stone) prende/apaga el script.
- **Si hay varios `equip` en el mismo frame, gana el último** que se ejecute.
- **El script corre 30 veces por segundo** (1 vez por frame).
- **Para scripts largos**, edítalos en un editor de texto (Notepad) y pega con
  `Ctrl+C` / `Ctrl+V`.
- **Mantén Tab en el juego** para ver mucha información del estado y la lista de errores
  recientes.
- **Parte un print en varias líneas** con `\n`.

---

## Errores frecuentes (y cómo evitarlos)

### 1. Sangría inconsistente
Mezclar tabuladores y espacios, o usar 2 espacios en un sitio y 4 en otro, hace que los
bloques se "enganchen" mal.

> **Solución:** usa siempre **2 espacios por nivel** y sé consistente en todo el archivo.

### 2. Pensar que el script corre una sola vez
Corre **cada frame**. Por eso un `>Hola` se ve siempre y un contador con `i++` sube sin
parar.

> **Solución:** usa variables para recordar estados y `loc.begin` para inicializar.

### 3. La línea `var` no reinicia la variable cada frame
`var i = 0` solo pone `i` en 0 **la primera vez**. Después, aunque el frame se repita, ese
`= 0` ya no actúa.

> **Solución:** si quieres reiniciar por run, hazlo dentro de `?loc.begin`.

### 4. Confundir `=` con igualdad exacta en textos
En condiciones, `=` con texto significa **"contiene"**, no "es exactamente igual".

> **Solución:** usa `string.Equals(a, b)` para comparar texto de forma exacta.

### 5. División de enteros que "pierde" decimales
`5 / 2` da `2`, no `2.5`, porque son enteros.

> **Solución:** usa un decimal: `5.0 / 2` da `2.5`.

### 6. Mezclar `&` y `|` sin pensar en la precedencia
`a & b | c` se evalúa como `(a & b) | c` (los `&` van primero).

> **Solución:** si dudas, separa en condiciones anidadas para dejarlo claro.

### 7. Solo un bloque de la cadena `?`/`:?`/`:` se ejecuta
Una vez que una condición de la cadena es verdadera, las demás se ignoran.

> **Solución:** si necesitas que varias cosas pasen a la vez, usa `?` separados (no una
> cadena con `:?`).

### 8. Crear arrays o cambiar bindings cada frame
Declarar `= []` o llamar a `key.Bind(...)` cada frame desperdicia recursos.

> **Solución:** créalos una vez (en `?loc.begin`) o usa `.Clear()` en lugar de `= []`.

### 9. Recursión infinita (error de stack)
Una función que se llama a sí misma sin condición de salida supera el límite de 215.

> **Solución:** añade siempre una condición que detenga la recursión (`?n <= 0` → `return`).

---

## Cómo depurar (ver qué está pasando)

La mejor herramienta es **imprimir valores** en pantalla. Si algo no funciona, muéstralo:

```
>`0,1,loc = @loc@
>`0,2,foe = @foe@  dist = @foe.distance@
>`0,3,hp = @hp@/@maxhp@
>`0,4,time = @time@
```

Así ves en vivo qué está leyendo el juego y por qué tu condición entra (o no).

Otros consejos:

- Mantén **Tab** para ver los **últimos errores** del script.
- Comenta partes con `//` o `/* */` para aislar el problema.
- Empieza simple y ve añadiendo poco a poco; prueba tras cada cambio.

---

## Plantilla recomendada para empezar un script

```
// === Inicialización (una vez por run) ===
var contador = 0
?loc.begin
  contador = 0

// === Supervivencia (siempre primero) ===
?hp < 8
  activate potion

// === Lógica por ubicación ===
?loc = caves
  loadout 1
  ?foe = boss
    equip crossbow
:
  loadout 2

// === Información en pantalla (depuración) ===
>`0,1,loc=@loc@ hp=@hp@/@maxhp@
```

---

¡Y eso es todo! Con estos 14 capítulos tienes una base sólida de StoneScript. Vuelve al
**[índice](README.md)** cuando necesites repasar algo.
