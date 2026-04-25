# Project State

## Project Reference

See: .planning/PROJECT.md (updated 2026-04-25)

**Core value:** Que un mecánico que ve un anuncio de MDI pueda comprar un cupo en menos de 3 clics — sin fricción, sin duda sobre el precio, y con urgencia real de que los cupos se acaban.
**Current focus:** Phase 1 — Fundamentos de Tracking

## Current Position

Phase: 1 of 3 (Fundamentos de Tracking)
Plan: 0 of 2 en la fase actual
Status: Ready to execute
Last activity: 2026-04-25 — Phase 1 planeada: 2 planes, 2 waves. Plan 01 (autonomous): countdown + UTMs en landing. Plan 02 (manual): verificación Pixel Purchase en Hotmart.

Progress: [░░░░░░░░░░] 0%

## Diagnóstico Completo (sesión 2026-04-25)

### Estado Meta Ads — Auditado vía API

**Cuenta:** act_2143097429552738 | **Pixel:** 2277921389363777

| Campaña | ID | Objetivo | Spend 30d | LP Views | Conversiones | Status |
|---------|-----|----------|-----------|----------|--------------|--------|
| MDI_AC_Mayo26_Prospecting_TrafficFix (motos) | 120242996870600095 | TRAFFIC | $78.70 USD | 1,947 | 0 reales | **PAUSADA hoy** |
| MDI_AC_VideoWA_Abr26 | 120241633803950095 | TRAFFIC | $48.50 USD | 770 | 0 | PAUSED |
| MDI_Ebook_Motos_Warm | 120242970473610095 | SALES | $0 | 0 | 0 | PAUSED (sin adsets) |
| MDI_Ebook_Motos_Frio | 120242970473250095 | SALES | $0 | 0 | 0 | PAUSED (sin adsets) |

**Adsets de la campaña que estaba activa (ahora pausada):**
- `MDI_AC_Mayo26_Cold_LAL` — CTR 6.28%, CPC $0.0099, 1,162 LP views, $54.29 spend
- `MDI_AC_Mayo26_Warm_Engagement_FB_IG` — CTR 5.74%, CPC $0.0117, 785 LP views, $24.41 spend, 2 InitiateCheckout (eran del scroll — señal contaminada, ya corregido)
- `MDI_AC_Mayo26_Warm_Visitantes180d` → destino WhatsApp (audiencia de AC, no motos — no usar)

**Creativos con mejor CTR — reutilizar en Phase 2:**
- Video "Ganas poco como mecánico?" → video_id: `1430736985403167`
- Video "Domina la especialidad que todos buscan" → video_id: `1987879962608217`
- Video "Genera ingresos como especialista" → video_id: `1468901108356276`
- (WA) Video "Chatea con nosotros" → video_id: `1279922063562873` → NO usar para OUTCOME_SALES

**URL de destino actual en creativos:** `https://go.hotmart.com/H105380102G?dp=1` (checkout directo, sin UTMs)
**Link caption (visual en el ad):** `manualdelindependiente.com/?fbclid=fbclid`

### Estado Landing Page — auditada vía WebFetch

**URL:** https://manualdelindependiente.com  
**Deploy:** Netlify auto-deploy | **Repo:** MDI_Inyeccion_Motos/LP_MDI_Inyeccion Motos/index.html

Reestructurada hoy (commit 7b1a39f):
- ✅ Oferta en posición #2 (antes era #13)
- ✅ Precio ARS primario ($45K virtual / $42K pregrabado)
- ✅ Garantía 7 días
- ✅ FBQ InitiateCheckout limpio (solo en botones de checkout reales)
- ❌ **Countdown timer muestra 00:00:00** — CRÍTICO, fix es Phase 1 / TRACK-01

**Estructura de productos en landing:**
- Card 1 (Virtual en Vivo): $45,000 ARS ≈ USD $40 | Fecha: 2-3 mayo | Link: `https://pay.hotmart.com/H105380102G?bid=1776180424703`
- Card 2 (Pregrabado): $42,000 ARS ≈ USD $37 | Link: `https://hotmart.com/es/marketplace/productos/clase-de-inyeccion-de-motos-pre-grabado/O105383269C`

### Estado Hotmart API

**Conectada:** ✅ OAuth 2.0 funcionando (client_credentials)
**Ventas totales:** 0 (confirmado vía API — `total_results: 0`)
**Credenciales:** `05_EL_SOLAR/.env` → `HOTMART_MDI_CLIENT_ID`, `HOTMART_MDI_CLIENT_SECRET`, `HOTMART_MDI_BASIC`
**Token:** se genera en 2 seg con curl — ver CLAUDE.md para comando
**Pixel Purchase en Hotmart:** instalado según Miguel, **no verificado** todavía con Pixel Helper → esto es Phase 1 / TRACK-02

## Accumulated Context

### Decisions

- [2026-04-25]: Pausar campaña OUTCOME_TRAFFIC — optimizaba clics, no compras. $78.70 USD gastados, 0 compras.
- [2026-04-25]: OUTCOME_SALES como objetivo nuevo — requiere evento Purchase limpio en Hotmart
- [2026-04-25]: Excluir CABA del targeting — CPA más alto, mecánico del interior es el target real
- [2026-04-25]: ARS como moneda primaria en ads y landing — reduce fricción del target argentino
- [2026-04-25]: Reutilizar video IDs existentes en nuevas campañas — CTR >5.7% probado
- [2026-04-25]: A/B test creativos: Variante A → landing, Variante B → Hotmart directo
- [2026-04-25]: No tocar diseño de landing hasta 48-72h de datos de scroll depth post-reestructuración

### Blockers (Phase 1 los resuelve)

- **TRACK-01 — Countdown 00:00:00:** Destruye urgencia. Fix: actualizar JS del timer con fecha target 2026-05-02.
- **TRACK-02 — Pixel Purchase no verificado:** Sin este evento, OUTCOME_SALES no puede optimizar. Fix: test con Pixel Helper en checkout Hotmart.
- **TRACK-03 — Sin UTMs:** Las ventas que ocurran no van a estar atribuidas. Fix: agregar params a todos los links Hotmart.

### Token Meta API

Generado: 11 marzo 2026. Expiración estimada: ~10 mayo 2026. Si hay error 190 al hacer llamadas API: renovar token en Meta Developers.

## Deferred Items

| Categoría | Item | Estado |
|-----------|------|--------|
| v2 | POST-01/02/03: Automatización post-venta y retargeting compradores | Deferred |
| OOS | PERF-01/02/03: Optimización LCP (preload hero, lazy images, click-to-play YouTube) | Out of scope |
| OOS | Campañas AC — producto/audiencia diferente | Out of scope |

## Session Continuity

Last session: 2026-04-25
Stopped at: Phase 1 planeada y verificada. 2 planes listos en .planning/phases/01-fundamentos-de-tracking/. Listo para `/gsd-execute-phase 1`.

**Comando para retomar:**
```bash
cd "C:/Users/USUARIO/Documents/Claude Code/05_EL_SOLAR/MDI_Inyeccion_Motos"
cat .planning/STATE.md
# Luego: /gsd-execute-phase 1
```
