# Phase 2: Meta Ads Relanzamiento — Research

**Researched:** 2026-04-26
**Domain:** Meta Marketing API v19.0–v25.0 — Campaigns, Adsets, Custom Audiences, Ad Creatives, Video Upload
**Confidence:** MEDIUM-HIGH (API payloads verificados en múltiples fuentes; interest IDs específicos requieren búsqueda dinámica en tiempo de ejecución)

---

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- **Objetivo OUTCOME_SALES** (no OUTCOME_TRAFFIC). La campaña anterior gastó $78.70 USD con 0 compras usando OUTCOME_TRAFFIC — ese objetivo optimizaba clics, no compras.
- **Nombre de campaña:** `MDI_Motos_VL_Mayo26`
- **Cuenta y Pixel:** Ad Account `act_2143097429552738`, Pixel `2277921389363777`
- **Token Meta:** generado 11 marzo 2026, expira ~10 mayo — vigente para esta fase.
- **Exclusión CABA:** Excluir de targeting cold. CPA más alto, mecánico del interior es el target real.
- **WhatsApp DESCARTADO** como CTA de conversión. Si va a WhatsApp, Meta no recibe Purchase y el algoritmo no aprende.
- **Moneda ARS primaria:** $45.000 ARS Virtual en Vivo / $42.000 ARS Pregrabado
- **Video IDs reutilizables** (CTR >5.7% confirmado): MDI_22 (`1468901108356276`), MDI_9 (`1987879962608217`), MDI_21 (`1430736985403167`), MDI_10 (`943274925086396`), MDI_24 (`2522742448128979`)
- **5 videos nuevos prioritarios a subir:** `vivo2.mp4`, `vivo1.mp4`, `vivo4.mp4`, `cambiapiezas.mp4`, `+3.mp4`
- **Estructura de adsets:** Cold / Warm / Hot Remarketing (ver CONTEXT.md para creativos y destinos exactos)
- **UTM convention:** confirmada en Phase 1 (`utm_source=meta`, `utm_medium=paid_social`, `utm_campaign=inyeccion-motos-mayo26`, `utm_content` por ad)

### Claude's Discretion
- Presupuesto total a definir por usuario. Sugerencia: $10–15 USD/día por adset.
- Si usar Meta Ads Manager UI o API para crear — ver investigación abajo.

### Deferred Ideas (OUT OF SCOPE)
- WhatsApp como canal de conversión → Phase futura
- Videos pregrabado/evergreen (1.mp4, 3.mp4, 5.mp4, 6.mp4, 7.mp4) → Phase 3
- Nuevas grabaciones con Miguel → después de datos reales Phase 2
- Video con precio explícito → posible grabación post-Phase 2
- Video con countdown → posible si no se llenan cupos en primeros 3 días
- Testimonio de alumno → Phase de optimización futura
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| ADS-01 | Campaña nueva con objetivo OUTCOME_SALES activa y saliendo del learning phase (mínimo 50 eventos Purchase / 7 días) | Investigado: payloads de creación de campaña OUTCOME_SALES, condiciones de salida de learning phase |
| ADS-02 | Adset cold con audiencia de intereses (mecánica, motos, reparación) + targeting Argentina, excluye CABA y 50km alrededor | Investigado: estructura `targeting_spec`, `geo_locations`, `excluded_geo_locations`, cómo obtener interest IDs via API |
| ADS-03 | Adset warm de retargeting — visitantes de la landing en últimos 7 días → URL directo a Hotmart checkout | Investigado: creación de Custom Audience website visitors via API, `custom_audiences` en targeting, exclusión de compradores |
| ADS-04 | Ads con copy de urgencia real: fecha 2-3 mayo, precio en ARS como primario, "cupos limitados" creíble en headline o primer párrafo | Investigado: límites de caracteres, campos primary_text / headline / description, restricciones de ad creative |
</phase_requirements>

---

## Summary

Esta fase crea la infraestructura de anuncios completa en Meta: campaña OUTCOME_SALES, tres adsets (cold/warm/hot), subida de 5 videos nuevos con captions, y ad copies con urgencia real. El trabajo es principalmente operativo en Meta Ads Manager (UI), con soporte de API bash para tareas que se benefician de automatización (crear custom audiences, verificar video status, buscar interest IDs).

La decisión clave de arquitectura ya está tomada: OUTCOME_SALES con `optimization_goal: OFFSITE_CONVERSIONS` + `billing_event: IMPRESSIONS` + `promoted_object: {pixel_id, custom_event_type: PURCHASE}`. Esta combinación es la forma estándar de optimizar para compras en Meta.

El principal riesgo de Phase 2 es el learning phase: con presupuesto bajo y una audiencia argentina de mecánicos (nicho), puede ser difícil acumular los 30–50 Purchase events por adset en 7 días que Meta necesita para estabilizar la entrega. La investigación identifica la estrategia correcta para este escenario.

**Recomendación primaria:** Crear la campaña y adsets via Meta Ads Manager UI (más rápido, menos errores de API), pero usar bash + curl para: (1) buscar interest IDs antes de crear el adset cold, (2) crear las Custom Audiences del warm y hot adset, (3) verificar que los videos nuevos terminaron de procesar antes de usarlos en ads.

## Architectural Responsibility Map

