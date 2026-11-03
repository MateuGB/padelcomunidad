# Documento de decisiones de diseño

> Cada decisión: **qué** elegiste, **por qué**, y **qué alternativa descartaste**.
> Se evalúa la justificación, no la longitud. Máximo 2 páginas en la Fase 1.

## Ruta de trabajo

- [ ] Ruta A · Figma (enlace con permiso de lectura: …)
- [x] Ruta B · VS Code (diseño directamente en `guia.html`)

## 1. Color (RA1 · CE b)

- **Tono de acento y escala:** el tono es 352° (OKLCH), un rosa frambuesa. He creado la escala en oklch.com con el tono fijo y la luminosidad del 96 % (50) al 26 % (950). La croma es de ≈ 0,15 en los pasos medios y menor en los extremos, para no salir de la gama sRGB. Solo defino los pasos que uso. *Descartado:* HSL o `color.scale()`, porque dan saltos de luminosidad irregulares y desvían el tono.
- **El 700 en claro y el 400 en oscuro:** el 700 es el paso más claro que supera 4,5:1 sobre blanco (6,52:1), así que sirve para el texto y para el fondo del botón. El 400 solo da 2,66:1. En oscuro es al revés: el 400 da 5,50:1 sobre la superficie. El hover va un paso más lejos del fondo (800 / 300), y el contraste sube a 8,8:1.
- **Tokens semánticos añadidos:** `error` y `error-tenue`, en un rojo de 27° alejado del acento para que «error» no se confunda con «seleccionado». Se usan en el turno ocupado y en el aviso de pago no recibido. El resto viene de la plantilla, con mis primitivos de acento.
- **Pares añadidos a `$pares-contraste`:** `error` sobre `error-tenue` y sobre `superficie`, y `texto` sobre los tres fondos tenues (para el aviso de pago). Todos superan 4,5:1 en los dos temas. La tabla de `guia.html` recoge los contrastes de cada token.

## 2. Tipografía (RA1 · CE b)

- **Familia:** fuentes del sistema (`system-ui`, Segoe UI, Roboto…). Cargan 0 KB, el texto no parpadea al cargar, cada plataforma usa la fuente que mejor renderiza y no hay licencia que registrar. *Descartado:* Google Fonts, porque suma 20–40 KB por peso y una petición a un servidor externo (un problema de privacidad en la UE) sin aportar nada a una app de reservas.
- **Base y razón:** en móvil, 16 px y razón 1,2 (h1 ≈ 33 px); en escritorio, 18 px y 1,333 (h1 ≈ 57 px). Con 16 px iOS no amplía la página al tocar un campo. En móvil, una razón suave evita títulos que ocupan media pantalla. Cambié la razón de escritorio de 1,25 a 1,333 porque el h1 se distinguía poco del h2. Entre los dos anchos, `clamp()` interpola sin media queries. *Descartado:* 1,333 también en móvil, porque el h1 pasaría de 50 px.
- **Medida de línea:** `max-inline-size: 65ch`, que da entre 60 y 75 caracteres por línea, el rango más cómodo para leer. Va en `ch` para que la medida se mantenga si cambia el tamaño de la fuente.

## 3. Espaciado y forma

Mantengo la escala de la plantilla: múltiplos de 4 px (de 4 a 64), escritos en px y publicados en rem para que respeten el zoom del usuario. Los radios son 8 px en los controles y 12 px en las tarjetas; el radio mayor va fuera, para que las esquinas sean concéntricas. **Único cambio:** el borde de `.turno` pasa a 2 px, porque con 1 px los estados seleccionado y ocupado apenas se distinguían. *Descartado:* valores libres por componente, porque se pierde la rejilla de 8.

## 4. Componentes (RA1 · CE f)

