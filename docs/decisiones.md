# Documento de decisiones de diseño

> Cada decisión: **qué** elegiste, **por qué**, y **qué alternativa descartaste**.
> Se evalúa la justificación, no la longitud. Máximo 2 páginas en la Fase 1.

## Ruta de trabajo

- [ ] Ruta A · Figma (enlace con permiso de lectura: …)
- [x] Ruta B · VS Code (diseño directamente en `guia.html`)

## 1. Color (RA1 · CE b)

- **Tono de acento de la ficha y cómo has obtenido la escala (50 … 950):**
  Tono 352° (OKLCH, H) de la ficha del caso 12, un rosa frambuesa. He sacado los seis pasos en
  oklch.com: dejo fijo el tono y bajo la luminosidad del 96 % (50) al 26 % (950). La croma va
  en torno a 0,15 en los pasos medios y baja en los extremos (0,03 en el 50 y 0,11 en el 950)
  para que no se salgan de la gama sRGB. Uso OKLCH porque sus pasos de luminosidad se perciben
  iguales y el tono no se desvía al aclarar u oscurecer. Solo defino los pasos que se usan
  (50, 300, 400, 700, 800, 950).
  *Descartado:* hacer la escala en HSL o con `color.scale()` de Sass. Con ellos los saltos de
  luminosidad son irregulares y los tonos claros pierden saturación.

- **Por qué el acento es el 700 en claro y el 400 en oscuro:**
  En claro, el 700 (L 50 %) es el paso más claro que todavía supera 4,5:1 sobre blanco
  (6,52:1). Así me sirve para el texto y también para el fondo del botón primario. El 400 sobre
  blanco solo llega a 2,66:1. En oscuro pasa lo contrario: el 400 (L 72 %) da 5,50:1 sobre la
  superficie `neutro-800`, y el 700 apenas destaca sobre un fondo oscuro. El hover se aleja un
  paso más del fondo (800 en claro y 300 en oscuro), así que el texto sobre el botón gana
  contraste al pasar el ratón (8,81:1 y 8,72:1).

- **Tokens semánticos añadidos y para qué:**
  - `error` (`error-700` / `error-300`): turno ocupado, mensajes de error de formulario y aviso
    de pago pendiente.
  - `error-tenue` (`error-50` / `error-950`): fondo suave de esos avisos.
  Su escala es un rojo de 27° obtenido como la del acento (L 96 → 26 %). Lo he alejado del
  acento (352°) para que «error» no se confunda con «seleccionado». Los nombres indican la
  función, no el color («error», no «rojo»). El resto de tokens (`acento`, `acento-hover`,
  `sobre-acento`, `acento-tenue`, `foco`, `exito`…) vienen de la plantilla y los he mantenido,
  cambiando solo los primitivos del acento por los de mi caso. Los de `exito` conservan los
  verdes originales.

- **Pares de contraste añadidos a `$pares-contraste`:**
  - `error` sobre `error-tenue` (mín. 4,5): 5,81:1 en claro · 7,75:1 en oscuro.
  - `error` sobre `superficie` (mín. 4,5): 6,63:1 en claro · 7,10:1 en oscuro.
  Con la nueva escala, los pares del acento que ya traía la plantilla siguen cumpliendo
  (claro / oscuro): acento sobre superficie 6,52 / 5,50 · sobre-acento sobre acento
  6,52 / 6,71 · acento sobre acento-tenue 5,71 / 6,08 · foco sobre fondo 6,24 / 6,71.
  
## 2. Tipografía (RA1 · CE b)

- **Familia elegida y por qué (rendimiento, legibilidad, licencia):**
  Pila de fuentes del sistema: `system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif`, y
  `ui-monospace, 'Cascadia Code', Menlo, monospace` para el código.
  - *Rendimiento:* la fuente ya está instalada en el dispositivo, así que la página no descarga
    nada (0 KB) y el texto no parpadea al cargar (sin FOUT ni FOIT).
  - *Legibilidad:* cada sistema muestra la fuente que su pantalla renderiza mejor (Segoe UI en
    Windows, San Francisco en Apple, Roboto en Android). Son sans serif con buena altura de x,
    pensadas para pantalla y tamaños pequeños, como los horarios y precios de la reserva.
  - *Licencia:* no distribuyo ningún archivo de fuente, así que no hay licencia que registrar
    en `licencias.md`.
  *Descartado:* una fuente web como Inter o Montserrat desde Google Fonts. Añade unos 20–40 KB
  por cada peso, una petición a un servidor externo (que en la UE plantea problemas de
  privacidad) y no aporta nada importante a una app de reservas para vecinos.

- **Base y razón en móvil y escritorio. Por qué esas:**
  | | Base | Razón | h1 (`--paso-4`) |
  | --- | --- | --- | --- |
  | Móvil (360 px) | 16 px | 1,2 (tercera menor) | ≈ 33 px |
  | Escritorio (1440 px) | 18 px | 1,333 (cuarta justa) | ≈ 57 px |
  - 16 px es el mínimo recomendado para el texto en móvil, y evita que iOS amplíe la página al
    tocar un campo del formulario. En escritorio subo a 18 px porque la pantalla se lee desde
    más lejos.
  - En móvil uso una razón suave (1,2) para que los títulos no ocupen media pantalla ni partan
    palabras. En escritorio hay sitio para más jerarquía. He cambiado la razón de 1,25 a 1,333
    porque con 1,25 el h1 se quedaba en ≈ 44 px y se diferenciaba poco del h2.
  - Entre los dos anchos, los tamaños se interpolan con `clamp()` (función `fluido()`), así que
    no hacen falta media queries para la tipografía.
  - Interlineado: 1,5 en el texto (mínimo de WCAG 1.4.12) y 1,15 en los títulos, que son grandes
    y cortos.
  *Descartado:* una sola escala fija (por ejemplo, 1,333 también en móvil). En el móvil, el h1
  pasaría de 50 px y desplazaría el contenido.

- **Medida de línea (`max-inline-size` del párrafo en `ch`):**
  `max-inline-size: 65ch`. Así quedan entre 60 y 75 caracteres por línea, el rango más
  cómodo de lectura: con líneas más largas el ojo pierde el salto a la siguiente línea, y con
  más cortas el texto se corta demasiado. Uso `ch` porque depende de la fuente y del tamaño, de
  modo que la medida se mantiene aunque cambie `--paso-0`.

## 3. Espaciado y forma

- Escala de espaciado y radios. Si cambias algo respecto a la plantilla, por qué:

## 4. Componentes (RA1 · CE f)

| Componente | Estados | Cómo se resuelve cada estado en CSS |
| --- | --- | --- |
| Botón | normal, hover, foco, desactivado, secundario | |
| Turno | disponible, seleccionado, ocupado, foco | |
| Contador de invitados | | |
| Aviso de pago | | |

## 5. Comparativa de tecnologías (RA1 · CE e · RA2 · CE i)

| Criterio | Figma | Penpot | Diseño en código |
| --- | --- | --- | --- |
| Coste / licencia | | | |
| Traslado a código (handoff) | | | |
| Mantenimiento del sistema | | | |

| Criterio | Sass + CSS propio | CSS nativo sin preprocesador | Bootstrap / Tailwind |
| --- | --- | --- | --- |
| Tokens y temas | | | |
| Peso del CSS final | | | |
| Control de la cascada | | | |

**Decisión y motivo:**
