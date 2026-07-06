# Re-validación de auditoría SEO — VLX (branch Hans-review fresco)

**Fecha:** 2026-07-04
**Auditora:** Laura / Software Craft CR
**Objeto auditado:** branch `Hans-review` @ `fb5e9b2` (30-jun-2026) de `Visualogyx/VLX-Marketing` — código fuente (worktree aislado)
**Baseline:** auditoría pre-migración 2026-06-19 (SEO Health 81/100)

---

## Por qué esta re-validación

El review del **2026-07-01** auditó el entorno `dev.vlx.ai`, que resultó estar corriendo un **build desactualizado**. Eso generó "errores nuevos" que no correspondían al código real más reciente. Esta re-validación audita el **código fresco del branch** (la última versión) para: (1) confirmar que la auditoría del 19-jun seguía siendo válida, y (2) separar los falsos positivos del 1-jul de los pendientes reales.

**Método:** auditoría de código estático sobre el worktree del branch (`next.config.ts`, `sitemap.ts`, schema, renderer, `*Content.tsx`, `Navbar`/`Footer`, rutas de blog). El contenido de los posts vive en Payload/Neon (DB compartida con `dev.vlx.ai`) → los issues de contenido se validan contra la revisión del Sheet "Blog Posts Review". El performance real (LCP) no es re-medible sin un entorno desplegado y navegable.

**Nota sobre indexación (importante):** el entorno pre-producción (`dev.vlx.ai` y cualquier subdominio de preview) está `noindex, nofollow` **por diseño** (check de `VERCEL_ENV` en el layout) hasta el lanzamiento en producción `https://vlx.ai`. Ese `noindex` es **esperado y NO se cuenta como hallazgo ni bloqueador**. El único `noindex` mencionado en este documento —las compare pages— es un *soft-unpublish* deliberado (phase 1), ajeno al entorno. Check de lanzamiento (no bloqueador): al pasar a `vlx.ai`, confirmar que la indexación quede habilitada.

---

## Veredicto

**La auditoría del 19-jun era correcta.** En el branch fresco, **todos los must-fix técnicos están resueltos salvo el bloqueador conocido de paid landing pages**. La mayoría de los "hallazgos nuevos" del review 1-jul eran **falsos positivos del build viejo**.

---

## 1. Estado de los 7 must-fix del 19-jun (en `fb5e9b2`)

| # | Must-fix | Estado | Evidencia |
|---|---|---|---|
| 1 | Hub LCP 7.9s | ⏳ Pendiente de medir en deploy | No re-medible en código estático |
| 2 | `aggregateRating` fabricado | ✅ RESUELTO | Eliminado; 0 ocurrencias (`src/lib/schema.ts:89-93`) |
| 3 | Sitemap con 5 categorías 308 | ✅ RESUELTO | Solo 4 categorías live (`sitemap.ts:175-188`) |
| 4 | 2 redirects `/use-case/` | ✅ RESUELTO | `next.config.ts:405-414` |
| 5 | Paid landing pages | ❌ ABIERTO (bloqueador) | 0/13 redirect (`next.config.ts`, `campaigns.ts:11-66`) |
| 6 | H1 homepage "VerificationInspections" | ✅ RESUELTO | `HeroTypewriter.tsx:67-81`, SSR limpio |
| 7 | Blog SSR + paginación rastreable | ✅ RESUELTO | `/blog/page/N` path-based, `index:true` |

---

## 2. Re-validación de los "hallazgos nuevos" del review 1-jul

| Hallazgo 1-jul | Veredicto en branch fresco | Evidencia |
|---|---|---|
| **NUEVO-1**: `/blog/page/N` → noindex + canonical a `/blog/` | ✅ **FALSO POSITIVO** (build viejo) | SSR path-based, canonical propio, `robots index:true` (`blog/page/[page]/page.tsx:79-81`) |
| **NUEVO-2**: slugs viejos top-level `/oil-and-gas/`, `/financial-institutions/` dan 404 | 🟡 **REAL menor** | Redirigen bajo el hub pero no en top-level; faltan 2 reglas (`next.config.ts:195-198,270-283`) |
| **NUEVO-3**: titles/meta fuera de rango (39/47 de 185) | 🟡 **ABIERTO parcial** | Sin guardrail de longitud; blog toma title/desc del CMS (`blog/[slug]/page.tsx:128-129`). Conteos no verificables en código |
| **NUEVO-4**: compare pages ganaron schema | ✅ **MAL ATRIBUIDO** | `Organization+WebSite` es global (`layout.tsx:67-74`); compare están **noindex** (despublicadas) |
| **NUEVO-5**: 32/37 SVG del hub sin `alt` | ⚪ Menor (no re-auditado a fondo) | — |
| **NUEVO-6**: sitemap 175→185 URLs | ⚪ Informativo | Crecimiento normal de contenido |
| OG "46/185 sin `og:image`" | ✅ **FALSO POSITIVO parcial** (build viejo) | 16 rutas con `opengraph-image.tsx` propio desde el 22-jun (commit `87557e1`) — supera el objetivo top-10 |

