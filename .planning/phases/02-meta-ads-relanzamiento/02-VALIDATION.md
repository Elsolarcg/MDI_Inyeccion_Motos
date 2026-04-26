---
phase: 2
phase_slug: meta-ads-relanzamiento
date: 2026-04-26
framework: manual
---

# Phase 2: Meta Ads Relanzamiento — Validation Strategy

## Framework

| Propiedad | Valor |
|-----------|-------|
| Framework | Manual verification (Meta Marketing API + Meta Ads Manager UI) |
| Quick check | `curl -G "https://graph.facebook.com/v19.0/act_2143097429552738?fields=name,account_status" -H "Authorization: Bearer $META_TOKEN"` |
| Full verification | API GET de todos los objetos creados + revisión visual en Meta Ads Manager |
| Phase gate | Aprobación explícita del usuario antes de activar la campaña (el activar es el único paso irreversible) |

## Requirements → Verification Map

| Req ID | Comportamiento a verificar | Tipo | Comando de verificación | Wave |
|--------|--------------------------|------|------------------------|------|
| ADS-01 | Campaña `MDI_Motos_VL_Mayo26` existe con `objective=OUTCOME_SALES` y `status=ACTIVE` | API GET | `curl -G "https://graph.facebook.com/v19.0/$CAMPAIGN_ID?fields=name,objective,status" -H "Authorization: Bearer $META_TOKEN"` → `"objective":"OUTCOME_SALES"` + `"status":"ACTIVE"` | Wave 2 (post-activación) |
| ADS-02 | Adset cold targetea Argentina, excluye CABA +50km, tiene intereses de motos, excluye audiencias AC | Manual + API | `curl -G "https://graph.facebook.com/v19.0/$ADSET_COLD_ID?fields=name,targeting,status" -H "Authorization: Bearer $META_TOKEN"` → revisar `geo_locations.countries:["AR"]` + `excluded_geo_locations.cities:[{key:CABA_KEY}]` + `interests` no vacío | Wave 2 |
| ADS-03 | Adset warm tiene Custom Audience visitantes 7d, destino Hotmart checkout con UTMs | API + Manual | `curl -G "https://graph.facebook.com/v19.0/$ADSET_WARM_ID?fields=name,targeting" -H "Authorization: Bearer $META_TOKEN"` → `custom_audiences` contiene `AUDIENCE_VISITANTES_7D` | Wave 2 |
| ADS-04 | Ads muestran precio ARS primario, fecha "2 y 3 de mayo", "cupos limitados" en primary_text o title | Manual review | Preview en Meta Ads Manager antes de activar — confirmar visualmente que cada ad muestra los 3 elementos requeridos | Wave 2 (checkpoint) |

## Sampling Strategy

| Momento | Qué verificar | Método |
|---------|--------------|--------|
| Tras cada POST (Wave 1) | Objeto creado existe y tiene status correcto | `curl -G /OBJECT_ID?fields=name,status` |
| Fin de Wave 1 | 3 Custom Audiences existen con `approximate_count > 0` | `curl -G /AUDIENCE_ID?fields=name,approximate_count` |
| Fin de Wave 2 (pre-activación) | Todos los ads en PAUSED, copy correcto, URLs con UTMs, crop OK en todos los placements | Revisión visual en Ads Manager — checkpoint bloqueante |
| Post-activación (48h) | Ads en ACTIVE, delivery iniciado, CTR > 0, 0 errores de creative | Meta Ads Manager → columnas: Status, Delivery, Impressions, CTR |
| Día 3 | Purchase events recibidos (> 0) o fallback InitiateCheckout | Meta Ads Manager → columnas: Purchases, Cost per Purchase, InitiateCheckout |

## Wave 0 Gaps (prerequisitos antes de Wave 1)

- [ ] Confirmar disponibilidad local de los 5 archivos MP4: `vivo2.mp4`, `vivo1.mp4`, `vivo4.mp4`, `cambiapiezas.mp4`, `+3.mp4`
- [ ] Verificar que el token Meta tiene permisos `ads_management` (GET /act_ID?fields=name)
- [ ] Obtener PAGE_ID de la página Facebook de MDI (GET /me/accounts)

## Phase Gate — Criterio de Activación

La campaña NO se activa hasta que el usuario confirme TODOS estos puntos en el checkpoint de Plan 02:

1. ✅ Todos los ads muestran precio `$45.000 ARS` visible
2. ✅ Todos los ads muestran fecha `2 y 3 de mayo`
3. ✅ Todos los ads contienen `cupos limitados` (en texto o headline)
4. ✅ URLs de destino contienen `utm_source=meta&utm_medium=paid_social&utm_campaign=inyeccion-motos-mayo26`
5. ✅ Captions visibles en preview de todos los videos nuevos
6. ✅ Crop correcto en Feed (4:5), Stories (9:16) y Reels (9:16) — sin texto o cara cortados
7. ✅ Budget confirmado: `$15 USD/día` cold, `$10 USD/día` warm, `$5 USD/día` hot = `$30 USD/día` total
8. ✅ Usuario escribe "aprobado" antes de ejecutar el PATCH status=ACTIVE

## Fallbacks Documentados

| Condición | Fallback |
|-----------|---------|
| Audiencia warm 7d < 100 personas | Ampliar a 14 días antes de crear adset |
| Error 100 en exclusión geo CABA via API | Manejar exclusión desde UI de Ads Manager |
| 0 Purchase events en 72h con pixel verificado | Escalar budget antes de cambiar optimization_goal |
| Si en día 3 hay 0 Purchase y 0 InitiateCheckout | Revisar que el pixel dispara en Hotmart + verificar creative delivery |
| Video no "ready" al crear creative | Esperar 5-15 min y re-verificar status antes de usar el video_id |

## Security Notes

- Token Meta: usar siempre como variable `$META_TOKEN` — nunca commitear en git
- Si error 190 (OAuthException): renovar token en developers.facebook.com antes de continuar
- Todos los comandos curl usan HTTPS nativo de Meta API

---
*Validation strategy: 2026-04-26*
*Framework: Manual verification — Meta Marketing API + Ads Manager UI*
