# Phase 2: Meta Ads Relanzamiento — Context

**Gathered:** 2026-04-26
**Status:** Ready for planning
**Source:** Sesión de estrategia con Juan Manuel Plazas (contexto completo desde conversación)

<domain>
## Phase Boundary

Crear y activar una campaña nueva con objetivo OUTCOME_SALES en Meta Ads para el evento en vivo de inyección electrónica de motos (2-3 mayo 2026). Incluye 3 adsets (cold, warm, hot remarketing), selección y subida de creativos con captions, copy de urgencia con precio en ARS y fecha, y A/B test de destino (landing vs Hotmart directo).

**NO incluye:**
- WhatsApp como canal de conversión (descartado para esta fase)
- Videos de pregrabado/evergreen (Phase 3)
- Nuevas grabaciones con Miguel (esperar data real de OUTCOME_SALES)
- Campañas de Aire Acondicionado (producto diferente)

</domain>

<decisions>
## Implementation Decisions

### Objetivo de Campaña
- **LOCKED**: Objetivo OUTCOME_SALES (no OUTCOME_TRAFFIC). La campaña anterior gastó $78.70 USD con 0 compras usando OUTCOME_TRAFFIC — ese objetivo optimizaba clics, no compras.
- Nombre de campaña: `MDI_Motos_VL_Mayo26` (VL = Virtual en Vivo)

### Estructura de Adsets

**Adset 1 — Cold (Frío)**
- Audiencia: Intereses mecánica/motos en Argentina
- Excluir: CABA + 50km radio alrededor
- Excluir también: audiencias de AC (Aire Acondicionado) de campañas anteriores
- Creativos: MDI_22 "Cambia pieza" (ID: 1468901108356276) + MDI_9 (ID: 1987879962608217) + cambiapiezas.mp4 (nuevo, mismo ángulo MDI_22 probado con CTR >5.7%)
- Destino A/B: Variante A → manualdelindependiente.com + UTMs | Variante B → Hotmart checkout directo + UTMs
- CTA: "Más información" o "Comprar ahora"

**Adset 2 — Warm (Tibio)**
- Audiencia: Visitantes de manualdelindependiente.com últimos 7 días + Engagement Facebook/Instagram últimos 30 días
- Excluir: Compradores (Purchase event)
- Creativos: MDI_21 "Menú soluciones" (ID: 1430736985403167) + MDI_10 FOMO cupos (ID: 943274925086396) + vivo1.mp4 (nuevo) + vivo2.mp4 (nuevo, escasez fuerte) + vivo4.mp4 (nuevo)
- Destino: Hotmart checkout directo + UTMs (ya conocen el producto, al checkout directo)
- CTA: "Comprar ahora"

**Adset 3 — Hot Remarketing**
- Audiencia: Visitantes de checkout Hotmart (pay.hotmart.com) últimos 7 días que NO completaron Purchase
- Creativos: MDI_24 "Reto final" (ID: 2522742448128979) + +3.mp4 (nuevo, humor "el bife")
- Destino: Hotmart checkout directo + UTMs
- CTA: "Comprar ahora"

### Creativos — Prioridad de Subida

**Subir ANTES de activar la campaña (urgentes para el evento):**
1. `vivo2.mp4` → MDI_VIVO_02_ESCASEZ_CUPOS — FOMO más fuerte
2. `vivo1.mp4` → MDI_VIVO_01_ZOOM_FACE_TO_FACE — Dolor carburador + Zoom
3. `vivo4.mp4` → MDI_VIVO_03_COMPLETO_ESCASEZ — Pitch completo + vacantes
4. `cambiapiezas.mp4` → MDI_GANCHO_AUTORIDAD_CAMBIAPIEZAS — mismo ángulo MDI_22
5. `+3.mp4` → MDI_OFERTAS_MULTIPLE_HUMOR — remarketing "el bife"

**No subir en Phase 2 (evergreen/pregrabado para Phase 3):**
- 1.mp4, 3.mp4, 5.mp4, 6.mp4, 7.mp4, vivo3.mp4, vivo5.mp4, 4.mp4, ++3.mp4

### Captions / Subtítulos
- **CRÍTICO**: Los nuevos videos NO tienen subtítulos en post-producción. 80% del tráfico mobile ve con sonido apagado.
- Los videos existentes en Meta (MDI_22, MDI_9, MDI_21, MDI_10, MDI_24) SÍ tienen subtítulos dinámicos — eso contribuye a CTR >5.7%.
- **Antes de subir**: Agregar captions a los 5 videos nuevos prioritarios. Usar la función nativa de Meta (caption generation automática en Ads Manager al subir) si el editor no los tiene.

### Copy de Anuncios
- Precio ARS como cifra primaria: **$45.000 ARS** (Virtual en Vivo) / **$42.000 ARS** (Pregrabado)
- Fecha visible: **2 y 3 de mayo**
- Frase de escasez: "Vacantes limitadas" (creíble porque es real)
- Moneda secundaria opcional: ≈ USD $40
- Headline: Debe mencionar fecha o precio o "cupos limitados" (al menos uno)

