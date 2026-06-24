# 1. Introducción: cómo funciona StoneScript

[← Volver al índice](README.md) · [Siguiente: Condiciones →](02-condiciones.md)

---

## ¿Qué es StoneScript?

Es el lenguaje de **Stone Story RPG**. Tu personaje pelea solo, pero **tú decides cómo**
escribiendo instrucciones en el **Mind Stone** (la piedra de la mente).

Piensa en StoneScript como una **lista de preguntas y respuestas**:

> "¿Estás en las cuevas? Entonces usa el equipo 1."
> "¿Tu vida bajó de 10? Entonces toma una poción."

El juego revisa tus preguntas constantemente y ejecuta las respuestas que correspondan.

---

## El detalle más importante: corre 30 veces por segundo

StoneScript **no se ejecuta una sola vez**. Se ejecuta **una vez por cada *frame***, y el
juego tiene **30 frames por segundo**. Es decir, **tu script entero se lee 30 veces cada
segundo**, de arriba a abajo, sin parar, durante toda la partida.

Esto es clave para entender muchas cosas:

- Por eso un `>Hola` muestra el texto de forma continua (se vuelve a imprimir cada frame).
- Por eso puedes "vigilar" la vida del enemigo en tiempo real.
- Por eso las variables (capítulo 6) sirven para **recordar** cosas entre frames.

```
// Esto se imprime en pantalla TODO el tiempo, porque se ejecuta cada frame
>Estoy peleando
```

---

## La sangría manda (scope)

La **sangría** son los espacios al inicio de una línea. En StoneScript definen **a qué
pertenece** cada instrucción. Lo que esté con sangría debajo de un `?` solo ocurre si esa
pregunta es verdadera.

```
?loc = caves        // ¿Estoy en las cuevas?
  loadout 1         // Sí -> usa el equipo 1 (fíjate en los 2 espacios)
```

Compáralo con esto, donde la orden **no** tiene sangría:

```
?loc = caves
loadout 1           // Esto se ejecuta SIEMPRE, esté donde esté el jugador
```

> Consejo: usa **2 espacios** por nivel de sangría y mantén el estilo en todo el archivo.
> Mezclar tabulaciones y espacios da errores difíciles de ver.

---

## Comentarios: notas para ti, que el juego ignora

Un comentario es texto que **no se ejecuta**. Sirve para explicarte a ti mismo qué hace
el código.

```
// Esto es un comentario de una línea
?hp < 10
  activate potion   // también puedes comentar al final de una línea
```

Para comentar **varias líneas** a la vez usa `/* */`:

```
/*
Todo esto está apagado.
No hace nada al ejecutarse.
?loc = caves
  loadout 1
*/
```

---

## Continuar una línea larga con `^`

Si una línea se hace muy larga, puedes partirla en dos usando `^` al inicio de la
siguiente. El juego las une como si fueran una sola.

```
?loc = caves |
^loc = mine
  equip repeating
```

Es **exactamente lo mismo** que escribir:

```
?loc = caves | loc = mine
  equip repeating
```

---

## Un primer script completo

Este es el script por defecto del juego. Léelo de arriba a abajo: son solo preguntas y
respuestas con sangría.

```
?hp < 7              // ¿Mi vida es menor a 7?
  activate potion    //   sí -> toma poción

?loc = caves         // ¿Estoy en las cuevas?
  equipL sword       //   sí -> espada en la mano izquierda
  equipR shield      //       y escudo en la derecha
  ?foe = boss        //   y además, ¿el enemigo es el jefe?
    equip crossbow   //       sí -> usa la ballesta (esto es una condición DENTRO de otra)
```

Fíjate en el último bloque: la pregunta `?foe = boss` está **dentro** de `?loc = caves`
(tiene más sangría). Eso significa: *"solo si estoy en las cuevas Y además el enemigo es
el jefe"*. A eso le llamamos **condiciones anidadas** y es el tema del próximo capítulo.

---

## Cómo probar tu script

1. Entra al **Mind Stone** y escribe (o pega) tu script.
2. El botón de **encendido** (arriba a la derecha) prende/apaga el script.
3. Puedes editarlo **en pleno combate** pulsando **M**.
4. Mantén **Tab** para ver el estado del juego y los **últimos errores**.
5. Para escribir scripts largos, es cómodo usar un editor de texto (Notepad, etc.) y luego
   copiar/pegar con `Ctrl+C` / `Ctrl+V`.

---

[← Volver al índice](README.md) · [Siguiente: Condiciones →](02-condiciones.md)
