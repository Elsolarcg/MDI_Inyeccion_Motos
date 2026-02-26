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

## Flujo de edición
1. Editar archivos en `LP_MDI_Inyeccion Motos/`
2. `git add`, `git commit`, `git push origin main`
3. Netlify redesplega en ~1 minuto