| Capability | Primary Tier | Secondary Tier | Rationale |
|------------|-------------|----------------|-----------|
| Creación de campaña / adsets / ads | Meta Ads Manager UI | Meta Marketing API (curl bash) | UI tiene validación en tiempo real; API para automatización y auditoría |
| Upload de videos nuevos | Meta Ads Manager UI | API `/advideos` (curl multipart) | UI gestiona captions automáticas + progress visual; API para lotes grandes |
| Custom Audiences (warm/hot) | Meta Marketing API (curl) | UI manual | API garantiza retention_days=7 exacto sin errores de UI |
| Búsqueda de interest IDs | Meta Marketing API (curl `targeting_search`) | — | Los IDs son requeridos para el adset cold; no hay forma UI de exportar los IDs exactos |
| Copy de anuncios | Redacción manual (Juan Manuel) | — | Decisión creativa, no automatizable |
| Captions de videos | Meta Ads Manager UI (auto-generate) | .srt manual | Meta genera captions automáticas en español al subir en UI |
| Verificación Post-Setup | Meta Ads Manager UI | API insights | Confirmar que ads quedan PAUSED + ad delivery check antes de activar |

---

## Standard Stack

### Core
| Herramienta | Versión/Endpoint | Propósito | Por qué estándar |
|-------------|-----------------|-----------|------------------|
| Meta Marketing API | v19.0 (o v21.0) | Crear campaign/adsets/ads, custom audiences, video upload | API oficial Meta — ya usada en Phase 1 con token vigente |
| curl + bash | Sistema (disponible en bash) | Ejecutar llamadas API sin instalar SDK | Sin dependencias, portátil, reproducible |
| Meta Ads Manager UI | web.facebook.com/adsmanager | Crear creativos con video, gestionar captions, revisar antes de activar | Más rápido para tareas únicas; captions automáticas solo disponibles en UI |

### API Endpoints a Usar

| Endpoint | Método | Para |
|----------|--------|------|
| `/act_ACT_ID/campaigns` | POST | Crear campaña OUTCOME_SALES |
| `/act_ACT_ID/adsets` | POST | Crear adset cold/warm/hot |
| `/act_ACT_ID/customaudiences` | POST | Crear website visitors 7d y checkout visitors 7d |
| `/act_ACT_ID/advideos` | POST multipart | Subir videos nuevos (alternativa a UI) |
| `/VIDEO_ID?fields=status` | GET | Verificar que video terminó de procesar |
| `/act_ACT_ID/targetingsearch` | GET | Buscar interest IDs por keyword en español |
| `/act_ACT_ID/adcreatives` | POST | Crear creative con video + copy |
| `/act_ACT_ID/ads` | POST | Crear ad vinculando creative a adset |

**Versión de API vigente:** El token del proyecto fue generado en marzo 2026 y expira ~mayo 2026. Meta está en v25.0 a abril 2026 [CITED: developers.facebook.com/docs/graph-api/changelog], pero v19.0 sigue siendo soportada dentro de la ventana de 2 años. Usar v19.0 (ya validado con el token del proyecto) para evitar sorpresas de deprecación.

---

## Architecture Patterns

### System Architecture Diagram

```
[Videos locales MP4]
        |
        v
[Meta Ads Manager — Upload + Captions]
        |
        v
[Meta Video Library — IDs generados]
        |
        +------------------------------------------+
        |                                          |
        v                                          v
[Adset COLD                                [Adset WARM
 Audiencia: intereses motos AR             Audiencia: Custom Audience
 Excluye CABA +50km                        Website Visitors 7d
 Excluye audiencias AC                     + Engagement FB/IG 30d
 CTAv A → Landing+UTMs                     Excluye: Compradores
 CTAv B → Hotmart checkout+UTMs            Destino → Hotmart directo+UTMs
 Creativos: MDI_22, MDI_9, cambiapiezas]   Creativos: MDI_21, MDI_10, vivo1/2/4]
        |                                          |
        |                                [Adset HOT
        |                                 Audiencia: Custom Audience
        |                                 Checkout Hotmart 7d
        |                                 Excluye: Purchase event
        |                                 Destino → Hotmart directo+UTMs
        |                                 Creativos: MDI_24, +3]
        |                                          |
        +------------------------------------------+
                          |
                          v
              [Campaña MDI_Motos_VL_Mayo26
               Objetivo: OUTCOME_SALES
               Status: ACTIVE
               Budget: por adset]
                          |
                          v
              [Meta Pixel 2277921389363777]
                 Purchase event en Hotmart
                          |
                          v
              [Algoritmo optimiza entrega
               hacia usuarios con mayor
               probabilidad de compra]
```

### Recommended Project Structure

```
MDI_Inyeccion_Motos/.planning/phases/02-meta-ads-relanzamiento/
├── 02-CONTEXT.md         # Decisiones estratégicas (ya existe)
├── 02-RESEARCH.md        # Este archivo
├── 02-PLAN-01.md         # Wave 1: Setup custom audiences + búsqueda interests + upload videos
└── 02-PLAN-02.md         # Wave 2: Crear campaña + adsets + ads + activar
```

Scripts bash sugeridos (ejecutar desde bash en sesión):
```
# No se crean archivos de script — los comandos curl se incluyen
# directamente en las tareas del PLAN para ejecución directa
```

---

## Pattern 1: Crear Campaña OUTCOME_SALES

**Qué:** POST a `/campaigns` con `objective: OUTCOME_SALES`
**Cuándo usar:** Primer paso de la estructura. Siempre crear como PAUSED para revisar antes de activar.

