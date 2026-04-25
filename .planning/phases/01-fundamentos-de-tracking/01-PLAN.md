---
phase: 1
plan: 1
type: execute
wave: 1
depends_on: []
files_modified:
  - "LP_MDI_Inyeccion Motos/index.html"
autonomous: true
requirements:
  - TRACK-01
  - TRACK-03
---

<objective>
Corregir el countdown del top banner para que apunte al evento real (2 mayo, no 29 abril), y agregar UTMs a los 3 links de Hotmart en la landing para atribución correcta de ventas. Deploy a producción via git push → Netlify.
</objective>

<context>
La landing tiene DOS countdowns:
- 4.1 Top Banner: IDs top-days/top-hours/top-minutes/top-seconds → apunta al 2026-04-29T23:59:59-03:00 (promo deadline 29 abril) — PROBLEMA: expirará 3 días antes del evento mostrando 00:00:00
- 4.2 Sección Oferta: IDs event-days/event-hours/event-minutes → apunta al 2026-05-02T09:00:00-03:00 (correcto)

Los 3 links de Hotmart sin UTMs:
- Línea 271: hero CTA → https://pay.hotmart.com/H105380102G?bid=1776180424703
- Línea 421: Card 1 "Inscribirme Ahora" → https://pay.hotmart.com/H105380102G?bid=1776180424703
- Línea 476: Card 2 "Comprar Ahora" → https://hotmart.com/es/marketplace/productos/clase-de-inyeccion-de-motos-pre-grabado/O105383269C

Archivo: LP_MDI_Inyeccion Motos/index.html (1245 líneas)
</context>

<tasks>

<task id="1">
<title>TRACK-01: Alinear countdown del top banner al 2 de mayo</title>
<type>execute</type>
<read_first>
- "LP_MDI_Inyeccion Motos/index.html" líneas 1163-1200 (promoCountDownDate y promoInterval)
</read_first>
<action>
Editar LP_MDI_Inyeccion Motos/index.html, línea 1165.

ANTES (exacto):
        const promoCountDownDate = new Date("2026-04-29T23:59:59-03:00").getTime();

DESPUÉS:
        const promoCountDownDate = new Date("2026-05-02T09:00:00-03:00").getTime();

Justificación: el top banner muestra 00:00:00 desde el 29 de abril hasta el 2 de mayo — destruye urgencia en los 3 días más críticos de venta. Al alinear ambos timers al evento, el contador sigue corriendo hasta el inicio de la clase.
</action>
<acceptance_criteria>
- grep "2026-05-02T09:00:00-03:00" "LP_MDI_Inyeccion Motos/index.html" retorna exactamente 2 líneas (promoCountDownDate + eventCountDownDate)
- grep "2026-04-29" "LP_MDI_Inyeccion Motos/index.html" retorna 0 resultados
</acceptance_criteria>
</task>

<task id="2">
<title>TRACK-03: UTMs en hero CTA (virtual en vivo)</title>
<type>execute</type>
<read_first>
- "LP_MDI_Inyeccion Motos/index.html" línea 271 (href del CTA gigante del hero)
</read_first>
<action>
Editar LP_MDI_Inyeccion Motos/index.html, línea 271.

ANTES (exacto):
                <a href="https://pay.hotmart.com/H105380102G?bid=1776180424703" target="_blank"

DESPUÉS:
                <a href="https://pay.hotmart.com/H105380102G?bid=1776180424703&utm_source=landing&utm_medium=web&utm_campaign=inyeccion-motos-mayo26&utm_content=hero-cta" target="_blank"
</action>
<acceptance_criteria>
- grep "hero-cta" "LP_MDI_Inyeccion Motos/index.html" retorna 1 línea
- Esa línea contiene los 4 parámetros: utm_source=landing, utm_medium=web, utm_campaign=inyeccion-motos-mayo26, utm_content=hero-cta
</acceptance_criteria>
</task>

<task id="3">
<title>TRACK-03: UTMs en Card 1 — Capacitación Virtual en Vivo</title>
<type>execute</type>
<read_first>
- "LP_MDI_Inyeccion Motos/index.html" línea 421 (href del botón "Inscribirme Ahora" de Card 1)
</read_first>
<action>
Editar LP_MDI_Inyeccion Motos/index.html, línea 421.

ANTES (exacto):
                    <a href="https://pay.hotmart.com/H105380102G?bid=1776180424703" target="_blank" onclick="fbq('track', 'InitiateCheckout')"

DESPUÉS:
                    <a href="https://pay.hotmart.com/H105380102G?bid=1776180424703&utm_source=landing&utm_medium=web&utm_campaign=inyeccion-motos-mayo26&utm_content=card-virtual" target="_blank" onclick="fbq('track', 'InitiateCheckout')"
