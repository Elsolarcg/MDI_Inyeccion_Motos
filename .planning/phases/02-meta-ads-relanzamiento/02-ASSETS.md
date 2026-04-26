# 02-ASSETS.md — MDI Meta Ads Relanzamiento
> Fuente de verdad para Plan 02. Generado en Wave 1 (2026-04-26).
> Plan 02 lee este archivo antes de ejecutar cualquier POST.

---

## Token + Cuenta

| Campo | Valor |
|-------|-------|
| Token status | VÁLIDO (verificado 2026-04-26) |
| Expiry estimado | ~10 mayo 2026 |
| Account name | Manual del Independiente |
| account_status | 1 (activa) |
| Currency | USD |
| AD_ACCOUNT_ID | act_2143097429552738 |
| PIXEL_ID | 2277921389363777 |

---

## PAGE_ID

PAGE_ID: 899522349909725
Nombre: Manual del Independiente
Fuente: GET /me/accounts (2026-04-26)

---

## Interest IDs seleccionados (adset Cold)

Selección criterio: relevancia al mecánico de motos argentino + audience_size_upper_bound > 20M.
"mecanica automotriz", "mecanica motos", "inyeccion electronica" → devolvieron 0 resultados en API.

| ID | Nombre | Audience Size (upper) | Categoría |
|----|--------|----------------------|-----------|
| 6003353550130 | Motocicletas (vehículos) | 489,179,400 | Intereses > Vehículos |
| 6003472344463 | Motociclismo (deportes de motor) | 70,524,820 | Deportes y actividades al aire libre |
| 6003090593301 | Taller mecánico (vehículos) | 24,276,330 | Negocios e industria |
| 6003527528795 | Ingeniería mecánica | 68,212,660 | Negocios e industria |

INTEREST_IDS para Plan 02 PASO 0:
```
[{"id":"6003353550130","name":"Motocicletas"},{"id":"6003472344463","name":"Motociclismo"},{"id":"6003090593301","name":"Taller mecanico"},{"id":"6003527528795","name":"Ingenieria mecanica"}]
```

---

## Location Key CABA

CABA_LOCATION_KEY: 83693
Nombre API: Buenos Aires
Region: Ciudad Autónoma de Buenos Aires (region_id: 103)
Fuente: GET /search?type=adgeolocation&q=Buenos+Aires&country_code=AR (2026-04-26)
Nota: Es la CABA (ciudad capital), NO la provincia de Buenos Aires.

---

## AC Audience IDs (audiencias de Aire Acondicionado — excluir en adset Cold)

AC_AUDIENCE_IDS: 120241666206530095

| ID | Nombre | Subtype |
|----|--------|---------|
| 120241666206530095 | Visitantes LP AireAcondicionado 180d | WEBSITE |

Nota: Solo se encontró 1 audiencia AC. Incluir en excluded_custom_audiences del adset Cold junto a AUDIENCE_COMPRADORES.

---

## Custom Audiences Creadas (Wave 1 — 2026-04-26)

| Audiencia | ID | Subtype | Retención | Uso en Plan 02 |
|-----------|-----|---------|-----------|----------------|
| MDI_Visitantes_Landing_7d | 120243600103560095 | WEBSITE | 7 días (604800s) | Adset WARM — incluir |
| MDI_Checkout_Hotmart_7d | 120243600111200095 | WEBSITE | 7 días (604800s) | Adset HOT — incluir (fallback si size=0) |
| MDI_Compradores_Purchase_90d | 120243600111660095 | WEBSITE | 90 días (7776000s) | Todos los adsets — EXCLUIR |

AUDIENCE_VISITANTES: 120243600103560095
AUDIENCE_CHECKOUT: 120243600111200095
AUDIENCE_COMPRADORES: 120243600111660095

Nota MDI_Checkout_Hotmart_7d: el pixel de MDI está instalado en manualdelindependiente.com,
NO en pay.hotmart.com. La audiencia de URL-filter puede quedar en 0 si el pixel no capturó
navegación a Hotmart. Verificar tamaño en Meta Audiences UI antes de crear adset Hot.
Si approximate_count = 0: usar AUDIENCE_VISITANTES como fallback para adset Hot.