```bash
# Source: adstellar.ai/blog/meta-ads-api-integration-tutorial (verificado)
# Versión: v19.0 (compatible con token vigente)

curl -X POST \
  "https://graph.facebook.com/v19.0/act_2143097429552738/campaigns" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "MDI_Motos_VL_Mayo26",
    "objective": "OUTCOME_SALES",
    "status": "PAUSED",
    "special_ad_categories": []
  }' \
  -H "Authorization: Bearer $META_TOKEN"
```

**Respuesta esperada:** `{"id": "120XXXXXXXXXX"}` — guardar como `$CAMPAIGN_ID`

---

## Pattern 2: Crear Adset OUTCOME_SALES — Parámetros Críticos

**Qué:** POST a `/adsets` con combinación correcta de campos para optimización por compra.

**Combinación requerida para OUTCOME_SALES + Purchase:** [VERIFIED: digitalapplied.com/blog/meta-ads-api-automation]
- `optimization_goal`: `"OFFSITE_CONVERSIONS"`
- `billing_event`: `"IMPRESSIONS"`
- `promoted_object`: `{"pixel_id": "2277921389363777", "custom_event_type": "PURCHASE"}`

```bash
# Adset COLD — estructura base (interests y geo se completan en Pattern 4)
curl -X POST \
  "https://graph.facebook.com/v19.0/act_2143097429552738/adsets" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "MDI_VL_Mayo26_Cold_Intereses_Motos",
    "campaign_id": "'"$CAMPAIGN_ID"'",
    "daily_budget": 1500,
    "billing_event": "IMPRESSIONS",
    "optimization_goal": "OFFSITE_CONVERSIONS",
    "bid_strategy": "LOWEST_COST_WITHOUT_CAP",
    "promoted_object": {
      "pixel_id": "2277921389363777",
      "custom_event_type": "PURCHASE"
    },
    "targeting": {
      "geo_locations": {
        "countries": ["AR"]
      },
      "interests": [
        {"id": "INTEREST_ID_MOTOS", "name": "Motorcycles"}
      ]
    },
    "start_time": "2026-04-27T00:00:00-0300",
    "status": "PAUSED"
  }' \
  -H "Authorization: Bearer $META_TOKEN"
```

**Nota:** `daily_budget` en centavos de la moneda de la cuenta. Si la cuenta factura en USD: 1500 = $15.00 USD/día.

---

## Pattern 3: Crear Custom Audience — Website Visitors 7 días

**Qué:** POST a `/customaudiences` con regla de pixel + retention_seconds para audiencia warm.

```bash
# Audiencia: Visitantes manualdelindependiente.com últimos 7 días
# Source: WebSearch verified (múltiples fuentes, estructura oficial confirmada)

curl -X POST \
  "https://graph.facebook.com/v19.0/act_2143097429552738/customaudiences" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "MDI_Visitantes_Landing_7d",
    "subtype": "WEBSITE",
    "description": "Visitantes manualdelindependiente.com ultimos 7 dias",
    "rule": {
      "inclusions": {
        "operator": "or",
        "rules": [
          {
            "event_sources": [
              {
                "id": "2277921389363777",
                "type": "pixel"
              }
            ],
            "retention_seconds": 604800,
            "filter": {
              "operator": "and",
              "filters": []
            }
          }
        ]
      }
    },
    "prefill": 1
  }' \
  -H "Authorization: Bearer $META_TOKEN"
```

**Respuesta:** `{"id": "AUDIENCE_ID"}` — usar en adset warm como `"custom_audiences": [{"id": "AUDIENCE_ID"}]`

**Para adset HOT (checkout Hotmart visitors):** Misma estructura pero con `url_contains: "pay.hotmart.com"` en el filter, `retention_seconds: 604800`.

**Nota:** El adset warm también incluye Engagement FB/IG 30d — esa audiencia se crea de forma similar pero con `event_sources.type: "page"` para páginas y `"ig_user"` para Instagram. La audiencia de engagement puede ser más fácil crearla manualmente desde Ads Manager (Audiences > Create > Engagement).

---

## Pattern 4: Buscar Interest IDs para Targeting Cold

**Qué:** GET a `/search` para obtener IDs de intereses relevantes para mecánica/motos en Argentina.

```bash
# Source: Meta Targeting Search API — [ASSUMED] estructura del endpoint
# Verificar con respuesta real antes de usar los IDs en producción

curl -G \
  "https://graph.facebook.com/v19.0/search" \
  --data-urlencode "type=adinterest" \
  --data-urlencode "q=motocicletas" \
  --data-urlencode "locale=es_AR" \
  --data-urlencode "limit=10" \
  -H "Authorization: Bearer $META_TOKEN"
```

**Keywords a buscar** (ejecutar una por una):
- `motocicletas`
- `mecanica automotriz`
- `reparacion de motos`
- `inyeccion electronica`
- `taller mecanico`
- `motorcycles` (en inglés, puede tener más cobertura en AR)

**Respuesta esperada:** Lista de objetos `{id, name, audience_size_upper_bound}` — seleccionar los 3-5 con mayor audience_size_upper_bound relevantes al mecánico argentino.

**CRÍTICO:** Los interest IDs no son transferibles entre cuentas ni regiones. Siempre buscar dinámicamente en la sesión, no hardcodear IDs de ejemplos online.

---

## Pattern 5: Subir Video via API

