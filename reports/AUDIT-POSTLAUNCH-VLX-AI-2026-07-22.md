# Auditoría SEO Post-Lanzamiento — vlx.ai

**Fecha:** 2026-07-22
**Sitio:** https://vlx.ai/ (Next.js 15 + Payload CMS — **ya lanzado en producción**)
**Auditor:** Claude Code — verificación en vivo (Googlebot UA + curl) + auditoría de código (`VLX-Marketing-master/`)
**Template aplicado:** `PROMPT-AUDIT-COMPLETO-VLX.md` v2.5 + `flujo-seo`
**Contexto:** El sitio Next.js **reemplazó a WordPress**. Ya **no hay "blockers" de pre-lanzamiento** — los pendientes se reclasifican como acciones post-lanzamiento por urgencia.

---

## 0. Cambio de estado — la migración salió

| Elemento | Antes (pre-launch) | Ahora (verificado en vivo 2026-07-22) |
|---|---|---|
| Producción `vlx.ai` | WordPress + NitroPack | **Next.js + Payload CMS** ✅ |
| Meta robots homepage | (WP) | `index, follow, max-image-preview:large, max-snippet:-1, max-video-preview:-1` ✅ |
| `robots.txt` | (WP) | Coincide con `src/app/robots.ts` (política AI-crawlers activa) ✅ |
| `visualogyx.com` | dominio viejo | **301 → vlx.ai en 1 salto** (CloudFront) ✅ |
| `prod.vlx.ai` | duplicado indexable (riesgo) | **301 → app.visualogyx.com** (riesgo resuelto) ✅ |
| `dev.vlx.ai` | Cognito | sigue tras Cognito (no indexable) ✅ |

**Veredicto general:** el lanzamiento fue **limpio en lo fundamental**: indexación abierta correctamente, dominio viejo redirige en 1 salto conservando equity, robots/sitemap coherentes, schema rico y headers de seguridad presentes. Quedan ajustes técnicos de calidad (ninguno bloqueante) que se listan abajo por prioridad.

---

## 1. Crawleabilidad e Indexación

### ✅ Correcto
- **robots.txt** (live): `Allow: /`; `Disallow: /lp/ /api/ /admin/` + 2 compare pages; política avanzada de crawlers IA (permite OAI-SearchBot, PerplexityBot, Applebot, Claude-SearchBot/User; bloquea GPTBot, ClaudeBot, Google-Extended, CCBot, Bytespider, Amazonbot, FacebookBot). `Sitemap: https://vlx.ai/sitemap.xml`.
- **sitemap.xml**: 200, **184 `<loc>`**. `urlset` válido. Excluye correctamente `/lp/*`, compare pages (fase 1), 9 páginas de integración consolidadas (404) y las 3 categorías de blog que redirigen (case-studies, checklists, product-updates). Usa fecha fija `2026-06-18` para estáticas (evita falsa frescura) y `updatedAt` para posts/case studies. **Buen sitemap.**
- **`index,follow`** confirmado en homepage, hub, páginas de industria y posts de blog.
- **Compare pages** = `noindex, nofollow` ✅ (fase 1) + disallow en robots.txt.
- **`trailingSlash: true`** global → forma de URL canónica consistente (todas con `/` final).

### 🟠 Hallazgo — paginación de blog "infinita" / soft-404 (P1)
`/blog/page/N/` con **N mayor al total de páginas devuelve HTTP 200** en vez de 404:
- `/blog/page/11/` → 200 · `/blog/page/50/` → 200 · `/blog/page/99/` → **200 con `<link rel="canonical" href="…/blog/page/99/">` (self-canonical)** y contenido "no posts".
- El código (`blog/page/[page]/page.tsx:121`) tiene `if (pageNum > totalPages) notFound()`, pero **el deploy en vivo no lo aplica** (desfase build/deploy o caché de edge sirviendo 200).
- **Riesgo:** Google puede rastrear infinitas URLs de paginación vacías, cada una auto-canónica → *index bloat* y señal de soft-404.
- **Contraste:** la paginación de **categoría** (`/blog/category/[slug]/page/N/`) sí redirige (308) en overflow — comportamiento inconsistente entre ambas rutas.
- **Fix:** garantizar que `/blog/page/N/` con N > total devuelva **404** (redeploy del branch que ya tiene el `notFound()`, o regla de edge). Verificar con `curl -I /blog/page/99/`.

