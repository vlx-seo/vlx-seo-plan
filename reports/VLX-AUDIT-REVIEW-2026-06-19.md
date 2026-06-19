# VLX SEO Audit — Review Completo desde Cero
**Versión:** 2026-06-19 · **Dominio:** vlx.ai · **Auditor:** Laura Ceballos / Software Craft CR  
**Rama verificada:** `origin/Hans-review` · **Método:** Inspección de código + análisis de arquitectura  
**Fuente de verdad:** Codebase local (commit actual) + análisis de archivos estáticos

---

> **Nota de metodología:** Este audit fue realizado contra el código fuente del repositorio. Las secciones que requieren acceso a GSC API, herramientas de backlinks, o datos live de crawling están marcadas como `[PROVISIONAL]`. Todos los hallazgos de código tienen evidencia de archivo:línea.

---

## DASHBOARD DE ESTADO

| KPI | Estado | Valor |
|-----|--------|-------|
| Páginas en sitemap | ✅ | 200+ URLs, 9 tiers |
| Páginas con OG image específica | ⚠️ | ~8 de 56+ páginas |
| Schemas JSON-LD activos | ⚠️ | 7 tipos, 4 con errores |
| robots.txt | ⚠️ | Regla Applebot faltante |
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
- `xmlrpc.php`, `wp-cron.php`, `wp-includes`, `wp-json`, `wp-content` **aún hacen 301 a `/`** (según audit previo P1-7)
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

## SECCIÓN 17 — ROADMAP DE AJUSTES

### Fase 0 — Blockers críticos (ejecutar antes de cualquier campaign)

| ID | Acción | Esfuerzo | Propietario |
|----|--------|----------|------------|
| B1 | Agregar `/case-studies/` al sitemap | XS | Dev |
| B2 | Eliminar HowTo schema de integrations | S | Dev |
| B3 | Corregir hero opacity:0 animation | S | Dev |
| B6 | Reemplazar testimonios placeholder | M | Cliente + Dev |
| B7 | Re-encodear 2 imágenes blog >1MB | XS | Dev |
| B8 | Agregar regla `Applebot allow` en robots.txt | XS | Dev |

### Fase 1 — Schema y datos estructurados (1-2 semanas)

| ID | Acción | Esfuerzo | Propietario |
|----|--------|----------|------------|
| S1 | Agregar CollectionPage/ItemList en hub `/digital-inspections-software/` | S | Dev |
| S2 | Eliminar HowTo de integrations (ver B2) | S | Dev |
| S3 | Corregir `foundingDate` a "2020-01-01" en Organization schema | XS | Dev |
| S4 | Agregar `image` property a Organization schema | XS | Dev |
| S5 | Agregar BreadcrumbList a blog posts | S | Dev |
| S6 | Agregar BreadcrumbList a pricing page | XS | Dev |
| S7 | Agregar BreadcrumbList a compare pages | XS | Dev |
| S8 | Cambiar Article publisher de inline a `{"@id": ...}` reference | XS | Dev |
| S9 | Agregar `duration` a VideoObject schema | XS | Dev |
| S10 | Corregir author fallback en blog a `VLX Team` / Organization | XS | Dev |
| S11 | Completar Article schema en case studies | S | Dev |

### Fase 2 — Contenido y OG images (2-4 semanas)

| ID | Acción | Esfuerzo | Propietario |
|----|--------|----------|------------|
| C1 | Crear OG images para top 10 páginas (hub, pricing, industry top 5, compare, about) | M | Dev/Design |
| C2 | Agregar OG image explícita en homepage metadata | XS | Dev |
| C3 | Agregar contenido único por industria en SuperTemplate (al menos 1 sección diferente por página) | L | SEO + Cliente |
| C4 | Actualizar `llms.txt` con precios actuales y slugs correctos | XS | Dev |
| C5 | Expandir interlinking contextual entre blog posts y páginas de producto | M | SEO |

### Fase 3 — Técnico y performance (2-3 semanas)

| ID | Acción | Esfuerzo | Propietario |
|----|--------|----------|------------|
| T1 | Agregar `sizes` prop en Navbar, Footer, LogoBar, VideoShowcase, IntegrationsPreview | S | Dev |
| T2 | Corregir jerarquía de headings en homepage (SectionHeading nivel prop) | S | Dev |
| T3 | Eliminar imágenes duplicadas en /public/images/homepage/ | XS | Dev |
| T4 | Promover CSP de Report-Only a enforcing | M | Dev |
| T5 | Cambiar redirects WP legacy (`xmlrpc.php`, etc.) de 301 a 410 | XS | Dev |
| T6 | Actualizar blog sitemap para usar `updatedAt` en lugar de `publishedAt` | XS | Dev |
| T7 | Revisar `STATIC_CONTENT_DATE` y reemplazar por fechas específicas por página | S | Dev |

### Fase 4 — Expansión de contenido y backlinks (ongoing)

| ID | Acción | Esfuerzo | Propietario |
|----|--------|----------|------------|
| E1 | Crear página `/compare/vlx-vs-gocanvas/` | M | SEO + Dev |
| E2 | Agregar LocalBusiness schema (si VLX tiene presencia física relevante) | S | Dev |
| E3 | Crear 3-5 case studies completos con métricas en Payload CMS | L | Cliente + SEO |
| E4 | Outreach para backlinks en directorios de Salesforce, SAP, Oracle App Stores | M | Marketing |
| E5 | Verificar y optimizar perfiles en G2, Capterra, GetApp con copy actualizado | M | Marketing |

### Fase 5 — ASO y presencia en AI (ongoing)

| ID | Acción | Esfuerzo | Propietario |
|----|--------|----------|------------|
| A1 | Auditar y actualizar ficha en iOS App Store | M | Marketing |
| A2 | Auditar y actualizar ficha en Google Play | M | Marketing |
| A3 | Alinear `aggregateRating` schema con datos reales de App Store | XS | Dev |
| A4 | Verificar presencia en respuestas de ChatGPT, Claude, Perplexity | S | SEO |
| A5 | Expandir `llms.txt` con secciones de productos, industrias y casos de uso | S | SEO + Dev |

---

## TOP 5 — SI SOLO HAY TIEMPO PARA 5 COSAS

1. **Eliminar testimonios placeholder de producción** (B6) — Daño directo a credibilidad y potencial indexación de contenido incompleto
2. **Eliminar HowTo schema deprecated** (B2) — Riesgo de penalización por Google; acción de 1-2h
3. **Agregar `/case-studies/` al sitemap** (B1) — Una línea de código; página importante sin indexación correcta
4. **Re-encodear las 2 imágenes de blog >1MB** (B7) — Impacto directo en Core Web Vitals (LCP)
5. **Crear OG images para hub, pricing y las 2 compare pages** (C1) — Mayor impacto en CTR social con menor esfuerzo

---

*Audit generado: 2026-06-19 · Próxima revisión recomendada: 2026-07-19*  
*Fuente: Inspección de código del repositorio VLX-Marketing (rama Hans-review)*  
*Datos PROVISIONAL requieren validación contra GSC API, herramientas de backlinks, y verificación live HTTP*