---

## 3. Blogs

### 3a. Sistema (código) — ✅ sólido
- **Renderer** (`RichTextRenderer.tsx`): converters estándar de Payload/Lexical + normaliza los links rotos de la migración de WordPress.
- **Paginación path-based** `/blog/page/N` y `/blog/category/*/page/N`, indexable, con `<a href>` reales SSR.
- **Sitemap**: solo 4 categorías live (200); posts enrutados por categoría canónica (`/checklists/`, `/use-case/`, `/forms/`, `/reports/`, `/blog/`).
- **Schema**: `Article` + `BreadcrumbList` (author no fabricado cuando el CMS no lo trae).

### 3b. Contenido (revisión "Blog Posts Review", 114 URLs) — ⚠️ fixes de CMS

Distribución: 61 `/blog/` · 35 `/checklists/` · 10 `/use-case/` · 2 `/forms/` · 1 `/reports/` · 5 otras.

| Categoría de issue | URLs afectadas | Prioridad | Quién lo arregla |
|---|---|---|---|
| Imágenes que no cargan | **12** | 🔴 **BLOQUEADOR** | ✏️ Cliente (CMS) |
| Estructura de listas perdida | **77** | 🟠 **P1** | ⚙️ Desarrollador |
| Meta description sin optimizar | 108 | 🟡 P2 | ✏️ Cliente (CMS) |
| Jerarquía de encabezados (h2/h3/h4) | 39 | 🟡 P2 | ✏️ Cliente (CMS) |
| Contenido faltante | 3 | 🟡 P2 | ✏️ Cliente (CMS) |
| CTAs/links rotos | 2 | 🟡 P2 | ✏️ Cliente (CMS) |
| Tablas incompletas | 1 | 🟡 P2 | ⚙️ Desarrollador |
| Display/HTML roto | 1 | 🟡 P2 | ⚙️ Desarrollador |

**Clasificación final:** las **imágenes que no cargan (12)** son un **bloqueador** — no se debe lanzar con contenido visiblemente roto. Las **listas mal estructuradas (77)** son **P1**. El resto es **P2** (meta descriptions 108, encabezados 39, contenido 3, CTAs 2, tabla 1, display 1).

Son issues de **contenido en Payload CMS** (migración WP→Payload), **no de código** — la mayoría se corrigen en el CMS, no en el branch. Los tres grandes (descriptions, listas, encabezados) son **sistemáticos**: el import del rich text perdió estructura de listas/encabezados y las meta descriptions no se optimizaron.

**Quién puede arreglar cada cosa:**
- ✏️ **Editable por el cliente en el CMS (Payload), sin código:** imágenes (re-subir), meta descriptions, contenido faltante, CTAs (re-enlazar) y el **nivel** de los encabezados (h2/h3/h4).
- ⚙️ **Requiere desarrollador (código):** listas mal estructuradas, tablas, display roto y encabezados anidados dentro de listas — porque el editor de Payload no permite crear ese HTML.

---

## 4. Pendientes REALES (priorizados)

1. **Paid landing pages** — P0 / bloqueador. 0/13 redirect; `/lp/[campaign]/` solo tiene 3 slugs nuevos; las campañas viejas dan `notFound()`. Hans debe crear los landers/redirects dedicados.
2. **Imágenes de blogs que no cargan (12)** — 🔴 bloqueador. Contenido visiblemente roto; no lanzar así. ✏️ El cliente las re-sube en el CMS (Payload), sin código.
3. **Listas mal estructuradas en blogs (77)** — P1. El import del rich text perdió la estructura de listas. ⚙️ Requiere desarrollador: el editor de Payload no permite crear ese HTML.
4. **Resto de contenido de blogs (CMS)** — P2. 108 descriptions + 39 encabezados + 3 contenido faltante + 2 CTAs + 1 tabla + 1 display. ✏️ Descriptions, encabezados (nivel), contenido y CTAs los edita el cliente en el CMS; ⚙️ tabla y display requieren desarrollador. Trabajo editorial en el CMS (varios en lote).
5. **2 redirects top-level** — P2. `/oil-and-gas/` y `/financial-institutions/` → sus destinos bajo el hub.
6. **Guardrail de longitud de title/description** — P2 / disciplina de CMS.
7. **Hub LCP** — P1 (heredado del 19-jun). Medir en un deploy navegable (no re-medible en código).
8. **H1 débiles menores** — `home-inspectors` y `asset-verification` (fuera del alcance del must-fix #8).

---

## 5. Conclusión

La versión anterior de la auditoría (**19-jun**) era válida. El equipo implementó correctamente los fixes técnicos. Los "errores adicionales" del review 1-jul provienen de **auditar un entorno con código desactualizado** (`dev.vlx.ai`); el código real del branch está en buen estado. El foco real queda en: **paid landers (bloqueador)** y **limpieza de contenido de blogs en el CMS**.
