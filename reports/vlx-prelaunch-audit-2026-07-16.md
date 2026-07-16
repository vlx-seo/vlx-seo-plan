# Auditoría técnica SEO — Sitio nuevo VLX (Next.js) · pre-lanzamiento
**Fecha:** 2026-07-16 · **Auditora:** Laura Ceballos / Software Craft CR
**Objeto:** Sitio nuevo Next.js (repo `Visualogyx/VLX-Marketing`, rama Hans-review). **El equipo confirmó que `dev.vlx.ai` y `prod.vlx.ai` sirven el mismo build**, por lo que la verificación en vivo se hizo sobre **`dev.vlx.ai`** (accesible) y aplica igual a `prod.vlx.ai`. Ambos entornos están tras **AWS ALB + Amazon Cognito**.
**Template base:** `PROMPT-AUDIT-COMPLETO-VLX.md` v2.4 — revisado íntegro, sin saltar secciones.
**Prioridades:** (1) **velocidad de carga**, (2) **problemas que impidan la salida a producción**. Se revisa además todo lo demás del template.

> ⚙️ **Documento vivo.** Datos capturados en vivo el 2026-07-16 vía crawler `fetch()` autenticado sobre las 184 URLs del sitemap + probes puntuales. El servidor presentó cortes intermitentes durante la sesión (ver §6).

---

## ⚠️ Notas metodológicas
1. **Acceso:** entornos tras Cognito → solo auditables desde navegador autenticado. `prod.vlx.ai` bloqueó la automatización; se auditó `dev.vlx.ai` (build idéntico confirmado por el equipo).
2. **Herramientas externas bloqueadas por Cognito:** PageSpeed/Lighthouse/CrUX/crawlers públicos **no** pueden entrar → performance medido con Performance APIs in-browser (lab, no campo).
3. **Herramientas de backlinks/keywords no disponibles en esta sesión** → backlinks/keywords/SERP/ASO en vivo van **PROVISIONAL** o desde el último dato verificado.
4. **Limitación FCP/LCP:** la pestaña de automatización queda en segundo plano (`visibility:hidden`), lo que *throttlea* el render y **falsea FCP/LCP**. La velocidad de carga se evalúa con métricas independientes del throttle (pesos de recursos, TTFB, DOM, CLS) + histórico. El **LCP real de campo solo se medirá con PageSpeed una vez retirado Cognito** (post-launch).
5. **GSC/GA4:** el sitio nuevo no es propiedad verificada → sin baseline de tráfico (pre-lanzamiento).
6. **Regla de indexabilidad (cliente, 2026-07-16):** los entornos previos a salida **deben** servir `noindex, nofollow`. `index, follow` en pre-lanzamiento = **hallazgo**.

---

## 🚦 VEREDICTO DE LANZAMIENTO

**¿Hay algo que impida la salida a producción? → SÍ. Hay 2 bloqueadores P0 y 1 riesgo de infraestructura que deben resolverse antes de go-live.**

| # | Bloqueador P0 | Por qué impide lanzar |
|---|---|---|
| **P0-1** | **Estrategia de indexación mal configurada (no es por entorno).** El noindex está *hardcodeado por página*, no por entorno: 182/184 páginas sirven `index, follow` (deberían ser `noindex,nofollow` en pre-lanzamiento) **y** `/blog/` + `/contact/` sirven `noindex, nofollow` de forma fija → **esas dos seguirán noindex en producción**. Resultado en prod: el índice del blog quedaría **desindexado** y con `nofollow` (corta el flujo de enlace a los posts). | Rompe indexación en ambos sentidos: expone el pre-prod a indexación accidental si se cae Cognito, y desindexa el blog al lanzar. |
| **P0-2** | **Landing pages pagas en 404.** De 7 LPs de campaña conocidas, **5 dan 404** (`capterra-a`, `linkedin`, `video`, `mold-remediation`, `isn-vlx`) + todas las `/lp/*`. Solo `g2` y `ads` resuelven 200. Las 404 con tráfico pagado histórico (video 47, mold 37, isn-vlx 7 sesiones/año). | Con campañas activas en Capterra/G2/LinkedIn/Ads, el clic pagado cae en 404 = dinero perdido. **Confirmar con Hans qué campañas estarán activas al lanzar.** |
| **⚠️ Riesgo infra** | **Inestabilidad de servidor.** Durante la auditoría hubo cortes intermitentes y el TTFB del hub osciló entre ~85ms (caché caliente) y **910ms**. | Si refleja la infra de prod, es riesgo de disponibilidad y de LCP al lanzar. Validar con el equipo de infraestructura. |

