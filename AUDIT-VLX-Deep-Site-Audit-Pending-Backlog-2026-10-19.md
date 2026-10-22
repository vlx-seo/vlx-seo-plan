# VLX — Auditoría profunda del sitio + backlog de pendientes

**Fecha:** 2026-10-19 · **Verificación:** EN VIVO (crawl de las 165 URLs del sitemap + lectura del dashboard).
**Nota:** GSC/GA4 por MCP no estuvo disponible (seo-server caído) → todo verificado con curl / navegador.
Las métricas de ranking/impresiones provienen del trabajo previo (Ranki + GSC export).

> **🔄 Re-validación en vivo 2026-10-22:** marcadas ✅ las ya corregidas desde la auditoría →
> A1 (404 fuera del sitemap), L13 (CollectionPage `/blog/`), S5 (alt de logos), S8 (H1 de industrias).
> **Siguen pendientes:** redirect "mold" (B5), doble H1 en `/blog/` (A2), schema de `/pricing/` (O6),
> y el H1 de `/quality/` (aún "Digital Quality Inspection Software").

---

## Veredicto técnico

El sitio está **muy limpio técnicamente**. El crawl de las 165 URLs del sitemap devuelve:
**164 en 200, con canonical propia, 1 solo H1 y title presente. 0 mismatches de canonical, 0 noindex indebidos, 0 títulos/H1 faltantes.**

La **canibalización de las páginas de servicio** sigue **resuelta y desplegada** (reaudit 2026-10-14): las 19 páginas bajo `/digital-inspections-software/` tienen la keyword unificada en title + meta description + H1 (el hub es la única excepción intencional con H1 en plural). Ningún vertical compite ya por el head term del hub.

---

## A. Nuevos problemas detectados en esta auditoría

1. **✅ HECHO (2026-10-22)** — ~~URL muerta en el sitemap~~ `/blog/digital-inspection-app-ai-human-review/` **ya NO está en el sitemap**. La URL suelta todavía devuelve 404 (servir 410 en lugar de 404 queda como opcional).
2. **🟡 `/blog/` tiene 2 `<h1>` idénticos** ("Insights & Updates") — duplicación responsive; uno debería ser `<p>` o estar visually-hidden. Menor.
3. **🟢 `lastmod` del sitemap estancado** — casi todas las entradas en 2026-06-18; debilita la señal de frescura. → Emitir `lastmod` real por página.
4. **🟢 Redirect apex con puerto explícito** — `http://vlx.ai` → 301 → `https://vlx.ai:443/`. Funciona, pero el `:443` es cosmético y conviene limpiarlo.

## B. Problemas de contenido confirmados abiertos (ya conocidos, siguen vivos)

5. **🔴 Redirect "mold" roto** — `/blog/checklists/mold-inspection-checklist/` → 308 → `/checklists/mold-inspection-checklist/`, que es **404**. La página real viva es `/blog/mold-inspection-checklist/` (200). → Corregir el destino del 308.
6. **🟠 Duplicado "7-modern-inspection-methods"** — `/blog/7-modern-inspection-methods/` y `/blog/7-modern-inspection-methods-that-boost-efficiency-save-costs/` ambos 200 y **ambos en el sitemap**. → Fusionar + 301 al ganador.
7. **🟠 Solape supply-chain** — `/blog/9-key-benefits-of-supply-chain-management/` + `/blog/why-supply-chain-management-software-is-essential-for-modern-logistics/` ambos 200 (≈84k + 8k impresiones, ~0 clics). → Consolidar + re-apuntar a intención informacional.

## C. Medición (confirmado caído — congelado por decisión de Laura, solo se reporta)

8. **🔴 Home sin GTM/GA4/Pixel** — 0 `googletagmanager`, 0 `GTM-`, 0 `fbq` en el HTML vivo. Coincide con el bloque de KPIs del dashboard ("medición muerta desde ~agosto"). Corresponde a las tareas **T4 + S27**. *No se acciona por instrucción previa de Laura.*

## D. Performance (pendiente — dashboard + notas previas)

9. **🔴 S22** — la imagen LCP a `w=1080&q=75` no respondía (no verificable por curl; medir con PageSpeed/CrUX).
10. **🔴 S25** — 64 imágenes devuelven 500; la rama que lo corrige está escrita pero **sin PR**. · **🟠 S23** — no hay CDN frente a vlx.ai (HTML, JS y `/_next/image` salen del origen).

## E. Schema (pendiente)

11. **🟠 O6 (PENDIENTE)** — `/pricing/` sin `SoftwareApplication`/`AggregateOffer` (0 en vivo). · **✅ L13 HECHO (2026-10-22)** — `/blog/` CollectionPage ya emite 14 `ListItem` (ya no capado a 12); confirmar en código que se quitó el `.slice(0,12)`.

---

## Estado del dashboard (vlx-seo-plan)

**99 tareas → 26 hechas · 1 en curso · 72 pendientes** (estado leído de Supabase, snapshot 2026-10-19).