### 🟢 Paginación de blog — lo que SÍ está bien
- Arquitectura **path-based crawleable** (fix B9): página 1 en `/blog/`, resto en `/blog/page/2/`…`/blog/page/10/` (10 páginas, 12 posts/página).
- `/blog/page/1/` → **308 → `/blog/`** (canonicaliza correctamente la página 1) ✅.
- Enlaces de paginación son `<Link href>` reales, **visibles en el HTML SSR** (componente `Pagination.tsx`, `<nav aria-label="Blog pagination">`). Prev/Next + números con `aria-current`.
- Cada página paginada declara su **canonical propio** (`/blog/page/2/` → self) ✅ y aparece en el sitemap.
- Post cards renderizados como `<a href>` crawleables en el fallback SSR (`BlogListContent.tsx` → `BlogListFallback`).
- *Nota menor:* `/blog/` usa `export const dynamic = 'force-dynamic'` (no SSG) — funcionalmente OK (el fallback SSR es crawleable) pero pierde cacheo estático; evaluar `revalidate` (ISR) en vez de `force-dynamic`.

---

## 2. Anclas de contenido (deep-linking con `#`)

**El requerimiento específico está mayormente cubierto**, con una mejora recomendada:

### ✅ Los encabezados de contenido llevan `id` (anclas `#`)
- `RichTextRenderer.tsx` sobreescribe el conversor de headings de Lexical para emitir **`id` en cada `h2`/`h3`** (slug único vía `uniqueSlugFor`, con `class="scroll-mt-24"` para el offset del navbar sticky).
- Verificado en vivo en `/blog/difference-qms-eqms/`: **25 de 30 `h2/h3` tienen `id`** (los 5 sin `id` son headings del chrome/nav, no del contenido). Ejemplos: `id="understanding-quality-management-in-modern-organizations"`, `id="key-differences-between-qms-and-eqms-software"`.
- Esto permite **deep-linking** (`…/post/#seccion`) y habilita los "jump links" de Google en la SERP.

### 🟡 Mejora — la tabla de contenidos no expone enlaces ancla crawleables (P2)
- `BlogTOC.tsx` navega con **`<button onClick={scrollTo}>` + IntersectionObserver**, no con `<a href="#id">`. En vivo el post tiene **solo 1** `href="#…"`.
- Consecuencia: los `id` existen (deep-linking manual funciona), pero **no hay enlaces ancla internos** que Google pueda usar como señal de estructura ni que funcionen sin JS.
- **Fix:** convertir cada entrada de la TOC en `<a href="#${entry.id}">` (mantener el smooth-scroll con `preventDefault` opcional). Bajo esfuerzo, mejora accesibilidad + posibilidad de sitelinks "Jump to".

---

## 3. HTML semántico y jerarquía de encabezados

### 🟠 Jerarquía de encabezados rota site-wide (P1) — CONFIRMADO EN VIVO
El outline de **todas** las páginas arranca con headings del chrome global **antes** del `<h1>`:
- Homepage (live): `<h4>` … `<h4>` … `<h5>` … **luego** `<h1>` "The AI-Native Enterprise Platform…".
- Hub (live): mismo patrón `<h4><h4><h5>` antes del `<h1>` "Digital Inspections Software…".
- Origen (código): `Navbar.tsx` emite `<h4>By Use Case</h4>`, `<h4>By Industry</h4>`, `<h5>Run your inspection business on VLX</h5>` (mega-menú, presente en SSR aunque oculto por CSS); `Footer.tsx` emite `<p role="heading" aria-level={2}>`.
- **Impacto:** estructura de encabezados incoherente en todo el sitio + viola WCAG 1.3.1 / 2.4.6. No es fatal para indexación (el `<h1>` de contenido existe y es único), pero es una señal de calidad y accesibilidad.
- **Fix:** cambiar los `<h4>/<h5>` del navbar a `<p>/<span>` (mismo estilo) y quitar `role="heading" aria-level` del footer. Objetivo: outline = `<h1>` (hero) → `<h2>` sección → `<h3>` subsección.

