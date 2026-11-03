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
  - `texto` sobre `acento-tenue`, `exito-tenue` y `error-tenue` (mín. 4,5), para el texto del
    aviso de pago: 15,62 / 16,95 / 15,66:1 en claro · 15,46 / 14,48 / 15,26:1 en oscuro.
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

- **Escala de espaciado y radios. Si cambias algo respecto a la plantilla, por qué:**
  Mantengo la escala de la plantilla: múltiplos de 4 px sobre una rejilla de 8
  (4, 8, 12, 16, 24, 32, 48, 64 → de `3xs` a `2xl`). Los valores se escriben en px, como en
  Figma, y se publican en rem, así que crecen si el usuario sube el tamaño de letra del
  navegador. Con pasos de 4/8 todo queda alineado y basta con una escala corta, sin valores
  sueltos como 13 o 22 px.
  Radios: 8 px en los controles (botones, inputs) y 12 px en las tarjetas (turno, fieldset).
  El elemento que contiene a otro lleva un radio mayor, así las esquinas se ven concéntricas.
  `pildora` (999px) queda para etiquetas y chips.
  **Único cambio:** el borde de `.turno` pasa de 1 px a 2 px. Con 1 px, el paso a
  `--color-acento` al seleccionar y el borde discontinuo del estado ocupado apenas se
  distinguían, y los estados no deben depender solo de un cambio de color sutil.
  *Descartado:* una escala de 5 px o valores libres por componente, porque se pierde la
  alineación con la rejilla de 8 y el sistema es más difícil de mantener.

## 4. Componentes (RA1 · CE f)

| Componente | Estados | Cómo se resuelve cada estado en CSS |
| --- | --- | --- |
| Botón | normal, hover, foco, desactivado, secundario | **Normal:** fondo `--color-acento` y texto `--color-sobre-acento` (6,52:1 en claro), con `objetivo-tactil` (≥ 44 px). **Hover:** `&:hover` pasa a `--color-acento-hover`, un paso más alejado del fondo, así que el contraste sube (8,81:1). **Foco:** `&:focus-visible` con el mixin `foco-visible` (contorno de 3 px de `--color-foco`, solo al usar el teclado). **Desactivado:** `&:disabled` con `opacity: 0.5` y `cursor: not-allowed`. **Secundario:** el modificador BEM `.boton--secundario` (fondo transparente, borde `currentcolor` y hover `--color-acento-tenue`) va fuera del bloque para que `:hover` quede en (0,2,0). |
| Turno | disponible, seleccionado, ocupado, foco | La tarjeta es un `<label>` que envuelve un radio, y los estados se leen con `:has()` **sin JavaScript**. El radio se oculta con `opacity: 0` y 1 px (nunca `display: none`), así que sigue funcionando con Tab y las flechas. **Disponible:** por defecto, borde continuo de 2 px y «●» en `--color-exito` delante del texto. **Seleccionado:** `.turno:has(:checked)`, borde `--color-acento`, fondo `--color-acento-tenue` y texto «✓ Seleccionado» en negrita. **Ocupado:** `.turno:has(:disabled)`, borde discontinuo, hora tachada, «✕» en `--color-error` y `cursor: not-allowed`. Solo dice «Ocupado», sin la vivienda (minimización de datos). **Foco:** `.turno:has(:focus-visible)` con el mixin `foco-visible`. Ningún estado depende solo del color: cada uno tiene su icono o texto y su borde (WCAG 1.4.1). Los selectores con `::before` envuelven el estado en `:where()`, así que quedan en (0,1,1) y no en (0,3,1), por debajo del máximo de (0,2,0). *Descartado:* ocultar el radio con `display: none`, porque deja de ser accesible por teclado. |
| Contador de invitados | normal, mínimo, máximo, hover, foco | Un grupo (`role="group"`) con dos `<button>` y un `<output>` para el valor (0–2 invitados, 2,50 € cada uno, según la ficha). Botones redondos de 44 px (`objetivo-tactil`), con borde y texto `--color-acento`. **Normal:** los dos botones activos. **Hover:** `&:hover` con fondo `--color-acento-tenue`. **Foco:** `&:focus-visible` con el mixin `foco-visible`. **Mínimo / máximo:** el botón que no se puede usar lleva `disabled` («−» con 0 invitados, «+» con 2). `&:disabled` va después de `:hover` con la misma especificidad (0,2,0), así que gana: borde discontinuo, color `--color-texto-suave` (4,5:1) y `cursor: not-allowed`. En el máximo, la nota también lo dice con texto («Máximo alcanzado»), así que no depende solo del color. Cada botón lleva `aria-label` («Añadir invitado» / «Quitar invitado») porque «+» y «−» no se entienden con un lector de pantalla. El valor y el total usan `tabular-nums` para que no bailen al cambiar. En la Fase 1, los estados se muestran fijos en la guía; en la Fase 2 el JS solo tendrá que cambiar `disabled` y el número. *Descartado:* `<input type="number">`, porque sus flechas son diminutas en el móvil y no se pueden estilizar igual en todos los navegadores. |
| Aviso de pago | pendiente, pagado, error | Bloque `.aviso` con modificadores BEM `--pendiente`, `--pagado` y `--error`. Cada modificador solo cambia **tres Custom Properties** (`--aviso-color`, `--aviso-fondo` y `--aviso-icono`), y el resto del componente no se repite. **Pendiente:** acento, icono «i», y el importe, el Bizum de la comunidad (600 000 000) y el concepto con turno y vivienda. **Pagado:** éxito, icono «✓» y «Reserva confirmada». **Error:** error, icono «!» y la consecuencia (el turno se libera). Lleva `role="alert"` para que el lector de pantalla lo anuncie enseguida; los otros dos, `role="status"`. El estado se comunica con icono y título, no solo con color. El borde izquierdo de 6 px refuerza la variante. El icono se dibuja con `::before` y `content: var(--aviso-icono)`, sin selectores de estado, así que la especificidad se queda en (0,1,1). He añadido a `$pares-contraste` el texto sobre cada fondo tenue (`texto` sobre `acento-tenue`, `exito-tenue` y `error-tenue`), y los títulos ya estaban cubiertos por los pares `acento`, `exito` y `error` sobre su fondo tenue. *Descartado:* un solo color para todos los avisos con el tipo escrito en el texto, porque el usuario necesita distinguir de un vistazo si tiene que pagar o ya está todo hecho. |