**Qué:** POST multipart a `/advideos` para subir los 5 videos nuevos.

```bash
# Source: WebSearch múltiples fuentes — [VERIFIED: sintaxis multipart curl]

curl -X POST \
  "https://graph.facebook.com/v19.0/act_2143097429552738/advideos" \
  -F "source=@/path/to/vivo2.mp4" \
  -F "title=MDI_VIVO_02_ESCASEZ_CUPOS" \
  -H "Authorization: Bearer $META_TOKEN"
```

**Verificar que el video terminó de procesar** (puede tardar 2–10 min dependiendo del tamaño):
```bash
curl -G \
  "https://graph.facebook.com/v19.0/VIDEO_ID" \
  --data-urlencode "fields=status,id,title" \
  -H "Authorization: Bearer $META_TOKEN"
# Esperar: "status": {"video_status": "ready"}
```

**Alternativa recomendada para captions:** Subir los videos directamente desde Meta Ads Manager UI. La función "Video Captions > Generate Automatically" está disponible al crear el ad creative — la UI lo hace en un solo paso. [CITED: facebook.com/business/help/1675722002698686]

---

## Pattern 6: Crear Ad Creative con Video

**Qué:** POST a `/adcreatives` con `object_story_spec` que incluye `video_data`.

```bash
# Source: adstellar.ai/blog/meta-ads-api-integration-tutorial (verificado)

curl -X POST \
  "https://graph.facebook.com/v19.0/act_2143097429552738/adcreatives" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "MDI_Cold_CambiaPiezas_Var_A_Landing",
    "object_story_spec": {
      "page_id": "PAGE_ID_MDI",
      "video_data": {
        "video_id": "1468901108356276",
        "message": "¿Ganas poco como mecánico?\n\nHay mecánicos que cobran el doble — no porque trabajen más, sino porque dominan lo que nadie sabe reparar: inyección electrónica.\n\nCupos limitados. 2 y 3 de mayo.\n$45.000 ARS ≈ USD $40",
        "call_to_action": {
          "type": "LEARN_MORE",
          "value": {
            "link": "https://manualdelindependiente.com/?utm_source=meta&utm_medium=paid_social&utm_campaign=inyeccion-motos-mayo26&utm_content=cold-cambiapiezas"
          }
        },
        "title": "2 y 3 de mayo — Cupos limitados"
      }
    }
  }' \
  -H "Authorization: Bearer $META_TOKEN"
```

**Para Variante B (destino directo Hotmart):** Cambiar el `link` en `call_to_action.value` a `https://pay.hotmart.com/H105380102G?bid=1776180424703&utm_source=meta&utm_medium=paid_social&utm_campaign=inyeccion-motos-mayo26&utm_content=cold-cambiapiezas-hotmart`

**Nota:** `page_id` es el ID de la página de Facebook de MDI — obtenerlo desde Meta Business Manager o desde la URL de la página.

---

## Pattern 7: Crear Ad Final

```bash
curl -X POST \
  "https://graph.facebook.com/v19.0/act_2143097429552738/ads" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "MDI_Cold_CambiaPiezas_Var_A",
    "adset_id": "'"$ADSET_ID"'",
    "creative": {
      "creative_id": "'"$CREATIVE_ID"'"
    },
    "status": "PAUSED"
  }' \
  -H "Authorization: Bearer $META_TOKEN"
```

---

## Pattern 8: Targeting — Geo Exclusión CABA + 50km

**Estructura verificada** (campo `excluded_geo_locations` en targeting_spec): [ASSUMED estructura exacta de excluded_geo_locations con radius — no pudo verificarse con curl real]

```json
{
  "geo_locations": {
    "countries": ["AR"]
  },
  "excluded_geo_locations": {
    "cities": [
      {
        "key": "2427178",
        "radius": 50,
        "distance_unit": "kilometer"
      }
    ]
  }
}
```

**CRÍTICO:** El `key` "2427178" es el location key de Buenos Aires/CABA. [ASSUMED — verificar con targeting search antes de usar]

**Cómo verificar el key correcto:**
```bash
curl -G \
  "https://graph.facebook.com/v19.0/search" \
  --data-urlencode "type=adgeolocation" \
  --data-urlencode "q=Buenos Aires" \
  --data-urlencode "country_code=AR" \
  --data-urlencode "location_types[]=city" \
  -H "Authorization: Bearer $META_TOKEN"
```

---

## Pattern 9: Custom Audience en targeting_spec del Adset

```json
{
  "targeting": {
    "geo_locations": {
      "countries": ["AR"]
    },
    "custom_audiences": [
      {"id": "AUDIENCE_ID_VISITANTES_7D"}
    ],
    "excluded_custom_audiences": [
      {"id": "AUDIENCE_ID_COMPRADORES"}
    ]
  }
}
```

**Excluir compradores del adset warm:** La audiencia de compradores (Purchase event) debe crearse como una Custom Audience separada con `retention_seconds: 7776000` (90 días) y `filter: {filters: [{field: "event", operator: "=", value: "Purchase"}]}`.

---

## Don't Hand-Roll