### ✅ Correcto
- Un único `<h1>` por página con keyword (18/18 páginas de industria en auditoría de código; verificado en vivo homepage/hub/post).
- `<article>`/`<time datetime>` semánticos en las cards de blog.

---

## 4. On-Page y estrategia de keyword (hallazgo estratégico)

### 🟠 Desalineación H1 ↔ title en la homepage (P1 estratégico)
- **`<title>` / OG:** "Digital Inspection Software | VLX by Visualogyx" (keyword principal ✅, 47 chars ✅).
- **`<h1>` (live):** "**The AI-Native Enterprise Platform** for inspections, audits, and compliance".
- La H1 pivotó a un término **brand-coined sin volumen de búsqueda** ("AI-Native Enterprise Platform") mientras el title mantiene la keyword de alto valor. La keyword principal del proyecto ("digital inspection software") **ya no está en el H1** de la página más importante.
- **Recomendación:** reincorporar la keyword al H1 (o a un `<h2>` inmediato del hero) sin perder el posicionamiento "AI-Native". Ej.: H1 con "AI-Native Digital Inspection Software…" o un H2 de apoyo con la keyword. Reconciliar con la estrategia de KW (`AUDIT-KW-STRATEGY-2026-04-09.md`).

### ✅ Correcto
- **Hub** `/digital-inspections-software/`: H1 con keyword ("Digital Inspections Software for inspections, audits, and compliance") + **48 enlaces internos en body** a subpáginas → arquitectura hub-cluster sólida (penalización del Core Update de marzo mitigada).
- Meta descriptions presentes con propuesta de valor y datos ("12,000+ users, 25+ countries").
- Slug canónico `oil-gas` (redirige `oil-and-gas` → `oil-gas` vía 308). ⚠️ *Pendiente estratégico:* la keyword objetivo es "oil **and** gas"; reconciliar slug vs estrategia (el H1 sí usa "Oil and Gas").

---

## 5. Redirects y arquitectura de URLs

### ✅ Correcto
- **`visualogyx.com` → 301 → vlx.ai** (1 salto, CloudFront); deep-links viejos (`/digital-inspections-software/`) también 301. **Equity de la migración de dominio fluye correctamente** → link recovery bien encaminado.
- `www.vlx.ai` → 301 → `vlx.ai` (Cloudflare) ✅.
- Slugs viejos redirigen: `/oil-and-gas/` → `/digital-inspections-software/oil-gas/`; `/real-estate/` resuelto.

### 🟡 Hallazgos
- **308 en los redirects internos de Next.js** (P2, baja urgencia): 297 reglas con `permanent: true` en `next.config.ts` emiten **308** (0 con 301). Confirmado en vivo (`/oil-and-gas/` → 308, `/blog/page/1/` → 308). **Google honra 308 como 301 desde 2016**, así que post-lanzamiento esto es **prioridad baja** (era P0 pre-launch por prudencia migratoria). Fix opcional: emitir 301 vía middleware/edge si se quiere el estándar canónico.
- **`http://vlx.ai/` (apex) devuelve 200, no 301→https** (P2): HSTS + preload protege navegadores reales, pero el apex http no fuerza redirect (mientras `http://www.vlx.ai` sí 301). Inconsistencia menor; idealmente 301 http→https en el apex.
- `www.visualogyx.com` no responde con cert válido (posible subdominio colgante) — verificar si algún backlink viejo lo usa.

