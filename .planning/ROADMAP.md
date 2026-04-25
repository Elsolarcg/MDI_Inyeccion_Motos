# Roadmap: MDI — Activar Conversiones Mayo 2026

## Overview

Sprint de 7 días (25 abril → 2-3 mayo) para convertir una landing reestructurada + pixel existente en un funnel activo que venda 100 cupos a USD $40. El camino es lineal y con dependencias duras: sin pixel Purchase verificado no se puede lanzar OUTCOME_SALES; sin ads activos no hay tráfico que retargetear para lowticket.

## Phases

- [ ] **Phase 1: Fundamentos de Tracking** - Contador real, pixel Purchase verificado y UTMs configurados antes de gastar un peso en ads
- [ ] **Phase 2: Meta Ads Relanzamiento** - Nueva campaña OUTCOME_SALES con audiencias correctas (motos, no AC) y copy de urgencia real
- [ ] **Phase 3: Lowticket Funnel** - Pregrabado y ebook configurados como destino de retargeting para visitantes que no compran el presencial

## Phase Details

### Phase 1: Fundamentos de Tracking
**Goal**: El embudo mide correctamente — cada clic, cada visita y cada compra llega atribuida a la fuente correcta, y la landing comunica urgencia real con un contador funcional
**Depends on**: Nothing (first phase)
**Requirements**: TRACK-01, TRACK-02, TRACK-03
**Success Criteria** (what must be TRUE):
  1. El countdown timer en manualdelindependiente.com muestra la cuenta regresiva real hacia el 2 de mayo y corre en tiempo real (no 00:00:00)
  2. Meta Pixel Helper confirma que el evento `Purchase` se dispara en la página de confirmación de Hotmart (checkout virtual en vivo y pregrabado)
  3. Todos los links a Hotmart desde la landing y desde los ads incluyen `utm_source`, `utm_medium`, `utm_campaign` y `utm_content` correctos
**Plans**: TBD

### Phase 2: Meta Ads Relanzamiento
**Goal**: Una campaña activa con objetivo OUTCOME_SALES, audiencias específicas de motos (sin contaminar con AC), y copy de urgencia con fecha y precio en ARS, generando compras verificadas en Meta Ads Manager
**Depends on**: Phase 1
**Requirements**: ADS-01, ADS-02, ADS-03, ADS-04
**Success Criteria** (what must be TRUE):
  1. La campaña MDI_Motos_Mayo26 existe con objetivo OUTCOME_SALES y estado ACTIVE en Meta Ads Manager
  2. El adset cold targetea intereses de mecánica/motos en Argentina excluyendo CABA y radio de 50km — sin overlap con audiencias de AC
  3. El adset warm tiene como audiencia a visitantes de la landing en los últimos 7 días, con URL de destino directo al checkout de Hotmart
  4. Los ads activos muestran precio en ARS como cifra primaria, fecha "2 y 3 de mayo" y mensaje de cupos limitados en headline o primer párrafo
**Plans**: TBD
**UI hint**: yes

### Phase 3: Lowticket Funnel
**Goal**: Los visitantes que no compran el presencial tienen un camino alternativo — pregrabado y ebook de Hotmart configurados como destino de retargeting, listo para activar si los cupos no se llenan o como secuencia post-evento
**Depends on**: Phase 2
**Requirements**: LOW-01
**Success Criteria** (what must be TRUE):
  1. Existe un adset o conjunto de ads en Meta que targetea a visitantes de la landing que no completaron Purchase, con links al pregrabado y/o ebook de Hotmart
  2. Los links del retargeting lowticket incluyen UTMs diferenciados para distinguir tráfico presencial vs lowticket en Analytics
**Plans**: TBD

## Progress

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Fundamentos de Tracking | 0/? | Not started | - |
| 2. Meta Ads Relanzamiento | 0/? | Not started | - |
| 3. Lowticket Funnel | 0/? | Not started | - |