## 5. Comparativa de tecnologías (RA1 · CE e · RA2 · CE i)

| Criterio | Figma | Penpot | Diseño en código |
| --- | --- | --- | --- |
| Coste / licencia | Software privativo (SaaS). Hay un plan gratuito con límites (pocos archivos de equipo, modos de variables limitados) y un plan Education gratis para estudiantes verificados. El modo de inspección para desarrollo (*Dev Mode*) es de pago. Los archivos viven en sus servidores. | Código abierto (MPL 2.0) y gratis sin límites de archivos ni de equipo. Se puede instalar en un servidor propio (*self-host*), así que los datos no tienen por qué salir del centro. | Gratis: VS Code, Sass y Stylelint son de código abierto y ya vienen en la plantilla, sin cuentas ni conexión. |
| Traslado a código (handoff) | Manual: las Variables y los estilos se copian a mano a `_tokens.scss`. El código que generan los plugins usa posiciones absolutas y medidas fijas, y no sirve (README). Riesgo de que el diseño y el código no coincidan. | Mejor que Figma: su modelo se basa en CSS (flex y grid reales) y el panel de inspección da CSS útil gratis. Tiene tokens de diseño nativos exportables. Aun así, hay que trasladarlo a la estructura del proyecto. | No hay traslado: el diseño **es** el código. La guía viva (`guia.html`) muestra los componentes reales compilados, así que no pueden diferir del producto. |
| Mantenimiento del sistema | Hay que mantener **dos** fuentes: las Variables en Figma y `_tokens.scss`. Cada cambio se hace dos veces. Muy cómodo para explorar ideas visuales y para enseñarlas a alguien que no programa. | Igual que Figma (dos fuentes que sincronizar), aunque sus tokens reducen el trabajo de copia. Comunidad y plugins más pequeños. | **Una sola fuente de verdad** (`_tokens.scss`). El control de versiones es Git, con commits que dicen qué cambió y por qué. Sass y Stylelint comprueban contraste y reglas en cada compilación. Contra: explorar variantes visuales es más lento que arrastrar en un lienzo. |

