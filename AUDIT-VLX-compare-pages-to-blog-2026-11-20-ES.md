# Auditoría páginas compare → blog (verificado en vivo 2026-11-20)

Páginas: `vlx.ai/compare/vlx-vs-goaudits/` y `vlx.ai/compare/vlx-vs-safetyculture/`.
Todo se verificó hoy en las webs oficiales de cada marca (vlx.ai, goaudits.com + support.goaudits.com, mitti.com + help.mitti.com). Nada sale de memoria.

---

## 0. Estado actual de las páginas (en vivo)

- Ambas responden 200 con `noindex, nofollow`. No están en el sitemap y la home no las enlaza.
- El contenido en vivo es idéntico al código de `origin/master` (`src/app/(frontend)/compare/*/page.tsx`). El último cambio de contenido es de junio de 2026: los datos tienen más de 5 meses.
- `/compare/` (el hub) da **404**, pero el breadcrumb JSON-LD de ambas páginas apunta a él.
- El comentario del código dice que también están bloqueadas en robots.txt, pero hoy robots.txt **no** bloquea `/compare/`. Lo único que las protege es el `noindex`.
- Volumen de keywords (Ranki, EE.UU.):
  - `safetyculture pricing`: 70/mes, KD 0.
  - `safetyculture alternatives`: 20/mes, KD 0.
  - `safetyculture iauditor`: 1.000/mes, navegacional.
  - `goaudits pricing`: 10/mes.
  - `goaudits alternative`: sin datos.
- No pude sacar impresiones de GSC: Search Console no está conectado en Ranki.

---

## 1. 🚨 Hallazgo nº1: SafetyCulture ya no se llama SafetyCulture

- El **11-ago-2026 se rebautizó como "Mitti"**. Nombre oficial actual: "Mitti (by SafetyCulture)".
- `safetyculture.com`, `/pricing` y `help.safetyculture.com` responden **301 → mitti.com**.
- Fuente: https://mitti.com/media-releases/safetyculture-becomes-mitti-unveiling-a-platform-built-for-the-future-of-frontline-work
- La página dice "SafetyCulture (formerly iAuditor)", así que está desfasada por dos nombres. Para el blog: **"Mitti (formerly SafetyCulture / iAuditor)"**, conservando "SafetyCulture" en el title, porque es lo que la gente sigue buscando.

---

## 2. Errores en los datos de VLX (afectan a ambas páginas)

| # | Lo que dice la página | Lo que dice vlx.ai hoy | Acción |
|---|---|---|---|
| 1 | KYPiT valida "image authenticity, user identity, **device fingerprinting**, location, **behavioral analysis**", con una fila por cada uno | Las 5 categorías oficiales son **Image Validation, User Identity Validation, Domain Analytics, Activity Analytics, IP Analytics** (/digital-inspections-software/app/kypit/, /faqs/). "Device fingerprinting" no aparece en ningún sitio del sitio. | Reescribir las filas con las 5 categorías reales |
| 2 | "AI-Powered **Fraud Detection**" | El FAQ dice literalmente "Does KYPiT prevent fraud? **No**…". Pricing lo llama "KYPiT data validation". | Usar "data validation" |
| 3 | "**Every** VLX customer gets a dedicated team member — **not just enterprise**" (GoAudits). "A VLX team member will help you set up your templates" (CTA de SafetyCulture) | FAQ: "Pro and Pro+ are **self-serve**". "**Enterprise** customers get a dedicated CSM". Pricing: "Dedicated relationship manager" solo en Enterprise. | **Falso, quitar.** CSM, custom onboarding, template setup y white-glove → "Enterprise" |
| 4 | Custom buttons disparan "status changes, notifications, **API calls**" | Help /action-buttons/: bloquean la página, cambian el estado y envían emails. No menciona llamadas a API. Nombre real: "Action Buttons", plan Pro+. | Quitar "API calls" |
| 5 | "Scheduled inspections" = ✓ (SafetyCulture) | Solo hay "Assignment & scheduling… with target completion dates". No hay inspecciones recurrentes programadas (eso existe solo en VLX Home, Early Access). | Reformular como "asignación con fecha objetivo" o quitar |
| 6 | Todas las funciones aparecen como ✓, sin indicar el plan | KYPiT, SSO, Excel/Word reports y KPI dashboards son **Enterprise**. Workflows, API y sharing son **Pro+**. | Indicar el plan en el blog. Si no, compara Enterprise de VLX con el plan barato del rival. |
| 7 | "SOC 2 Type 2 compliance" | /about-us/security/: "SOC 2 Type II **Attested**". | ✅ Correcto. No usar "certified". |
| 8 | "Start a free trial… No credit card required" | /pricing/: "15-day free trial on Pro or Pro+. No credit card required." | ✅ Correcto |

**Precios VLX hoy** (/pricing/):
- Pro: $29/usuario/mes facturado anual, $35 mensual (hasta 3 usuarios).
- Pro+: $47 anual, $55 mensual (3-20 usuarios).
- Enterprise: a medida.
- VLX Home + Pro+: $69.
- Virtual Inspections: $25 / $15 / $5 por validación.

