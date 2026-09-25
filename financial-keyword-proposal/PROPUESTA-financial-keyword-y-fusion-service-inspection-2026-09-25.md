# Propuesta: keyword de Financial Institutions + fusión de Service/Inspection Companies

**Fecha:** 2026-09-25
**Autor:** lceballos-seo
**Estado:** Propuesta — pendiente de aprobación del equipo (no implementada)
**Alcance:** 2 decisiones sobre páginas de servicio bajo `/digital-inspections-software/`

---

## Resumen

Este documento propone dos cambios y explica por qué importan y cómo llevarlos a cabo:

1. **Cambiar la keyword principal** de la página de *Financial Institutions* a `collateral inspection software`.
2. **Unificar** las páginas de *Service Companies* e *Inspection Companies* en una sola.

Ambos salen del mismo análisis de canibalización y de una investigación de keywords validada contra la SERP real (mercado: EE. UU.). Los datos de respaldo están en `vlx-keyword-decision-financial-y-service-companies-2026-09-24.csv` y `vlx-ranki-keyword-research-detalle-2026-09-17.csv`.

---

## Parte A — Financial Institutions: cambiar la keyword principal

### Situación actual
- **Página:** `/digital-inspections-software/financial-institutions/`
- **Keyword actual:** `virtual field inspection software for financial institutions`
- Describe con precisión el producto y **rankeamos #1** por ella, pero **no tiene volumen de búsqueda medible**. Es una frase que redactamos nosotros, no la que teclean los compradores. Resultado: un primer lugar que casi no trae tráfico.

### Propuesta
Adoptar como principal **`collateral inspection software`** y bajar la actual a secundaria (se conserva en el copy porque ya rankea).

### Por qué importa
- **Es como la industria nombra el trabajo:** bancos y prestamistas de *asset-based lending* buscan una herramienta para inspeccionar y verificar el **colateral** de un préstamo (inventario, equipo, floor plan, obra).
- **Demanda real + intención correcta:** la SERP es 100% jugadores de colateral de préstamos (First American, Alogent, Truepic…) y **vlx.ai ya aparece ~5.º** en la variante con "software".
- **Baja competencia:** KD 0, fácil de escalar.
- **Sin canibalización:** no choca con el hub, asset-verification, inspection-companies ni home-inspectors.
- **No perdemos nada:** la frase actual se mantiene como secundaria.

### Objetivo de la keyword
Capturar a la audiencia que hoy **no nos encuentra** — equipos de banca/ABL que buscan software para inspección y verificación de colateral — y llevarlos a la página con la intención de compra correcta.

### Cómo implementarlo
1. Reconciliar primero el dato de posición: la investigación dice que salimos #1 para la keyword actual, pero GSC/mapping de abril decía pos 9.1 (probablemente consultas distintas). Verificar en GSC antes de tocar.
2. Unificar `collateral inspection software` en **H1 + meta title + meta description + un H2 + alt del hero** (mismo criterio que las otras 19 páginas).
3. Respetar la convención del skill `vlx-marketing`: meta title 50–60 car. antes de `| VLX`; meta description 129–155 con la keyword en los primeros 120 y CTA al final; H1 y ≥1 alt con la keyword al inicio.
4. Mantener `virtual field inspection software for financial institutions` como secundaria en el copy.
5. Trabajo **en local primero** (working tree, sin PR) → revisión → despliegue en el lote correspondiente.

### Pregunta abierta para el equipo
¿La keyword **limita** el alcance real que debería tener la página? La página cubre más que "colateral": field exams, monitoreo de portafolio, verificación de inventario y equipo, anti-fraude. Si el objetivo de negocio es más amplio, quizá convenga un término más paraguas — aunque hoy `collateral inspection software` es el único con demanda + intención + sin canibalizar. **Necesito confirmar el alcance deseado de la página antes de fijar la keyword.**

---

## Parte B — Unificar Service Companies + Inspection Companies

### El hallazgo
`/service-companies/` e `/inspection-companies/` son **prácticamente la misma página**:

- **Misma audiencia:** empresas cuyo negocio son las inspecciones.
- **Mismo mensaje:** ambas abren con "run your inspection business on VLX".
- **Mismas features ancla:** tasks/work orders, branded client reports, team management, field capture.
- **Mismo cliente de prueba social:** ITI International en las dos.
- **Ya están anidadas:** inspection-companies lista a service-companies como una tarjeta hija en su propio grid "By Industry".

### Por qué importa
- **Canibalización interna:** dos páginas casi idénticas compiten por las mismas búsquedas; Google reparte la señal y ninguna sube.
- **Solo una tiene keyword real:** inspection-companies posee `inspection management software` (260 vol, KD 5) y es la página más completa (tiene sección VLX AI y el grid hub). Service-companies **no tiene keyword propia** con volumen + intención + sin pisar otra página, y **no aparece en el top 9** de su propia SERP.
- Unir el esfuerzo en una sola página hace que todo empuje en la misma dirección.

### Cómo llevarlo a cabo
1. **301** en `next.config.ts`: `/digital-inspections-software/service-companies/` → `/digital-inspections-software/inspection-companies/`.
2. **Incorporar** el ángulo diferencial de service-companies (multi-cliente / separación por cliente / white-label / free-form) al copy de inspection-companies, para no perder ese mensaje.
3. **Quitar** la tarjeta hija de service-companies del grid "By Industry" de inspection-companies.
4. Revisar enlaces internos que apunten a service-companies y redirigirlos a la página unificada.
5. **Respetar el orden de despliegue:** inspection-companies se despliega de ÚLTIMA (es la única URL viva de VLX en su SERP; si se mueve antes de que el hub tome el relevo, VLX se queda sin posición).

### Pregunta abierta para el equipo
¿**Se pueden unificar**, o hay un objetivo comercial/de producto para mantenerlas separadas que no estemos viendo? Si no lo hay, la unión es la recomendación.

---

## Riesgos y consideraciones
- Todo el trabajo se hace **en local primero**, sin PR ni deploy, hasta tener el OK.
- La keyword de Financial depende de confirmar el **alcance de la página** (ver pregunta A).
- La unión de páginas es un cambio de arquitectura (301) que debe entrar en el **orden de despliegue** ya definido.
- Sin créditos de Ranki disponibles al momento de escribir esto; los datos usados son de las corridas del 17 y 24 de sep.

## Próximos pasos (decisiones pendientes del equipo)
- [ ] **A.** Aprobar `collateral inspection software` como principal de Financial (y confirmar el alcance de la página).
- [ ] **B.** Aprobar la unificación Service → Inspection Companies (o justificar mantenerlas separadas).
- [ ] Con los OK, implementar en local y agendar el despliegue en el orden correcto.
