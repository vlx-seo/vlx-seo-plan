# VLX SEO Audit — Plan de Correcciones 2026-06-19

**Creado:** 2026-06-19 · **Actualizado:** 2026-07-01 (integrado review dev.vlx.ai) · **Dominio:** vlx.ai · **Auditor:** Laura Ceballos / Software Craft CR  
**Rama verificada:** `origin/Hans-review` · **Score actual:** Ver Dashboard de Estado  
**Fuente:** Inspección de código + análisis de arquitectura (commit actual)

---

## Cómo usar este archivo

- Marcar tareas completadas: `- [ ]` → `- [x]`
- Cada tarea tiene un **Commit label** — usarlo en el mensaje del commit para trazabilidad
- Si algo se rompe, buscar el commit label para identificar qué cambio causó el problema
- Las tareas dentro de cada Wave son independientes y se pueden hacer en paralelo
- Los Waves deben hacerse en orden (Wave 0 antes de Wave 1, etc.)

### Protocolo de fin de sesión

Al terminar cada sesión de trabajo, Claude debe:

1. **Actualizar este archivo** — marcar tareas completadas, agregar notas sobre desvíos o problemas encontrados
2. **Proveer resumen de sesión** — listar qué cambió (archivos modificados, commits realizados, problemas encontrados)
3. **Proveer prompt de continuación** — un prompt copy-paste para la siguiente sesión que incluya:
   - Desde qué Wave/tarea retomar
   - Contexto necesario (blockers, decisiones tomadas, trabajo parcial)
   - Los pasos de verificación que aún faltan

### Session Log

| Sesión | Fecha | Tareas Completadas | Notas |
|--------|-------|-------------------|-------|
| 0 | 2026-06-19 | Audit creado | Inspección completa de codebase rama Hans-review |
| 1 | 2026-07-01 | Review dev.vlx.ai integrado | Ver **SEGUIMIENTO 2026-07-01** abajo. 4 must-fix confirmados resueltos · 1 sin resolver (LPs de pauta) · 2 parciales (LCP, blog SSR) · discrepancia oil-and-gas re-abierta · +6 hallazgos nuevos · errores visuales de blog clasificados pre/post-lanzamiento |

---

## 🔄 SEGUIMIENTO 2026-07-01 — Review dev.vlx.ai (post-auditoría · pre-lanzamiento)

**Fecha:** 2026-07-01 · **Auditora:** Laura Ceballos / Software Craft CR
**Objeto:** Nueva versión del sitio en `https://dev.vlx.ai` (protegido con AWS ALB + Amazon Cognito)
**Baseline:** esta misma auditoría (2026-06-19 · score 81/100 · "Adelante con condiciones")
**Fuente MD completa:** `reports/REVIEW-DEV-VLX-AI-2026-07-01.md`

> Esta sección **integra** el seguimiento del 2026-07-01 sobre la auditoría del 2026-06-19. No reemplaza el contenido previo: marca qué se corrigió, qué sigue abierto y qué se identificó nuevo. Los IDs entre paréntesis refieren a los blockers/tareas del dashboard (`index.html`) y de la Sección 2.

### Nota metodológica (leer primero)
- **Acceso:** `dev.vlx.ai` está tras AWS ALB + Cognito (OAuth2). El navegador ya tenía sesión activa; no hizo falta probar credenciales.
- **Herramientas externas bloqueadas:** PageSpeed / Lighthouse / CrUX / crawlers públicos **NO** pueden auditar esta URL (sin cookie de Cognito). Toda métrica de performance de esta pasada viene de Navigation Timing API medido manualmente en el navegador autenticado, no de Lighthouse.
- **GSC / GA4:** `dev.vlx.ai` no es propiedad verificada — sin baseline de tráfico real todavía (pre-lanzamiento).
- **🔴 Duda de rama (validar con Hans/Abe):** el H1 de homepage ahora dice *"The AI-Native Enterprise Platform for [Inspections/…]"* (typewriter), un posicionamiento distinto al *"Run Your Inspection Business on VLX"* visto el 19-jun. Puede ser evolución de `Hans-review` o una rama/ambiente distinto. **Confirmar qué rama está desplegada en dev.vlx.ai** antes de dar por buena la comparación 1:1.
- **Método:** crawler propio dentro del navegador autenticado (`fetch()` hereda la cookie) sobre las **185 URLs del sitemap** + pruebas puntuales de URLs viejas de WordPress.

### Estado de los must-fix del 2026-06-19
| Ítem (ID) | Estado 2026-07-01 | Evidencia |
|---|---|---|
| Hub LCP ~7.6s (AUD-1) | 🟡 Probablemente resuelto — **falta PSI** | Navigation Timing en `/digital-inspections-software/`: TTFB 82ms, load 354ms (órdenes de magnitud mejor). LCP exacto no verificable (PSI bloqueado por Cognito). **Sigue abierto hasta correr PSI real.** |
| aggregateRating fabricado (AUD-2) | ✅ Confirmado resuelto | Barrido de las 185 páginas: `aggregateRating` no aparece en ninguna (lo quitaron). |
| Sitemap listaba 5 categorías con 308 (AUD-3) | ✅ Confirmado resuelto | Las 8 URLs de categoría del sitemap dan 200 directo. |
| 2 `/use-case/` con 404 (AUD-4) | ✅ Confirmado resuelto | Ambas → 200 (insurance / financial-services), destinos recomendados. |
| H1 homepage sin espacio (AUD-5) | ✅ Resuelto (ver duda de rama) | HTML actual: "The AI-Native Enterprise Platform for Inspections". |
| Landing pages de pauta (B3) | ❌ **Sigue abierto** | Las 13 LPs del `AUDIT-MIGRATIONS-EN-2026-05-28` siguen 404; `/lp/` está en robots.txt (disallow) pero **sin contenido**. Único bloqueador crítico sin resolver. |
| Blog SSR + paginación (B2) | 🟡 Parcial — regresión nueva | 116 posts SSR OK, pero la paginación principal se rompió de otra forma (ver **NUEVO-1**). |
| robots.txt Applebot allow (Sec.2 B8) | ✅ Confirmado | Postura IA intacta: Perplexity / OAI-SearchBot / Applebot permitidos; GPTBot / ClaudeBot / CCBot / Amazonbot bloqueados. |
| OG images (Sec.2 B5 / tarea A10) | 🟡 Parcial | Las 10 top se hicieron (A10), pero **46/185** páginas siguen sin `og:image` (ver **NUEVO-3b**). |
| Redirect `/oil-and-gas/` (dashboard B5) | ⛔ **Re-abierto — error en última versión de dev** | El dashboard lo marcaba resuelto (1 hop → 200), pero en dev.vlx.ai (2026-07-01) `/oil-and-gas/` y `/financial-institutions/` devuelven **404** (ver **NUEVO-2**). Se actualizó B5 en el dashboard a "abierto". |

**Resumen must-fix:** 4 confirmados resueltos · 1 sin resolver (LPs de pauta) · 2 parciales (LCP, blog SSR/paginación) · 1 re-abierto (oil-and-gas 404).

### Hallazgos NUEVOS (no estaban en la auditoría del 19-jun)
- **NUEVO-1 · 🔴 Pre-lanzamiento — Paginación principal del blog contradictoria.** El sitemap lista `/blog/page/2…10/` (ruta limpia), pero esas 9 URLs redirigen a `/blog/?page=N`, que tiene `noindex,nofollow` + canonical → `/blog/`. Señal contradictoria + presupuesto de rastreo desperdiciado. La paginación de **categorías** sí quedó bien (200, index/follow, canonical propio). **Fix:** quitarlas del sitemap **o** servir contenido real en esa ruta (no redirigir a `?page=N` con noindex).
- **NUEVO-2 · 🟡 Post-lanzamiento (higiene, re-verificar) — 2 slugs viejos de WordPress sin redirect.** `/oil-and-gas/` y `/financial-institutions/` → **404** (deberían → `/digital-inspections-software/oil-gas/` y `/financial-services/`). **Contradice** el estado "resuelto" de B5 en el dashboard → B5 re-abierto. Tráfico histórico bajo, pero es higiene de migración.
- **NUEVO-3 · 🟡 Post-lanzamiento — Títulos/meta fuera de rango.** 39/185 con `title` vacío o >60c; 47/185 con `meta description` vacía o >160c.
- **NUEVO-3b · 🟡 Post-lanzamiento — 46/185 sin `og:image`.** 12 páginas de industria/app, 11 posts de blog y 23 más (pricing, integrations, demo, checklists index, whats-new, security, developers…). Relaciona con Sec.2 B5 / tarea A10.
- **NUEVO-4 · 🟢 Positivo — Compare pages ganaron schema.** `/compare/vlx-vs-safetyculture/` y `/compare/vlx-vs-goaudits/` pasaron de 0 schema a `Organization + WebSite + BreadcrumbList`. Falta schema específico de comparación/producto.
- **NUEVO-5 · ⚪ Post-lanzamiento (baja) — Íconos decorativos sin `alt`.** 32/37 imágenes del hub sin atributo `alt` (íconos SVG junto a los links de industria). Ideal: `alt=""` explícito.
- **NUEVO-6 · ⚪ Info — Sitemap creció 175 → 185 URLs.** 10 URLs nuevas desde el inventario del 19-jun (solo el conteo, no el detalle línea por línea).

### Errores visuales en blog posts — clasificados (fuente: *GTM – VLX Marketing Site – Blog Posts Review*)
> Solo se listan errores **visuales** (render / estilos / assets). Los de **HTML/estructura** (list structure, encabezados, meta) se excluyen por alcance y se corrigen aparte; además, corregir la estructura de listas depende del builder (pendiente). ~46 páginas afectadas.

**🚫 Blockers pre-lanzamiento** (dañan UX / credibilidad / SEO al lanzar):
- **V1 — Imágenes rotas (12 págs):** ej. construction-industry-trends, hotel-room-housekeeping, why-audits-failed, difference-qms-eqms.
- **V3 — Contenido embebido/dinámico no carga (6 págs):** best-gemba-walk, sb-326-balcony-laws, hotel-maintenance, why-container-inspections, food-safety-checklist, y la "Sample Incident Report Template" de how-to-write-an-incident-report.
- **V4 — CTAs rotos (2 págs):** what-to-look-for-when-renting-a-houses (no cargan links/estilos); photo-authentication-software (CTA final no funciona).
- **V5 — H1 muestra `&amp;` literal (7 págs):** on-site-inspection-methods, inspection-software-that-prevents-fraud, financial-software-that-cuts-risk, 7-modern-inspection-methods, truck-inspection-checklist, how-to-write-an-incident-report, why-supply-chain-management-software-essential.
- **V8 — Tabla no carga todos los datos (1 pág):** "Sample Forklift Daily Inspection Form" en el checklist de forklift (+ primer ítem de lista vacío).