---

## 6. Datos estructurados (Schema)

### ✅ Muy completo (live)
- **Homepage:** Organization, WebSite, SoftwareApplication + AggregateOffer, **FAQPage** (Question/Answer), VideoObject, ImageObject, ContactPoint, Place, PostalAddress.
- **Hub:** **CollectionPage + ItemList (ListItem)** ✅ — el gap de "hub sin ItemList" **ya está resuelto**. + BreadcrumbList, SoftwareApplication, Organization, WebPage.
- **Posts de blog:** **Article** + BreadcrumbList + **Person** (autor, E-E-A-T ✅) + Organization + WebSite.

### 🟡 Ajustes finos (P2)
- Posts usan `Article` en vez de **`BlogPosting`** (válido, pero `BlogPosting` es el tipo idóneo para blog).
- `CollectionPage.itemListElement` del `/blog/` sigue con **`.slice(0, 12)`** mientras declara `numberOfItems: allPosts.length` → **mismatch de conteo** (Google marca discrepancia). Fix: remover el `.slice(0,12)` (no afecta crawleabilidad del HTML, solo el JSON-LD).
- `AggregateOffer` con `lowPrice 35 / highPrice 55` hardcodeado ignora los planes anuales (29/47) → `lowPrice` engañoso.
- `SoftwareApplication` duplicado sin `@id` en integrations/kypit (no enlaza al nodo canónico).

---

## 7. Rendimiento, headers y social

### ✅ Correcto (live)
- **Headers de seguridad:** `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, `Referrer-Policy: strict-origin-when-cross-origin`, **HSTS 2 años + preload**. `Cache-Control: s-maxage=31536000` (cache CDN de estáticas) ✅.
- **OG/Twitter:** `og:title`, `og:description`, `og:image` **1200×629** absoluto (`https://vlx.ai/opengraph-image…jpg`), `twitter:card=summary_large_image`, `twitter:site=@vlx_ai`. `metadataBase` correcto (URLs OG absolutas a vlx.ai).

### 🟡 Ajustes
- **CSP en `Content-Security-Policy-Report-Only`** (decisión documentada) — pasar a modo *enforce* post-estabilización (P2).
- `og:image` 1200×**629** (debería 630 — trivial).
- **Core Web Vitals:** no medibles con datos de campo aún (CrUX tarda ~28 días post-lanzamiento). Pendiente: PageSpeed en homepage/hub/blog/pricing como baseline de laboratorio (P1 — ver acciones urgentes). El WAF (503 ante rastreo rápido) impidió correrlo en esta sesión.

---

## 8. Analytics / Tracking
- CSP live confirma la presencia de **GA4 (googletagmanager/google-analytics), PostHog, Meta Pixel, Vercel** (allowlist en `connect-src`/`script-src`).
- **Riesgo conocido (código, P2):** GA4 (`GoogleTag.tsx`) y Meta Pixel (`MetaPixel.tsx`) disparan pageview solo en el montaje inicial → **sub-conteo** en navegación SPA del App Router. PostHog sí maneja route changes. No hay GTM → sin riesgo de doble conteo. Fix: listener de cambio de ruta para gtag/Pixel.
- *(La verificación en vivo de IDs quedó pendiente por el 503; confirmar en próxima sesión con petición espaciada.)*

---

## 9. Barrido de sitemap (184 URLs) — estado de todas las URLs

Barrido completo en vivo (Googlebot UA, pacing 2s tras el rate-limit del WAF):

| Métrica | Valor |
|---|---|
| Total URLs en sitemap | **184** |
| **200 limpio** | **176** |
| Redirects 308 | **8** |
| 301 / 404 / 5xx | **0 / 0 / 0** |
| noindex (muestra 15) | 0 |
| canonical apuntando a otra URL (muestra 15) | 0 |
| URLs del sitemap bloqueadas por robots.txt | **0** |