**Alto impacto (no bloquean, resolver en semana 1):** OG images faltantes en 12 páginas de industria (P1), velocidad de carga del hub (JS pesado, P1), sitemap con 8 URLs que redirigen (P2), titles/metas fuera de rango (P2).

---

## 🧭 Rastreador de progreso

| # | Sección | Estado |
|---|---|---|
| 1 | Rendering / SSR | ✅ |
| 2 | Sitemap | ✅ |
| 3 | Schema (JSON-LD) | ✅ |
| 4 | H1s | ✅ |
| 5 | noindex / indexabilidad | ✅ |
| 6 | Core Web Vitals / velocidad de carga | ✅ (lab; campo pendiente post-launch) |
| 7 | Análisis GSC | ⬜ PROV (no aplica pre-launch) |
| 8 | Anomalías GSC | ⬜ PROV |
| 9 | Analytics y tracking | 🟨 (gtag presente; verificar en launch) |
| 10 | Servidor y headers HTTP | ✅ |
| 11 | Tags sociales (OG/Twitter) | ✅ |
| 12 | Normalización de URLs / redirects | ✅ |
| 13 | Content audit | ✅ |
| 14 | Benchmark de competidores | ⬜ PROV |
| 15 | Backlinks / link recovery | ⬜ PROV |
| 16 | AI / GEO | ✅ |
| 17 | Blockers pre-lanzamiento (B1–B8) | ✅ |
| 18 | Issues críticos (C1–C4) | ✅ |
| 19 | Documentos de estrategia | ✅ |
| 20 | Veredicto de lanzamiento | ✅ |
| 21 | Checklist post-lanzamiento | ✅ |
| 22 | Keyword walkthrough (on-page por URL) | ✅ |

---

# Hallazgos por sección

## 1. Rendering / SSR — ✅ OK
- **184/184 URLs del sitemap responden 200** con HTML completo (H1, title, meta, schema, links) vía `fetch()` crudo = exactamente lo que ve Googlebot. **SSR funcionando correctamente** en todo el sitio.
- No se requiere JS para el contenido principal. ✅

## 2. Sitemap — 🟡 (higiene P2)
- 184 URLs, sitemap **único** (no index), `Content-Type: application/xml`. ✅
- **Todas las URLs canonicalizadas a `vlx.ai`** (dominio de producción) aunque se sirven desde dev/prod → correcto para el cutover.
- **🟡 8 URLs del sitemap REDIRIGEN** (no deberían estar en el sitemap; solo URLs finales 200):
  - `/case-studies/empowers-qa-team-.../` → `/blog/empowers-qa-team-.../`
  - `/blog/product-updates-2026/`, `/blog/product-updates-2025/`, `/blog/2020-2/`, `/blog/2021-2/`, `/blog/2022-2/`, `/blog/2023-2/`, `/blog/2024-2/` → todas → `/whats-new/`
  - **Fix:** removerlas del sitemap (o servir contenido real en esas rutas).

## 3. Schema (JSON-LD) — ✅ (muy completo; gaps menores P3)
Cobertura por tipo de página (verificada en código, no asumida):
| Familia | Nº | Schemas presentes |
|---|---|---|
| Home | 1 | Organization, WebSite, SoftwareApplication, FAQPage, VideoObject |
| Hub | 1 | Organization, WebSite, **CollectionPage**, SoftwareApplication, BreadcrumbList |
| Industrias | 18 | Organization, WebSite, **Service**, **FAQPage**, BreadcrumbList (+1 con SoftwareApplication) |
| App (kypit/countit) | 2 | Organization, WebSite, SoftwareApplication, BreadcrumbList |
| Blog index | 1 | Organization, WebSite, CollectionPage\|Blog, BreadcrumbList |
| Blog posts | 68 | **Article** (61/68), Organization, WebSite, BreadcrumbList |
| Checklists | 36 | Article (35), + índice con CollectionPage/FAQPage |
| Use-case | 10 | Article, Organization, WebSite, BreadcrumbList |

