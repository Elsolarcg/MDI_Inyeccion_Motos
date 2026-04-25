# MDI — Activar Conversiones Mayo 2026

## What This Is

Sistema de conversión para el Manual del Independiente (MDI) orientado a vender el curso de Inyección Electrónica de Motos. El producto principal es una capacitación virtual en vivo vía YouTube los días 2 y 3 de mayo (100 cupos a USD $40). Complementado por lowticket en Hotmart: curso pregrabado (~USD $37) y ebook. El canal de adquisición principal es Meta Ads hacia la landing manualdelindependiente.com.

## Core Value

Que un mecánico que ve un anuncio de MDI pueda comprar un cupo en menos de 3 clics — sin fricción, sin duda sobre el precio, y con urgencia real de que los cupos se acaban.

## Requirements

### Validated

- ✓ Landing reestructurada (oferta en posición #2, ARS primario, garantía 7 días) — Fase 0, 2026-04-25
- ✓ FBQ InitiateCheckout limpio (solo en botones de checkout reales) — Fase 0, 2026-04-25
- ✓ Campaña Meta activa pausada para reestructuración — 2026-04-25

### Active

- [ ] Countdown timer funcional con fecha real (2 mayo) — actualmente muestra 00:00:00
- [ ] Pixel Purchase verificado y disparando en Hotmart (checkout virtual en vivo + pregrabado)
- [ ] UTMs configurados en todos los links de Hotmart para tracking de fuente
- [ ] Nueva campaña Meta con objetivo OUTCOME_SALES activa
- [ ] Audiencias correctas para motos (no reutilizar audiencias de AC)
- [ ] Adset de retargeting: visitantes landing últimos 7 días → checkout directo
- [ ] Adset cold: Lookalike o intereses mecánica/motos Argentina (excluir CABA)
- [ ] Copy de ads alineado: mensaje presencia urgencia + fecha + precio en ARS

### Out of Scope

- Campañas de AC en este proyecto — son un producto diferente, audiencia diferente; contaminarían las métricas
- Rebranding o rediseño de la landing — está reestructurada y en producción, no tocar hasta tener datos de scroll depth (48-72h)
- Campañas para el ebook por separado — el ebook es funnel de nurture, no objetivo primario esta semana
- Google Ads, TikTok, otras plataformas — presupuesto y tiempo insuficientes para diversificar

## Context

**Situación al 2026-04-25:**
- Landing: manualdelindependiente.com — reestructurada hoy (commit 7b1a39f). Baseline: 4.86% scroll depth, 0% CR sobre 827 visitas Meta Ads.
- Meta Ads: una campaña activa (MDI_AC_Mayo26_Prospecting_TrafficFix) — PAUSADA hoy. Objetivo era OUTCOME_TRAFFIC. 2 InitiateCheckout registrados eran del scroll del hero (señal contaminada). $78.70 USD gastados, 1,947 landing page views, 0 compras.
- Pixel: ID 2277921389363777. Instalado en landing + en Hotmart según Miguel. Eventos Purchase no verificados todavía.
- Hotmart: pay.hotmart.com/H105380102G?bid=1776180424703 (virtual en vivo). hotmart.com/es/marketplace/.../O105383269C (pregrabado). Ebook: ya publicado.
- Countdown: El timer de la landing sigue en 00:00:00 — fix crítico pendiente.
- Audiencias AC: Existen en la cuenta (Interactuaron FB/IG 180d, Visitantes LP AC) — sirven para ebook de AC, NO para motos.

**Por qué 0 conversiones:**
La causa raíz fue estructura: oferta enterrada en posición #13 de 15. Ya corregido. El segundo problema es el objetivo TRAFFIC + señal contaminada que impedía que Meta optimizara para compradores.

**Plazo real:**
Hoy 25 abril → curso 2-3 mayo = 7 días para vender 100 cupos. Es un objetivo ambicioso. Con $300-600 USD de budget y un CPA esperado de $4-8 USD (si el pixel Purchase funciona bien), el rango realista es 40-150 ventas. Depende fuertemente de que el evento Purchase esté bien configurado en Hotmart.

**Stack técnico:**
- Landing: HTML estático, Tailwind CDN, Netlify (auto-deploy en push a main)
- Repo: MDI_Inyeccion_Motos/ (branch: main)
- Meta API: v19.0, token en 05_EL_SOLAR/.env (vence ~60 días desde 11 marzo)
- Clarity: analytics de heatmaps y scroll depth

## Constraints

- **Timeline**: 7 días hasta el evento (2-3 mayo) — cada día sin conversiones es un cupo sin vender
- **Budget**: USD $300-600 total para Meta Ads esta semana
- **Leads**: Sin base de leads existente — 100% dependiente de tráfico pagado
- **Pixel**: La calidad del señal de conversión en Hotmart determina si Meta puede optimizar; sin Purchase limpio, OUTCOME_SALES no funciona
- **Landing**: No rediseñar hasta tener datos de scroll depth post-reestructuración (mínimo 48-72h)
- **Token Meta API**: Generado 11 marzo ~60 días → puede expirar ~10 mayo; renovar si hay error 190

## Key Decisions

| Decisión | Rationale | Outcome |
|----------|-----------|---------|
| Pausar campaña OUTCOME_TRAFFIC | Objetivo equivocado: optimizaba clics, no compras. Sin señal Purchase, desperdiciar budget. | — Pending |
| Reestructurar landing antes de relanzar ads | Sin estructura clara, cualquier tráfico rebota. Prioridad: arreglar el embudo antes de llenarla. | — Pending |
| OUTCOME_SALES como objetivo de campaña | Necesita evento Purchase limpio en Hotmart para funcionar. Si no hay Purchase, fallback a OUTCOME_TRAFFIC con URL Hotmart directo. | — Pending |
| Excluir CABA de targeting | El curso es para mecánicos del interior de Argentina (GBA Sur y provincias). CABA tiene perfil diferente y CPA más alto. | ✓ Validado en campañas previas |
| ARS como moneda primaria en pricing | El target es Argentina. Ver USD primero genera fricción ("¿cuánto es eso en pesos?"). | — Pending |

---

## Evolution

Este documento evoluciona en cada transición de fase y milestone.

**Después de cada fase (via `/gsd-transition`):**
1. ¿Requirements invalidados? → Mover a Out of Scope con razón
2. ¿Requirements validados? → Mover a Validated con referencia de fase
3. ¿Emergieron nuevos requirements? → Agregar a Active
4. ¿Decisiones que registrar? → Agregar a Key Decisions
5. ¿"What This Is" sigue siendo preciso? → Actualizar si derivó

**Después de cada milestone (via `/gsd-complete-milestone`):**
1. Revisión completa de todas las secciones
2. Core Value check — ¿sigue siendo la prioridad correcta?
3. Audit Out of Scope — ¿las razones siguen siendo válidas?
4. Actualizar Context con estado actual (métricas, conversiones, aprendizajes)

---
*Last updated: 2026-04-25 después de inicialización del proyecto*