**Muestra de 15 (homepage, hub, industrias, posts, categorías, paginación blog+categoría, integrations, pricing, product):** todas `200`, `index,follow` y **canonical auto-referencial** — incluida la paginación `/blog/page/2/` y `/blog/category/industry-trends/page/2/`, que se auto-canonicalizan correctamente. Sin conflicto sitemap↔robots (el sitemap no incluye `/lp/`, `/api/`, `/admin/` ni `/compare/`).

### 🟡 Hallazgo — 8 URLs del sitemap son redirects 308 (P2)
Un sitemap no debe listar URLs que redirigen; sus **destinos ya están en el sitemap como 200** (duplicación de señales):

| URL en sitemap (308) | Redirige a (200, ya en sitemap) |
|---|---|
| `/case-studies/empowers-qa-team-with-streamlined-inspection-processes/` | `/blog/empowers-qa-team-…/` |
| `/blog/product-updates-2026/`, `/blog/product-updates-2025/` | `/whats-new/` |
| `/blog/2020-2/` … `/blog/2024-2/` (5 URLs) | `/whats-new/` |

**Fix:** excluir estas 8 del `sitemap.ts` (dejar solo destinos finales 200).

### 🟡 Hallazgo — posible contenido duplicado (P2, revisar)
Dos slugs casi idénticos, ambos 200 e indexables:
`/blog/7-modern-inspection-methods/` **y** `/blog/7-modern-inspection-methods-that-boost-efficiency-save-costs/`. Revisar si son el mismo contenido → consolidar con canonical/301.

