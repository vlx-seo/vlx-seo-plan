# VLX — Comparativa Visual: Local vs Vercel

Comparativa visual lado a lado del sitio **VLX-Marketing** entre:

- **LOCAL** — `http://localhost:3000` (branch `dev`, corriendo sin base de datos)
- **VERCEL** — `https://vlx-website-rebuild.vercel.app` (preview de despliegue)

## Contenido

- [`comparativa-local-vs-vercel.html`](comparativa-local-vs-vercel.html) — informe autocontenido con las capturas embebidas (abrir en el navegador).
- `shots/` — las 12 capturas individuales (PNG/JPEG) por página y versión.

## Páginas comparadas

Home · Producto (`/digital-inspections-software/`) · Pricing · Demo · Blog · Industria Oil & Gas

## Cómo se generaron las capturas

Con Puppeteer (Chromium headless) a 1366px de ancho. Notas técnicas relevantes:

1. Se **descarta el banner de cookies** antes de capturar (bloquea el scroll con `overflow:hidden`, lo que impedía cargar el contenido diferido).
2. Se captura por **regiones del documento** (`clip` + `captureBeyondViewport`) y se unen con `sharp`. No se usa `fullPage` porque duplica el hero/navbar fijo en estas páginas.
3. Se neutralizan elementos `fixed`/`sticky` y se fuerza `opacity:1` para congelar las animaciones de Framer Motion antes de capturar.

> El blog en LOCAL aparece vacío porque su contenido vive en la base de datos (Neon) no disponible localmente; en Vercel sí trae los posts reales.

Generado el 2026-06-24.