**🕒 Se pueden corregir después del lanzamiento** (cosmético / menor impacto):
- **V2 — Íconos no cargan (2 págs):** 6s-lean-audit, brc-audit.
- **V6 — Estilos de FAQ/listas no cargan (~14 págs):** iso-9001, food-safety, mold-inspection, house-insurance, hotel-maintenance, financial-software-that-cuts-risk, etc. (contenido legible, solo sin estilos).
- **V7 — Espaciado / texto mal asentado (~8 págs):** understanding-qapi, boiler-maintenance, steel-pipes-oil-gas, pre-drywall, health-inspection-restaurants, etc.
- **V9 — Otros defectos de render (4 págs):** texto no alineado con listas (strengthening-trust-…lending, evolution-of-audits); display roto con contenido dentro de `<!Doctype html>` (virtual-inspections-shaping-the-future — render break genuino); etiquetas `<code>` visibles (seeing-risk-before-it-spreads).

### Lo que se confirma sólido (sin cambios para mal)
- Sitemap 100% canonicalizado a `vlx.ai` (dominio de producción) aunque se sirve desde `dev.vlx.ai`.
- robots.txt con postura IA deliberada intacta (ver AUD/Sec.2 B8).
- SSR funcionando: todas las páginas devuelven HTML completo con H1/schema/links sin necesitar JS (fetch crudo = lo que ve Googlebot).

### Próximos pasos (por prioridad)
1. **Confirmar con Hans/Abe** qué rama/ambiente es dev.vlx.ai (el copy de homepage cambió vs. 19-jun).
2. **[BLOQUEADOR] Arreglar las 13 landing pages de pauta** (B3) — único bloqueador crítico abierto; con inversión activa = dinero en 404.
3. **[Pre-lanzamiento] Corregir `/blog/page/N/`** (NUEVO-1) — quitar del sitemap o servir contenido real (no redirigir a `?page=N` con noindex).
4. **[Pre-lanzamiento] Errores visuales de blog** V1 / V3 / V4 / V5 / V8 (imágenes, embeds, CTAs, `&amp;` en H1, tabla).
5. **[Post-lanzamiento] Agregar redirects** `/oil-and-gas/` y `/financial-institutions/` (NUEVO-2 · B5 re-abierto).
6. **[Post-lanzamiento] Rellenar 46 `og:image`** (priorizar 12 industria), pasada de títulos/meta (~40-47 págs), y errores visuales V2 / V6 / V7 / V9.
7. Cuando el sitio sea accesible sin Cognito, **correr PSI/Lighthouse real** en el hub para confirmar el LCP (AUD-1).

### 🛠️ Prompt para el Claude de Hans — fixes urgentes pre-lanzamiento
> Copiar-pegar el bloque de abajo en una sesión de Claude Code sobre el repo **VLX-Marketing**. También disponible como archivo suelto: `reports/prompt-hans-technical-fixes-2026-07-01-en.md`. Hacer primero los **blockers pre-lanzamiento**; la lista post-lanzamiento puede esperar.

```text
# VLX Pre-Launch URGENT Fixes (2026-07-01 follow-up) — for Hans/Abe + their Claude

You are a senior dev in the VLX-Marketing repo (Next.js 15 App Router, src/app/(frontend), Payload CMS, Tailwind, Framer Motion). The site is PRE-LAUNCH on dev.vlx.ai (behind AWS ALB + Cognito); production is still WordPress. Market: USA + Canada, English. Implement each fix, verify, and report. Ask before destructive changes. These are the URGENT items from the 2026-07-01 review of dev.vlx.ai.

STEP 0 — Confirm the deployed branch. The homepage H1 on dev.vlx.ai now reads "The AI-Native Enterprise Platform for [Inspections/…]" (typewriter), different from the Hans-review branch H1 "Run Your Inspection Business on VLX". Confirm which branch/environment is deployed to dev.vlx.ai before assuming file paths.

PRE-LAUNCH BLOCKERS (must fix before launch):

1. [CRITICAL] Paid landing pages still 404. The 13 paid LPs from AUDIT-MIGRATIONS-EN-2026-05-28 (Capterra / G2 / LinkedIn: /landing-inspection-software-capterra-a/, /landing-inspection-software-g2/, /landing-inspections-software-linkedin/, etc.) return 404 on the new site. /lp/ is disallowed in robots.txt but has no content. Per campaign, either create a dedicated lander (/lp/[campaign]/) OR 301 the old URL to the best-matching page — do NOT 404 paid clicks. Wire redirects in next.config.ts.

2. [PRE-LAUNCH] Blog main pagination sends contradictory signals. sitemap.ts lists /blog/page/2…10/ (clean paths), but those redirect to /blog/?page=N, which is noindex,nofollow + canonical → /blog/. Either (a) remove /blog/page/N/ from the sitemap, or (b) serve real server-rendered content at /blog/page/N/ (index,follow, self-canonical). Category pagination already works — mirror it. Verify each /blog/page/N/ returns 200 index/follow, or is absent from the sitemap.

3. [PRE-LAUNCH] Old WordPress slugs 404. /oil-and-gas/ and /financial-institutions/ return 404. Add redirects in next.config.ts → /digital-inspections-software/oil-gas/ and /digital-inspections-software/financial-services/.

4. [PRE-LAUNCH · verify] Hub LCP. Audit 2026-06-19 measured 7.9s; the 2026-07-01 check saw TTFB 82ms / load event 354ms (likely fine) but could NOT run PSI (Cognito wall). Once dev is reachable without Cognito (or on a preview URL), run mobile Lighthouse/PSI on /digital-inspections-software/ and confirm LCP < 2.5s.

5. [PRE-LAUNCH] Blog post VISUAL errors (source: GTM – VLX Marketing Site – Blog Posts Review). Fix rendering/assets, not HTML structure:
   - Broken images (12 pages): e.g. construction-industry-trends, hotel-room-housekeeping, why-audits-failed, difference-qms-eqms. Fix asset paths / hosting so images load.
   - Embedded/dynamic content not loading (6): best-gemba-walk, sb-326-balcony-laws, hotel-maintenance, why-container-inspections, food-safety-checklist, and the "Sample Incident Report Template" on how-to-write-an-incident-report.
   - Broken CTAs (2): what-to-look-for-when-renting-a-houses (CTAs load no links/styles); photo-authentication-software (end CTA doesn't work).
   - H1 shows a literal "&amp;" (7): on-site-inspection-methods, inspection-software-that-prevents-fraud, financial-software-that-cuts-risk, 7-modern-inspection-methods, truck-inspection-checklist, how-to-write-an-incident-report, why-supply-chain-management-software-essential. Fix the double-encoding of & → &amp; in the post title/metadata render.
   - Table missing data (1): forklift checklist "Sample Forklift Daily Inspection Form" doesn't load all rows; its lists also have an empty first item.

WHEN DONE (pre-launch gate):
1. npm run build → 0 errors.
2. curl -I the 13 paid LP URLs + /oil-and-gas/ + /financial-institutions/ → 301/200 (no 404).
3. Every /blog/page/N/ in the sitemap → 200 index/follow, or removed from the sitemap.
4. Spot-check the visual-error pages render correctly (images, embeds, CTAs, H1 text, table).
5. Report done vs. needs-decision. Do NOT launch until 1–3 and 5 pass.

POST-LAUNCH (can wait — do NOT block launch):
- Icons not loading (6s-lean-audit, brc-audit); FAQ/list styles not loading (~14 pages); bad spacing (~8 pages); other render defects (text alignment; content inside a <!Doctype html> on virtual-inspections-shaping-the-future; visible <code> tags on seeing-risk-before-it-spreads).
- Titles/meta out of range (39 titles empty/>60c, 47 meta empty/>160c); 46/185 pages missing og:image (prioritize the 12 industry pages); decorative hub icons missing alt.
- Note: list-structure HTML fixes depend on the site builder (pending) — out of scope here.

SEO note (Laura): 308 = 301 for Google — don't swap those. Prioritize the paid LPs, the blog-pagination signal, and the visual breakage.
```

---

## Kickoff Prompt

Copy-paste para iniciar la primera sesión de implementación:

```
Lee docs/seo/VLX-AUDIT-REVIEW-2026-06-19.md y ejecuta Wave 0 (tareas B1, B2, B3, B6, B7, B8).
Estos son los blockers críticos que deben resolverse antes de cualquier campaña.

Contexto clave:
- Rama: Hans-review
- Stack: Next.js 15 App Router + Payload CMS + Tailwind + Framer Motion
- Los testimonios placeholder "QUOTE: needs final copy" están en producción — requieren aprobación del cliente
- HowTo schema está deprecated desde 2024; eliminar de las 10 páginas de integración
- Hero opacity:0 puede impedir que Googlebot vea contenido above-fold

Después de completar Wave 0:
1. Ejecutar npm run build para verificar 0 errores
2. Actualizar este archivo con checkboxes completados
3. Dar resumen de cambios y prompt para continuar con Wave 1
```

---

## Wave 0 — Blockers Críticos (ejecutar antes de cualquier campaign)

### B1 — Agregar `/case-studies/` al sitemap
- [ ] **File:** `src/app/sitemap.ts:107-118`
- [ ] **Change:** Agregar entrada estática `{ url: "${BASE_URL}/case-studies/", priority: 0.7, changeFrequency: "weekly" }` en el tier correcto. Actualmente `getAllCaseStudies()` mapea solo slugs dinámicos `/case-studies/${cs.slug}/`; la ruta índice no aparece en ningún tier.
- [ ] **Impact:** P0 — La página índice de case studies no está indexada correctamente por Google
- [ ] **Verify:** Visitar `/sitemap.xml` — URL `/case-studies/` presente con priority 0.7
- **Esfuerzo:** XS (<30 min)
- **Commit label:** `seo: add /case-studies/ index to sitemap`

### B2 — Eliminar HowTo schema deprecated de integrations
- [ ] **File:** `src/app/(frontend)/integrations/[slug]/page.tsx`
- [ ] **Change:** Eliminar el objeto `@type: "HowTo"` del JSON-LD emitido para los 10 slugs pre-built (salesforce, zapier, oracle, sap, netsuite, hubspot, google-drive, sharepoint, power-bi, microsoft-teams). Mantener SoftwareApplication.
- [ ] **Impact:** P0 — Google deprecó HowTo para software SaaS en 2024; puede resultar en penalización o pérdida de rich results
- [ ] **Verify:** Google Rich Results Test en `/integrations/salesforce/` — no debe aparecer HowTo schema
- **Esfuerzo:** S (1-2h)
- **Commit label:** `seo: remove deprecated HowTo schema from integration pages`

### B3 — Corregir hero opacity:0 (riesgo de crawling)
- [ ] **File:** `src/components/sections/HeroSection.tsx`
- [ ] **Change:** Aplicar `opacity-0` solo cuando JS detecta `prefers-reduced-motion=no`, o usar `will-change: opacity` con valor inicial visible. Actualmente `animate-hero-fade-in` arranca desde `opacity: 0` con `0.3s both` definido en `tailwind.config.ts`.
- [ ] **Impact:** P0 — Googlebot puede no ejecutar la animación completa; contenido above-fold invisible en crawl
- [ ] **Verify:** View-source en homepage — H1 visible sin JS; Lighthouse no reporta contenido oculto
- **Esfuerzo:** S (1-2h)
- **Commit label:** `perf: fix hero opacity:0 crawlability issue`