- ✅ **NO reportar como faltantes** (el template lo advierte): el hub ya tiene CollectionPage; Organization/SoftwareApplication/FAQ ya implementados.
- **Los "7 blog posts sin Article"** = exactamente las 7 URLs-stub que redirigen a `/whats-new/` (no son posts reales) → se resuelve al limpiarlas del sitemap (§2). Los posts reales tienen Article. ✅
- **Gap menor (P3):** compare pages (`/compare/vlx-vs-safetyculture/`, `/vlx-vs-goaudits/`) **despublicadas** (no están en sitemap y `Disallow` en robots) → sin schema de comparación, pero es decisión de producto, no un error.

## 4. H1s — ✅ (1 excepción)
- **Home H1:** `"The AI-Native Enterprise Platform for [Inspections/Compliance/…]"` (typewriter). Renderiza limpio en SSR, **sin** la concatenación "VerificationInspections" (bug histórico **resuelto**). ✅
- **12/12 industrias con H1 orientado a keyword.** ✅
- **🟡 `/blog/` tiene 2 H1** (debería ser 1). Menor, pero corregir.

## 5. noindex / indexabilidad — 🔴 **P0-1 (bloqueador)**
- **182/184 páginas = `index, follow`**. Solo `/contact/` y `/blog/` = `noindex, nofollow`.
- **El noindex NO es por entorno** (no hay lógica `VERCEL_ENV`/env que lo aplique) — está **fijo por página**. Consecuencias:
  1. **Pre-lanzamiento (regla del cliente):** las 182 páginas deberían ser `noindex,nofollow` y no lo son → violación. Mitigante parcial: canonical→`vlx.ai` + Cognito bloquea crawlers hoy, pero si Cognito se retira/se filtra una URL, se indexan como dev/prod.
  2. **Al lanzar:** `/blog/` y `/contact/` **seguirán `noindex,nofollow`** → el **índice del blog quedaría desindexado** y con `nofollow` (corta enlace a los posts). `/contact/` noindex suele ser aceptable, pero `/blog/` NO.
- **Fix (antes de go-live):** implementar **noindex por entorno** (todo el pre-prod `noindex,nofollow`) y garantizar que en producción las páginas públicas — incluida `/blog/` — sean `index,follow`. Que la lógica dependa del entorno, no de valores fijos por página.

## 6. Core Web Vitals / VELOCIDAD DE CARGA — 🟠 P1 (énfasis)
**Medición lab in-browser (hub `/digital-inspections-software/`), 2026-07-16:**
| Métrica | Valor | Lectura |
|---|---|---|
| TTFB | 85ms (caliente) … **910ms** (frío) | Variable — **inestabilidad de servidor** (ver riesgo infra) |
| HTML (SSR) | 141 KB descomprimido / 20 KB gzip | Aceptable en hub |
| **JS** | **893 KB–1.46 MB descomprimido / 274–445 KB gzip · 29–69 archivos** | **Pesado — principal lastre de FCP/LCP bajo CPU móvil** |
| CSS | 130–260 KB descomprimido / 20–40 KB gzip · 3–5 archivos | Alto |
| Imágenes | ~24 KB · hero liviana | ✅ **No son el problema** |
| Fuentes | 69 KB · 2 archivos | OK |
| DOM | 656 nodos (hub) / **1776 (home)** | Home pesada |
| CLS | 0 | ✅ |
| FCP / LCP | No medibles en automatización (pestaña en background falsea el paint) | Medir con PageSpeed post-Cognito |

- **Diagnóstico (independiente del throttle):** infra rápida en caché caliente (TTFB ~85ms, CLS 0, imágenes optimizadas). El cuello es **JS pesado (274–445 KB gzip, hasta 69 archivos) + DOM SSR grande (home 352 KB descomprimido / 1776 nodos)** → bajo CPU móvil real esto retrasa FCP y por ende LCP.
- **Histórico:** último LCP real con throttling móvil (PSI, 23-jun, build previo) = **7.6s en el hub**, diagnosticado como limitado por FCP/render-path (no por la imagen). El diagnóstico **persiste** con los pesos actuales.
- **Fix (P1):** reducir/diferir JS (code-splitting, lazy de secciones below-the-fold, purgar JS/CSS no usado); re-medir LCP móvil <2.5s con PageSpeed en cuanto el sitio sea público.
- **Home:** HTML 352 KB descomprimido / 43 KB gzip, 1776 nodos DOM, JS 876 KB/269 KB gzip — la home es la más pesada de parsear; priorizar aligerarla.

