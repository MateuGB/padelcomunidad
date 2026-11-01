# Documento de decisiones de diseño

> Cada decisión: **qué** elegiste, **por qué**, y **qué alternativa descartaste**.
> Se evalúa la justificación, no la longitud. Máximo 2 páginas en la Fase 1.

## Ruta de trabajo

- [ ] Ruta A · Figma (enlace con permiso de lectura: …)
- [ ] Ruta B · VS Code (diseño directamente en `guia.html`)

## 1. Color (RA1 · CE b)

- Tono de acento de la ficha y cómo has obtenido la escala (50 … 950):
- Por qué el acento de texto/fondo es el 700 en claro y el 400 en oscuro (o el que elijas):
- Tokens semánticos añadidos y para qué:
- Pares de contraste añadidos a `$pares-contraste`:

## 2. Tipografía (RA1 · CE b)

- Familia elegida y por qué (rendimiento, legibilidad, licencia):
- Base y razón en móvil y escritorio. Por qué esas:
- Medida de línea (`max-inline-size` del párrafo en `ch`):

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