### B6 — Reemplazar testimonios placeholder en producción
- [ ] **File:** Datos en páginas que usan `SuperTemplate` — 14 bloques de `data.quote` bajo `/digital-inspections-software/`
- [ ] **Change:** Reemplazar texto "QUOTE: needs final copy" con citas aprobadas de clientes, o eliminar las secciones de testimonial donde no haya citas disponibles.
- [ ] **Impact:** P0 — Texto de relleno en producción daña credibilidad y puede ser indexado por Google
- [ ] **Verify:** Buscar en el sitio en producción "QUOTE" — no debe aparecer ningún resultado
- **Esfuerzo:** M (requiere aprobación del cliente)
- **Commit label:** `content: replace placeholder testimonials with approved quotes`

### B7 — Re-encodear 2 imágenes de blog >1 MB
- [ ] **Files:** `public/images/blog/aio-master-the-art...webp` (1,164,538 bytes) · `public/images/blog/default-bank.webp` (1,144,160 bytes)
- [ ] **Change:** Re-encodear/redimensionar a max 300-400 KB. Usar `quality={75}` y prop `sizes` apropiado en los componentes `next/image` que las sirven.
- [ ] **Impact:** P0 — Core Web Vitals (LCP) comprometido; Google PageSpeed Insights penaliza
- [ ] **Verify:** Lighthouse en un post que use estas imágenes — LCP < 2.5s; imágenes < 400 KB
- **Esfuerzo:** XS (<30 min)
- **Commit label:** `perf: compress oversized blog images (aio-master, default-bank)`

### B8 — Agregar regla `Applebot` allow en robots.txt
- [ ] **File:** `src/app/robots.ts:36-41`
- [ ] **Change:** Agregar `{ userAgent: "Applebot", allow: "/" }` antes de la regla `Applebot-Extended`. Actualmente solo `Applebot-Extended` está disallowed pero no hay regla explícita para `Applebot`.
- [ ] **Impact:** P1 — Sin regla explícita, Apple Search (Spotlight, Siri) depende del wildcard; best practice es regla explícita
- [ ] **Verify:** Visitar `/robots.txt` — regla `Applebot allow: /` presente
- **Esfuerzo:** XS (<15 min)
- **Commit label:** `seo: add explicit Applebot allow rule to robots.txt`

---

## Wave 1 — Schema y Datos Estructurados (1-2 semanas)

### S1 — Agregar CollectionPage/ItemList schema en hub de Digital Inspections
- [ ] **File:** `src/app/(frontend)/digital-inspections-software/page.tsx`
- [ ] **Change:** Agregar JSON-LD con `@type: CollectionPage` + `hasPart` listando las 18 URLs hijas. Actualmente solo emite `softwareApplicationSchema()`. `SuperTemplate` emite BreadcrumbList/FAQ/Service pero no CollectionPage ni `hasPart`.
- [ ] **Impact:** P0 — El hub page no comunica su estructura hub+spokes a search engines
- [ ] **Verify:** Google Rich Results Test en `/digital-inspections-software/` — CollectionPage schema válido
- **Esfuerzo:** S (1-3h)
- **Commit label:** `seo: add CollectionPage/ItemList schema to digital-inspections hub`

### S2 — (ver B2 — ya cubierto en Wave 0)

### S3 — Corregir `foundingDate` en Organization schema
- [ ] **File:** `src/lib/schema.ts:38`
- [ ] **Change:** Cambiar `"foundingDate": "2020"` a `"foundingDate": "2020-01-01"` (ISO 8601 completo requerido por Google)
- [ ] **Impact:** Schema inválido según spec de schema.org
- [ ] **Verify:** Google Rich Results Test — Organization schema sin errores de formato de fecha
- **Esfuerzo:** XS (<15 min)
- **Commit label:** `seo: fix foundingDate ISO 8601 format in Organization schema`

### S4 — Agregar `image` property a Organization schema
- [ ] **File:** `src/lib/schema.ts:6-49`
- [ ] **Change:** Agregar `"image": "https://vlx.ai/images/logos/vlx-logo.svg"` como propiedad separada de `logo` en el objeto Organization
- [ ] **Impact:** Schema incompleto; `image` es distinto de `logo` según schema.org
- [ ] **Verify:** Google Rich Results Test — Organization schema con `image` presente
- **Esfuerzo:** XS (<15 min)
- **Commit label:** `seo: add image property to Organization schema`

### S5 — Agregar BreadcrumbList a blog posts
- [ ] **File:** `src/app/(frontend)/blog/[slug]/page.tsx`
- [ ] **Change:** Importar `buildBreadcrumbJsonLd()` de `src/lib/metadata.ts` y emitir BreadcrumbList JSON-LD. Actualmente solo emite Article schema.
- [ ] **Impact:** Blog posts sin breadcrumb schema; señal de estructura ausente para Google
- [ ] **Verify:** Google Rich Results Test en cualquier blog post — BreadcrumbList presente
- **Esfuerzo:** S (1-2h)
- **Commit label:** `seo: add BreadcrumbList schema to blog posts`

### S6 — Agregar BreadcrumbList a pricing page
- [ ] **File:** `src/app/(frontend)/pricing/page.tsx`
- [ ] **Change:** Agregar BreadcrumbList JSON-LD via `buildBreadcrumbJsonLd()`
- [ ] **Impact:** Página de pricing sin breadcrumb schema
- [ ] **Verify:** View-source en `/pricing/` — BreadcrumbList JSON-LD presente
- **Esfuerzo:** XS (<15 min)
- **Commit label:** `seo: add BreadcrumbList to pricing page`

### S7 — Agregar BreadcrumbList a compare pages
- [ ] **Files:** `src/app/(frontend)/compare/vlx-vs-safetyculture/page.tsx` · `src/app/(frontend)/compare/vlx-vs-goaudits/page.tsx`
- [ ] **Change:** Agregar BreadcrumbList JSON-LD via `buildBreadcrumbJsonLd()` en ambas páginas
- [ ] **Impact:** Páginas de comparación sin breadcrumb schema
- [ ] **Verify:** View-source en ambas compare pages — BreadcrumbList JSON-LD presente
- **Esfuerzo:** XS (<15 min)
- **Commit label:** `seo: add BreadcrumbList to compare pages`

### S8 — Cambiar Article publisher de inline a referencia por @id
- [ ] **File:** `src/app/(frontend)/blog/[slug]/page.tsx`
- [ ] **Change:** Reemplazar objeto Organization completo inline por `{"@id": "https://vlx.ai/#organization"}` en la propiedad `publisher` del Article schema
- [ ] **Impact:** Schema verboso y no usa linked data correctamente; puede causar advertencias en Rich Results Test
- [ ] **Verify:** Google Rich Results Test en blog post — Article schema sin warnings de publisher
- **Esfuerzo:** XS (<15 min)
- **Commit label:** `seo: use @id reference for Article publisher schema`

### S9 — Agregar `duration` a VideoObject schema
- [ ] **File:** `src/app/(frontend)/page.tsx` (o donde esté el VideoObject del homepage)
- [ ] **Change:** Agregar `"duration": "PT3M42S"` (o el valor real en ISO 8601) y verificar que `uploadDate` esté actualizado
- [ ] **Impact:** VideoObject incompleto; `duration` es requerido para rich results de video
- [ ] **Verify:** Google Rich Results Test en homepage — VideoObject con duration válida
- **Esfuerzo:** XS (<15 min)
- **Commit label:** `seo: add duration to VideoObject schema on homepage`

### S10 — Corregir author fallback en blog posts
- [ ] **File:** `src/app/(frontend)/blog/[slug]/page.tsx`
- [ ] **Change:** Cambiar fallback de autor de `David Woldenberg` como Person a `VLX Team` / Organization para posts sin autor específico asignado en Payload CMS
- [ ] **Impact:** Posts sin autor asignado usan el nombre del CEO como fallback; semánticamente incorrecto
- [ ] **Verify:** View-source en un blog post sin autor — Article schema usa "VLX Team" en `author`
- **Esfuerzo:** XS (<15 min)
- **Commit label:** `seo: fix blog post author fallback to VLX Team`

### S11 — Completar Article schema en case studies
- [ ] **File:** `src/app/(frontend)/case-studies/[slug]/page.tsx`
- [ ] **Change:** Agregar `@id`, `author`, `dateModified`, `image`, y `publisher` como `{"@id": "https://vlx.ai/#organization"}` al Article schema. Actualmente el schema está incompleto.
- [ ] **Impact:** Case studies sin schema completo; menor elegibilidad para rich results
- [ ] **Verify:** Google Rich Results Test en cualquier case study — Article schema completo sin errores
- **Esfuerzo:** S (1-2h)
- **Commit label:** `seo: complete Article schema for case studies`

---

## Wave 2 — Contenido y OG Images (2-4 semanas)

### C1 — Crear OG images para top 10 páginas
- [ ] **Files:** Crear `opengraph-image.tsx` en: `/digital-inspections-software/`, `/pricing/`, `/digital-inspections-software/financial-services/`, `/digital-inspections-software/manufacturing/`, `/digital-inspections-software/insurance/`, `/digital-inspections-software/transportation-logistics/`, `/compare/vlx-vs-safetyculture/`, `/compare/vlx-vs-goaudits/`, `/about-us/`, `/blog/`
- [ ] **Change:** Reutilizar `src/lib/og-image.tsx` como generador base. Actualmente solo 8 rutas tienen `opengraph-image.tsx` propio; todas las demás usan el OG global genérico `opengraph-image.jpg` (178 KB)
- [ ] **Impact:** P1 — CTR social bajo; brand inconsistente al compartir en LinkedIn/Twitter
- [ ] **Verify:** Verificar OG images con opengraph.dev para cada URL — imagen específica por página visible
- **Esfuerzo:** M (4-8h por batch)
- **Commit label:** `seo: add page-specific OG images for top 10 pages`

### C2 — Agregar OG image explícita en metadata de homepage
- [ ] **File:** `src/app/(frontend)/layout.tsx:35-42`
- [ ] **Change:** Agregar en la metadata del homepage:
  ```typescript
  openGraph: {
    images: [{ url: "/opengraph-image.jpg", width: 1200, height: 630, alt: "VLX Digital Inspection Platform" }]
  }
  ```
  Actualmente el bloque OpenGraph de la metadata global no incluye `images`.
- [ ] **Impact:** Homepage sin OG image explícita en metadata (depende de convención de archivo Next.js, no de metadata declarada)
- [ ] **Verify:** Compartir URL de homepage en Twitter Card Validator — imagen visible
- **Esfuerzo:** XS (<15 min)
- **Commit label:** `seo: add explicit OG image to homepage metadata`

