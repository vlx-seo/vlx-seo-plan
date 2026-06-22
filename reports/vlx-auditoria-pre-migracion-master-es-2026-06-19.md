# Auditoría SEO Pre-Migración VLX — REPORTE MAESTRO
**Fecha:** 2026-06-19 · **Auditora:** Laura Ceballos / Software Craft CR
**Objeto:** Rebuild Next.js 15 + Payload CMS (rama `Hans-review` = futuro `dev`), pre-lanzamiento
**Baseline:** `vlx.ai` (WordPress + Yoast, aún en producción) · **Mercados:** USA + Canadá · **Idioma del sitio:** inglés

---

## 1. Veredicto: ADELANTE — con condiciones

**El sitio nuevo está técnicamente más sano que el WordPress que reemplaza.** Renderiza en servidor correctamente, tiene canonicals y redirects casi perfectos, schema más rico, y un rendimiento de homepage dramáticamente mejor. **No hay ningún bloqueador catastrófico.** El lanzamiento es seguro una vez se resuelva una lista corta de arreglos pre-lanzamiento — la mayoría pequeños.

**Salud SEO global: 81/100** (preparación pre-lanzamiento)

| Dimensión | Puntaje | Veredicto |
|---|---|---|
| Técnico (crawl/SSR/canonical) | 88 | ✅ Sólido |
| Arquitectura (diseño) | 88 | ✅ Sólido |
| Migración / redirects | 86 | ✅ Sólido |
| Schema / JSON-LD | 83 | ✅ Bueno (1 riesgo de rating) |
| GEO / IA | 82 | ✅ Bueno |
| Sitemap & robots | 80 | ✅ Bueno (308s en sitemap) |
| Imágenes | 80 | ✅ Bueno |
| Contenido & E-E-A-T | 79 | 🟡 Bueno, mejorando |
| Rendimiento | 76 | 🟡 LCP del hub |
| Activación de enlazado interno | 72 | 🟡 Distribución desigual |

---

## 2. Lo que NO puede dejar de hacerse antes de migrar

Estos son los arreglos obligatorios. Ninguno es catastrófico; todos son concretos y en su mayoría rápidos.

| # | Arreglo obligatorio | Por qué | Esfuerzo | Reporte |
|---|---|---|---|---|
| 1 | **Corregir LCP del hub 7.9s** (`/digital-inspections-software/`) | La página comercial #1 carga más lento que la de WP; la página del término principal no debería salir con LCP 7.9s | M | PERF-P1-1 |
| 2 | **Resolver `aggregateRating` hardcodeado 4.9/20** — usar reseñas reales de Capterra/SoftwareAdvice o eliminarlo | Rating fabricado sin reseñas en página = riesgo de acción manual de Google. Hay reseñas reales en Capterra (usarlas) | S | SCHEMA-P1-1 / LOCAL-1 |
| 3 | **Quitar del sitemap 5 URLs `/blog/category/*` que redirigen** | El sitemap lista URLs que dan 308 → Google las marca "Página con redirección", no las indexa | XS | SR-P1-1 |
| 4 | **Agregar redirects a 2 URLs `/use-case/` que dan 404** | Dos URLs viejas de WP dan 404 duro al lanzar (poco tráfico, pero higiene) | XS | MIG-P0-1 |
| 5 | **Confirmar que todas las landing pages de pauta redirigen** | La inversión en Capterra/G2 cae en 404 si no están mapeadas (~150 sesiones pagas/mes) | S | MIG-P1-1 |
| 6 | **Corregir el H1 de homepage "VerificationInspections"** (sin espacio) | H1 principal malformado (concatenación de animación) | XS | TECH-P1-2 |
| 7 | **Renderizar en servidor el listado de blog + paginación rastreable** | 109 posts; solo la página 1 es rastreable por enlaces → los posts profundos dependen del sitemap | M | TECH-P1-1 / SR-P1-2 |

**Recomendado antes de lanzar (alto valor, no estrictamente bloqueante):**
- Alinear los 4 H1 de industria que son copywriting (construction, safety, hospitality, mining) a keyword-led — los otros 8 ya lo están (CONTENT-P1-1).
- Agregar 6 industrias faltantes al menú y footer (vehicle, construction, safety, hospitality, mining, inspection-companies) — están al fondo del grafo de enlaces internos (LINKING P1).
- Confirmar que los testimonios con nombre estén aprobados por el cliente (CONTENT-P1-2).

---

## 3. Lo que ya está bien hecho (no tocar)

- **Rastreabilidad/SSR:** todo tipo de página renderiza H1 + cuerpo + enlaces en el HTML crudo; Googlebot recibe todo.
- **Canonicals + redirects:** absolutos, con slash, coinciden con el sitemap; 52/54 URLs viejas redirigen en **1 solo salto** limpio para la forma indexada (con slash). El temor previo de "cadenas de 3 saltos" NO se reproduce.
- **Rendimiento de homepage transformado:** LCP 20.5s (WP) → 2.7s (Next).
- **Schema:** CollectionPage en hub, Article en case studies, BreadcrumbList en todo el sitio, HowTo eliminado, SearchAction deprecado removido.
- **robots.txt:** postura de IA deliberada (bots de búsqueda permitidos, de entrenamiento bloqueados) + llms.txt completo.
- **Arquitectura:** el hub ahora enlaza las 18 clusters en el cuerpo (P0 previo resuelto), profundidad de clic 2, anchors con keyword.
- **H1 de industria:** 8/12 ahora son keyword-led (era la gran brecha previa).
- **Redirects de migración:** 284 reglas, 152 derivadas de datos reales de impresiones de GSC.

