# MDI — Manual del Independiente

## Sitio en producción
- **Dominio:** https://manualdelindependiente.com/
- **Hosting:** Netlify (auto-deploy en cada push a `main`)
- **Publish directory:** `LP_MDI_Inyeccion Motos` (configurado en `netlify.toml`)

## Estructura de páginas
| URL | Archivo |
|-----|---------|
| `manualdelindependiente.com/` | `LP_MDI_Inyeccion Motos/index.html` |
| `.../25022026.oferta.jueves.45/` | `LP_MDI_Inyeccion Motos/25022026.oferta.jueves.45/index.html` |
| `.../TYP_MDI1052418561365/` | `LP_MDI_Inyeccion Motos/TYP_MDI1052418561365/index.html` |

## Tecnología
- HTML estático puro, sin build process
- Tailwind CSS via CDN, Lucide Icons via CDN
- Google Fonts: Plus Jakarta Sans, Inter, Space Grotesk

## Meta Pixel — ID: `2277921389363777`
Instalado directamente en los 3 HTML. Eventos en `index.html`:
- `PageView` — carga de página (automático)
- `InitiateCheckout` — botón MercadoPago (`onclick`)
- `Contact` — ambos botones WhatsApp (`onclick`)

## WhatsApp
- Número: `5491133919502`
- Siempre usar URL con mensaje predeterminado:
  `https://wa.me/5491133919502?text=Hola%2C%20vengo%20de%20la%20p%C3%A1gina%20web`

## MercadoPago checkout
- `https://mpago.la/2ZcmHXM`

## Hotmart API — MDI
- **Credenciales:** `05_EL_SOLAR/.env` → `HOTMART_MDI_CLIENT_ID`, `HOTMART_MDI_CLIENT_SECRET`, `HOTMART_MDI_BASIC`
- **Token endpoint:** `POST https://api-sec-vlc.hotmart.com/security/oauth/token?grant_type=client_credentials`
- **Auth header:** `Authorization: $HOTMART_MDI_BASIC`
- **Token TTL:** ~48h (regenerar si hay 401)
- **Productos en Hotmart:**
  - Virtual en vivo (2-3 mayo): `https://pay.hotmart.com/H105380102G?bid=1776180424703`
  - Pregrabado: `https://hotmart.com/es/marketplace/productos/clase-de-inyeccion-de-motos-pre-grabado/O105383269C`
  - Ebook: publicado (link pendiente de confirmar)
- **Ventas consultables:** `GET https://developers.hotmart.com/payments/api/v1/sales/history`

## Creativos Meta Ads — reutilizables
Estos video IDs tuvieron CTR >5.7% en campañas previas. Usar en nuevas campañas:
- Video 1 ("Ganas poco como mecánico?"): `1430736985403167`
- Video 2 ("Domina la especialidad que todos buscan"): `1987879962608217`
- Video 3 ("Genera ingresos como especialista"): `1468901108356276`

## GSD Planning
- **Roadmap:** `.planning/ROADMAP.md` — 3 fases
- **Estado:** `.planning/STATE.md` — Phase 1 activa
- **Próximo paso:** `/gsd-plan-phase 1` (Fundamentos de Tracking)

## Flujo de edición
1. Editar archivos en `LP_MDI_Inyeccion Motos/`
2. `git add`, `git commit`, `git push origin main`
3. Netlify redesplega en ~1 minuto
