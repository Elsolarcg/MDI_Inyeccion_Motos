# Project State

## Project Reference

See: .planning/PROJECT.md (updated 2026-04-25)

**Core value:** Que un mecánico que ve un anuncio de MDI pueda comprar un cupo en menos de 3 clics — sin fricción, sin duda sobre el precio, y con urgencia real de que los cupos se acaban.
**Current focus:** Phase 1 — Fundamentos de Tracking

## Current Position

Phase: 1 of 3 (Fundamentos de Tracking)
Plan: 0 of ? in current phase
Status: Ready to plan
Last activity: 2026-04-25 — Roadmap creado. Landing reestructurada (oferta en #2, ARS primario). Campaña OUTCOME_TRAFFIC pausada. Pixel instalado pero Purchase sin verificar.

Progress: [░░░░░░░░░░] 0%

## Performance Metrics

**Velocity:**
- Total plans completed: 0
- Average duration: —
- Total execution time: —

**By Phase:**

| Phase | Plans | Total | Avg/Plan |
|-------|-------|-------|----------|
| - | - | - | - |

*Updated after each plan completion*

## Accumulated Context

### Decisions

- [Pre-fase]: Pausar campaña OUTCOME_TRAFFIC — optimizaba clics, no compras
- [Pre-fase]: OUTCOME_SALES como objetivo nuevo — requiere evento Purchase limpio en Hotmart
- [Pre-fase]: Excluir CABA del targeting — CPA más alto, perfil diferente al mecánico del interior
- [Pre-fase]: ARS como moneda primaria en ads y landing — reduce fricción del target argentino

### Pending Todos

None yet.

### Blockers/Concerns

- **Pixel Purchase (CRÍTICO):** No verificado en Hotmart. Si no dispara correctamente, OUTCOME_SALES no puede optimizar — fallback a OUTCOME_TRAFFIC con URL Hotmart directo como destino.
- **Token Meta API:** Generado 11 marzo (~60 días) — puede expirar ~10 mayo. Renovar si aparece error 190.
- **Countdown timer:** Actualmente muestra 00:00:00 — fix es el primer paso antes de cualquier ad activo.

## Deferred Items

| Category | Item | Status | Deferred At |
|----------|------|--------|-------------|
| v2 | POST-01: Secuencia email/WhatsApp compradores | Deferred | 2026-04-25 |
| v2 | POST-02: Retargeting post-evento pregrabado | Deferred | 2026-04-25 |
| v2 | POST-03: Audiencia compradores para exclusión | Deferred | 2026-04-25 |
| OOS | PERF-01/02/03: Optimización LCP landing | Out of scope | 2026-04-25 |

## Session Continuity

Last session: 2026-04-25
Stopped at: Roadmap creado y aprobado. Listo para planificar Phase 1.
Resume file: None