| Problema | No construir | Usar en cambio | Por qué |
|----------|-------------|----------------|---------|
| Captions de video | Script de transcripción propio | Meta Ads Manager UI → "Generate Automatically" | Meta usa ASR nativo; el resultado se puede editar en la UI antes de guardar |
| A/B test formal de Meta | Crear "A/B Test" via Meta Experiments | Crear 2 ads en el mismo adset con destinos distintos | Meta Experiments requiere dividir audiencia 50/50 y puede resetear learning phase — más complejidad de la necesaria para este test de destino |
| Audience Lookalike prematura | Lookalike de compradores ahora | Esperar Phase 2 completa con Purchase data real | Sin eventos Purchase reales, la lookalike no tiene base estadística para ser efectiva |
| Creación de audiences de engagement FB/IG | Reglas complejas via API | Meta Ads Manager UI → Audiences → Create → Engagement | La UI tiene los eventos de engagement FB/IG ya clasificados; via API requiere tipos de fuente complejos |

---

## Learning Phase — Qué Esperar

### Cifras estándar [VERIFIED: lebesgue.io, cometly.com, multiple sources]

| Métrica | Valor estándar | Aplica a MDI |
|---------|---------------|--------------|
| Purchase events para salir de learning | 30–50 por adset por semana | Desafiante con $15 USD/día en nicho AR |
| Umbral marcador (Meta UI) | 10 events muestran barra de progreso | Primer check a los 3 días |
| Duración de learning phase | 7 días desde el primer ad delivery | Empezar a monitorear a las 48h |
| Señal alternativa si Purchase es bajo | Add-to-Cart, InitiateCheckout | USAR SOLO si Purchase < 5 en 3 días — ver pitfall |

### Escenario MDI específico

Con $15 USD/día/adset y un precio de $40 USD por cupo:
- Si ROAS break-even = 1.0x → necesita 1 compra cada $40 de gasto
- Para 30 purchases/semana = $1,200 USD de gasto en 7 días = $171 USD/día
- **Conclusión:** Con $15/día, es casi imposible salir de learning phase en 7 días si se optimiza por Purchase exclusivamente.

### Estrategia para MDI [MEDIUM confidence]

**Opción A — Mantener PURCHASE y aceptar "Learning Limited":**
- Ventaja: El algoritmo sigue optimizando aunque sea en learning limited.
- El status "Learning Limited" no impide que los ads se sirvan ni conviertan.
- Monitorear CPA real en primeras 48h y ajustar budget si hay señales positivas.

**Opción B — Optimizar por InitiateCheckout temporalmente:**
- Más eventos de señal → sale más rápido de learning.
- Riesgo: Meta optimiza para clicks en checkout, no compras reales.
- Solo usar si en 72h hay 0 Purchase events y el pixel está verificado.
- Cambiar a Purchase una vez que haya 5+ InitiateCheckout por día.

**Recomendación:** Empezar con PURCHASE. Si al día 3 hay 0 conversiones y el pixel está confirmado, escalar budget antes de cambiar optimization_goal.

---

## A/B Test de Destino — Cómo Implementar

### Opción recomendada: 2 ads en el mismo adset [VERIFIED: bigflare.com/blog/meta-ads-landing-page-testing]

**No usar Meta Experiments** para este test porque:
1. Meta Experiments divide la audiencia 50/50 y puede resetear la learning phase.
2. Para un test de 7 días con presupuesto bajo, la diferencia estadística puede no ser significativa de todas formas.
3. El objetivo es simplemente observar qué destino genera más Purchase events.

**Implementación:**
- En el adset COLD: crear 2 ads con el mismo creative/video pero URLs distintas.
  - Ad A: `utm_content=cold-cambiapiezas-landing` → `manualdelindependiente.com?utm_...`
  - Ad B: `utm_content=cold-cambiapiezas-hotmart` → `pay.hotmart.com/H105380102G?utm_...`
- Meta sirve ambos ads y aprende cuál genera más Purchase events.
- Revisar resultados a las 72h: winner define destino para Phase 3.

**Métrica de decisión:** Cost per Purchase por ad. Si ambos tienen 0 Purchase en 72h, usar InitiateCheckout como proxy.

---

## Captions / Subtítulos — Flujo Recomendado

### Via Meta Ads Manager UI (recomendado) [CITED: facebook.com/business/help/1675722002698686]

1. Al crear el Ad Creative con video en Ads Manager: sección "Video Captions"
2. Seleccionar "Generate Automatically"
3. Meta usa ASR (Automatic Speech Recognition) para generar subtítulos
4. En la ventana "Review Your Captions": editar errores de transcripción (tecnicismos como "inyección electrónica", "carburador", "MDI")
5. Guardar — los captions se incrustan en el video para la UI de Meta

**Limitaciones del auto-generate:**
- Funciona mejor en inglés; en español puede tener errores en palabras técnicas de mecánica
- Si el audio es poco claro, Meta muestra "Manually Add Captions" en lugar del auto-generate
- Para videos con jerga argentina de mecánica, planear 5–10 min de edición por video

### Alternativa: archivo .srt

Formato: `nombrevideo.es_AR.srt` (ISO 639-1 + ISO 3166-1)
Subir via Ads Manager en la misma sección "Video Captions > Upload".

---

## Especificaciones de Ad Copy

### Límites de caracteres [VERIFIED: adsuploader.com/blog/meta-ad-copy-specs]

| Campo | Límite recomendado | Límite técnico API | Corte en mobile |
|-------|-------------------|-------------------|-----------------|
| Primary Text | 125 caracteres | 4,096 | 125 (después: "Ver más") |
| Headline (title) | 40 caracteres | 255 | 27 en Facebook Feed |
| Description | 25 caracteres | variable | Solo visible en 4 placements |

