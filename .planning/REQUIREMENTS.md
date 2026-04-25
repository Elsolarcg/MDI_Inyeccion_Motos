# Requirements: MDI Activar Conversiones Mayo 2026

**Defined:** 2026-04-25
**Core Value:** Que un mecánico que ve un anuncio de MDI pueda comprar un cupo en menos de 3 clics — sin fricción, sin duda sobre el precio, y con urgencia real de que los cupos se acaban.

## v1 Requirements

### Tracking & Pixel

- [ ] **TRACK-01**: Countdown timer en la landing muestra la fecha real del evento (2 mayo) y corre en tiempo real — actualmente muestra 00:00:00
- [ ] **TRACK-02**: Evento `Purchase` del Meta Pixel dispara correctamente en Hotmart al completar una compra (verificado con Pixel Helper en checkout virtual en vivo y pregrabado)
- [ ] **TRACK-03**: UTMs configurados en todos los links de Hotmart desde la landing y desde Meta Ads (`utm_source`, `utm_medium`, `utm_campaign`, `utm_content`)

### Meta Ads

- [ ] **ADS-01**: Campaña nueva con objetivo OUTCOME_SALES activa y saliendo del learning phase (mínimo 50 eventos Purchase / 7 días)
- [ ] **ADS-02**: Adset cold con audiencia de intereses (mecánica, motos, reparación) + targeting Argentina, excluye CABA y 50km alrededor
- [ ] **ADS-03**: Adset warm de retargeting — visitantes de la landing en últimos 7 días → URL directo a Hotmart checkout
- [ ] **ADS-04**: Ads con copy de urgencia real: fecha 2-3 mayo, precio en ARS como primario, "cupos limitados" creíble

### Lowticket Funnel

- [ ] **LOW-01**: Pregrabado y ebook de Hotmart configurados como destino de retargeting para usuarios que visitaron la landing pero no compraron el presencial (secuencia post-evento si no se llenan los cupos)

## v2 Requirements

### Automatización Post-Venta

- **POST-01**: Secuencia de email/WhatsApp automática para compradores (recordatorio del curso, materiales, link YouTube)
- **POST-02**: Campaña de retargeting post-evento para vender el pregrabado a quienes asistieron en vivo
- **POST-03**: Audiencia de compradores en Meta para excluirlos de campañas futuras de cold traffic

### Optimización LCP Landing

- **PERF-01**: Preload del hero image en `<head>` para mejorar LCP (actualmente 4.44s)
- **PERF-02**: Reemplazar iframes YouTube embebidos por thumbnails click-to-play
- **PERF-03**: `loading="lazy"` en todas las imágenes excepto el hero

## Out of Scope

| Feature | Razón |
|---------|-------|
| Campañas de Aire Acondicionado | Producto diferente, audiencia diferente — contaminaría señal y métricas del funnel de motos |
| Google Ads, TikTok | Budget y tiempo insuficientes para diversificar en este sprint de 7 días |
| Rediseño de la landing | Reestructurada hoy (2026-04-25) — no tocar hasta tener datos de scroll depth 48-72h post-deploy |
| Optimización LCP (Fase PERF) | Impacto secundario si el countdown y pixel funcionan — priorizar conversión sobre velocidad |
| Ebook como campaña independiente | Lowticket es funnel de nurture, no objetivo primario esta semana; se activa en retargeting |

## Traceability

| Requirement | Fase | Status |
|-------------|------|--------|
| TRACK-01 | Fase 1 | Pending |
| TRACK-02 | Fase 1 | Pending |
| TRACK-03 | Fase 1 | Pending |
| ADS-01 | Fase 2 | Pending |
| ADS-02 | Fase 2 | Pending |
| ADS-03 | Fase 2 | Pending |
| ADS-04 | Fase 2 | Pending |
| LOW-01 | Fase 3 | Pending |

**Coverage:**
- v1 requirements: 8 total
- Mapeados a fases: 8
- Sin mapear: 0 ✓

---
*Requirements defined: 2026-04-25*
*Last updated: 2026-04-25 after initial definition*