## 7–8. GSC / Anomalías GSC — ⬜ PROVISIONAL
- El sitio nuevo no es propiedad verificada en GSC → sin baseline de tráfico/indexación real (esperado pre-lanzamiento). Se poblará al lanzar. Ver checklist post-launch (§21-E).

## 9. Analytics y tracking — 🟨 (verificar en launch)
- `gtag/js` **presente** (GA4 vía gtag). **GTM no detectado** en el HTML.
- No se pudo extraer el ID GA4 de forma limpia (posible inyección client-side). **Verificar en launch:** GA4 único, sin doble conteo (GTM + directo), y que los eventos de conversión (Start Free Trial / Book a Demo) disparen. Ver §21-D.

## 10. Servidor y headers HTTP — ✅ (1 mejora P3)
- `Strict-Transport-Security: max-age=31536000; includeSubDomains` ✅
- `X-Frame-Options: DENY` ✅ · `X-Content-Type-Options: nosniff` ✅ · `Referrer-Policy: strict-origin-when-cross-origin` ✅
- `Cache-Control: s-maxage=31536000` (CDN) ✅
- **Content-Encoding: gzip** — 🟡 P3: preferible **Brotli** (mejor ratio) para HTML/CSS/JS de texto.
- **CSP no observado** — P3: evaluar Content-Security-Policy.

## 11. Tags sociales (OG / Twitter) — 🟠 P1/P2
- **46/184 páginas SIN `og:image`**, incluyendo **12 páginas de industria** (alta intención comercial: quality, virtual, field-operations, asset-verification, transportation-logistics, oil-gas, manufacturing, real-estate, home-inspectors, service-companies, vehicle) + countit app, integrations, demo, whats-new, checklists, security, developers, faqs, careers, partners, contact, press (todas), case-studies, categorías de blog, y 7 stubs.
- **Fix (P1 para industrias, P2 resto):** agregar `og:image` propia ≥1200×630 por página, priorizando las 12 de industria.
- Twitter card `summary_large_image` presente donde hay OG. ✅

## 12. Normalización de URLs / redirects — ✅ (resuelto en su mayoría)
- **Trailing slash:** `/checklists` → `/checklists/` (301). Consistente. ✅
- **Slugs viejos WP:** `/oil-and-gas/` → `/digital-inspections-software/oil-gas/` (200) ✅ · `/financial-institutions/` → `/digital-inspections-software/financial-services/` (200) ✅ (antes daban 404 — **resueltos**).
- **🟡 `/oil-gas/` (top-level) → 404** (P3, tráfico bajo; agregar redirect si tuvo enlaces).
- **Blog pagination:** `/blog/page/2/` = 200, `index,follow`, canonical propio ✅ (**resuelto**; el falso positivo del 01-jul no aplica). 🟡 `/blog/?page=2` también 200 (URL con parámetro duplicada — verificar que el canonical la consolide; P3).
- **301 vs 308 (B1 histórico):** los redirects vienen de `permanent:true` en `next.config.ts` → emiten **308**. Google trata 308 = 301 para SEO; **no es bloqueador**, convención opcional pasar a 301. (No pude confirmar el código exacto en vivo por el opaque-redirect de `fetch`; se mantiene como P3 según código histórico.)

## 13. Content audit — 🟡 P2
- **31 titles fuera de rango** (vacío o >60): hub (71), varias industrias (64–67), press releases (78–103), paginación de blog (68–69), varios checklists/use-case (61–71). Mayoría marginal; priorizar press + hub.
- **32 meta descriptions fuera de rango** (>160): destaca `/use-case/the-essential-hotel-room-housekeeping-checklist/` con **1177 chars**, press releases 272–352, 10 industrias 167–210. Pasada de limpieza recomendada.
- **Thin/huérfanas:** sin hallazgos críticos nuevos; SSR y links internos (hub→19 hijas) OK.