---

## 4. Checklist pre-lanzamiento (consolidado)

**Bloqueadores / obligatorios:**
- [ ] LCP del hub 7.9s → perfilar + precargar el elemento LCP, re-medir en build de Hans-review
- [ ] aggregateRating → conteos reales de reseñas (Capterra/G2) en página, o eliminar
- [ ] Quitar 5 URLs de categoría que redirigen del `sitemap.ts`
- [ ] Agregar 2 redirects de `/use-case/`
- [ ] Verificar redirects de landing pages de pauta (revisar landing pages pagas en GA4)
- [ ] Corregir espaciado del H1 de homepage
- [ ] SSR del listado de blog + paginación basada en ruta

**Muy recomendado:**
- [ ] 4 H1 de industria → keyword-led
- [ ] 6 industrias al menú + footer
- [ ] Testimonios aprobados por el cliente
- [ ] Imágenes OG propias para las 10 páginas principales

**Al momento del cambio (cutover):**
- [ ] Enviar `sitemap.xml` en GSC; confirmar que el robots.txt de producción = versión Hans-review
- [ ] Monitorear Cobertura de GSC (404/redirección) + clics USA (≥100/28d) + sesiones orgánicas (≥330/28d) por 2-4 semanas
- [ ] Re-correr PSI en el build de producción

---

## 5. Baseline pre-lanzamiento a proteger (congelar)

| Métrica (28d, pre-lanzamiento) | Valor |
|---|---|
| Clics GSC — global / USA / CAN | 341 / 121 / 11 |
| Impresiones GSC — global / USA / CAN | 86,494 / 37,683 / 2,253 |
| Posición promedio GSC — USA / CAN | 24.8 / 15.1 |
| Sesiones GA4 / orgánicas | 2,335 / 389 |

⚠️ Solo ~39% del tráfico es de mercado objetivo (USA+CAN); las impresiones globales están infladas por Vietnam/India/Indonesia. Medir el éxito sobre el segmento USA+CAN.

---

## 6. Estrategia post-lanzamiento (las verdaderas palancas de crecimiento)

La migración **protege y mejora** el sitio, pero rankear por términos comerciales requiere trabajo que el rebuild por sí solo no entrega:

1. **Backlinks / autoridad — la restricción principal.** Bing reporta 0 enlaces entrantes; el dominio tiene ~9 meses. Prioridad: perfiles G2/Capterra/GetApp, directorios de integración (Salesforce/SAP/Oracle), PR digital (plan E4/E5).
2. **Ganar el SERP de marca** para "vlx" (hoy en pos 10.6) — entidad + sameAs + GBP.
3. **Quick wins de striking-distance** — 13 queries de checklists en pos 4-20 con 0 clics; optimizar títulos/CTR.
4. **Rankear el hub** para "digital inspection software" — necesita profundidad (hoy ~700 palabras vs 6,000+ de competidores) + autoridad.
5. **Baseline de visibilidad en IA** — correr los 6 prompts del reporte GEO en ChatGPT/Perplexity/AI Overviews; medir mensual.
6. **Apps + reseñas** — rebrandear listings Visualogyx→VLX, llevar reseñas de Capterra al schema.

---

## 7. Notas de herramientas / brechas de datos
- **El perfil de backlinks, volumen/dificultad de keywords y SERP de competidores en vivo** son un análisis aparte, fuera del alcance de esta revisión pre-migración. Esta auditoría priorizó datos reales de ranking de GSC — lo mejor para el estado actual (sin profundidad de volumen/competidores de terceros).
- **CrUX sin datos de campo** para vlx.ai (bajo el umbral de tráfico) — rendimiento evaluado con datos de laboratorio (PSI) + debería apoyarse en Vercel Speed Insights / PostHog RUM post-lanzamiento.
- **Vercel preview = rama `dev`** (sin los micro-fixes de rendimiento del PR #11) — la auditoría de código corrió sobre `localhost:3000` (Hans-review, exacto); las métricas de laboratorio externas mejorarán algo en el build real.

---

## Anexo — Reportes por dimensión
`AUDIT-MIGRATIONS`, `AUDIT-TECHNICAL`, `AUDIT-SCHEMA`, `AUDIT-SITEMAP-ROBOTS`, `AUDIT-CONTENT`, `AUDIT-GEO`, `AUDIT-IMAGES`, `AUDIT-PERFORMANCE`, `AUDIT-GOOGLE-BASELINE`, `AUDIT-KEYWORDS-BACKLINKS`, `AUDIT-LINKING-ARCH`, `AUDIT-ASO-LOCAL` (todos fechados 2026-06-19, en `/reportes/`). Datos crudos en `/data/`, inventario en `/inventario/`.
