# PádelComunidad 2.0 · Plantilla de partida

Proyecto integrador de **Diseño de Interfaces Web (0615) · 2.º DAW**.
Todo el alumnado parte de esta plantilla, use o no Figma.

## Puesta en marcha (2 minutos)

Requisitos: **Node.js 12.22 o superior** y **VS Code**. Compruébalo con `node -v` y `npm -v`.

Las dependencias (Sass y Stylelint) ya vienen dentro de la carpeta `node_modules`: **no hace falta ejecutar `npm install`** ni tener conexión a internet.

```bash
npm run dev        # compila scss/ → css/estilos.css y vigila cambios
```

Abre `guia.html` con **Live Preview** (clic derecho → *Show Preview*) o con tu navegador.
Al abrir la carpeta, VS Code te propondrá las extensiones recomendadas: acéptalas.

| Script | Qué hace |
| --- | --- |
| `npm run dev` | Compila en modo vigilancia mientras trabajas |
| `npm run build` | Compila una vez |
| `npm run lint` | Revisa el SCSS con las reglas del proyecto |
| `npm run check` | `build` + `lint`. **Debe terminar sin errores antes de cada commit** |

## Estructura

```text
scss/
├── main.scss          Hoja maestra: solo @use, en orden
├── _capas.scss        Orden de la cascada con @layer
├── abstracts/         No genera CSS: tokens, funciones, mixins, verificador de contraste
│   └── _tokens.scss   ← ÚNICA fuente de verdad del diseño. Empiezas aquí.
├── tokens/            Convierte los tokens Sass en Custom Properties (:root) y gestiona el tema
├── base/              Reset y estilos de etiquetas HTML (sin clases)
├── layout/            Estructura de página (contenedor, rejillas)
├── components/        Bloques BEM: botón (resuelto), turno (por hacer), guía
└── utilities/         Clases de utilidad; única capa donde se permite !important
css/estilos.css        Hoja compilada: la única que enlazan los HTML. No la edites a mano.
guia.html              Guía de estilo viva
docs/                  Ficha de caso, decisiones de diseño, registro de licencias
```

## Reglas que comprueba la plantilla

- **Contraste**: Sass detiene la compilación si un par de `$pares-contraste` no llega al mínimo, en tema claro u oscuro.
- **Stylelint**: hexadecimales solo en `_tokens.scss`, especificidad máxima (0,2,0), nada de IDs ni `!important`, BEM, anidado ≤ 3, prohibido `@import`.

## Ruta A · Vengo de Figma

1. En Figma, abre el panel de **Variables** (*Local variables*).
2. Copia cada color a `$primitivos` y cada alias a `$tema` en `scss/abstracts/_tokens.scss`.
3. Copia tamaños de texto, espaciados y radios **en px**: la plantilla los convierte a rem.
4. No copies código de plugins de Figma: genera posiciones absolutas y medidas fijas.

## Commits

Un commit al final de cada sesión: `S01: tokens de color del caso 07`.