### ✅ Cobertura por sección (todas 200 salvo lo anotado)
- **Core:** product, pricing, features, integrations (+zapier), demo, developers, whats-new.
- **Hub + 21 subpáginas** `/digital-inspections-software/*` (incl. app/countit, app/kypit, 16 industrias, use-cases): 200.
- **Corporativas/legales** (about, careers, contact, partners, faqs, security, accessibility, privacy, terms, eula): 200.
- **/press/** (4 releases) + **/case-studies/** (index + iti-international) 200; 1 case-study → 308.
- **Blog:** hub + paginación `/blog/page/2..10/` (9 pág, 200) + 4 categorías con su paginación (200) + ~55 posts (200); 7 posts "año/product-updates" → 308.
- **/checklists/** (hub + 35), **/use-case/** (10), **/forms/** (2), **/reports/** (1): 200.

> **Nota de infraestructura:** el barrido de 184 peticiones disparó **503 (rate-limiting del ALB/WAF, `Server: awselb/2.0`)**, no una caída — todas las URLs sirvieron 200/308 durante el crawl. Googlebot real espacia más las peticiones; conviene revisar que la regla de rate-limit no estrangule crawlers legítimos en picos.

---

## 10. Acciones — URGENTE vs. NO PRIORITARIO

> Ya **no hay blockers** (el sitio salió). Clasificación por urgencia real post-lanzamiento.

### 🔴 HOY / Launch week (verificación e indexación)
1. **Enviar sitemap a GSC** (`vlx.ai/sitemap.xml`) y **URLs vía IndexNow** (Bing) — arrancar recrawl de la nueva estructura.
2. **Capturar baseline GSC/GA4** post-lanzamiento (clicks, impresiones, posición) para medir recuperación.
3. **Monitorear GSC Coverage a diario** — vigilar picos de 404 y cómo se indexan las URLs nuevas.
4. **PageSpeed/CWV baseline** en homepage, hub, industria top, blog, pricing (con peticiones espaciadas por el WAF).
5. **Arreglar paginación infinita** `/blog/page/N/` (N>total) → debe dar **404**, no 200 self-canónico (§1).

### 🟠 Semana 1–4 (calidad técnica, alto impacto)
6. **Jerarquía de encabezados**: quitar `<h4>/<h5>` del navbar y `role=heading` del footer (§3).
7. **H1 homepage**: reincorporar keyword "digital inspection software" al H1/H2 del hero (§4).
8. **TOC del blog** → enlaces ancla `<a href="#id">` en vez de `<button>` (§2).
9. **Schema blog**: remover `.slice(0,12)` del `itemListElement`; migrar `Article` → `BlogPosting` + `BreadcrumbList` (§6).
10. **Meta descriptions** faltantes/fuera de rango (revisar inventario) con CTA.
11. **Sub-conteo analytics**: listener de route-change para GA4 + Meta Pixel (§8).
12. **Limpiar sitemap**: excluir las 8 URLs 308 (`sitemap.ts`) — dejar solo destinos 200 (§9).

### 🟡 Mes 1–2 (optimización)
13. **308 → 301** en redirects internos (baja urgencia; Google honra 308) — vía middleware/edge (§5).
14. `http://vlx.ai` (apex) → 301 https; verificar `www.visualogyx.com` colgante (§5).
15. **CSP** Report-Only → enforce (§7). Corregir `AggregateOffer` (incluir anual) y `@id` de SoftwareApplication (§6).
16. Reconciliar slug `oil-gas` vs "oil-and-gas" con estrategia de KW (§4).
17. Revisar/consolidar slugs duplicados `7-modern-inspection-methods*` (§9).
18. Revisar regla de rate-limit del ALB/WAF (503 en picos) — que no estrangule crawlers legítimos (§9).
19. Keywords striking-distance (pos 5–25) — priorizar hub y páginas de industria con datos GSC frescos.

### ⚪ Continuo
- Link recovery / outreach (~80 links a visualogyx.com — el 301 ya conserva equity, reclamar los directos).
- Listicle propio "Best Digital Inspection Software 2026" + outreach a wifitalents/gitnux/zipdo/xenia.
- AI mentions (target 80+/mes), ASO (App Store/Play), GBP local (Aventura FL).
- Monitoreo CWV de campo (CrUX) a los ~28 días.
- **Estrategia HCU + foros/comunidades (nuevo, Fase 3 → HCU1/HCU2 en el dashboard):**
  - **HCU1 — Presencia en foros:** Google prioriza hoy resultados de comunidad (Reddit, Quora "hidden gems") y las AI Overviews los citan. Mapear e intervenir en hilos de alta intención donde rankean "digital inspection software", "best inspection app" y los dolores por industria — Reddit (r/QualityAssurance, r/facilities, r/CommercialRealEstate, r/insurance, r/askengineers), Quora, foros nicho de EHS/inspecciones, Q&A de G2/Capterra, grupos de LinkedIn. Participación **auténtica y útil de primera mano** (declarar afiliación, aportar valor real — nada de spam); sembrar respuestas donde VLX está ausente; medir presencia en el SERP de foros. Se conecta con AI mentions (S9/S13) y outreach de listicles (S15).
  - **HCU2 — Contenido experience-first:** el Helpful Content system premia experiencia real y utilidad. Producir piezas con POV de inspector/auditor, datos propios anonimizados, before/after y metodología, con **autores nombrados + bios (E-E-A-T)**; reutilizar esa experiencia en las respuestas de foros y en `llms-full.txt` para alimentar también la citación por IA. Complementa GEO (G2) y FAQ/AEO (G3).
  - _Nota: el notebook de NotebookLM referenciado por Lau es privado (requiere su cuenta Google) y no pudo abrirse; esta tarea se basa en la interpretación estándar de "estrategia HCU en foros" — ajustar si el notebook tenía especificaciones concretas._

---

## Anexo — Metodología
- Verificación en vivo con `Invoke-WebRequest`/`curl.exe` (Googlebot UA), sin herramientas de pago (seo-server no disponible en la sesión → secciones de rankings/tráfico/backlinks quedan para próxima sesión con GSC).
- Auditoría de código sobre `VLX-Marketing-master/` (branch master, checkout local).
- ⚠️ El rastreo rápido activa el WAF del sitio (503 Service Temporarily Unavailable) → auditar espaciado.
