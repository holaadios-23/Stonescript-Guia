# 10. Interfaz de usuario (UI): botones, paneles y texto

[← Imprimir y ASCII-art](09-impresion-y-ascii.md) · [Índice](README.md) · [Siguiente: Funciones nativas →](11-funciones-nativas.md)

---

StoneScript trae un sistema de **interfaz** para crear botones, paneles, textos y
animaciones. Es lo que usan scripts avanzados (como tu `BotonesMovibles` o `DashBoard`).

## La idea: un árbol de elementos

Existe un panel **raíz** invisible (`ui.root`). A él le añades elementos, y a esos
elementos puedes añadirles otros, formando un árbol:

```
root
├─ Panel
│  ├─ Text
│  ├─ ASCII-art
│  └─ Button
└─ Panel
   └─ Panel
      └─ Text
```

> Casi siempre creas la interfaz **una sola vez** dentro de `?loc.begin`, no cada frame.

---

## Crear elementos

| Función | Crea... |
|---------|---------|
| `ui.AddButton()` | un botón |
| `ui.AddPanel()` | un panel (contenedor) |
| `ui.AddText()` / `ui.AddText("texto")` | un texto |
| `ui.AddAnim("...")` | una animación ASCII |
| `ui.AddCanvas()` | un lienzo para dibujar glifos |
| `ui.AddStyle("...")` | un estilo nuevo para paneles/botones |
| `ui.Clear()` | borra toda la interfaz |

### Un botón básico

```
func OnPressed()
  >¡Hola Mundo!

?loc.begin
  var button = ui.AddButton()
  button.y = 1
  button.text = "Púlsame"
  button.SetPressed(OnPressed)   // qué función se llama al pulsarlo
```

### Un texto

```
?loc.begin
  var t = ui.AddText()
  t.text = "Hola [color=#red]Mundo[/color]!"   // se puede colorear parte del texto
```

### Un panel (contenedor)

```
?loc.begin
  var p = ui.AddPanel()
  p.color = #red
```

---

## Propiedades comunes (todo elemento las tiene)

Todos los elementos (paneles, textos, botones, animaciones) comparten estas propiedades:

| Propiedad | Qué controla |
|-----------|--------------|
| `.x` / `.y` | Posición relativa a su anclaje |
| `.w` / `.h` | Ancho / alto |
| `.absoluteX` / `.absoluteY` | Posición real en pantalla (solo lectura) |
| `.anchor` | Pivote **interno** del elemento |
| `.dock` | Pivote **externo** (dónde se coloca dentro del padre) |
| `.ax` / `.ay` | Partes X/Y del anchor (`left`/`center`/`right`, `top`/`center`/`bottom`) |
| `.dx` / `.dy` | Partes X/Y del dock |
| `.parent` | Su panel padre (solo lectura) |
| `.visible` | `true`, `false` o `inherit` (heredar del padre) |
| `.Recycle()` | Lo quita de su padre para reutilizarlo |

Valores posibles de `anchor`/`dock`: `top_left`, `top_center`, `top_right`,
`center_left`, `center_center`, `center_right`, `bottom_left`, `bottom_center`,
`bottom_right`.

> Regla práctica: si dudas, usa **el mismo valor** para `anchor` y `dock`. Es lo más común.

```
?loc.begin
  var p = ui.AddPanel()
  p.anchor = top_right
  p.dock = top_right
  p.w = 20
  p.h = 9
  var t = ui.AddText("Hola Mundo!")
  t.anchor = bottom_left
  t.dock = bottom_left
  p.Add(t)            // mete el texto dentro del panel
```

---

## Paneles: contener y organizar

| Propiedad / método | Qué hace |
|--------------------|----------|
| `panel.Add(componente)` | Añade un hijo (afecta el orden de dibujo) |
| `panel.children` | Array con los hijos |
| `panel.Remove(componente)` o `panel.Remove(i)` | Quita un hijo |
| `panel.Clear()` | Quita todos los hijos |
| `panel.color` | Color del panel |
| `panel.style` | Estilo (de -8 a 8) |
| `panel.clip` | Si `true`, recorta lo que se salga de sus bordes |

---

## Botones: detalles

| Propiedad / método | Qué hace |
|--------------------|----------|
| `button.text` | Texto dentro del botón |
| `button.tcolor` | Color del texto |
| `button.bcolor` | Color del borde |
| `button.hcolor` | Color al pulsarlo (highlight) |
| `button.sound` | Sonido al pulsarlo (por defecto "confirm") |
| `button.style` | Estilo (de -8 a 8) |
| `button.SetPressed(f)` | Función al **pulsar y soltar** encima |
| `button.SetDown(f)` | Función al **empezar** a pulsar |
| `button.SetUp(f)` | Función al **soltar** encima |

La función que asignas recibe como **primer parámetro el propio botón**, útil para saber
cuál se pulsó:

```
var button1
var button2
func OnPressed(btn)
  ?btn = button1
    >Se pulsó el botón 1
  :
    >Se pulsó el botón 2

?loc.begin
  button1 = ui.AddButton()
  button1.y = 1
  button1.SetPressed(OnPressed)

  button2 = ui.AddButton()
  button2.y = 6
  button2.SetPressed(OnPressed)
```

---

## Animaciones (`Anim`)

`ui.AddAnim(sprites)` crea una animación a partir de "hojas" de ASCII. Propiedades útiles:
`color`, `duration`, `loop`, `frame`, `flipX`, `flipY`, `playOnStart`, y métodos
`Play()`, `Pause()`, `Stop()`, `AddLayer(...)`, `Load(...)`.

```
var dance
?loc.begin
  dance = ui.AddAnim(ascii
(O/
%%
\O)
asciiend)
  dance.duration = 20
  dance.loop = true
  dance.Play()
```

---

## Canvas (lienzo)

`ui.AddCanvas()` crea un lienzo para pintar glifos y colores posición a posición. Útil
para filtros de color, mapas, etc.

| Método | Qué hace |
|--------|----------|
| `canvas.Set("X")` | Rellena todo el lienzo con un glifo |
| `canvas.Set(x, y, "A")` | Cambia un glifo en una posición |
| `canvas.Set(x, y, fg, bg, glifo)` | Glifo + color de frente y fondo |
| `canvas.SetFG(color)` / `canvas.SetBG(color)` | Color de frente / fondo a todo |
| `canvas.Get(x, y)` | Lee el glifo en una posición |
| `canvas.blend` | Mezcla: `Opaque`, `Multiply`, `Divide`, `Add`, `Subtract` |

---

## Otras funciones de `ui`

| Función | Qué hace |
|---------|----------|
| `ui.OpenInv()` | Abre el inventario |
| `ui.OpenMind()` | Abre el Mind Stone |
| `ui.ShowBanner(str)` / `ui.ShowBanner(str, str)` | Muestra el cartel animado |

```
?time = 120
  ui.ShowBanner("¡Hola Mundo!")
```

---

## Consejo: reutiliza con funciones

Como en tu `Funciones.txt`, conviene crear funciones tipo `CreateButton(...)` y
`CreateText(...)` que devuelvan el elemento ya configurado, para no repetir código.
Ver [capítulo 7](07-funciones.md).

---

[← Imprimir y ASCII-art](09-impresion-y-ascii.md) · [Índice](README.md) · [Siguiente: Funciones nativas →](11-funciones-nativas.md)