### C3 — Agregar contenido único por industria en SuperTemplate
- [ ] **File:** `src/components/sections/SuperTemplate.tsx` (94 KB) + archivos de data de cada industria
- [ ] **Change:** Agregar al menos 1 sección de contenido único por industria (casos específicos, regulaciones del sector, workflows propios). Actualmente 17 páginas de industria comparten la misma estructura con solo el keyword swapeado en H1/H2.
- [ ] **Impact:** Google puede percibir thin content o near-duplicate content entre páginas de industria
- [ ] **Verify:** Comparar contenido de 3 páginas de industria — diferencias sustanciales en al menos 1 sección
- **Esfuerzo:** L (requiere SEO + Cliente)
- **Commit label:** `content: add unique industry-specific sections to SuperTemplate pages`

### C4 — Actualizar `llms.txt` con precios y slugs actuales
- [ ] **File:** `public/llms.txt` o `src/app/(frontend)/llms.txt/route.ts`
- [ ] **Change:** Actualizar precios vigentes (actualmente indica Pro desde $29; precio actual $35-$55), slugs de URL actuales, y expandir descripción de features
- [ ] **Impact:** llms.txt con datos desactualizados; AI queries sobre VLX pueden recibir información incorrecta
- [ ] **Verify:** Visitar `/llms.txt` — precios y slugs correctos; sin referencias a visualogyx.com desactualizadas
- **Esfuerzo:** XS (<30 min)
- **Commit label:** `seo: update llms.txt with current pricing and slugs`

### C5 — Expandir interlinking contextual entre blog posts y páginas de producto
- [ ] **Files:** Posts de blog en Payload CMS + páginas de producto relevantes
- [ ] **Change:** Agregar enlaces contextuales en posts de blog hacia páginas de producto relacionadas. Actualmente hay escaso interlinking cross-content-type; el hub page tampoco referencia posts de blog relevantes por industria.
- [ ] **Impact:** P1 — Señal de autoridad fragmentada; PageRank no fluye entre contenido y producto
- [ ] **Verify:** Auditar 10 posts de blog — cada uno debe tener al menos 1 enlace a una página de producto relacionada
- **Esfuerzo:** M (requiere SEO)
- **Commit label:** `content: add contextual interlinking between blog posts and product pages`

---

## Wave 3 — Técnico y Performance (2-3 semanas)

### T1 — Agregar `sizes` prop en imágenes sin ella
- [ ] **Files:** `src/components/layout/Navbar.tsx` · `src/components/layout/Footer.tsx` · `src/components/sections/LogoBar.tsx` · `src/components/sections/VideoShowcase.tsx` · `src/components/sections/IntegrationsPreview.tsx`
- [ ] **Change:** Agregar `sizes` apropiado en cada componente (ej: `sizes="(max-width: 768px) 100vw, 50vw"` para imágenes de ancho variable; `sizes="120px"` para logos fijos). Actualmente ninguno de estos componentes especifica `sizes`.
- [ ] **Impact:** Sin `sizes`, el browser descarga imágenes más grandes de lo necesario; afecta LCP y CLS
- [ ] **Verify:** Lighthouse en homepage — no reportar "Image elements do not have explicit width and height" ni "Properly size images"
- **Esfuerzo:** S (2-3h)
- **Commit label:** `perf: add sizes prop to Navbar, Footer, LogoBar, VideoShowcase, IntegrationsPreview images`

### T2 — Corregir jerarquía de headings en homepage
- [ ] **Files:** `src/components/sections/FeaturesAccordion.tsx` · `src/components/sections/StatsSection.tsx` · `src/components/ui/SectionHeading.tsx`
- [ ] **Change:** Agregar prop `level` a `SectionHeading` para permitir renderizar `h3` en lugar de `h2` hardcoded. Convertir `<span>` usados como labels de subsección a elementos heading apropiados (`h3`).
- [ ] **Impact:** P1 — Señales semánticas débiles para crawlers; accesibilidad comprometida
- [ ] **Verify:** Usar herramienta de outline de headings — estructura H1→H2→H3 sin saltos en homepage
- **Esfuerzo:** S (2-3h)
- **Commit label:** `a11y: fix heading hierarchy on homepage (SectionHeading level prop)`

### T3 — Eliminar imágenes duplicadas en /public/images/homepage/
- [ ] **Files:** `public/images/homepage/seamless.webp` · `public/images/homepage/seamless-new.png` · `public/images/homepage/seamless-new.jpg`
- [ ] **Change:** Solo `seamless-new.png` está referenciado en código. Eliminar `seamless.webp` y `seamless-new.jpg` después de auditar todas las referencias con grep.
- [ ] **Impact:** Archivos no usados aumentan el repo y el build time
- [ ] **Verify:** `grep -r "seamless.webp" src/` y `grep -r "seamless-new.jpg" src/` — 0 resultados
- **Esfuerzo:** XS (<15 min)
- **Commit label:** `chore: remove unused duplicate images from public/images/homepage`

### T4 — Promover CSP de Report-Only a enforcing
- [ ] **File:** `next.config.ts`
- [ ] **Change:** Revisar violation reports acumulados en el endpoint configurado, corregir directivas problemáticas, y cambiar `Content-Security-Policy-Report-Only` a `Content-Security-Policy` enforcing.
- [ ] **Impact:** P1 — Sin protección real contra XSS/injection; el header de seguridad actual es inefectivo
- [ ] **Verify:** `curl -I https://vlx.ai` — header `Content-Security-Policy` (sin -Report-Only) presente
- **Esfuerzo:** M (4-8h de QA)
- **Commit label:** `security: promote CSP from Report-Only to enforcing`

### T5 — Cambiar redirects WordPress legacy de 301 a 410
- [ ] **File:** `next.config.ts`
- [ ] **Change:** Cambiar los redirects de `xmlrpc.php`, `wp-cron.php`, `wp-includes`, `wp-json`, `wp-content` de `301 a /` a respuesta `410 Gone`. Actualmente `/wp-admin` y `/wp-login.php` retornan 410 correctamente, pero las demás rutas WP hacen 301 a `/`.
- [ ] **Impact:** 301 a homepage transfiere link equity a rutas sin valor; 410 comunica que el recurso está permanentemente eliminado
- [ ] **Verify:** `curl -I https://vlx.ai/xmlrpc.php` — responde 410, no 301
- **Esfuerzo:** XS (<15 min)
- **Commit label:** `seo: change WordPress legacy redirects from 301 to 410`

### T6 — Actualizar blog sitemap para usar `updatedAt` en lugar de `publishedAt`
- [ ] **File:** `src/app/sitemap.ts`
- [ ] **Change:** Usar `post.updatedAt` (fecha de última modificación) en lugar de `post.publishedAt` para el campo `lastModified` de los posts en el sitemap
- [ ] **Impact:** Señala a Google cuándo fue la última actualización real del contenido, no la publicación original
- [ ] **Verify:** Visitar `/sitemap.xml` — fechas de blog posts reflejan `updatedAt`
- **Esfuerzo:** XS (<15 min)
- **Commit label:** `seo: use updatedAt for blog post lastModified in sitemap`

### T7 — Revisar `STATIC_CONTENT_DATE` y reemplazar por fechas específicas por página
- [ ] **File:** `src/app/sitemap.ts`
- [ ] **Change:** Reemplazar la constante global `STATIC_CONTENT_DATE = new Date("2026-03-30")` por fechas específicas por página basadas en el último cambio real de cada ruta
- [ ] **Impact:** Sitemap reporta la misma fecha para todas las páginas estáticas; Google puede ignorar señales de freshness
- [ ] **Verify:** Visitar `/sitemap.xml` — páginas estáticas con fechas variadas reflejando cambios reales
- **Esfuerzo:** S (2-3h)
- **Commit label:** `seo: replace STATIC_CONTENT_DATE with per-page lastModified dates`

---

## Wave 4 — Expansión de Contenido y Backlinks (ongoing)

### E1 — Crear página `/compare/vlx-vs-gocanvas/`
- [ ] **File:** Crear `src/app/(frontend)/compare/vlx-vs-gocanvas/page.tsx` + archivo de data de comparación
- [ ] **Change:** Página de comparación VLX vs GoCanvas siguiendo el mismo patrón de las páginas compare existentes. GoCanvas es el competidor directo sin página de comparación; mayor volumen estimado de búsqueda entre los gaps.
- [ ] **Impact:** Gap de keyword "VLX vs GoCanvas" sin cobertura; competidor directo mapeado en CLAUDE.md
- [ ] **Verify:** `/compare/vlx-vs-gocanvas/` accesible, en sitemap, con metadata completa
- **Esfuerzo:** M (requiere SEO + Dev)
- **Commit label:** `content: add VLX vs GoCanvas comparison page`

### E2 — Agregar LocalBusiness schema
- [ ] **File:** `src/lib/schema.ts` + `src/app/(frontend)/layout.tsx` o `src/app/(frontend)/contact/page.tsx`
- [ ] **Change:** Agregar `@type: LocalBusiness` con address (Aventura, FL), email (`info@vlx.ai`), y URL. Verificar primero si VLX tiene presencia física relevante (GBP verificado).
- [ ] **Impact:** Sin LocalBusiness schema; Google Business Profile no conectado con el sitio
- [ ] **Verify:** Google Rich Results Test — LocalBusiness schema válido
- **Esfuerzo:** S (1-2h)
- **Commit label:** `seo: add LocalBusiness schema`

### E3 — Crear 3-5 case studies completos en Payload CMS
- [ ] **File:** Payload CMS Admin — colección CaseStudies
- [ ] **Change:** Crear case studies completos con métricas de clientes (ITI International confirmado en seed como punto de partida). Actualmente los case studies existen pero están incompletos.
- [ ] **Impact:** Contenido linkable; potencial para outreach a clientes mencionados; señal de autoridad
- [ ] **Verify:** `/case-studies/` muestra 3+ case studies completos con métricas reales
- **Esfuerzo:** L (requiere Cliente + SEO)
- **Commit label:** `content: add complete case studies with client metrics`

### E4 — Outreach para backlinks en directorios de integraciones
- [ ] **Acción:** Enviar solicitud de listing en directorios de apps de Salesforce AppExchange, SAP App Center, Oracle Cloud Marketplace, NetSuite SuiteApp — las 10 páginas de integración son credenciales para aplicar
- [ ] **Impact:** Backlinks de alta autoridad desde dominios de los partners de integración
- [ ] **Verify:** Ahrefs — nuevos backlinks desde dominios de Salesforce, SAP, Oracle dentro de 60 días
- **Esfuerzo:** M (requiere Marketing)
- **Commit label:** N/A (outreach, no cambio de código)

### E5 — Optimizar perfiles en G2, Capterra, GetApp
- [ ] **Acción:** Verificar y actualizar copy en G2 (`g2.com/products/vlx/reviews`), Capterra y GetApp con copy actualizado, screenshots actuales, y categorías correctas
- [ ] **Impact:** Perfiles desactualizados afectan conversión; reviews y ratings impactan SoftwareApplication schema
- [ ] **Verify:** Revisar perfiles en las 3 plataformas — copy actual, screenshots correctos, categorías alineadas con posicionamiento
- **Esfuerzo:** M (requiere Marketing)
- **Commit label:** N/A (outreach, no cambio de código)