## 14–15. Benchmark competidores / Backlinks — ⬜ PROVISIONAL
- Sin herramientas de backlinks/keywords en esta sesión. Referencia (última auditoría): competidores SafetyCulture, CompanyCam, GoFormz, Joyfill, Donesafe, Certainty, Fulcrum. Link recovery post-migración (~80 links a visualogyx.com sin reclamar). VLX ausente de listicles top (wifitalents/gitnux/zipdo/xenia). **Sin cambios verificables hoy** — no dependen del código del sitio.

## 16. AI / GEO — ✅
- **robots.txt** con postura IA deliberada intacta: `Allow` para OAI-SearchBot, PerplexityBot, Applebot, Claude-SearchBot, Claude-User; `Disallow` para CCBot, Bytespider, Google-Extended, GPTBot, Applebot-Extended, ClaudeBot, Amazonbot, FacebookBot.
- `Sitemap: https://vlx.ai/sitemap.xml` (producción). ✅
- **llms.txt presente** (5.65 KB, 200). ✅
- ⚠️ Nota: el robots.txt del pre-prod tiene `Allow: /` general → refuerza la necesidad del noindex por entorno (P0-1): hoy nada impide rastrear salvo Cognito.

## 17. Blockers pre-lanzamiento (B1–B8) — estado actual
| # | Blocker histórico | Estado hoy |
|---|---|---|
| B1 | 308 vs 301 | 🟡 308 (aceptado por Google; P3, no bloquea) |
| B2 | Blog SSR / posts en SSR | ✅ Resuelto (todos SSR) |
| B3 | Landing pages pagas sin redirect | 🔴 **Parcial — 5/7 en 404 + `/lp/*` 404** (P0-2) |
| B4 | Wildcard `/blog/:cat/:slug` | ✅ Paginación de categorías 200/index |
| B5 | `/oil-gas/` sin redirect | 🟡 top-level 404 (P3); el canónico `/digital-inspections-software/oil-gas/` OK |
| B6 | `/checklists/` redirect | ✅ Resuelto |
| B7 | BlogFilterBar no rastreable | ✅ Resuelto (SSR) |
| B8 | `/real-estate` | ✅ `/digital-inspections-software/real-estate/` 200 |

## 18. Issues críticos confirmados (C1–C4)
- **C1 (308→301):** 🟡 P3 (ver B1).
- **C2 (`/oil-gas/` 200 sin redirect):** en el sitio nuevo da 404 (no 200); el canónico redirige OK → resuelto en lo esencial.
- **C3 (`CollectionPage.itemListElement .slice(0,12)`):** no verificable por HTML (es detalle de código); el hub renderiza CollectionPage. Revisar en código si el itemList está completo — P2.
- **C4 (sitemap 0 indexadas en GSC):** N/A pre-launch; validar al enviar sitemap en el cutover (§21-A).
- **NUEVO — indexabilidad (P0-1)** y **NUEVO — inestabilidad de servidor** (ver veredicto).

## 19. Documentos de estrategia — ✅ revisados
- Consistente con `session-context.md` y re-auditorías de junio/julio. Los must-fix técnicos del 19-jun están mayormente resueltos; los reales pendientes coinciden: **paid landers** (P0-2) y **LCP/performance del hub** (P1). Se suma ahora la **indexación por entorno** (P0-1), no capturada antes por asumir auto-noindex del template.

---

## 📋 21. Checklist de validación POST-LANZAMIENTO
> Ejecutar en orden apenas se retire Cognito y el sitio quede público en `vlx.ai`.

### A. Indexabilidad y rastreo (primeras horas)
- [ ] `<meta name="robots">` = **`index, follow`** en todas las páginas públicas (¡lo contrario al pre-lanzamiento!). Confirmar que se retiró el noindex de entorno.
- [ ] **`/blog/` y `/contact/`**: confirmar que `/blog/` quedó `index,follow` (era noindex,nofollow en pre-prod → verificar que NO se propagó a prod).
- [ ] `robots.txt` público: permite Googlebot/Bingbot/Applebot; postura IA intacta; `Sitemap:` → `https://vlx.ai/sitemap.xml`.
- [ ] `sitemap.xml` 200, **sin URLs que redirijan** (limpiar las 8) ni 404.
- [ ] Canonicals → `https://vlx.ai/...`.
- [ ] Enviar sitemap en GSC (`sc-domain:vlx.ai`) + solicitar indexación de home y hub.
- [ ] IndexNow / Bing: enviar URLs clave.

