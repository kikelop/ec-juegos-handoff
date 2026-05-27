# EC Juegos — Handoff a desarrollo

Maqueta HTML+CSS de la sección de Juegos para el hackatón de EINNO. Fiel al Figma (frames `612:2539`, `612:8751`, `612:9207`, `612:9531`) y al Sistema de Diseño de Suscripciones (Marshall 3 + Confi Serif/Sans).

**Diseñado por:** Kike López · Product Design
**Sistema de diseño:** `el-confidencial/sdd/landings-ui-kit-app/public/CONTEXT.md`
**Figma file:** `vxsBIea26DQWgdLHM5EDpC`

---

## Estructura del paquete

```
ec-juegos-handoff/
├── README.md          ← este archivo
├── index.html         ← landing de juegos (4 cards)
├── game.html          ← plantilla de página de un juego (con slot gris donde encaja el motor)
├── styles.css         ← CSS único · single source of truth de tokens
├── fonts/             ← 12 woff2 de Confi Serif + Confi Sans
└── svg/               ← 4 iconos (crucigrama / sudoku / wordle / autodefinidos)
```

Para verlo en local, abrir `index.html` directo en el navegador, o servir la carpeta con cualquier server estático:

```bash
npx serve ec-juegos-handoff
# o
python3 -m http.server --directory ec-juegos-handoff
```

---

## Tokens usados (todos del SDD Marshall 3)

| Categoría | Token | Hex |
|---|---|---|
| Texto principal | Semantic/Text/Primary | `#121212` |
| Texto secundario | Semantic/Text/Secondary | `#737373` |
| Borde sutil | Core/Steel/200 | `#bfc7ce` |
| Fondo card Crucigrama | Core/Neutral/0 | `#ffffff` |
| Fondo card Sudoku | Core/Neutral/50 | `#efefef` |
| Fondo card Wordle | Core/Steel/150 | `#d2d8dd` |
| Fondo card Autodefinidos | Core/Bronze/400 | `#dba281` |
| Botón "Jugar" (bg) | Core/Blue/900 | `#0f2124` ⚠️ |
| Botón "Jugar" hover | Core/Blue/700 | `#1d4044` |
| Enlace | Semantic/Link/Default | `#2380be` |

**Tipografía** — `Confi Serif Bold` para `Juegos` y nombres de juego; `Confi Sans` (Regular 400 / Medium 500 / Bold 700) para todo lo demás. Tamaños:

| Elemento | Token / tamaño |
|---|---|
| Logo "ElConfidencial" | Confi Serif Bold 24 |
| Hero "Juegos" | Confi Serif Bold 40 (h2) · mobile 32 (h3) |
| Subtítulo hero | Confi Sans Regular 15 (parrafo) |
| Nombre de juego en card | Confi Serif Bold 28 ⚠️ |
| Botón | Confi Sans Medium 15 |
| Footer | Confi Sans Regular 14 (13 mobile) |

⚠️ Las dos celdas marcadas son **deuda registrada del SDD**:

1. **Botón "Jugar" oscuro full-width** — el SDD canónico tiene Button.featured con `bg Steel/200`. El Figma de esta landing usa un botón oscuro tipo "Continuar" que en el SDD aparece como deuda en §15.11 y §6.5. He mapeado a `Blue/900` para no introducir un literal. Pendiente abrir D nueva para formalizar el variant.
2. **Confi Serif Bold 28** para el título del juego está fuera de la escala oficial §5.1 (que solo cubre 32 / 24 / 20). Si la sección de juegos crece, candidato a token nuevo `game-title/lg`.

---

## Breakpoints

| | Anchura | Comportamiento |
|---|---|---|
| Desktop | ≥ 900px | 2 columnas: cards 2×2 izquierda, aside ad 300×600 derecha |
| Tablet / mobile | < 900px | 1 columna apilada, aside ad pasa a billboard horizontal |
| Mobile cerrado | < 600px | Hero h2 → h3, padding lateral 16, cards 1×4 |

El sticky del aside derecho está activado en desktop (`position: sticky; top: 24px`).

---

## Componentes y selectores

- `.site-header` — cabecera con logo centrado
- `.hero` — bloque título + subtítulo
- `.ad` + `.ad__slot--billboard` / `.ad__slot--sidebar` — placeholders publicidad. El `<div class="ad__slot">` está vacío para que el ad-server lo rellene. El fondo rosa (`#ffa7a7`) es solo visual del placeholder y desaparece cuando se inyecte el iframe del ad.
- `.main__row` — grid principal con aside (1fr · 320px)
- `.games-grid` — grid 2×2 de juegos
- `.game-card` — card de juego (head coloreado + actions)
  - `.game-card__head--{crucigrama|sudoku|wordle|autodefinidos}` — variante de fondo