---

## Wave 5 — ASO y Presencia en AI (ongoing)

### A1 — Auditar y actualizar ficha en iOS App Store
- [ ] **Acción:** Revisar título, descripción, keywords, y screenshots del app en iOS App Store. Verificar que "digital inspection" aparezca en título o keywords.
- [ ] **Verify:** App Store listing actualizado; "digital inspection" visible en metadata del app
- **Esfuerzo:** M (requiere Marketing)
- **Commit label:** N/A

### A2 — Auditar y actualizar ficha en Google Play
- [ ] **Acción:** Revisar descripción, keywords, y screenshots del app en Google Play. Alinear con posicionamiento actual de vlx.ai.
- [ ] **Verify:** Google Play listing actualizado; descripción alineada con copy del sitio
- **Esfuerzo:** M (requiere Marketing)
- **Commit label:** N/A

### A3 — Alinear `aggregateRating` schema con datos reales de App Store
- [ ] **File:** `src/lib/schema.ts:87-95`
- [ ] **Change:** Actualizar `ratingCount` y `reviewCount` con datos verificados de G2+Capterra+App Store. Actualmente `ratingCount: "20"` pero el sitio afirma "4.9/5 across 12,000+ users" — el copy y el schema deben alinearse.
- [ ] **Impact:** Inconsistencia entre copy del sitio y datos del schema puede confundir a Google
- [ ] **Verify:** Schema refleja ratingCount real verificable de las fuentes declaradas
- **Esfuerzo:** XS (<15 min)
- **Commit label:** `seo: align aggregateRating schema with verified review counts`

### A4 — Verificar presencia en respuestas de AI
- [ ] **Acción:** Probar en Claude, ChatGPT y Perplexity: "What is the best digital inspection software for field operations?" y "VLX vs SafetyCulture for inspection management"
- [ ] **Verify:** VLX aparece en respuestas a queries relacionados con digital inspections, asset verification, field operations software
- **Esfuerzo:** S (requiere SEO)
- **Commit label:** N/A (verificación manual)

### A5 — Expandir `llms.txt` con secciones de productos, industrias y casos de uso
- [ ] **File:** `public/llms.txt` o `src/app/(frontend)/llms.txt/route.ts`
- [ ] **Change:** Agregar secciones expandidas de: productos (VLX, KYPiT, CountIt, InstaCount), industrias cubiertas (18 verticales), casos de uso principales, y al menos 2-3 case studies en formato conciso
- [ ] **Impact:** llms.txt más completo mejora la probabilidad de que AI systems citen VLX correctamente
- [ ] **Verify:** Visitar `/llms.txt` — secciones de productos, industrias, y casos de uso presentes
- **Esfuerzo:** S (requiere SEO + Dev)
- **Commit label:** `seo: expand llms.txt with products, industries, and use cases`

---

## Checklist de Verificación (ejecutar después de cada Wave)

- [ ] `npm run build` — 0 errores, todas las páginas generan
- [ ] Visitar `/sitemap.xml` — todas las URLs presentes incluyendo `/case-studies/`
- [ ] Visitar `/robots.txt` — regla `Applebot allow: /` presente
- [ ] Google Rich Results Test en blog post + homepage + integration page
- [ ] Lighthouse en homepage (target 95+)
- [ ] `curl -I` para rutas WP legacy — retornan 410
- [ ] Verificar OG tags con opengraph.dev para top 5 páginas
- [ ] Buscar "QUOTE" en producción — 0 resultados de testimonios placeholder

---

## Referencia de Archivos Clave

| Archivo | Modificado en Wave |
|---------|-------------------|
| `src/app/sitemap.ts` | B1, T6, T7 |
| `src/app/(frontend)/integrations/[slug]/page.tsx` | B2 |
| `src/components/sections/HeroSection.tsx` | B3 |
| `public/images/blog/` | B7 |
| `src/app/robots.ts` | B8 |
| `src/app/(frontend)/digital-inspections-software/page.tsx` | S1 |
| `src/lib/schema.ts` | S3, S4, A3 |
| `src/app/(frontend)/blog/[slug]/page.tsx` | S5, S8, S10 |
| `src/app/(frontend)/pricing/page.tsx` | S6 |
| `src/app/(frontend)/compare/*/page.tsx` | S7 |
| `src/app/(frontend)/case-studies/[slug]/page.tsx` | S11 |
| `src/app/(frontend)/layout.tsx` | C2 |
| `src/components/layout/Navbar.tsx` | T1 |
| `src/components/layout/Footer.tsx` | T1 |
| `src/components/sections/LogoBar.tsx` | T1 |
| `src/components/sections/VideoShowcase.tsx` | T1 |
| `src/components/sections/IntegrationsPreview.tsx` | T1 |
| `src/components/sections/FeaturesAccordion.tsx` | T2 |
| `src/components/sections/StatsSection.tsx` | T2 |
| `src/components/ui/SectionHeading.tsx` | T2 |
| `next.config.ts` | T4, T5 |
| `public/llms.txt` | C4, A5 |

---

---

# REFERENCIA — ANÁLISIS DETALLADO DEL AUDIT

> Las secciones a continuación son el análisis de respaldo del audit. No requieren modificación; son la fuente de evidencia para las tareas de los Waves anteriores.

**Versión:** 2026-06-19 · **Auditor:** Laura Ceballos / Software Craft CR  
**Rama verificada:** `origin/Hans-review` · **Método:** Inspección de código + análisis de arquitectura  
**Fuente de verdad:** Codebase local (commit actual) + análisis de archivos estáticos

> **Nota de metodología:** Este audit fue realizado contra el código fuente del repositorio. Las secciones que requieren acceso a GSC API, herramientas de backlinks, o datos live de crawling están marcadas como `[PROVISIONAL]`. Todos los hallazgos de código tienen evidencia de archivo:línea.

---

## DASHBOARD DE ESTADO

| KPI | Estado | Valor |
|-----|--------|-------|
| Páginas en sitemap | ✅ | 200+ URLs, 9 tiers |
| Páginas con OG image específica | ⚠️ | ~8 de 56+ páginas · 2026-07-01: aún faltan 46/185 |
| Schemas JSON-LD activos | ⚠️ | 7 tipos, 4 con errores · 2026-07-01: aggregateRating eliminado; compare pages ganaron Organization+WebSite+BreadcrumbList |
| robots.txt | ✅ | 2026-07-01: Applebot allow presente; postura IA intacta |
| CSP | ❌ | Report-Only, no enforcing |
| /case-studies/ en sitemap | ❌ | Ausente (solo slugs dinámicos) |
| HowTo schema deprecated | ❌ | Activo en 10 páginas de integración |
| Testimonios placeholder públicos | ❌ | "QUOTE: needs final copy" en producción |
| Imágenes blog >1 MB | ❌ | 2 imágenes identificadas |
| llms.txt | ⚠️ | Presente pero con datos desactualizados |
| i18n | ❌ | No implementado (solo inglés) |
| LocalBusiness schema | ❌ | Ausente |

---

## SECCIÓN 1 — RECONCILIACIÓN CON AUDITS ANTERIORES

### Fuentes comparadas
- `docs/seo/SEO-AUDIT-VERIFIED-2026-04-27.md` — audit verificado previo
- `docs/seo/VLX-AUDIT-HANS-REVIEW-VERIFIED-2026-06-11.html` — review Hans
- Estado actual del código en rama `Hans-review`

### Matriz de reconciliación

| ID | Hallazgo anterior | Estado actual verificado | Evidencia |
|----|-------------------|--------------------------|-----------|
| P0-1 | Hub sin enlaces crawlables | ✅ RESUELTO | `6cf73df` — BROWSE_INDUSTRIES activo |
| P0-2 | Hub H1 genérico | ✅ RESUELTO | `4ca0490` — H1 = "Digital Inspection Software for Every Industry" |
| P0-3 | Hub sin CollectionPage/ItemList schema | ❌ ABIERTO | `src/app/(frontend)/digital-inspections-software/page.tsx` solo emite softwareApplicationSchema() |
| P0-4 | Jerarquía de headings rota | ⚠️ PARCIAL | SectionHeading hardcoded a h2; FeaturesAccordion usa spans |
| P0-5 | Hero arranca en opacity:0 | ❌ ABIERTO | `HeroSection.tsx` aplica `animate-hero-fade-in` desde opacity:0 |
| P0-6 | Pricing duplicado en SoftwareApplication | ✅ RESUELTO | `3e1a33f` — unificado via softwareApplicationSchema() |
| P0-7 | /case-studies/ ausente del sitemap | ❌ ABIERTO | `src/app/sitemap.ts:113-118` — solo slugs, no índice |
| P0-8 | HowTo schema deprecated en integrations | ❌ ABIERTO | `src/app/(frontend)/integrations/[slug]/page.tsx` |
| P0-9 | trustLogos vacío renderizando strip | ✅ CHANGED | SuperTemplate ya salta trust strip cuando length===0 |
| P0-10 | Alt text genérico en trust logos | ❌ ABIERTO | Tipo `string[]`, alt="Trusted customer" |
| P0-11 | 2 imágenes blog >1MB | ❌ ABIERTO | aio-master...webp (1.16MB), default-bank.webp (1.14MB) |
| P0-12 | Testimonios placeholder en producción | ❌ ABIERTO | "QUOTE: needs final copy" en 14 bloques de SuperTemplate |
| P1-5 | Homepage sin product definition | ✅ RESUELTO | `0f8dc24` |
| P1-6 | CSP en Report-Only | ❌ ABIERTO | `next.config.ts` — Content-Security-Policy-Report-Only |
| P1-8 | OG images genéricas en mayoría de páginas | ❌ ABIERTO | Solo 8 rutas con opengraph-image.tsx propias |
| P1-21 | Hub sin OG image específica | ✅ RESUELTO | `2b9a6b3` |
| P1-22 | TrustBar sin next/image | ✅ RESUELTO | `2a6e72c` |

**Drift del audit anterior:** 6 de 17 items revisados siguen abiertos (35.3%)

---

## SECCIÓN 2 — BLOCKERS (B1–B10)

> 🔄 **Estado post-review 2026-07-01:** varios de estos blockers cambiaron de estado tras el review de dev.vlx.ai. Ver la sección **SEGUIMIENTO 2026-07-01** arriba para el detalle (aggregateRating eliminado, Applebot allow confirmado, `og:image` aún 46/185, redirect `/oil-and-gas/` re-abierto por 404 en dev, etc.).

### B1 — /case-studies/ ausente del sitemap
- **Estado:** ❌ ABIERTO
- **Impacto:** P0 — La página índice de case studies no está indexada correctamente
- **Evidencia:** `src/app/sitemap.ts:107-118` — `getAllCaseStudies()` mapea solo slugs dinámicos `/case-studies/${cs.slug}/`. La ruta `/case-studies/` no aparece en ningún tier.
- **Acción:** Agregar entrada estática `{ url: "${BASE_URL}/case-studies/", priority: 0.7 }` en `src/app/sitemap.ts`
- **Esfuerzo:** XS (<30 min)