**Contradicciones internas de vlx.ai.** Hay que resolverlas antes de publicar el blog, porque si no el post hereda el error:
- **KYPiT**:
  - La propia página de KYPiT mezcla la lista nueva (image/user/domain/activity/IP) con la vieja ("image, identity, device, location, and behavior"), igual que /product/.
  - Plan: Enterprise según pricing, pero "Pro+ and Enterprise" según /help/kypit/.
  - La sigla se desarrolla como "Know Your Product" en un sitio y "Know Your Product, Process, Provenance" en otro.
- **AI Template Builder**: Pro en /features/, Pro+ en la home y en el FAQ.
- **API**: Pro+ en pricing y product, Enterprise en features.
- **Word reports**: Enterprise en pricing; "Pro+ (upon request)" en el JSON-LD del FAQ de pricing.

**Funciones actuales de VLX que las comparativas no mencionan:**
- AI Scribe y AI Inspection Walkthroughs.
- Virtual Inspections (captura en vivo por un tercero, sin app).
- COUNTiT/Instacount.
- Biblioteca pública de más de 1.000 plantillas.
- Sincronización con Drive, Dropbox, OneDrive y SharePoint.
- BI (Power BI, Tableau, Looker).
- Custom approval workflows.
- Historial de versiones de plantillas.
- Marca de agua con los metadatos de la imagen.

---

## 3. GoAudits: fila por fila (solo lo que cambia)

Precios hoy (goaudits.com/pricing, por usuario/mes):
- **Starter**: $10 al año / $12 al mes.
- **Enterprise**: $30 al año / $35 al mes.
- Trial de 14 días sin tarjeta y garantía de devolución de 60 días.
- ✔ verificado por mí directamente.

| Fila | Página dice | Realidad hoy | Veredicto |
|---|---|---|---|
| SSO (Entra ID) | ✗ | SAML 2.0 con **Microsoft Azure AD**/Okta. Add-on de pago en Enterprise (support.goaudits.com/en/articles/13176270) ✔ | **INCORRECTO** |
| Dedicated CSM | ✗ | "Dedicated Account Manager — YES - for annual plans over 3 users" (pricing) ✔ | **INCORRECTO** |
| Template setup assistance | ✗ | "Free checklist setup from our team!" (pricing) ✔. Construyen tu checklist gratis en 1-2 días. | **INCORRECTO** |
| Custom onboarding | ✗ | Programa de onboarding gratuito vía demo (/free-trial/) | **INCORRECTO** |
| Location verification | ✗ | **Geofencing GPS** que impide empezar la auditoría fuera de la zona. Enterprise (artículo 9691522) ✔ | **INCORRECTO** |
| Excel exports | "Single view" | Dos exports: Details y Summary (Excel/CSV), más APIs de export | **INCORRECTO** |
| Custom Word reports | ✗ | "Reports in Word/Excel" en Enterprise ✔. No hay evidencia de plantillas .docx personalizadas. | PARCIAL → "Word export sí; plantillas .docx a medida no" |
| Daily activity reports | ✗ | "Daily Digest" programado (Enterprise, bajo petición) | PARCIAL |
| Inspection locking | ✗ | Al reabrir una auditoría, ubicación, fecha y auditor quedan bloqueados. Hay "Audit Date Lock". | PARCIAL |
| Third-party sharing | ✗ | Responsables externos sin licencia reciben la acción por email | PARCIAL |
| Roles / Team hierarchy | "Basic" / "Limited" | En Enterprise: roles personalizados por empresa, ubicación y checklist, más "Roles & Hierarchy" | SUBESTIMADO |
| Repeatable sections | "Limited" | No existen; solo se pueden clonar preguntas | Cambiar a ✗ |
| Video/audio | "Photos only" | Correcto, pero hay un add-on de subida de archivos (pdf/docx/xlsx) | OK con matiz |
| AI de cualquier tipo | ✗ | **Ninguna IA** en web, ayuda ni app | ✅ Correcto. Es una ventaja real de VLX. |
| Calculated fields, barcode, free-form, custom statuses, custom buttons, field ID mapping, behavioral/device | ✗ | No encontrados | ✅ Correcto |
| "Más asequible para equipos pequeños" | — | Starter a $10 es justo. Pero muchas filas ✓ de GoAudits son solo del Enterprise a $30 (≈ VLX Pro a $29). | OK. Aclarar los planes. |

Verificado por el agente en las 218 páginas del help center. Yo lo confirmé directamente en: SSO, account manager, checklist setup, geofencing y precios.

---

## 4. SafetyCulture → Mitti: fila por fila (solo lo que cambia)

Precios hoy (mitti.com/pricing) ✔ verificado por mí:
- **Free**: $0 (hasta 10 seats).
- **Premium**: $24/seat/mes al año, $29 al mes.
- **Enterprise**: a medida.
- Banner: "AI included in every plan".
- **El trial de Premium pide tarjeta.** VLX no la pide: es un diferenciador real.