| Criterio | Sass + CSS propio | CSS nativo sin preprocesador | Bootstrap / Tailwind |
| --- | --- | --- | --- |
| Tokens y temas | Mapas Sass (`$primitivos`, `$tema`) que un `@each` convierte en Custom Properties con `light-dark()`. Sass añade lo que el CSS no puede hacer: **comprobar el contraste al compilar** (`verificar-contraste`) y calcular la escala tipográfica fluida (`fluido()`). | Las Custom Properties y `light-dark()` dan los mismos temas en el navegador, pero sin comprobaciones: un contraste insuficiente no se detectaría hasta probarlo a mano. La escala tipográfica habría que calcularla y escribirla valor a valor. | Bootstrap: variables propias `--bs-*` y modo oscuro con `data-bs-theme`, pero tu paleta tiene que encajar en sus nombres (`primary`, `danger`…). Tailwind: los tokens van en su configuración (`@theme` en la v4) y el tema oscuro con `dark:` en cada clase del HTML. Ninguno comprueba el contraste. |
| Peso del CSS final | ≈ 12,7 KB **sin minificar** (`css/estilos.css`): solo lo que se usa. | Parecido o un poco mayor: sin bucles ni mixins, lo que genera Sass se escribiría a mano, y el resultado final sería muy similar. | Bootstrap completo: ≈ 230 KB minificado (≈ 30 KB comprimido), y casi todo sin usar si no se personaliza la compilación. Tailwind: pequeño en producción (≈ 10–20 KB), porque solo genera las clases que aparecen en el HTML, pero exige un paso de compilación con su herramienta. |
| Control de la cascada | Total: `@layer` (reset → tokens → base → layout → components → utilities) decide qué gana sin depender del orden de los archivos. Stylelint limita la especificidad a (0,2,0) y prohíbe IDs y `!important`, salvo en las utilidades. BEM evita choques de nombres. | El mismo `@layer` y las mismas reglas de Stylelint funcionan igual: la cascada no depende de Sass. Lo que se pierde es organizar el código en parciales con `@use`. | Bootstrap: su CSS ya trae especificidad propia y para sobrescribirlo a menudo hace falta subirla o usar `!important`. Tailwind: casi no hay cascada (una clase, una propiedad), pero el diseño se traslada al HTML con largas listas de clases y los estados se repiten en cada elemento. |

**Decisión y motivo:**
**Diseño en código (Ruta B) con Sass + CSS propio.**

- **Por qué no Figma ni Penpot:** el proyecto es un sistema pequeño (un acento, una escala, cuatro componentes) que yo mismo diseño y programo. No hay que pasar el diseño a otra persona, así que mantener una segunda fuente de verdad solo añade trabajo y el riesgo de que no coincida con el código. Si hubiera que presentarlo a la comunidad o trabajar con un diseñador, elegiría **Penpot**, por ser libre, gratuito y más cercano a CSS.
- **Por qué Sass:** lo que aporta al proyecto no es sintaxis más cómoda, sino **comprobar el contraste y calcular la escala tipográfica al compilar**. Así cumplir WCAG queda garantizado en cada compilación, en los dos temas. La cascada la controlan `@layer` y Stylelint, que son CSS nativo y seguirían sirviendo si un día se quitara Sass.
- **Por qué no Bootstrap ni Tailwind:**
  - **Bootstrap:** su peso se debe sobre todo a código que no se usa.
  - **Tailwind:** saca el diseño del CSS y lo lleva al HTML.
  - **Los dos:** imponen su forma de nombrar las cosas sobre los tokens semánticos del caso.
  - **Además:** el módulo evalúa que sepa construir el sistema, no que configure uno ajeno.
- **Coste:** cero. Todo es de código abierto y funciona sin conexión, porque las dependencias vienen en `node_modules`.