### Template de copy para MDI (cumple todos los requisitos de ADS-04)

**Primary Text (dentro de 125 chars para que no quede cortado):**
```
¿Gastas y gastas en piezas y el problema vuelve?

El problema no son las piezas — es el diagnóstico.
Con inyección electrónica vas directo al error real.

2 y 3 de mayo. $45.000 ARS ≈ USD $40.
Solo quedan [X] cupos.
```

**Headline (máximo 40 chars):**
```
2-3 mayo • $45.000 ARS • Cupos limitados
```

**Nota:** Meta permite hasta 5 variaciones de primary text y 5 de headline por ad — recomendado crear 2-3 variaciones para que el algoritmo optimice.

---

## Common Pitfalls

### Pitfall 1: Cambiar la optimización a mitad de la learning phase
**Qué sale mal:** Si se cambia `optimization_goal` o `billing_event` después de que el adset tiene delivery, Meta reinicia la learning phase desde cero.
**Por qué pasa:** Cualquier "significant edit" (cambio de creative, audiencia, bid strategy, optimization goal) = reset.
**Cómo evitar:** Definir todos los parámetros correctamente ANTES de activar. Una vez activo, no tocar en los primeros 7 días.
**Señales de alerta:** Status del adset vuelve a "Learning" después de editar.

### Pitfall 2: URL de destino sin UTMs genera atribución ciega
**Qué sale mal:** Si el link de Hotmart no tiene UTMs, Meta no puede atribuir la Purchase a un ad específico — el algoritmo no aprende cuál creative genera compras.
**Por qué pasa:** Hotmart redirección limpia las UTMs si no están configuradas en el parámetro correcto de Hotmart.
**Cómo evitar:** Verificar en Phase 1 (TRACK-03) que los UTMs sobreviven el redirect de Hotmart. Usar el link exacto con `?bid=1776180424703&utm_source=meta...`.
**Señales de alerta:** GA4 o Hotmart muestran compras sin source/medium definido.

### Pitfall 3: Subir video antes de que termine de procesar
**Qué sale mal:** El ad creative se crea con un video_id que no está "ready" — el ad queda en error o con creative inválido.
**Por qué pasa:** Meta procesa los videos de forma asíncrona; el upload devuelve un ID inmediatamente pero el video puede tardar 5–15 minutos en estar disponible.
**Cómo evitar:** Siempre verificar `status.video_status == "ready"` antes de usar el video_id en un ad creative.
**Señales de alerta:** API devuelve error "Video is not ready" al crear el creative.

### Pitfall 4: Location overlap — CABA dentro de Argentina
**Qué sale mal:** Si se incluye `countries: ["AR"]` y al mismo tiempo se excluye CABA en `excluded_geo_locations`, Meta puede devolver error de "location overlap".
**Por qué pasa:** Meta detecta que la exclusión está dentro del área incluida y lo considera inválido en algunas versiones de la API.
**Cómo evitar:** El campo correcto es `excluded_geo_locations`, no una negación dentro de `geo_locations`. Si la API devuelve error 100, manejar la exclusión desde la UI de Ads Manager que valida el formato automáticamente.
**Señales de alerta:** Error de API con código 100 "Invalid parameter" al crear el adset.

### Pitfall 5: Custom Audience de warm sin suficiente población
**Qué sale mal:** El adset warm no entra en delivery porque la audiencia de visitantes últimos 7d es demasiado pequeña para que Meta optimice.
**Por qué pasa:** Si la landing tiene poco tráfico (< 100 visitantes únicos en 7 días), Meta considera la audiencia demasiado pequeña y el adset queda en "Learning Limited" por falta de escala.
**Cómo evitar:** Expandir retention a 14 o 30 días si la audiencia de 7d es < 100 personas. Combinar con Engagement FB/IG 30d para aumentar el pool.
**Señales de alerta:** Adset warm en "Not Delivering" con razón "Audience Too Small".

### Pitfall 6: Autocrop de videos corta texto o cara
**Qué sale mal:** Meta genera automáticamente versiones 1:1 y 4:5 del video. Si el texto de caption o la cara del instructor queda en los bordes, el autocrop lo corta.
**Por qué pasa:** Meta hace autocrop vertical/cuadrado sin considerar el contenido.
**Cómo evitar:** Al subir en Ads Manager, revisar el preview en todos los placements (Feed 4:5, Stories 9:16, Reels 9:16) ANTES de publicar. Ajustar el crop manualmente en la UI si es necesario.
**Señales de alerta:** En el preview del ad se ve el video cortado con texto parcial.

---

## State of the Art

| Enfoque Anterior | Enfoque Actual (2024-2025) | Impacto para MDI |
|-----------------|--------------------------|-----------------|
| OUTCOME_TRAFFIC para vender tickets/cursos | OUTCOME_SALES con evento Purchase | Crítico — la campaña anterior gastó $78.70 con 0 ventas por usar el objetivo equivocado |
| Interés targeting detallado con exclusiones granulares | Advantage+ Audience (Meta retiró muchas exclusiones en 2025) | Algunas exclusiones detalladas (comportamientos, intereses negativos) ya no están disponibles via API — usar solo geo + intereses amplios |
| 50 conversiones/semana para salir de learning | 10 conversiones como umbral inicial (barra de progreso); 30-50 para estabilidad completa | Con budget bajo, esperar "Learning Limited" pero no pausar — el algoritmo sigue aprendiendo |
| Split test formal (Meta Experiments) | 2 ads en mismo adset con URLs distintas para test rápido | Para este test de 7 días, crear 2 ads en el mismo adset es más simple y suficiente |