| Fila | Página dice | Realidad hoy | Veredicto |
|---|---|---|---|
| Nombre | "SafetyCulture (formerly iAuditor)" | **Mitti (by SafetyCulture)** desde el 11-ago-2026 ✔ | **INCORRECTO** |
| AI-generated templates | ✗ | "The AI Assistant generates a structured template" (help.mitti.com/000087) ✔ | **INCORRECTO**. Se cae una "ventaja" de VLX. |
| Custom Word (.docx) reports | ✗ | "Word export is only available to organizations on Premium Plan or Enterprise Plan" (help.mitti.com/000055) ✔ | **INCORRECTO** → "Word export sí; Field ID mapping no" |
| Calculated fields | "Limited" | Fórmulas con funciones en cualquier plan; solo admite campos numéricos o slider (help.mitti.com/002991) | Casi ✓ |
| No-code triggers | ✗ | Logic triggers, Zapier, Power Automate, Integration Builder (no-code sobre Workato) y Agents programados. "Custom buttons" como tal no existe. | PARCIAL → comparar "action buttons", no automatización en general |
| CSM / custom onboarding | "Enterprise only" | "Onboarding services and training: Contact sales" en **Premium y Enterprise** ✔ | PARCIAL |
| Location verification | ✗ | GPS por respuesta con precisión, "proof… at the correct location" (help.mitti.com/002230) | PARCIAL |
| Video & audio | ✓ | Vídeo de hasta 3 min sí. Audio en inspecciones no (solo en AI Issue Creation). | PARCIAL |
| Signatures with GPS | ✓ | Hay firma, pero el GPS no va ligado a ella | PARCIAL |
| Free-form (no template) | ✗ | Las inspecciones requieren plantilla, pero los "Issues" permiten reportar sin plantilla (con IA desde foto o voz) | PARCIAL |
| Excel "Single view" | — | Export a CSV/Excel, bulk export en Premium+ | Probablemente INCORRECTO |
| API, webhooks, Zapier, SSO (Azure), ERP/CRM | ✓ | Correcto, pero **solo en Premium+** (SSO Azure ✔ help.mitti.com/000017) | OK. Añadir el plan. |
| AI/fraud (image authenticity, behavioral, etc.) | ✗ | Su lista de IA (actualizada el 3-nov-2026) no incluye validación ni antifraude | ✅ Correcto. KYPiT sigue siendo el diferenciador. |
| Public Library / Sensors & IoT | fortalezas de SC | "Thousands of free templates" ✔. Sensors & IoT siguen activos, más telemática (Samsara, Geotab). | ✅ Correcto |
| Template setup / white-glove | ✗ | No verificable. Venden servicios de onboarding y una red de partners. | Quitar o suavizar |

**Funciones de 2025-2026 que la página ignora:**
- AI Assistant (chat y voz, crea acciones e informes).
- Agents programados.
- AI Issue Creation.
- Resúmenes IA en informes.
- Training (EdApp integrado).
- Assets con telemática.
- Contractor Management.
- Mitti Insurance.

---

## 5. Diferenciadores de VLX que siguen siendo verdad (base del blog)

1. **KYPiT (data validation en 5 categorías)**: ningún rival tiene algo equivalente.
2. **Word (.docx) personalizado con Field ID mapping**: los rivales exportan a Word, pero no con plantilla propia.
3. Free-form inspections en timeline, sin plantilla.
4. Barcode con auto-parsing a campos (GoAudits no tiene; Mitti lo tiene básico).
5. Bloqueo a nivel de página con Action Buttons, y custom statuses en inspecciones.
6. Sharing externo con 3 roles (Collaborator, Viewer, Assignee).
7. Trial sin tarjeta (frente a Mitti, que la pide).
8. IA frente a GoAudits (que no tiene ninguna). Frente a Mitti la IA ya no es un diferenciador.

---

## 6. Plan de conversión a blog

1. **Corregir la fuente de verdad primero**: KYPiT (lista y plan), plan de AI Template Builder, API y Word. Si no, el blog hereda las contradicciones.
2. Dos posts, KW principal al comienzo del title (convención VLX):
   - `/blog/safetyculture-alternative/` → **title**: "SafetyCulture Alternative: VLX vs Mitti (formerly SafetyCulture) 2026". **H1**: "SafetyCulture Alternative for Teams That Need Verified Inspection Data". Secundarias: `safetyculture pricing` (70), `mitti`, `iauditor alternative`.
   - `/blog/goaudits-alternative/` → **title**: "GoAudits Alternative: VLX vs GoAudits Features & Pricing (2026)". Secundaria: `goaudits pricing`.
3. Tabla con **columna de plan** en ambos lados, fecha "Verified Nov 2026" y enlaces a las fuentes. Sin ✗ que no se puedan sostener.
4. Al publicar: **308** de `/compare/vlx-vs-*/` al post nuevo. Quitar las rutas y el breadcrumb al `/compare/` (404). Tener en cuenta el guardrail de SEO del build (hreflang/señales) al borrar rutas.
5. Repetir esta verificación de precios y funciones del rival justo antes de publicar y cada trimestre (Mitti está sacando IA cada mes).