### UTM Convention
Confirmada en Phase 1:
- `utm_source`: `meta` (ads) / `landing` (desde landing)
- `utm_medium`: `paid_social`
- `utm_campaign`: `inyeccion-motos-mayo26`
- `utm_content`: por ad (ej: `cold-cambiapiezas`, `warm-escasez-cupos`, `hot-reto-final`)

### WhatsApp
- **DESCARTADO** para Phase 2 como CTA de conversión.
- Razón: Si tráfico pagado va a WhatsApp, Meta no recibe evento Purchase y el algoritmo OUTCOME_SALES no aprende.
- WhatsApp reservado para: retargeting orgánico en comentarios, soporte manual a leads calientes.
- Posible Phase futura separada con objetivo de mensajes.

### A/B Test Destino
- **Variante A** (cold adset): landing con UTMs → Hotmart checkout (2 clics)
- **Variante B** (cold adset): Hotmart checkout directo con UTMs (1 clic)
- Resultado determina decisión de destino para fases futuras.

### Budget
- Claude's Discretion: presupuesto total a definir por usuario. Sugerencia basada en historial ($78.70 en campaña anterior de 7+ días): empezar con mínimo $10-15 USD/día por adset, ajustar según CPM y purchase events en primeras 48h.

### Cuenta y Pixel
- Ad Account: `act_2143097429552738`
- Pixel: `2277921389363777`
- Token Meta: generado 11 marzo 2026, expira ~10 mayo — vigente para esta fase.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning o implementando.**

### Estado del Proyecto
- `.planning/STATE.md` — estado completo, blockers actualizados, historial de decisiones
- `.planning/ROADMAP.md` — dependencias entre fases, success criteria ADS-01 a ADS-04
- `.planning/REQUIREMENTS.md` — requirements v1 y v2, traceability

### Contexto de Producto
- `CLAUDE.md` — pixel ID, video IDs con CTR, URLs Hotmart, credenciales API
- `.planning/phases/01-fundamentos-de-tracking/.continue-here.md` — estado Phase 1, UTMs aplicados, commit 3551935

### Creativos disponibles en Meta
Video IDs con CTR confirmado >5.7% (usar primero):
- MDI_22 "Cambia pieza": `1468901108356276` (50s)
- MDI_9 "Metodología diagnóstico": `1987879962608217` (28s)
- MDI_21 "Menú soluciones": `1430736985403167` (1:02)
Videos adicionales testados:
- MDI_10 "FOMO cupos": `943274925086396` (1:11)
- MDI_24 "Reto final humor": `2522742448128979` (54s)
Videos nuevos a subir (5 prioritarios):
- `vivo2.mp4` → MDI_VIVO_02_ESCASEZ_CUPOS
- `vivo1.mp4` → MDI_VIVO_01_ZOOM_FACE_TO_FACE
- `vivo4.mp4` → MDI_VIVO_03_COMPLETO_ESCASEZ
- `cambiapiezas.mp4` → MDI_GANCHO_AUTORIDAD_CAMBIAPIEZAS
- `+3.mp4` → MDI_OFERTAS_MULTIPLE_HUMOR

### URLs críticas
- Landing: `https://manualdelindependiente.com/`
- Checkout Virtual en Vivo: `https://pay.hotmart.com/H105380102G?bid=1776180424703`
- Checkout Pregrabado: `https://hotmart.com/es/marketplace/productos/clase-de-inyeccion-de-motos-pre-grabado/O105383269C`
- Meta Ads Manager: `https://www.facebook.com/adsmanager/`
- Test Events Pixel: `https://www.facebook.com/events_manager2/pixel/2277921389363777/test_events/`

</canonical_refs>

<specifics>
## Specific Ideas

- **Budget mínimo sugerido**: $10-15 USD/día por adset para salir de learning phase en 7 días (necesita ~50 Purchase events)
- **Exclusión CABA**: Confirmada por datos de campaña anterior — CPA más alto, mecánico del interior es el target real
- **Autocrop 4:5**: Meta genera versiones autocrop automáticamente al subir. Verificar que el crop no corta texto/cara en los nuevos videos.
- **Caption automático de Meta**: Al subir video en Ads Manager → "Video Captions" → Generate automatically. Revisar y corregir antes de activar.
- **Moneda en anuncio**: ARS primario. La audiencia argentina entiende ARS como referencia de accesibilidad. USD como referencia secundaria para credibilidad internacional.

</specifics>

<deferred>
## Deferred Ideas

- WhatsApp como canal de conversión → Phase futura independiente con objetivo mensajes
- Videos pregrabado/evergreen (1.mp4, 3.mp4, 5.mp4, 6.mp4, 7.mp4) → Phase 3
- Nuevas grabaciones con Miguel → después de ver data real de OUTCOME_SALES Phase 2
- Video con precio explícito ($45K ARS en el video) → posible grabación post-Phase 2
- Video con countdown de días → posible grabación si no se llenan cupos en primeros 3 días
- Testimonio de alumno → grabación futura para Phase de optimización

</deferred>

---

*Phase: 02-meta-ads-relanzamiento*
*Context gathered: 2026-04-26 — sesión estratégica completa con Juan Manuel Plazas*
*Requirements: ADS-01, ADS-02, ADS-03, ADS-04*