### B. Redirects y migración (día 0)
- [ ] Toda URL con tráfico/enlaces de vlx.ai (WordPress) resuelve o redirige en **1 hop**, sin 404.
- [ ] **Landing pages pagas activas resuelven 200** (no 404): capterra, g2, linkedin, ads, video, mold, isn-vlx según campañas activas.
- [ ] Redirects 301 (o 308 aceptado), sin cadenas A→B→C.
- [ ] `visualogyx.com` sigue redirigiendo a `vlx.ai`.

### C. Performance real (día 0–1, ya sin Cognito)
- [ ] PageSpeed **móvil** sobre home, hub `/digital-inspections-software/` y top industry → registrar LCP/INP/CLS/FCP/TTFB.
- [ ] Confirmar **LCP hub < 2.5s** (histórico ~7.6s; foco en reducir JS/FCP).
- [ ] Revisar INP con el bundle JS real en campo.
- [ ] Confirmar estabilidad de TTFB (no picos ~900ms).

### D. Analytics y tracking (día 0)
- [ ] GA4 registrando pageviews una sola vez por navegación (sin doble conteo GTM+directo).
- [ ] Eventos de conversión (Start Free Trial / Book a Demo) disparando.
- [ ] GSC ↔ GA4 vinculados.

### E. Datos y monitoreo (semana 1)
- [ ] GSC cobertura sin errores masivos; sitemap "success".
- [ ] Export baseline GSC/GA4 para medir impacto de migración.
- [ ] Core Web Vitals de campo (CrUX) — se pueblan ~28 días post-launch.
- [ ] Monitorear rankings y tráfico orgánico vs baseline WordPress.

### F. Schema y social (semana 1)
- [ ] Validar rich results (Organization, SoftwareApplication, FAQ, Article, Breadcrumb) en GSC → Rich Results.
- [ ] `og:image` ≥1200×630 en las 12 industrias + páginas clave; validar en Meta/X/LinkedIn.

---

## 22. Keyword walkthrough (on-page, por URL) — 🟡 P2 (no bloquea)

Recorrido on-page de las páginas prioritarias contra el keyword-URL mapping (`KW-URL-MAPPING-2026-04-01-EN.md`): ¿el keyword objetivo de cada página está en title/H1/meta? Verificado en vivo el 2026-07-16.

**Veredicto:** el targeting on-page es **sólido** — mejora grande vs el mapping de abril (que advertía H1 con keyword perdido en varias industrias). **11/12 industrias tienen H1 keyword-led.** Los ajustes son refinamientos P2/P3, **ninguno bloquea el lanzamiento**.