**Correcciones por verificación en vivo** (marcadas "backlog" pero ya ciertas): **L1** (robots permite Google/Bing/Applebot), **L7** (hreflang + x-default live), **L2** (prod sirve index,follow), y el **apex http→https** (301) → cerrables. **S24 (ISR)** probablemente cerrable: fue revertido y el equipo resolvió con revalidación on-demand.

### 🔄 En curso (1)
- **S28** — CMS no publica a todos los visitantes (VT-9376); Vivek, fix en dev/testing.

### ⏳ Pendientes (72)

**Fase 0 — Launch (verificaciones)**
- L3 enviar sitemap a GSC · L4 IndexNow (Bing) día 0 · L5 baseline GSC · L8 monitor Coverage diario · L9 `compare_gsc.py` redirects · L6 PageSpeed 5 páginas.
- A13 ASO: rebrand CountIt (Visualogyx→VLX) · A14 GMB + claim G2/Capterra/GetApp.

**Fase 1 — Stabilization**
- 🔴 T4 integrar GTM · S27 montar contenedor GTM + cutover *(medición: congelada por decisión de Laura)*.
- 🔴 S22 LCP image · S23 sin CDN · S25 64 imágenes 500 (rama lista, sin PR) · S26 ops PR#89 (NEXT_PUBLIC_SITE_URL dev + rotar draft secret).
- S29 `seoTitle` viejo al renombrar post (revisar 116) · S31 verificar noindex en dev.vlx.ai · S24 ISR *(probablemente cerrable)*.
- S18 7 páginas con lista rota · S17 limpieza de contenido migrado (CMS) · **✅ S5 HECHO** (alt de logos ya con nombre de empresa) · S7 CTAs en 22 meta descriptions · **✅ S8 HECHO** (H1 de industrias ya keyword-first) · S3 re-encode 2 imágenes blog >1MB a WebP.
- C1 2 imágenes case-study 403 (CloudFront) · C4 2 páginas con jerarquía de headings rota · C5 3 posts sin contenido · C6 2 posts con CTA roto · C7 1 tabla incompleta + 1 display roto (dev).
- S2 CRO checklist→free trial · S30 botón primario falla contraste · S32 congelar scope de los 3 nuevos servicios.
- S1 ASO keywords/screenshots · S19 reindexar en GSC · S20 añadir canal YouTube a GSC.
- S9 test AI Mentions (5 plataformas) · S13 AI Overviews · S12 GMB local pack · S10 disavow ~120 spam · S15 outreach (wifitalents/gitnux/zipdo/xenia) · A11 backlinks (G2/Capterra) · S37 enviar emails de outreach.

**Fase 2 — Optimization (0/14)**
- 🟠 O6 schema SoftwareApplication+AggregateOffer en `/pricing/` (confirmado pendiente) · **✅ L13 CollectionPage `/blog/` HECHO (2026-10-22)**.
- O7 keyword remapping desde GSC (canibalización) · O14 re-priorizar keywords por volumen real · O10 sprint de enlazado interno.
- O2 brc audit checklist (pos 10.2) · O3 apartment inspection checklist (pos 12–16) · O4 homeowners insurance checklist (pos 11) · O1 home insurance checklist (pos 29, rebuild) · O5 rebuild oil-and-gas (pos 22).
- O9 title+meta striking-distance · O12 listicle "Best Digital Inspection Software 2026" · O13 rework 3 product landings · O15 revisar posts que alimentan las 3 product pages · O11 programmatic (mapear 17 SuperTemplate → gaps).

**Fase 3 — Growth & AI (0/6)**
- G4 audit 2 competidores directos · G5 audit 3 alternativos · G1 comparison pages (2 directos) · G2 GEO hub+about para citación IA · G3 FAQ/AEO + schema · G6 benchmark 50 keywords.

**Fase 4+ — Growth 90+ (0/6)**
- P1 content gaps de competidores · P2 comparison pages (alternativos) · P3 ASO deep A/B · P4 local SEO expansion · P5 programmatic 20–40 industry pages · P6 monitoreo semanal GSC.

---

## Quick wins técnicos

**✅ Ya hechos (validado en vivo 2026-10-22):** URL 404 fuera del sitemap (A1) · CollectionPage `/blog/` (L13) · alt text de logos (S5) · H1 de industrias con keyword (S8).

**⏳ Pendientes — las 4 de código van por PR a VLX-Marketing (`dev` protegida, solo por PR):**
1. Corregir el redirect "mold" (B5).
2. Arreglar el doble H1 en `/blog/` (A2).
3. Inyectar schema `SoftwareApplication`+`AggregateOffer` en `/pricing/` (O6).
4. Quitar el "Digital" del H1 de `/quality/` para unificar con title/desc (residual de canibalización).

**⏳ Pendientes de contenido (CMS, sin PR salvo páginas de servicio):** CTAs en 22 meta descriptions (S7) · fusionar duplicados 7-modern (B6) y supply-chain (B7).