### B2 — HowTo schema deprecated activo en 10 páginas de integración
- **Estado:** ❌ ABIERTO
- **Impacto:** P0 — Google deprecó HowTo para software SaaS en 2024; puede resultar en penalización o pérdida de rich results
- **Evidencia:** `src/app/(frontend)/integrations/[slug]/page.tsx` emite `@type: "HowTo"` para los 10 slugs pre-built (salesforce, zapier, oracle, sap, netsuite, hubspot, google-drive, sharepoint, power-bi, microsoft-teams)
- **Acción:** Eliminar el objeto HowTo; mantener SoftwareApplication
- **Esfuerzo:** S (1-2h)

### B3 — Hero arranca con opacity:0 (riesgo de crawling)
- **Estado:** ❌ ABIERTO
- **Impacto:** P0 — Googlebot puede no ejecutar la animación completa; contenido above-fold invisible en crawl
- **Evidencia:** `src/components/sections/HeroSection.tsx` aplica `animate-hero-fade-in`; `tailwind.config.ts` define la animación desde `opacity: 0` con `0.3s both`
- **Acción:** Aplicar `opacity-0` solo cuando JS detecta prefers-reduced-motion=no o usar `will-change: opacity` con valor inicial visible
- **Esfuerzo:** S (1-2h)

### B4 — CollectionPage/ItemList schema ausente en hub de Digital Inspections
- **Estado:** ❌ ABIERTO
- **Impacto:** P0 — El hub page no comunica su estructura de hub+spokes a search engines
- **Evidencia:** `src/app/(frontend)/digital-inspections-software/page.tsx` emite solo `softwareApplicationSchema()`. `SuperTemplate` emite BreadcrumbList/FAQ/Service pero no `CollectionPage` ni `hasPart`
- **Acción:** Agregar JSON-LD con `@type: CollectionPage` + `hasPart` listando las 18 URLs hijas
- **Esfuerzo:** S (1-3h)

### B5 — OG image genérica en 50+ páginas clave
- **Estado:** ❌ ABIERTO
- **Impacto:** P1 — CTR social bajo; brand inconsistente al compartir en LinkedIn/Twitter
- **Evidencia:** Solo 8 rutas tienen `opengraph-image.tsx` propio: homepage, features, KYPiT, y algunas más. Todas las 18 industry pages, pricing, about-us, contact, comparisons, etc. usan el OG global genérico `opengraph-image.jpg` (178KB)
- **Acción:** Crear opengraph-image.tsx para las 10 páginas de mayor tráfico primero (hub, pricing, financial-services, manufacturing, insurance, transportation-logistics, compare pages)
- **Esfuerzo:** M (4-8h por batch)

### B6 — Testimonios placeholder en producción ("QUOTE: needs final copy")
- **Estado:** ❌ ABIERTO
- **Impacto:** P0 — Texto de relleno en producción daña credibilidad y puede ser indexado por Google
- **Evidencia:** `data.quote` renderizado públicamente por `SuperTemplate`; 14 bloques de quote bajo `/digital-inspections-software/` contienen texto provisional
- **Acción:** Reemplazar con citas aprobadas de clientes o eliminar secciones de testimonial donde no haya citas disponibles
- **Esfuerzo:** M (requiere aprobación del cliente)

### B7 — 2 imágenes de blog superan 1 MB
- **Estado:** ❌ ABIERTO
- **Impacto:** P0 — Core Web Vitals (LCP) comprometido; Google PageSpeed Insights penaliza
- **Evidencia:**
  - `aio-master-the-art...webp`: 1,164,538 bytes (1.11 MB)
  - `default-bank.webp`: 1,144,160 bytes (1.09 MB)
- **Acción:** Re-encodear/redimensionar a max 300-400KB; usar next/image con `quality={75}` y `sizes` apropiado
- **Esfuerzo:** XS (<30 min)

### B8 — robots.txt: falta regla `Applebot` allow
- **Estado:** ❌ ABIERTO
- **Impacto:** P1 — Apple Search (Spotlight, Siri) no puede rastrear vlx.ai
- **Evidencia:** `src/app/robots.ts:36-41` — `Applebot-Extended` está disallowed pero no hay regla `Applebot` (allow). Por defecto el wildcard `*` permite rastreo, pero la best practice es tener regla explícita
- **Acción:** Agregar `{ userAgent: "Applebot", allow: "/" }` antes de la regla Applebot-Extended
- **Esfuerzo:** XS (<15 min)

### B9 — CSP en modo Report-Only (no enforcing)
- **Estado:** ❌ ABIERTO
- **Impacto:** P1 — Sin protección real contra XSS/injection; header de seguridad inefectivo
- **Evidencia:** `next.config.ts` envía `Content-Security-Policy-Report-Only` en lugar de `Content-Security-Policy`
- **Acción:** Revisar violation reports acumulados, corregir directivas problemáticas y promover a enforcing
- **Esfuerzo:** M (4-8h de QA)

### B10 — Heading hierarchy incompleta en Homepage
- **Estado:** ⚠️ PARCIAL
- **Impacto:** P1 — Señales semánticas débiles para crawlers; accesibilidad comprometida
- **Evidencia:** `src/components/sections/FeaturesAccordion.tsx` y `StatsSection.tsx` usan `<span>` en lugar de `<h3>` para subsection labels. `SectionHeading` component está hardcoded a `h2`
- **Acción:** Agregar prop `level` a SectionHeading; convertir span labels a elementos heading apropiados
- **Esfuerzo:** S (2-3h)

---

## SECCIÓN 3 — GSC LIVE DATA [PROVISIONAL]

> **Estado:** PROVISIONAL — Sin acceso a GSC API en esta sesión. Los datos a continuación son estimaciones basadas en estructura del sitio y audits previos. Verificar en GSC Search Console > Performance antes de ejecutar el plan.

### Métricas base recomendadas a extraer

| Métrica | Período | Nota |
|---------|---------|------|
| Clicks totales | 28d + 90d | Comparar tendencias |
| Impressions totales | 28d + 90d | |
| CTR promedio | 28d | Target: >2.5% para SaaS B2B |
| Posición promedio | 28d | Target: <25 para keywords primarios |
| URLs indexadas | Live | Verificar contra sitemap count |
| URLs excluidas | Live | Identificar páginas problemáticas |

### URLs de alta prioridad a monitorear en GSC

- `https://vlx.ai/` — Homepage
- `https://vlx.ai/digital-inspections-software/` — Hub principal
- `https://vlx.ai/digital-inspections-software/financial-services/`
- `https://vlx.ai/digital-inspections-software/manufacturing/`
- `https://vlx.ai/digital-inspections-software/insurance/`
- `https://vlx.ai/pricing/`
- `https://vlx.ai/blog/` (index de 116+ posts)

### Páginas con mayor riesgo de no-indexación

- `/case-studies/` — No está en sitemap
- `/lp/[campaign]/` — Correctamente excluido (noindex)
- `/admin/` — Correctamente excluido
- Páginas con `noindex` en preview deployments (Vercel ENV check en layout.tsx:32-34)

---

## SECCIÓN 4 — KEYWORDS Y OPORTUNIDADES

> **Estado:** PROVISIONAL — Sin datos GSC live. Análisis basado en targeting actual del código y estructura de páginas.

### Clusters de keywords actuales (basado en páginas existentes)

| Cluster | URL objetivo | Keyword primario | Estado |
|---------|-------------|-----------------|--------|
| Hub / Brand | `/digital-inspections-software/` | "digital inspection software" | H1 ✅ |
| Financial Services | `/digital-inspections-software/financial-services/` | "digital inspection software financial services" | ✅ |
| Manufacturing | `/digital-inspections-software/manufacturing/` | "inspection software manufacturing" | ✅ |
| Insurance | `/digital-inspections-software/insurance/` | "digital inspection insurance" | ✅ |
| Transportation | `/digital-inspections-software/transportation-logistics/` | "transportation inspection software" | ✅ |
| KYPiT (AI Fraud) | `/digital-inspections-software/app/kypit/` | "AI fraud detection inspection" | ✅ |
| Comparison | `/compare/vlx-vs-safetyculture/` | "VLX vs SafetyCulture" | ✅ |
| Comparison | `/compare/vlx-vs-goaudits/` | "VLX vs GoAudits" | ✅ |
| Blog general | `/blog/` | (116 posts) | ✅ |

### Gaps de keywords identificados en código

| Oportunidad | Justificación | Acción recomendada |
|-------------|--------------|-------------------|
| "inspection app" | KPIs en homepage mencionan "app" pero no hay página dedicada | Crear o expandir /product/ targeting este keyword |
| "audit software" | Término relacionado sin página específica | Considerar en hub o blog |
| "field inspection software" | `/field-operations/` existe pero puede rankear mejor | Revisar meta title/description |
| "checklist software" | `/checklists/` existe con content pero no está tier-1 en sitemap | Aumentar prioridad en sitemap |
| "inspection report software" | Mencionado en features pero sin página propia | Evaluar blog cluster o feature page |

### Páginas en posible striking distance [PROVISIONAL]

> Verificar en GSC: filtrar posición 5-25 + impressions >30 + CTR <3%

Basado en estructura del sitio, las páginas con mayor probabilidad de estar en striking distance son:
- `/digital-inspections-software/manufacturing/`
- `/digital-inspections-software/construction/`
- `/digital-inspections-software/oil-gas/`
- `/compare/vlx-vs-safetyculture/`

---

## SECCIÓN 5 — CONTENIDO Y ARQUITECTURA

### Inventario de páginas

| Categoría | Cantidad | Estado SEO |
|-----------|----------|------------|
| Core product pages | 8 | ✅ Con metadata completa |
| Industry/use-case pages (hub+spokes) | 19 | ⚠️ Contenido muy similar entre páginas |
| Blog posts (Payload CMS) | 116 | ✅ Con seoTitle/seoDescription/seoKeyword |
| Integration pages | 10 pre-built + [slug] | ⚠️ HowTo schema deprecated |
| Comparison pages | 2 | ✅ |
| Company pages | 6 | ✅ |
| Legal pages | 4 | ✅ |
| Landing pages /lp/ | Dinámico | ✅ Excluidas de sitemap/index |
| Case studies | Dinámico (CMS) | ⚠️ Índice no en sitemap |
| Checklists/Forms/Reports | Dinámico | ✅ |

**Total URLs en sitemap:** ~200+ (verificar en producción con `https://vlx.ai/sitemap.xml`)

### Problemas de arquitectura de contenido

#### A1 — SuperTemplate produce contenido muy similar en 17 páginas
- **Evidencia:** `src/components/sections/SuperTemplate.tsx` (94KB) maneja 17 páginas de industria
- **Riesgo:** Google puede percibir thin content o near-duplicate content entre páginas de industria
- **Faltante:** Contenido único por industria más allá del keyword swap en H1/H2
- **Acción:** Agregar secciones de contenido único por industria (casos específicos, regulaciones, workflows propios del sector)