**Deprecado:**
- Intereses negativos (excluir "audiencia que le gusta X"): Meta eliminó la mayoría de las exclusiones de intereses detailed targeting en 2023-2024. Solo quedan exclusiones por custom audiences y geo.
- WhatsApp como CTA principal para OUTCOME_SALES: Si el tráfico va a WhatsApp, Meta no recibe el evento Purchase del pixel y el algoritmo no aprende. Descartado correctamente en CONTEXT.md.

---

## Assumptions Log

| # | Claim | Section | Riesgo si está Mal |
|---|-------|---------|-------------------|
| A1 | Location key de CABA/Buenos Aires es "2427178" | Pattern 8 | El adset cold no excluye CABA correctamente → CPA más alto en audiencia equivocada |
| A2 | El endpoint de targeting search acepta `type=adinterest` con `locale=es_AR` para obtener interest IDs en español | Pattern 4 | Los interest IDs devueltos no corresponden a la audiencia argentina correcta |
| A3 | La audiencia de Engagement FB/IG es más fácil crearla desde UI que desde API | Don't Hand-Roll | Impacto bajo — si la API tiene soporte simple, se puede hacer via curl igualmente |
| A4 | Los videos existentes MDI_22, MDI_9, etc. siguen disponibles en la cuenta Meta con los IDs documentados | User Constraints | Si algún video fue eliminado, ese creative no se puede reutilizar — verificar en Media Library antes de crear creatives |
| A5 | La página de Facebook de MDI tiene un PAGE_ID accesible para el token vigente | Pattern 6 | Sin el PAGE_ID correcto, el ad creative no se puede crear via API |

---

## Open Questions (RESOLVED)

1. **¿Cuál es el PAGE_ID de la página de Facebook de MDI?**
   - RESOLVED: Obtener al inicio de Plan 01 Tarea 1 ejecutando `GET /me/accounts` con el token vigente. El PAGE_ID se guarda en 02-ASSETS.md como prerequisito bloqueante antes de crear cualquier ad creative. Plan 01 Tarea 1 incluye este paso explícitamente.

2. **¿Cuánto tráfico tiene actualmente la landing en los últimos 7 días?**
   - RESOLVED: La audiencia warm se crea con `retention_seconds: 604800` (7d). Si al verificar la audiencia en Meta la población es < 100 personas, el fallback documentado en Plan 01 Tarea 2 es ampliar a `retention_seconds: 1209600` (14d) antes de crear el adset warm. El Plan 02 incluye verificación del audience size antes de activar.

3. **¿Hay acceso de escritura a la cuenta de Meta para el token vigente?**
   - RESOLVED: Plan 01 Tarea 1 PASO 1 ejecuta `GET /act_2143097429552738?fields=name,account_status` como verificación de token antes de cualquier POST. Si devuelve error 190 (OAuthException), la tarea falla explícitamente con instrucción de renovar el token en developers.facebook.com. No se procede a crear nada sin confirmar acceso de escritura.

---

## Environment Availability

| Dependencia | Requerida Por | Disponible | Versión | Fallback |
|-------------|--------------|-----------|---------|---------|
| curl | Llamadas API Meta | ✓ | Bash nativo | — |
| Meta Marketing API token | Todos los POST | ✓ (hasta ~10 mayo) | v19.0 | Renovar en Meta Developers si error 190 |
| Meta Ads Manager UI | Upload videos + captions + revisión | ✓ (web) | — | API advideos para upload si UI falla |
| Videos locales (5 prioritarios) | Wave 1 upload | [ASSUMED] disponibles en equipo de Miguel | — | Bloqueante si los archivos no están en el equipo donde se ejecuta el trabajo |
| PAGE_ID de página Facebook MDI | Creación de ad creatives | Pendiente obtener | — | GET /me/accounts con el token |

**Dependencias bloqueantes sin fallback:**
- Videos locales MP4: si `vivo2.mp4`, `vivo1.mp4`, `vivo4.mp4`, `cambiapiezas.mp4`, `+3.mp4` no están disponibles localmente, no se pueden subir. Verificar con Miguel antes de comenzar Wave 1.
- Token Meta vigente: si el token expiró antes del 10 mayo, renovar via Meta Developers antes de cualquier llamada API.

---

## Validation Architecture

> `nyquist_validation: true` en config.json — sección incluida.

### Test Framework

| Propiedad | Valor |
|-----------|-------|
| Framework | Manual verification (no unit test framework — API interactions and Meta Ads Manager UI) |
| Config file | — (ningún framework de test automático aplicable a Meta Ads API) |
| Quick run command | `curl -G "https://graph.facebook.com/v19.0/act_2143097429552738?fields=name,account_status" -H "Authorization: Bearer $META_TOKEN"` |
| Full suite command | Verificación manual en Meta Ads Manager + API GET de todos los objetos creados |

### Phase Requirements → Test Map

