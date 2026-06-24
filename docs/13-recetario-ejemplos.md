# 13. Recetario de ejemplos (copiar y pegar)

[← Importar scripts](12-importar-scripts.md) · [Índice](README.md) · [Siguiente: Trucos y errores →](14-trucos-y-errores.md)

---

Scripts completos y comentados, ordenados de más fácil a más avanzado. Cópialos en tu Mind
Stone y modifícalos a tu gusto.

---

## 1. Beber poción cuando la vida es baja (lo más básico)

```
?hp < 7
  activate potion
```

---

## 2. Equipo según la ubicación (tu estilo de preguntas/respuestas)

```
?loc = caves
  loadout 1
:?loc = deadwood
  loadout 2
:?loc = rocky
  equip shovel
:
  loadout 3        // cualquier otra zona
```

---

## 3. Tu ejemplo: equipar según la distancia del enemigo

```
?foe.distance > 20   // enemigo lejos
  equipL triskel
  equipR mask
:                    // enemigo cerca
  equipL sword
  equipR shield
```

---

## 4. Combate completo con condiciones anidadas

```
?hp < 10             // prioridad: sobrevivir
  activate potion

?loc = caves
  loadout 1
  ?foe = boss        // dentro de cuevas: si es el jefe
    equip crossbow   //   cambia a ballesta
    ?foe.hp < foe.maxhp / 2   // y si está a menos de media vida
      activate R     //     usa la habilidad del arma derecha
```

---

## 5. Contador que sube cada frame y se reinicia con una tecla

```
var count = 0
count++
>Contador = @count@

func ResetCounter()
  count = 0

?key = begin
  ResetCounter()
```

---

## 6. Hacer algo "de vez en cuando" con el tiempo

```
// Mensaje cada 5 segundos (5 * 30 = 150 frames)
?time % 150 = 0
  >Han pasado 5 segundos

// Un "tic" cada segundo
?time % 30 = 0
  play click
```

---

## 7. Mostrar tu vida y la del enemigo en pantalla

```
disable hud p f        // ocultamos las barras por defecto del juego

>`0,1,#green,Vida: @hp@/@maxhp@
?foe
  >`0,2,#red,Enemigo: @foe.hp@/@foe.maxhp@
```

---

## 8. Guardar un récord con `storage`

```
var score = 0
score++                       // por ejemplo, aumenta con el tiempo

var best = storage.Get("highscore", 0)
?score > best
  storage.Set("highscore", score)
  best = score

>`0,1,Puntuación: @score@
>`0,2,Récord: @best@
```

---

## 9. Moneda al azar (cara o cruz)

```
?loc.begin
  ?rng < 5000
    >`0,1,#yellow,¡CARA!
  :
    >`0,1,#cyan,¡CRUZ!
```

---

## 10. Generar un número aleatorio en un rango con una función

```
func RandomRange(min, max)
  ?min >= max
    return min
  return min + rng % (max - min + 1)

var dado
?key = primaryBegin        // al pulsar, "tiramos el dado"
  dado = RandomRange(1, 6)

>`0,1,Dado = @dado@
```

---

## 11. Mover un personaje `@` por la pantalla

```
var x = 10
var y = 5

?key = leftBegin
  x--
  ?x < 0
    x = 0
?key = rightBegin
  x++
?key = upBegin
  y--
  ?y < 0
    y = 0
?key = downBegin
  y++

>`@x@,@y@,#ffffff,@
```

---

## 12. Recorrer una lista e imprimirla

```
var frutas = ["Manzana", "Plátano", "Cereza"]
for i = 0 .. frutas.Count() - 1
  >`0,@i@,@frutas[i]@
```

---

## 13. Botón de interfaz que hace algo al pulsarlo

```
func OnPlay()
  >¡Botón pulsado!
  play confirm

?loc.begin
  var b = ui.AddButton()
  b.text = "JUGAR"
  b.y = 2
  b.SetPressed(OnPlay)
```

---

## 14. Animación ASCII sencilla con `time`

```
?time % 16 < 8
  >`10,5,#green,ascii
\o)
%%
asciiend
:
  >`10,5,#green,ascii
(o/
%%
asciiend
```

---

## 15. Activar habilidad solo cuando se puede y no está en enfriamiento

```
?foe = boss & item.GetCooldown("bardiche") <= 0
  equip bardiche
  ?item.CanActivate("bardiche")
    activate R
```

---

## 16. Patrón "vigilar un valor entre frames" (basado en tu CruzRojaCounter)

```
var last_hp = 0
?loc.begin
  last_hp = hp

var current_hp = hp
?current_hp < last_hp
  var d = last_hp - current_hp
  >`0,1,#red,Recibiste @d@ de daño
last_hp = current_hp
```

---

Sigue con los **[trucos y errores comunes](14-trucos-y-errores.md)** para depurar como un
profesional.

---

[← Importar scripts](12-importar-scripts.md) · [Índice](README.md) · [Siguiente: Trucos y errores →](14-trucos-y-errores.md)