- `.btn` / `.btn--dark` — botones secundario (outline) y primario (oscuro full-width)
- `.game-slot` — rectángulo gris donde encaja el motor de juego en `game.html`

---

## Dónde encaja el juego (`game.html`)

El motor del juego va dentro de `<section class="game-slot">`. Tres opciones según cómo lo monte el equipo:

```html
<!-- A · iframe a motor externo (Amuse, etc.) -->
<section class="game-slot">
  <iframe src="https://juegos.elconfidencial.com/crucigrama/2026-05-27" loading="lazy" allowfullscreen></iframe>
</section>

<!-- B · Componente JS montado por nosotros -->
<section class="game-slot" id="game-root" data-game="crucigrama" data-date="2026-05-27"></section>
<script type="module" src="./crucigrama.js"></script>

<!-- C · Canvas HTML5 -->
<section class="game-slot">
  <canvas id="game-canvas" width="800" height="1000" aria-label="Crucigrama"></canvas>
</section>
```

El slot ya tiene:
- Fondo `Neutral/100` (`#e2e2e2`)
- Radio 8px
- Aspect-ratio 4:5 mobile, min-height 600px desktop

Si necesitas que el iframe ocupe el slot al 100%, añade en su CSS:
```css
.game-slot iframe { width: 100%; height: 100%; border: 0; display: block; }
```

---

## Accesibilidad

- Estructura semántica: `<header>`, `<main>`, `<aside>`, `<footer>`, `<article>` por card
- `lang="es"` en el `<html>`
- Cada card lleva `aria-hidden="true"` en el span del icono — el nombre del juego en el `<h2>` ya identifica la card para lectores de pantalla
- Botones primario/secundario son `<a>` por defecto (navegación), no `<button>`. Si pasan a abrir un modal sin cambiar de URL, cambiarlos a `<button type="button">`
- Foco visible: heredado del browser (sin overrides). Si se quiere ring custom, añadir `:focus-visible` con outline 2px en `--text-primary`
- Contraste verificado WCAG AA en todos los fondos: el texto negro sobre Bronze/400 da 4.65:1 ✓, sobre Steel/150 da 12.5:1 ✓
- Touch targets: botones 48px altura ✓ (mínimo recomendado 44px)

---

## Pendientes para producción

- [ ] Cambiar el subtítulo placeholder del hero (`"Elige tu plan y accede de manera ilimitada y sin permanencia"` es el default de Landings — no encaja para Juegos)
- [ ] Conectar el ad-server real en los dos `.ad__slot`
- [ ] Decidir si las cards llevan **badge "Nuevo"** o **streak indicator** (la versión actual del Figma no lo tiene)
- [ ] Si el equipo opta por el patrón de **racha diaria** que comentamos en el brainstorm, añadir un componente `.streak` debajo del grid (no incluido en este handoff porque no está en el Figma final)
- [ ] Conectar el navegado: ¿`/juegos/crucigrama` o `/juegos?juego=crucigrama`? Los `href` actuales usan querystring por simplicidad — cambiar a la URL canónica que decida el equipo de backend
- [ ] **Logo "ElConfidencial"**: ahora es texto Confi Serif Bold. Si existe SVG oficial del logo, sustituir en el `<a class="site-header__logo">`
- [ ] **OG image / favicon**: no incluidos

---

## Diferencias con el SDD de Suscripciones (lo que cambia)

| | SDD Landings | Juegos |
|---|---|---|
| Botón principal | `Button.featured` bg `Steel/200`, label "Quiero suscribirme" | `.btn--dark` bg `Blue/900` full-width, label "Jugar" |
| Card head | Padding 20-24, radius 3 (PlanCard) | Padding 40×24, radius 8, fondo coloreado por juego |
| Tamaño tipográfico del título | h4 24 / h5 20 (PlanCard) | 28 ⚠️ fuera de escala |
| Grid | 3 columnas (mensual / anual destacada / cobrand) | 2×2 + aside ad |

Lo demás (header, hero, footer, ad placement, breakpoints, espaciados) sigue los patrones del SDD de Landings.

---

## Para iterar

El CSS está en un único fichero, todos los tokens en `:root` arriba. Cambiar un color o tipografía es un único punto.

Si surgen componentes nuevos (streak, badge, leaderboard, etc.), mantenedlos en este mismo `styles.css` siguiendo la convención BEM ligera que se ve aquí (`.game-card`, `.game-card__head`, `.game-card__head--variant`).

Dudas → kike.lopez@elconfidencial.com