| Componente | Estados | Cómo se resuelve cada estado en CSS |
| --- | --- | --- |
| Botón | normal, hover, foco, desactivado, secundario | `&:hover` cambia a `acento-hover`. `&:focus-visible` usa el mixin `foco-visible`. `&:disabled` lleva `opacity` y `cursor: not-allowed`. `.boton--secundario` va fuera del bloque para que su `:hover` quede en (0,2,0). |
| Turno | disponible, seleccionado, ocupado, foco | Es un `<label>` con un radio, y cada estado se lee con `:has()`, sin JS: `:has(:checked)` da borde y fondo de acento y «✓ Seleccionado»; `:has(:disabled)` da borde discontinuo, hora tachada y «✕», sin mostrar la vivienda; `:has(:focus-visible)` da el anillo de foco. Ningún estado depende solo del color. El radio se oculta con `opacity: 0`, no con `display: none`, para que siga accesible por teclado. `:where()` mantiene los `::before` en (0,1,1). |
| Contador de invitados | normal, mínimo, máximo, hover, foco | Dos `<button>` de 44 px con `aria-label` y un `<output>` (0–2, 2,50 € por invitado). En el mínimo y el máximo, el botón correspondiente lleva `disabled`: borde discontinuo y `cursor: not-allowed`. Como `:disabled` va después de `:hover` con la misma especificidad, gana. En el máximo, un texto lo indica. *Descartado:* `<input type="number">`, porque sus flechas son diminutas en el móvil. |
| Aviso de pago | pendiente, pagado, error | `.aviso` con modificadores BEM. Cada uno solo cambia `--aviso-color`, `--aviso-fondo` y `--aviso-icono` (i, ✓, !), así que no se repite código. El estado se indica con icono y título. El error usa `role="alert"` y los otros, `role="status"`. |

## 5. Comparativa de tecnologías (RA1 · CE e · RA2 · CE i)

| Criterio | Figma | Penpot | Diseño en código |
| --- | --- | --- | --- |
| Coste / licencia | Privativo, con un plan gratuito limitado y uno para estudiantes. Dev Mode es de pago. | Libre (MPL 2.0) y gratuito; se puede instalar en un servidor propio. | Gratis: VS Code, Sass y Stylelint. |
| Traslado a código (handoff) | Manual. El código de los plugins no sirve. | Mejor: se basa en flex y grid y exporta tokens. | No hay traslado: la guía son los componentes reales. |
| Mantenimiento del sistema | Dos fuentes que sincronizar. Cómodo para explorar ideas. | Dos fuentes, aunque los tokens ayudan. | Una sola fuente (`_tokens.scss`), con Git y comprobaciones automáticas. |

| Criterio | Sass + CSS propio | CSS nativo sin preprocesador | Bootstrap / Tailwind |
| --- | --- | --- | --- |
| Tokens y temas | Mapas que se convierten en Custom Properties con `light-dark()`, y **contraste comprobado al compilar**. | Los mismos temas, pero sin comprobaciones ni escala calculada. | Imponen sus nombres (`primary`, `dark:`…). Ninguno comprueba el contraste. |
| Peso del CSS final | ≈ 12,7 KB sin minificar. | Similar. | Bootstrap: ≈ 230 KB minificado. Tailwind: ≈ 10–20 KB, pero con su propia herramienta de compilación. |
| Control de la cascada | `@layer`, especificidad máxima de (0,2,0) y BEM, comprobados por Stylelint. | Igual: la cascada no depende de Sass. | Bootstrap suele obligar a usar `!important`. Tailwind lleva el diseño al HTML. |

**Decisión y motivo:** diseño en código (Ruta B) con Sass y CSS propio. El sistema es pequeño y lo diseño y programo yo, así que una segunda fuente en Figma solo añade trabajo y el riesgo de que no coincida. Si tuviera que trabajar con un diseñador, elegiría Penpot, porque es libre y está más cerca de CSS. Sass aporta lo que el CSS nativo no tiene: comprobar el contraste y calcular la escala al compilar. Bootstrap y Tailwind pesan más o sacan el diseño del CSS, e imponen sus nombres sobre los tokens del caso. El coste es cero y todo funciona sin conexión.