#### A2 — /blog/ como único hub de contenido largo
- **Faltante:** No hay hub de recursos consolidado (/resources/ o /learn/)
- **116 posts** distribuidos en `/blog/`, `/checklists/`, `/use-case/`, `/forms/`, `/reports/` — fragmenta señal de autoridad
- **Acción:** Evaluar si consolidar bajo un hub de recursos o mejorar interlinking cross-content-type

#### A3 — Breadcrumbs incompletos
- **Faltante:** Blog posts no tienen BreadcrumbList schema (solo tienen Article)
  - Evidencia: `src/app/(frontend)/blog/[slug]/page.tsx` — no emite breadcrumb JSON-LD
- **Faltante:** Pricing page no tiene BreadcrumbList
- **Faltante:** Comparison pages no tienen BreadcrumbList
- **Acción:** Agregar BreadcrumbList JSON-LD en blog posts, pricing y compare pages

### Estructura de headings

| Página | H1 | H2 | H3 | Estado |
|--------|-----|-----|-----|--------|
| Homepage | ✅ 1 H1 | ✅ Múltiples | ⚠️ Algunos como `<span>` | Parcial |
| Industry pages (SuperTemplate) | ✅ 1 H1 | ✅ | ✅ | OK |
| Hub `/digital-inspections-software/` | ✅ "Digital Inspection Software for Every Industry" | ✅ | ✅ | OK |
| Blog posts | ✅ (post title) | ✅ | ✅ | OK |
| Pricing | ✅ | ✅ | ✅ | OK |

### Interlinking interno

- **Positivo:** Navbar con mega-menu, footer organizado, breadcrumbs en blog y case studies
- **Positivo:** Industry pages cross-link a use cases
- **Faltante:** Interlinking contextual entre posts de blog y páginas de producto relacionadas (escaso según audit previo P1-16)
- **Faltante:** Hub page no hace referencia explícita a posts de blog relevantes por industria

---

## SECCIÓN 6 — SCHEMA MARKUP

### Inventario de schemas activos

| Schema Type | Estado | Archivo | Notas |
|------------|--------|---------|-------|
| Organization | ⚠️ ERRORES | `src/lib/schema.ts:6-49` | foundingDate mal formateado, falta `image` |
| WebSite | ✅ | `src/lib/schema.ts:51-65` | OK |
| SoftwareApplication | ⚠️ ERRORES | `src/lib/schema.ts:67-108` | AggregateRating ratingCount:20 inconsistente con "12,000+ usuarios" |
| FAQPage | ✅ | `src/lib/schema.ts:123-149` | Filtra entries sin texto correctamente |
| Article (blog) | ⚠️ INCOMPLETO | `src/app/(frontend)/blog/[slug]/page.tsx` | Publisher inlined, falta @id, falta BreadcrumbList |
| Article (case studies) | ❌ INCOMPLETO | `src/app/(frontend)/case-studies/[slug]/page.tsx` | Falta @id, author, dateModified, image, publisher @id |
| BreadcrumbList | ⚠️ PARCIAL | Múltiples páginas | Ausente en blog posts, pricing, compare pages |
| VideoObject | ⚠️ | Homepage | Falta `duration` en ISO 8601 |
| HowTo | ❌ DEPRECATED | `src/app/(frontend)/integrations/[slug]/page.tsx` | Google deprecó HowTo para SaaS |
| LocalBusiness | ❌ AUSENTE | — | No existe en ninguna página |
| CollectionPage | ❌ AUSENTE | Hub page | Necesario para comunicar estructura hub+spokes |
| ContactPage | ✅ | `src/lib/schema.ts:110-121` | OK si está implementado en /contact/ |

### Errores específicos por schema

#### Organization Schema (`src/lib/schema.ts:38`)
```json
"foundingDate": "2020"  // ❌ Debe ser "2020-01-01" (ISO 8601 completo)
```
Falta: `"image": "https://vlx.ai/images/logos/vlx-logo.svg"` (distinto de `logo`)

#### SoftwareApplication Schema (`src/lib/schema.ts:87-95`)
```json
"aggregateRating": {
  "ratingCount": "20",    // ❌ Solo 20 reviews, pero el sitio dice "12,000+ users"
  "reviewCount": "20"     // No es inconsistencia crítica pero puede confundir a Google
}
```
La `ratingCount` refleja reviews verificadas (G2+Capterra), lo cual es correcto, pero el sitio afirma en copy "4.9/5 across 12,000+ users" — alinear messaging.

#### Article Schema (Blog)
- Publisher inlined como objeto Organization completo en lugar de `{"@id": "https://vlx.ai/#organization"}`
- Falta `@id` propio del Article
- Autor fallback usa `David Woldenberg` como Person — debería ser `VLX Team` / Organization para posts sin autor específico

#### VideoObject (Homepage)
- Falta `"duration": "PT3M42S"` (o el valor real en ISO 8601)
- Verificar `uploadDate` esté actualizado

---

## SECCIÓN 7 — CORE WEB VITALS

> **Estado:** PROVISIONAL — Sin acceso a PageSpeed Insights API o datos de campo de CrUX. Análisis basado en revisión de código.

### Riesgos identificados en código

#### LCP (Largest Contentful Paint)
| Factor | Estado | Evidencia |
|--------|--------|---------|
| Hero image con `priority` | ✅ | next/image priority en hero |
| Rubik font con `display: swap` | ⚠️ | Puede causar FOUT; considerar `display: optional` |
| 2 imágenes blog >1MB | ❌ | Afecta LCP en posts individuales |
| Animación hero desde opacity:0 | ❌ | Contenido above-fold no visible inmediatamente |

#### INP (Interaction to Next Paint)
| Factor | Estado | Evidencia |
|--------|--------|---------|
| Dynamic imports en homepage | ✅ | 9 componentes con `dynamic()` lazy loading |
| PostHog via requestIdleCallback | ✅ | No bloquea main thread |
| Framer Motion animations | ⚠️ | Puede afectar INP en dispositivos lentos |

#### CLS (Cumulative Layout Shift)
| Factor | Estado | Evidencia |
|--------|--------|---------|
| next/image con width/height | ✅ | Reserva espacio de layout |
| Font display swap | ⚠️ | FOUT puede causar layout shift |
| Imágenes sin `sizes` prop | ⚠️ | Navbar, Footer, LogoBar, VideoShowcase sin `sizes` |

### Imágenes sin `sizes` prop (Identificadas)
- `src/components/layout/Navbar.tsx` — Logo image sin sizes
- `src/components/layout/Footer.tsx` — Logo/icon images sin sizes
- `src/components/sections/LogoBar.tsx` — Customer logos sin sizes
- `src/components/sections/VideoShowcase.tsx` — Thumbnail sin sizes
- `src/components/sections/IntegrationsPreview.tsx` — Partner logos sin sizes

**Acción:** Agregar `sizes` apropiado (ej: `sizes="(max-width: 768px) 100vw, 50vw"`) en todos los componentes listados.

### Fuentes duplicadas en /public/images/homepage/
- `seamless.webp`, `seamless-new.png`, y `seamless-new.jpg` coexisten
- Solo `seamless-new.png` está referenciado en código
- **Acción:** Eliminar variantes no usadas después de auditar todas las referencias

---

## SECCIÓN 8 — AI/GEO VISIBILITY

### robots.txt — Configuración de crawlers IA

| Bot | Regla actual | Evaluación |
|----|-------------|-----------|
| OAI-SearchBot (OpenAI Search) | `allow: "/"` | ✅ Correcto |
| PerplexityBot | `allow: "/"` | ✅ Correcto |
| Claude-SearchBot | `allow: "/"` | ✅ Correcto |
| Claude-User | `allow: "/"` | ✅ Correcto |
| GPTBot | `disallow: "/"` | ✅ (training-only, correcto bloquear) |
| CCBot | `disallow: "/"` | ✅ |
| Bytespider | `disallow: "/"` | ✅ |
| Google-Extended | `disallow: "/"` | ✅ (Gemini training, correcto) |
| ClaudeBot | `disallow: "/"` | ✅ (training-only, search separado) |
| Applebot-Extended | `disallow: "/"` | ✅ (training) |
| **Applebot** | **SIN REGLA EXPLÍCITA** | **❌ Falta allow explícito** |
| Amazonbot | `disallow: "/"` | ✅ |
| FacebookBot | `disallow: "/"` | ✅ |

**Acción prioritaria:** Agregar en `src/app/robots.ts`:
```typescript
{
  userAgent: "Applebot",
  allow: "/",
},
```

### llms.txt [PROVISIONAL]
- Mencionado en audits previos (P1-3, P1-4) como existente pero con datos desactualizados
- **Verificar existencia:** `public/llms.txt` o `src/app/(frontend)/llms.txt/route.ts`
- **Issues reportados:** Precios desactualizados (Pro desde $29 no $35), slugs de URL desactualizados
- **Acción:** Actualizar con precios vigentes ($35-$55), slugs actuales, y expandir descripción de features

### Presencia en respuestas de AI [PROVISIONAL — verificación manual requerida]
- Probar en Claude, ChatGPT, Perplexity: "What is the best digital inspection software for field operations?"
- Target: VLX debe aparecer en respuestas a queries relacionados con digital inspections, asset verification, field operations software

---

## SECCIÓN 9 — BACKLINKS [PROVISIONAL]

> Sin acceso a Ahrefs, DataForSEO, o Semrush en esta sesión. Análisis basado en presencia de perfil declarada en schema.

### Perfiles declarados en Organization schema (`sameAs`)
```json
"sameAs": [
  "https://www.linkedin.com/company/vlxai/",
  "https://www.youtube.com/@vlx-ai",
  "https://www.instagram.com/vlx_ai",
  "https://x.com/vlx_ai",
  "https://www.facebook.com/vlxai",
  "https://www.g2.com/products/vlx/reviews"
]
```

### Verificaciones recomendadas

| Acción | Herramienta |
|--------|-----------|
| Verificar Domain Authority actual | Ahrefs / Moz |
| Identificar páginas con más backlinks | Ahrefs Site Explorer |
| Detectar backlinks rotos o tóxicos | Google Search Console > Links |
| Gap de backlinks vs SafetyCulture, GoAudits | Ahrefs Link Intersect |
| Verificar que perfiles sociales enlacen a vlx.ai | Manual |

### Oportunidades de link building identificadas desde el código
- Case studies publicados (ITI International confirmado en seed) — potencial para outreach a clientes mencionados
- 10 páginas de integración (Salesforce, SAP, Oracle, etc.) — oportunidad para link building desde directorios de apps de cada plataforma
- Blog con 116 posts — contenido linkable si está bien optimizado

---

## SECCIÓN 10 — POSICIONAMIENTO COMPETITIVO

### Competidores mapeados en código
- `vlx-vs-safetyculture` — `/compare/vlx-vs-safetyculture/` ✅ Página existe
- `vlx-vs-goaudits` — `/compare/vlx-vs-goaudits/` ✅ Página existe
- Mencionados en CLAUDE.md: GoCanvas, Jotform, Fulcrum, MaintainX, Fluix, Workiz — **Sin páginas de comparación**