| Req ID | Behavior | Test Type | Automated Command | Existe? |
|--------|----------|-----------|-------------------|---------|
| ADS-01 | Campaña MDI_Motos_VL_Mayo26 existe con OUTCOME_SALES y status ACTIVE | Manual + API GET | `curl -G "https://graph.facebook.com/v19.0/CAMPAIGN_ID?fields=name,objective,status" -H "Authorization: Bearer $META_TOKEN"` | ❌ Wave 0 (se crea en Wave 2) |
| ADS-02 | Adset cold targetea AR, excluye CABA, tiene intereses de motos | Manual (Meta Ads Manager UI) | Revisar "Audience Definition" del adset en UI — debería mostrar "Argentina excluding Buenos Aires" | ❌ Wave 0 |
| ADS-03 | Adset warm tiene Custom Audience visitantes 7d, destino Hotmart checkout | API GET + Manual | `curl -G "https://graph.facebook.com/v19.0/ADSET_ID?fields=targeting,name" -H "Authorization: Bearer $META_TOKEN"` | ❌ Wave 0 |
| ADS-04 | Ads muestran precio ARS, fecha 2-3 mayo, "cupos limitados" en primary_text o title | Manual review | Revisar "Preview" del ad en Ads Manager antes de activar | ❌ Wave 0 |

### Sampling Rate
- **Por tarea commit:** Verificar via API GET que el objeto creado tiene el status correcto (PAUSED).
- **Al final de Wave 1:** Confirmar que las 3 Custom Audiences existen y tienen audience_size > 0.
- **Al final de Wave 2:** Revisar en Meta Ads Manager que todos los ads están PAUSED, listos para activar. No activar hasta que el usuario confirme.
- **Phase gate antes de activar:** Usuario revisa todos los ads en "Preview" y da aprobación explícita. No usar `/gsd-verify-work` automatizado — la verificación final es manual en Meta Ads Manager.

### Wave 0 Gaps
- [ ] Obtener PAGE_ID de la página Facebook de MDI (ejecutar GET /me/accounts en Wave 1 Tarea 0)
- [ ] Verificar que el token Meta tiene permisos `ads_management` (ejecutar GET /act_ID?fields=name en Wave 1 Tarea 0)
- [ ] Confirmar disponibilidad local de los 5 archivos MP4 antes de subir

*(No hay archivos de test que crear — la validación es operacional, no de código)*

---

## Security Domain

> `security_enforcement` no está explícitamente en config.json — se trata como habilitado.

### Applicable ASVS Categories

| ASVS Category | Applies | Standard Control |
|---------------|---------|-----------------|
| V2 Authentication | no | Token OAuth Meta manejado externamente |
| V3 Session Management | no | Sin sesiones de usuario en esta fase |
| V4 Access Control | parcial | Token Meta debe tener scope mínimo necesario (ads_management, ads_read) |
| V5 Input Validation | no | No hay inputs de usuario en esta fase |
| V6 Cryptography | no | HTTPS nativo de Meta API |

### Manejo del Token Meta

- **NUNCA** incluir el token en commits de git o en archivos del repo.
- Usar variable de entorno `$META_TOKEN` en todos los comandos curl.
- El token expira ~10 mayo 2026 — si hay error 190 (OAuthException), renovar en developers.facebook.com.
- Si se crean scripts .sh reutilizables: agregar al .gitignore o usar variables de entorno del sistema.

---

## Sources

### Primary (HIGH confidence)
- `adstellar.ai/blog/meta-ads-api-integration-tutorial` — payloads de campaña + adset OUTCOME_SALES v19.0
- `digitalapplied.com/blog/meta-ads-api-automation` — combinación OFFSITE_CONVERSIONS + IMPRESSIONS + promoted_object PURCHASE
- `facebook.com/business/help/1675722002698686` — captions automáticas en Meta Ads Manager (official docs)
- `lebesgue.io/facebook-ads/facebook-ads-learning-phase-what-you-need-to-know-2024-update` — 30-50 Purchase events para salir de learning phase
- `adsuploader.com/blog/meta-ad-copy-specs` — límites de caracteres primary_text, headline, description

### Secondary (MEDIUM confidence)
- WebSearch múltiples fuentes — estructura de Custom Audience website visitors (`retention_seconds: 604800`, `event_sources.type: pixel`)
- WebSearch — `custom_audiences` y `excluded_custom_audiences` en targeting_spec de adsets
- WebSearch — `special_ad_categories: []` como campo obligatorio al crear campaña
- `bigflare.com/blog/meta-ads-landing-page-testing` — recomendación de 2 ads en mismo adset vs Meta Experiments

### Tertiary (LOW confidence — marcados como ASSUMED en texto)
- Location key de CABA (2427178) — no pudo verificarse con API real en esta sesión
- Endpoint `type=adgeolocation` para buscar city keys — estructura inferida de documentación de targeting search

---

## Metadata

**Confidence breakdown:**
- Payloads de campaña y adset: MEDIUM-HIGH — verificado en múltiples tutoriales, no en docs oficiales directas (Meta docs truncaron en WebFetch)
- Custom Audiences: MEDIUM — estructura confirmada en búsqueda pero no ejecutada en la cuenta real
- Interest IDs: LOW — requieren búsqueda dinámica en tiempo de ejecución; ningún ID puede hardcodearse
- Learning phase: HIGH — múltiples fuentes consistentes con cifras de 30-50 events
- Captions: HIGH — documentación oficial de Meta accedida via búsqueda

**Research date:** 2026-04-26
**Valid until:** 2026-05-15 (30 días — Meta API estable; pero los interest IDs y location keys son dinámicos y deben verificarse en cada sesión)