| Página | Keyword objetivo | Title | H1 | Veredicto |
|---|---|---|---|---|
| `/` (home) | digital inspections software | 🟡 "Digital Inspection Software" (singular) | ❌ "The AI-Native Enterprise Platform for Inspections" (brand-led) | H1 no keyword-led — decisión de marca; al menos reforzar el KW en title |
| `/digital-inspections-software/` (hub) | digital inspections software | ✅ | ✅ | Perfecto |
| `…/oil-gas/` | oil and gas inspection software | 🟡 "…for Oil & Gas" | ✅ "Oil and Gas Inspection Software for Field Operations" | H1 recuperó el KW ✅; alinear title |
| `…/vehicle/` | digital vehicle inspection software | ✅ | ✅ | Perfecto |
| `…/real-estate/` | property inspection software for real estate | ❌ "Digital Inspection Software for Real Estate" | ✅ "Property Inspection Software for Real Estate" | Alinear title al KW del H1 |
| `…/insurance/` | insurance claims inspection software | 🟡 "…for Insurance & Claims" | ✅ "Insurance Claims Inspection Software" | Bien; title aceptable |
| `…/financial-services/` | virtual field inspection software for financial institutions | 🟡 "…for Financial Services" | ✅ "Virtual Field Inspection Software for Financial Institutions" | H1 nail el KW del mapping ✅; term URL "services" vs "institutions" |
| `…/manufacturing/` | digital inspection software for manufacturing | ✅ | ✅ | Perfecto |
| `…/transportation-logistics/` | transportation inspection software | 🟡 | 🟡 "Digital Inspection Software for Transportation & Logistics" | KW de bajo volumen; aceptable |
| `…/quality/` | digital quality inspection software | ❌ "Quality & Compliance Inspection Software" | ✅ "Digital Quality Inspection Software" | Alinear title al KW del H1 |
| `…/virtual/` | virtual inspection software | ✅ | ✅ | Bien |
| `…/mining/` | mining inspection software | ✅ | ✅ | Perfecto |
| `…/hospitality/` | hotel inspection software | ❌ | ❌ "Hospitality Inspection Software…" | **Gap:** apunta a "hospitality", no a "hotel" (donde está el volumen: hotel maintenance 170, hotel inspection). Evaluar incluir "hotel" |
| `…/construction/` | construction inspection software | 🟡 | ✅ "Construction Inspection Software for Jobsite Safety & QA" | Bien |
| `…/safety/` | safety inspection software | ✅ | ✅ | Bien |
| `…/home-inspectors/` | home inspection software | ✅ | ❌ "Home Inspections, Completed Before You Leave the Property" (benefit-led) | Title tiene el KW; H1 no — hacer H1 keyword-led |
| `…/service-companies/` | service company inspection software | ✅ | 🟡 "Inspection Software for Service Companies" | Bien |
| `…/asset-verification/` | asset verification software | ✅ | 🟡 "Asset & Vendor Verification" | Bien |
| `…/field-operations/` | field operations inspection software | ✅ | 🟡 "Field Inspection Software for Operations & Audits" | Bien |
| `…/inspection-companies/` | inspection software for inspection companies | ✅ | 🟡 "Digital Inspection Software for Every Industry" | H1 genérico; podría targetear "inspection companies" |
| `…/app/countit/` | AI object counting app | ✅ "…AI-Powered Object Counting" | ❌ "Snap, Count & Share" (brand-led) | H1 no keyword-led — el mapping sugiere "Count Any Object Instantly With AI". CountIt tenía 3,002 impr GSC (pos 12.6) |
| `…/app/kypit/` | KYPiT fraud detection | ✅ | ✅ "KYPiT: Know Your Product" | Brand + feature — OK |

### Hallazgos del walkthrough
1. **H1 no keyword-led en 3 páginas** (P2): home ("AI-Native Enterprise Platform"), home-inspectors ("…Before You Leave the Property"), countit ("Snap, Count & Share"). En home es decisión de marca deliberada; en home-inspectors y countit conviene un H1 keyword-led (el title ya lo tiene → fácil alinear).
2. **Desalineación title↔H1** (P2): real-estate y quality tienen el KW correcto en H1 pero el title arranca con "Digital Inspection Software for…" en vez del KW específico ("Property Inspection Software", "Quality Inspection Software"). Alinear titles.
3. **Gap de keyword en hospitality** (P2): la página usa "hospitality" y no "hotel" — el volumen está en "hotel maintenance/inspection". Evaluar incluir "hotel".
4. **Divergencias de slug vs el mapping** (P3 — confirmar si es intencional): `oil-gas` (mapping pedía `oil-and-gas`), `insurance` (vs `insurance-claims`), `financial-services` (vs `financial-institutions`), y countit/kypit bajo `…/app/` (mapping los quería en `/countit/` y `…/kypit/`). El mapping tenía rationale de keyword para los slugs largos; confirmar con el equipo si el cambio fue deliberado.
5. **Sin canibalización detectada** entre landings de industria (targetean keywords "software") y checklists (targetean keywords "checklist") — separación de intención correcta.

**Nota:** este walkthrough no cambia el veredicto de lanzamiento — son optimizaciones on-page P2/P3, no bloqueadores.

---

## 📝 Log de progreso
- **2026-07-16:** Auditoría completada sobre `dev.vlx.ai` (build confirmado idéntico a `prod.vlx.ai` por el equipo). Crawler `fetch()` autenticado sobre 184 URLs + probes + performance lab. Datos volcados a todas las secciones. **Pendiente:** reporte HTML (a solicitud del cliente, después del MD). LCP de campo real → medir con PageSpeed post-launch.