### Gaps de comparación identificados

| Competidor | Página compare | Prioridad |
|-----------|---------------|---------|
| GoCanvas | ❌ Ausente | Alta — competidor directo |
| Jotform | ❌ Ausente | Media — comparación frecuente |
| Fulcrum | ❌ Ausente | Media |
| MaintainX | ❌ Ausente | Media |
| Fluix | ❌ Ausente | Media |
| Workiz | ❌ Ausente | Baja |

**Acción:** Priorizar `/compare/vlx-vs-gocanvas/` como siguiente página de comparación (mayor volumen estimado de búsqueda).

### Posicionamiento en SERP por vertical [PROVISIONAL]
- Verificar en GSC qué queries específicas están posicionando para cada vertical
- Comparar posición vs competidores para "digital inspection software [industry]" en cada vertical

---

## SECCIÓN 11 — ANALYTICS Y TRACKING

### Stack de analytics identificado en código

| Herramienta | Estado | Implementación | Notas |
|-------------|--------|----------------|-------|
| Google Analytics 4 | ✅ | `src/components/analytics/GoogleTag.tsx` | Via GTM |
| Meta Pixel | ✅ | `src/components/analytics/MetaPixel.tsx` | Implementado |
| PostHog | ✅ | `src/components/providers/PostHogProvider.tsx` | Via requestIdleCallback (no bloquea) |
| Vercel Analytics | ✅ | `<Analytics />` component en layout | Mínimo footprint |
| Vercel Speed Insights | ✅ | `<SpeedInsights />` component | Core Web Vitals tracking |

### Consent y GDPR
- `CookieConsent` component presente en layout
- Analytics cargados condicionalmente (PostHog via idle callback)
- **Verificar:** Que GA4/Meta Pixel respeten el estado del cookie consent antes de activarse

### Variables sin verificar [PROVISIONAL]
- **GA4 Measurement ID:** Verificar que esté configurado en env vars y no hardcodeado
- **Meta Pixel ID:** Verificar configuración
- **PostHog API Key:** Verificar env vars
- **Conversiones configuradas:** Verificar goals en GA4 para demo request y contact form submissions

### Formularios y tracking de conversiones
- Contact form: `src/app/(frontend)/api/contact/route.ts` — con rate limiting
- Demo request: `src/app/(frontend)/api/demo-request/route.ts` — con rate limiting
- **Faltante:** No hay evidencia de eventos de conversión explícitos en GA4 desde los handlers de formulario

---

## SECCIÓN 12 — HEADERS HTTP Y SEGURIDAD

### Headers de seguridad verificados en código (`next.config.ts`)

| Header | Estado | Valor |
|--------|--------|-------|
| Strict-Transport-Security (HSTS) | ✅ | `max-age=63072000; includeSubDomains; preload` (2 años) |
| X-Frame-Options | ✅ | `DENY` |
| X-Content-Type-Options | ✅ | `nosniff` |
| X-XSS-Protection | ✅ | `1; mode=block` |
| Referrer-Policy | ✅ | `strict-origin-when-cross-origin` |
| Permissions-Policy | ✅ | `camera=(), microphone=(), geolocation=()` |
| Content-Security-Policy | ❌ REPORT-ONLY | `Content-Security-Policy-Report-Only` — no enforcing |
| `X-Powered-By` | ✅ | Suprimido (`poweredByHeader: false`) |

### CSP Report-Only — Problema crítico

El CSP está en modo `Report-Only`, lo que significa que **no ofrece protección real**. Es necesario:
1. Revisar violation reports en el endpoint configurado
2. Identificar y corregir directivas que bloquean scripts legítimos
3. Promover a `Content-Security-Policy` enforcing

### Rutas WordPress legacy
- `/wp-admin` y `/wp-login.php` retornan 410 (correcto)
- `xmlrpc.php`, `wp-cron.php`, `wp-includes`, `wp-json`, `wp-content`, `wp-login.php` **aún hacen 301 a `/`** (según audit previo P1-7)
- **Acción:** Cambiar redirects de WordPress legacy a 410 (Gone)

---

## SECCIÓN 13 — OG TAGS Y SOCIAL META

### Cobertura actual de OG/Social tags

| Elemento | Estado | Evidencia |
|---------|--------|---------|
| OG Title (default) | ✅ | "VLX \| Digital Inspections & Verification Platform" |
| OG Description (default) | ✅ | Descripción global en layout.tsx:28-30 |
| OG Image (default) | ⚠️ | `opengraph-image.jpg` (178KB) — genérico |
| OG Image específica por ruta | ⚠️ | Solo 8 rutas |
| Twitter Card | ✅ | `summary_large_image`, site=`@vlx_ai` |
| OG Locale | ✅ | `en_US` |
| OG Site Name | ✅ | "VLX" |
| OG Type | ✅ | `website` global, `article` en blog posts |
| Canonical URL | ✅ | `alternates.canonical` en todas las páginas |
| hreflang | ❌ AUSENTE | No hay i18n implementado |

### Páginas sin OG image específica (prioridad alta)

Las siguientes páginas de alto valor social comparten el OG image genérico:
- `/digital-inspections-software/` (Hub)
- `/pricing/`
- `/about-us/`
- `/compare/vlx-vs-safetyculture/`
- `/compare/vlx-vs-goaudits/`
- Todas las 18 industry/use-case pages
- `/blog/` (índice)
- `/contact/`, `/demo/`

### Homepage sin OG image explícita en metadata

**Evidencia:** `src/app/(frontend)/layout.tsx:35-42` — el bloque OpenGraph de la metadata global no incluye `images`. Si bien existe el archivo `opengraph-image.jpg` como convención Next.js, no hay `openGraph.images` explícito en el metadata del homepage.

**Acción:** Agregar en la metadata de homepage:
```typescript
openGraph: {
  images: [{ url: "/opengraph-image.jpg", width: 1200, height: 630, alt: "VLX Digital Inspection Platform" }]
}
```

---

## SECCIÓN 14 — NORMALIZACIÓN DE URLS

### Configuración de trailing slash

- `next.config.ts`: `trailingSlash: true` ✅
- Todos los canonicals incluyen trailing slash ✅
- Sitemap usa trailing slash en todas las URLs ✅

### Redirects 301 configurados

| De | A | Estado |
|----|---|--------|
| `/digital-inspection-software` | `/digital-inspections-software/` | ✅ |
| `/inspection-companies` | `/digital-inspections-software/` | ✅ |
| `/asset-based-lending` | `/digital-inspections-software/financial-services/` | ✅ |
| `/team` | `/about-us/` | ✅ |
| `/wp-admin` | Retorna 410 | ✅ |
| `/wp-login.php` | Retorna 410 | ✅ |
| `xmlrpc.php`, `wp-cron.php`, etc. | 301 a `/` (pendiente cambiar a 410) | ⚠️ |

### IndexNow API

- Implementado en `src/app/(frontend)/api/indexnow/route.ts` ✅
- Submits URLs a Bing/search engines para indexación rápida
- **Verificar:** Que se llame correctamente al publicar contenido nuevo en Payload CMS

### URLs de blog con prefijos por categoría

El sistema de routing mapea posts a diferentes prefijos según categoría:
- `/blog/` — posts generales
- `/checklists/` — posts tipo checklist
- `/use-case/` — posts de casos de uso
- `/forms/` — posts tipo formulario
- `/reports/` — posts tipo reporte

**Riesgo:** Fragmenta la autoridad del blog. Considerar si esta dispersión es intencional y si los canonicals están correctamente configurados.

---

## SECCIÓN 15 — SOCIAL Y GMB

> **Estado:** PROVISIONAL — Verificación de perfiles requiere acceso manual a cada plataforma.

### Perfiles sociales declarados en schema

| Red | URL en schema | Verificación requerida |
|-----|--------------|----------------------|
| LinkedIn | `linkedin.com/company/vlxai/` | Verificar handle, descripción actualizada |
| YouTube | `youtube.com/@vlx-ai` | Verificar canal activo, videos actuales |
| Instagram | `instagram.com/vlx_ai` | Verificar handle activo |
| X (Twitter) | `x.com/vlx_ai` | Verificar @vlx_ai activo |
| Facebook | `facebook.com/vlxai` | Verificar página activa |
| G2 | `g2.com/products/vlx/reviews` | Verificar perfil actualizado |

### Checklist de consistencia NAP (Name, Address, Phone)

| Campo | Valor en schema | Verificar en perfiles |
|-------|----------------|----------------------|
| Name | "VLX" / "Visualogyx" | Consistente en todas las redes |
| Address | Aventura, FL, US | GMB + LinkedIn + Facebook |
| Email | `info@vlx.ai` | Consistente |
| URL | `https://vlx.ai/` | Consistente |

### Google Business Profile [PROVISIONAL]
- **No hay LocalBusiness schema** en el sitio — esto es un gap si VLX tiene presencia física en Aventura, FL
- **Verificar:** Si existe perfil en GBP, asegurar que esté verificado y actualizado
- **Acción:** Agregar LocalBusiness JSON-LD si hay presencia física relevante

---

## SECCIÓN 16 — ASO (APP STORES) [PROVISIONAL]

> Sin acceso a iTunes Lookup API o Google Play data en esta sesión.

### Presencia en App Stores declarada en código

- **SoftwareApplication schema:** `"operatingSystem": "Web, iOS, Android"` ✅
- **Aggregated rating:** 4.9/5 (20 reviews de G2+Capterra, no de App Store)

### Verificaciones recomendadas

| Plataforma | Acción |
|------------|--------|
| iOS App Store | Verificar rating actual, descripción, keywords, screenshots |
| Google Play Store | Verificar rating actual, descripción, keywords actualizada |
| Consistencia de rating | Alinear aggregateRating del schema con data real de stores |
| App name/keywords | Verificar que "digital inspection" aparezca en título/keywords del app |

---

## TOP 5 — SI SOLO HAY TIEMPO PARA 5 COSAS

1. **Eliminar testimonios placeholder de producción** (B6) — Daño directo a credibilidad y potencial indexación de contenido incompleto
2. **Eliminar HowTo schema deprecated** (B2) — Riesgo de penalización por Google; acción de 1-2h
3. **Agregar `/case-studies/` al sitemap** (B1) — Una línea de código; página importante sin indexación correcta
4. **Re-encodear las 2 imágenes de blog >1MB** (B7) — Impacto directo en Core Web Vitals (LCP)
5. **Crear OG images para hub, pricing y las 2 compare pages** (C1) — Mayor impacto en CTR social con menor esfuerzo

---

*Audit generado: 2026-06-19 · Última actualización: 2026-07-01 (integrado review dev.vlx.ai) · Próxima revisión recomendada: 2026-07-19*  
*Fuente: Inspección de código del repositorio VLX-Marketing (rama Hans-review)*  
*Datos PROVISIONAL requieren validación contra GSC API, herramientas de backlinks, y verificación live HTTP*
