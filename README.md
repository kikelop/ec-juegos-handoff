# EC Juegos — Handoff a desarrollo (v2)

Maqueta HTML+CSS de la sección de Juegos para el hackatón de EINNO, **v2 con capa de retención**. Fiel al Figma al pixel en los 3 breakpoints.

**Diseñado por:** Kike López · Product Design
**Figma:** `vxsBIea26DQWgdLHM5EDpC` · página "Pruebas inno" — frames `859:2755` (desktop 1512) · `886:3082` (tablet 768) · `886:3209` (mobile 375)
**Sistema de diseño:** SDD (`ec.color.v2`) · Confi Serif/Sans · header en Roboto (producción)

---

## Qué hay de nuevo en v2

- **Capa de retención:** cards "Reto diario" (con progreso de 4 dots) y "Puntos totales"
- **Dos secciones de juegos:** "Retos diarios" (4 juegos) y "Para entretenerte" (2 juegos)
- **6 juegos** con paleta pastel propia: Crucigrama, Sudoku, Adivina la palabra, Sopa de letras, Caramelos, Buscaminas
- **Estados de card:** `.game-card--done` (badge check + caption COMPLETADO) vs pendiente
- **Publicidad con variantes por dispositivo** (ver tabla abajo)
- **Header EC de producción** (Menú · Últimas noticias · logo · Iniciar sesión · Suscríbete)

## Estructura del paquete

```
ec-juegos-handoff/
├── README.md
├── index.html         ← landing v2 (responsive, pixel-match 1512/768/375)
├── game.html          ← plantilla de página de juego (v1, sin cambios)
├── styles.css         ← CSS único · tokens en :root
├── fonts/             ← 12 woff2 de Confi Serif + Confi Sans
└── svg/               ← iconos de juego (87×87) + logo-ec.svg (206×18)
```

Servir en local: `python3 -m http.server` o abrir `index.html` directo.

---

## Paleta pastel de cards (derivada de ec.color.v2)

Mismo tono que el primitive, saturación/luminosidad llevadas a franja pastel. Texto de card en `#0F2124` (core/azul/900), contraste >8:1 (AA).

| Card | Hex | Derivación |
|---|---|---|
| Acero (Crucigrama) | `#D2D8DD` | = `core/acero/150` (token existente) |
| Lavanda (Sudoku) | `#CFCAE9` | ⚠️ fuera de paleta EC — requiere primitive nuevo (Brand/Pablo) |
| Teal (Adivina la palabra) | `#C3E3E9` | de `corporativo/azul-oscuro` |
| Dorado (Sopa de letras) | `#ECE0BC` | de dorado dataviz `#E9C54A` ⚠️ primitive propuesto, sin aprobar |
| Rosa (Caramelos) | `#EBC6DF` | orchid suavizado |
| Oliva (Buscaminas) | `#DCDBB1` | de oliva dataviz `#767324` ⚠️ primitive propuesto, sin aprobar |

**Deuda registrada:** estos pasteles no existen como tokens en `ec.color.v2`. Propuesta pendiente: `semantic/juegos/*` en el Status Board del SDD.

## Otros tokens

| Rol | Hex | Origen |
|---|---|---|
| Texto principal | `#121212` | semantic/text/primary |
| Texto secundario | `#737373` | semantic/text/secondary |
| Texto footer | `#687476` | corporativo/piedra |
| Borde sutil / dots | `#BFC7CE` | core/acero/300 |
| Publi fondo / slot / label | `#F9F9F9` / `#EFEFEF` / `#A3A3A3` | header producción |
| CTA Suscríbete | `#F2C001` | header producción |

## Tipografía

| Elemento | Spec |
|---|---|
| Hero "Juegos" | Confi Sans Bold 32 (desktop) · 22 (tablet/mobile), subrayado 4px |
| Headings de sección | Confi Sans Bold 22 + línea acero 1px |
| Subtítulo hero | Confi Sans Regular 18/1.33 |
| Card retención: título / valor | Confi Serif Bold 24 (ls −0.3) / 32 |
| Card retención: subtítulo | Confi Sans Regular 15/21 |
| Card juego: título | Confi Serif Bold 28/26.4 (ls −0.4) |
| Card juego: caption | Confi Sans Bold 13/16.5, ls 1px, uppercase |
| Footer | Confi Sans Regular 14/22 |
| Header | Roboto 15 (Google Fonts) |

---

## Publicidad — variantes por dispositivo

Placeholders con fondo `#F9F9F9`, slot `#EFEFEF` y label "publicidad". El ad-server rellena el `.ad__slot`.

| Variante | Desktop | Tablet | Mobile |
|---|---|---|---|
| **Billboard** (top) | 1012×303 · slot 980×250 | 768×303 · slot 736×250 | 320×263 · slot 288×210 |
| **Doble roba** | 368×653 · sidebar sticky | 348×653 · in-flow entre secciones | 332×653 · in-flow entre secciones |

Posiciones: billboard tras el header en desktop/tablet; en **mobile baja tras el bloque de retención**. Doble roba: columna derecha en desktop (sticky), centrada entre "Retos diarios" y "Para entretenerte" en tablet/mobile.

## Breakpoints

| | Corte | Contenido | Grid juegos | Retención |
|---|---|---|---|---|
| Desktop | ≥1200px | 1152 (760 + 368 sidebar) | 2 col · cards 368 | 760 + 368, con dots |
| Tablet | 768–1199px | 720 | 2 col · cards 348 | mitades 348+348, con dots |
| Mobile | <768px | 343 | 1 col · cards 343 | apilada, compacta, **sin dots** |

Pixel-match verificado contra los frames de Figma a 1512 / 768 / 375 (alturas ±25px).

---

## Accesibilidad

- Estructura semántica: `header / section / footer`, cards como `<a>`, headings jerarquizados
- Dots de progreso con `role="img"` + `aria-label` ("1 de 4 retos completados")
- Badge de completado `aria-hidden` — el estado lo da el caption COMPLETADO
- Contraste AA verificado en los 6 fondos pastel (>8:1 con `#0F2124`)
- Mobile oculta elementos del header con CSS (`display:none`), candidatos a menú colapsado en producción

## Pendientes para producción

- [ ] Conectar el ad-server real en los `.ad__slot` (respetando las variantes por breakpoint)
- [ ] Estados de retención dinámicos (dots, contador 1/4, puntos) — la maqueta lleva el estado del Figma
- [ ] Copy del Reto diario por estados: 0/4 "Completa los 4 juegos…", n/4 "Te quedan…", 4/4 "Reto completado · +10 puntos"
- [ ] URL canónica de juego (`/juegos/crucigrama` vs querystring) — los href usan querystring
- [ ] OG image / favicon
- [ ] Decidir tokens `semantic/juegos/*` con Brand (deuda de paleta)

Dudas → kike.lopez@elconfidencial.com