</action>
<acceptance_criteria>
- grep "card-virtual" "LP_MDI_Inyeccion Motos/index.html" retorna 1 línea
- Esa línea contiene fbq InitiateCheckout Y los 4 parámetros UTM
</acceptance_criteria>
</task>

<task id="4">
<title>TRACK-03: UTMs en Card 2 — Pregrabado</title>
<type>execute</type>
<read_first>
- "LP_MDI_Inyeccion Motos/index.html" línea 476 (href del botón "Comprar Ahora" de Card 2)
</read_first>
<action>
Editar LP_MDI_Inyeccion Motos/index.html, línea 476.

ANTES (exacto):
                    <a href="https://hotmart.com/es/marketplace/productos/clase-de-inyeccion-de-motos-pre-grabado/O105383269C" target="_blank" onclick="fbq('track', 'InitiateCheckout')"

DESPUÉS:
                    <a href="https://hotmart.com/es/marketplace/productos/clase-de-inyeccion-de-motos-pre-grabado/O105383269C?utm_source=landing&utm_medium=web&utm_campaign=inyeccion-motos-mayo26&utm_content=card-pregrabado" target="_blank" onclick="fbq('track', 'InitiateCheckout')"
</action>
<acceptance_criteria>
- grep "card-pregrabado" "LP_MDI_Inyeccion Motos/index.html" retorna 1 línea
- Esa línea contiene fbq InitiateCheckout Y los 4 parámetros UTM
- La URL usa ? (no &) para el primer parámetro UTM (no hay ?bid= en este link)
</acceptance_criteria>
</task>

<task id="5">
<title>Deploy: commit y push → Netlify auto-deploy</title>
<type>execute</type>
<read_first>
- "LP_MDI_Inyeccion Motos/index.html" (verificar las 4 ediciones antes del commit)
</read_first>
<action>
Desde el directorio del repo MDI_Inyeccion_Motos:

1. Verificar countdown: grep "2026-05-02T09:00:00-03:00" "LP_MDI_Inyeccion Motos/index.html" → debe retornar 2 líneas
2. Verificar UTMs: grep "utm_source=landing" "LP_MDI_Inyeccion Motos/index.html" → debe retornar 3 líneas
3. Stage: git add "LP_MDI_Inyeccion Motos/index.html"
4. Commit: git commit -m "fix(landing): countdown→mayo2 + UTMs en 3 links Hotmart (TRACK-01, TRACK-03)"
5. Push: git push origin main
6. Netlify auto-deploya en ~1 minuto. URL: https://manualdelindependiente.com
</action>
<acceptance_criteria>
- git push origin main retorna exit code 0
- grep "2026-05-02T09:00:00-03:00" retorna 2 líneas
- grep "utm_source=landing" retorna 3 líneas (hero-cta, card-virtual, card-pregrabado)
- git log --oneline -1 muestra commit con "TRACK-01, TRACK-03"
</acceptance_criteria>
</task>

</tasks>

<verification>
1. grep "2026-04-29" "LP_MDI_Inyeccion Motos/index.html" → 0 resultados (countdown viejo eliminado)
2. grep "2026-05-02T09:00:00-03:00" "LP_MDI_Inyeccion Motos/index.html" → 2 resultados (ambos timers apuntan al evento)
3. grep "utm_source=landing" "LP_MDI_Inyeccion Motos/index.html" → 3 resultados (hero-cta, card-virtual, card-pregrabado)
4. grep "utm_content=hero-cta" "LP_MDI_Inyeccion Motos/index.html" → 1 resultado
5. grep "utm_content=card-virtual" "LP_MDI_Inyeccion Motos/index.html" → 1 resultado
6. grep "utm_content=card-pregrabado" "LP_MDI_Inyeccion Motos/index.html" → 1 resultado
7. git log --oneline -1 contiene "TRACK-01, TRACK-03"
</verification>

<success_criteria>
- [ ] Ambos countdowns (top banner y sección oferta) apuntan al 2 de mayo 2026 a las 09:00 ART
- [ ] Los 3 links de Hotmart desde la landing incluyen los 4 parámetros UTM correctos
- [ ] Cambios desplegados en producción (manualdelindependiente.com)
- [ ] Ningún link de Hotmart en la landing queda sin UTMs
</success_criteria>

<must_haves>
- countdown apunta a mayo 2 en ambos timers — el top banner no mostrará 00:00:00 el 29 de abril
- 3 links con UTMs: utm_source=landing, utm_medium=web, utm_campaign=inyeccion-motos-mayo26, utm_content=[hero-cta|card-virtual|card-pregrabado]
- deploy exitoso en producción
</must_haves>

<note_track03_partial>
TRACK-03 cubre links de landing en este plan. Los UTMs para Meta Ads se configuran en Phase 2 (Plan de Campaña OUTCOME_SALES) al crear los nuevos ads con utm_source=meta&utm_medium=paid_social.
</note_track03_partial>
