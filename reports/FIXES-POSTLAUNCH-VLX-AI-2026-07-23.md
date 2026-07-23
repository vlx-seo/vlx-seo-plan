# Reporte de correcciones — Auditoría post-lanzamiento vlx.ai

**Fecha:** 2026-07-23
**Fuente:** [AUDIT-POSTLAUNCH-VLX-AI-2026-07-22](https://github.com/vlx-seo/vlx-seo-plan/blob/main/reports/AUDIT-POSTLAUNCH-VLX-AI-2026-07-22.md)
**Rama / PR:** `seo/postlaunch-audit-fixes` → `master` (repo `Visualogyx/VLX-Marketing`)
**Commit:** `2c55d15` — 30 archivos

---

## ✅ Corregido (incluido en el PR)

| # | Hallazgo | Cambio | Archivos |
|---|----------|--------|----------|
| **#2** | Jerarquía de headings rota (WCAG 1.3.1 / 2.4.6) | Navbar `<h4>/<h5>` → `<p>` (mismas clases); se elimina `role="heading" aria-level={2}` del Footer | `Navbar.tsx`, `Footer.tsx` |
| **#4** | TOC del blog no crawleable | `<button onClick>` → `<a href="#id">` (mantiene scroll suave + hash; habilita sitelinks "Jump to" y funciona sin JS) | `BlogTOC.tsx` |
| **#5** | Sitemap listaba URLs con 308 | Se excluyen posts `product-updates` (→ /whats-new/) y case studies duplicados que redirigen a /blog/ | `sitemap.ts` |
| **#9** | `CollectionPage.numberOfItems` no coincidía con los ítems listados | `numberOfItems` ahora deriva del mismo slice de 12 posts | `blog/page.tsx` |
| **#10** | `AggregateOffer` con precio incompleto | `lowPrice` 35 → 29 (refleja el plan Pro anual) | `schema.ts` |
| **#11** | `SoftwareApplication` sin `@id` vinculada | `@id` únicos en KYPiT/COUNTiT/integraciones, enlazados a `#software` / `#organization` | `kypit/page.tsx`, `countit/page.tsx`, `integrations/[slug]/page.tsx` |
| **#12** | Blog usaba `Article` genérico | `Article` → `BlogPosting` | `blog/[slug]/page.tsx` |
| **#13** | GA4 + Meta Pixel no contaban navegaciones SPA | Pageview en cada cambio de ruta vía `usePathname()` (salta el mount inicial para no duplicar) | `GoogleTag.tsx`, `MetaPixel.tsx` |
| **#15** | OG image 1200×629 | Redimensionadas a 1200×630 (OG + Twitter, root y (frontend)) | 4× `opengraph-image.jpg` / `twitter-image.jpg` |
| **#16** | Slug `oil-gas` ≠ keyword "oil and gas" | Ruta renombrada a `/oil-and-gas/` + 301 del slug viejo; enlaces internos y slugs actualizados | `next.config.ts`, ruta `oil-and-gas/`, +11 archivos de enlaces |

### Nota sobre #3 (H1 del homepage)
Se implementó y luego **se revirtió a petición**. El H1 vuelve a *"The AI-Native Enterprise Platform for…"* con la rotación original del typewriter. **No forma parte del PR.**

---

## ⏳ Pendiente

### Infraestructura (no es código de la app — requiere acceso a AWS/DNS)
| # | Tema | Acción |
|---|------|--------|
| **#1** | `/blog/page/N/` con N > total devuelve **200** en vez de 404 | El código ya tiene `notFound()`; es un desfase de **caché/edge del deploy**. Redeploy + invalidar CloudFront y verificar con `curl -I https://vlx.ai/blog/page/99/` |
| **#7** | `http://vlx.ai` no fuerza HTTPS (200 en vez de 301) | Regla de redirección en el edge/ALB |
| **#8** | `www.visualogyx.com` sin SSL válido (subdominio colgante) | Verificar backlinks y configurar 301 → vlx.ai |
| **#14** | CSP en `Report-Only` | Pasar a modo enforce tras estabilización (semana 2–4) |
| **#17** | WAF/ALB puede lanzar 503 en crawls rápidos | Revisar umbral de rate-limit para no frenar a Googlebot |

### Contenido / decisiones
| # | Tema | Acción |
|---|------|--------|
| **#6** | Slugs casi idénticos `7-modern-inspection-methods` vs `…-that-boost-efficiency-save-costs` | Son **dos posts distintos con tráfico propio**; revisar en Payload si el contenido es duplicado. Si lo es → 301 del más débil al fuerte |
| **#18** | Meta descriptions fuera de rango | ~14 páginas con description 161–210 chars (ninguna faltante). Recortar a ≤160 con CTA (decisión de copy) |

### Pasos de despliegue (tras aprobar el PR)
1. Merge del PR a `master` → deploy automático a AWS ECS (prod).
2. **Invalidar CloudFront** (`aws cloudfront create-invalidation --distribution-id <ID> --paths "/*"`) — sin esto la CDN sigue sirviendo el HTML viejo.
3. Verificar: `curl -I https://vlx.ai/digital-inspections-software/oil-gas/` → **301** a `/oil-and-gas/`.
4. Reenviar `sitemap.xml` en GSC + IndexNow; validar schemas en Rich Results Test; refrescar OG en el Sharing Debugger de Facebook.

---

## 🛠️ Tarea propuesta — Herramienta de gestión de caché desde el sitio

**Objetivo:** permitir purgar/invalidar la caché de CloudFront (y ver el estado de caché) desde el propio admin del sitio, sin depender de la AWS CLI ni de que quien edita contenido tenga credenciales de AWS.

**Motivación:** hoy, cada vez que se despliega un cambio o se corrige una página, el contenido no se refleja hasta invalidar CloudFront manualmente por línea de comandos. Esto crea fricción y hace que "arreglos ya desplegados" parezcan no aplicados (ver el análisis de caché abajo — es la causa raíz del #1 y de que los fixes no se vean sin invalidación).

**Alcance sugerido (MVP):**
- Acción protegida en el admin de Payload (detrás de auth) para disparar una invalidación de CloudFront:
  - Purga total (`/*`) o por rutas específicas (ej. solo `/blog/*`).
  - Idealmente, botón "purgar esta página" en el editor de cada post/página.
- Mostrar el estado de la última invalidación (pendiente/completada) y los headers de caché actuales de una URL.

**Consideraciones técnicas / de seguridad:**
- Usar el AWS SDK con un **rol IAM de alcance mínimo** (solo `cloudfront:CreateInvalidation` / `GetInvalidation` sobre la distribución concreta). Nunca exponer credenciales en el cliente.
- **Rate-limit**: las invalidaciones cuestan después del tramo gratuito (1.000/mes); evitar purgas totales en bucle.
- Registrar quién dispara cada purga (auditoría).
- Revisar de paso la política de redirects: los `permanent: true` (301) se cachean de forma agresiva; considerar `302` para reglas que puedan cambiar, para no "envenenar" cachés de navegadores.

**Prioridad:** media. No bloquea el lanzamiento, pero elimina fricción operativa recurrente y reduce el riesgo de "se corrigió pero no se ve".

---

## 📌 ¿Por qué la caché estaría afectando al sitio?

El sitio tiene **dos capas de caché** que explican los síntomas observados:

### 1. CDN (CloudFront) — `Cache-Control: s-maxage=31536000`
Las páginas se sirven con caché de CDN de **hasta 1 año**. Cuando se despliega un cambio de código, **CloudFront sigue entregando el HTML viejo** hasta que se hace una invalidación explícita. Consecuencias:
- Correcciones ya desplegadas "no se ven" hasta invalidar.
- Es la causa del **#1**: el código de paginación ya devuelve `notFound()` para páginas fuera de rango, pero el edge sirve una respuesta 200 cacheada de antes.

### 2. Navegador — redirects `permanent: true` (HTTP 301)
Los navegadores **cachean los 301 de forma indefinida**. Si en algún momento existió una regla de redirección de paginación (documentado en `next.config.ts:905`, hubo una versión con `?page=` que rompía la paginación), cualquier navegador que la visitó **guardó ese 301 para siempre**. Resultado: ese navegador sigue redirigiendo `/blog/page/N/` → `/blog/` localmente, **aunque el servidor ya esté corregido**.
- Esto fue exactamente lo observado: en un navegador limpio/incógnito la paginación funciona (200, sin redirect); en el navegador con el 301 cacheado, "cualquier página redirige a la principal".
- No hay forma sencilla de "des-cachear" un 301 ya guardado del lado del servidor: el usuario debe limpiar caché, o esperar a que expire. Por eso conviene usar `302` en reglas potencialmente temporales.

**Resumen:** la caché no "daña" el sitio, pero desincroniza lo que ve el usuario respecto al estado real del servidor — de ahí el valor de una herramienta de invalidación accesible y de una política de redirects más cuidadosa.