Audiencias existentes en cuenta (pre-existentes, no creadas en Wave 1):
- 120241666206530095 — Visitantes LP AireAcondicionado 180d (AC — usar para exclusión cold)
- 120240241419400095 — Iniciaron checkout 30 días (ignorar en Phase 2)
- 120240241397700095 — Interactuaron FB 180 días (ENGAGEMENT — fallback si no se crea la de 30d)
- 120240241370360095 — Interactuaron IG 180 días (IG_BUSINESS — fallback warm)
- 120240241315470095 — Visitantes web 30 días (ignorar — puede solaparse con nueva)

---

## Engagement Audience (adset Warm)

ENGAGEMENT_AUDIENCE_ID: PENDIENTE

Acción requerida del usuario:
1. Ir a https://www.facebook.com/adsmanager/audiences
2. Create Audience → Custom Audience → Facebook Page / Instagram Account
3. Configurar: Anyone who engaged → últimos 30 días
4. Nombre: MDI_Engagement_FBIG_30d
5. Responder en el chat con el ID obtenido

Fallback disponible si no se crea nueva:
- 120240241397700095 — "Interactuaron FB 180 días" (existente, 180d en vez de 30d)
- 120240241370360095 — "Interactuaron IG 180 días" (existente, 180d en vez de 30d)

---

## Videos Nuevos (Tarea 3 — PENDIENTE)

Los archivos MP4 no están disponibles en la máquina local. Se deben conseguir antes de Plan 02.

| Título | Video ID | Status | Adset destino |
|--------|----------|--------|---------------|
| MDI_VIVO_02_ESCASEZ_CUPOS | PENDIENTE | — | Warm |
| MDI_VIVO_01_ZOOM_FACE_TO_FACE | PENDIENTE | — | Warm |
| MDI_VIVO_03_COMPLETO_ESCASEZ | PENDIENTE | — | Warm |
| MDI_GANCHO_AUTORIDAD_CAMBIAPIEZAS | PENDIENTE | — | Cold |
| MDI_OFERTAS_MULTIPLE_HUMOR | PENDIENTE | — | Hot |

Cómo obtener los IDs cuando estén disponibles:
- Opción A: Subir via API: `curl -X POST /act_ID/advideos -F "source=@/ruta/video.mp4" -F "title=NOMBRE"`
- Opción B: Subir desde Meta Ads Manager → Media Library → subir → el ID aparece en la URL del video
- Una vez subidos: anotar IDs aquí y reemplazar PENDIENTE

NOTA CAPTIONS: Todos los videos nuevos NO tienen subtítulos. Al crear ad creatives en Plan 02,
ir a Meta Ads Manager → editar el ad → Video Captions → Generate Automatically.

---

## Videos Existentes en Meta (CTR >5.7% — siempre disponibles)

| ID | Nombre | Adset destino |
|----|--------|---------------|
| 1468901108356276 | MDI_22 "Cambia pieza / Cambiapiezas" | Cold principal |
| 1987879962608217 | MDI_9 "Metodología diagnóstico" | Cold secundario |
| 1430736985403167 | MDI_21 "Menú soluciones" | Warm |
| 943274925086396 | MDI_10 "FOMO cupos" | Warm |
| 2522742448128979 | MDI_24 "Reto final humor" | Hot |

---

## Resumen para Plan 02 — Variables PASO 0

```bash
PAGE_ID="899522349909725"
INTEREST_IDS='[{"id":"6003353550130","name":"Motocicletas"},{"id":"6003472344463","name":"Motociclismo"},{"id":"6003090593301","name":"Taller mecanico"},{"id":"6003527528795","name":"Ingenieria mecanica"}]'
CABA_KEY="83693"
AUDIENCE_VISITANTES="120243600103560095"
AUDIENCE_CHECKOUT="120243600111200095"
AUDIENCE_COMPRADORES="120243600111660095"
ENGAGEMENT_AUDIENCE_ID="[PENDIENTE — completar con ID del usuario]"
AC_AUDIENCE_IDS="120241666206530095"
```

---

## Campaña y Adsets (se completa en Plan 02)

CAMPAIGN_ID: [pendiente — Plan 02 Tarea 1]
ADSET_ID_COLD: [pendiente]  — MDI_VL_Mayo26_Cold_Intereses_Motos
ADSET_ID_WARM: [pendiente]  — MDI_VL_Mayo26_Warm_Visitantes_7d
ADSET_ID_HOT: [pendiente]   — MDI_VL_Mayo26_Hot_Checkout_7d

---

*Wave 1 completada: 2026-04-26 | Pendiente: videos MP4 + ENGAGEMENT_AUDIENCE_ID (acción manual)*
