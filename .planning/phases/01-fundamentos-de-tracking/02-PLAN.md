---
phase: 1
plan: 2
type: execute
wave: 2
depends_on: [1]
files_modified:
  - ".planning/STATE.md"
autonomous: false
requirements:
  - TRACK-02
---

<objective>
Verificar que el evento Purchase del Meta Pixel (ID: 2277921389363777) dispara correctamente en la página de confirmación de Hotmart. Este resultado determina si Phase 2 puede usar OUTCOME_SALES (óptimo) o debe usar OUTCOME_TRAFFIC como fallback.
</objective>

<context>
El evento Purchase en Hotmart fue instalado "según Miguel" pero nunca verificado con herramientas reales. Sin este evento limpio:
- Meta OUTCOME_SALES no puede optimizar para compradores reales
- CPA efectivo será 5-10x peor que con señal correcta
- Cada dólar de budget se desperdicia parcialmente

Pixel ID: 2277921389363777
Productos Hotmart a verificar:
- Virtual en Vivo: https://pay.hotmart.com/H105380102G?bid=1776180424703
- Pregrabado: https://hotmart.com/es/marketplace/productos/clase-de-inyeccion-de-motos-pre-grabado/O105383269C

Credenciales Hotmart (productor MDI): ver .env → HOTMART_MDI_CLIENT_ID, HOTMART_MDI_CLIENT_SECRET
</context>

<tasks>

<task id="1">
<title>TRACK-02: Verificar configuración de Pixel en Hotmart</title>
<type>execute</type>
<autonomous>false</autonomous>
<read_first>
- CLAUDE.md (sección "Hotmart API")
- .planning/STATE.md (sección "Estado Hotmart API" y blocker TRACK-02)
</read_first>
<action>
[ACCIÓN MANUAL — realizar en browser]

1. Ir a https://app-vlc.hotmart.com/ (panel de productores Hotmart)
2. Iniciar sesión con las credenciales de Miguel (productor de MDI)
3. Navegar a: Ferramentas → Rastreamento → Pixels (o "Herramientas" → "Pixel de rastreo")
4. Buscar si Meta Pixel ID 2277921389363777 está configurado

RESULTADO POSIBLE A — Pixel configurado y activo:
→ Continuar a Task 2 para verificar que dispara

RESULTADO POSIBLE B — Pixel NO está configurado:
→ Hacer clic en "Adicionar pixel" → seleccionar Facebook/Meta
→ Ingresar Pixel ID: 2277921389363777
→ Guardar
→ Continuar a Task 2

RESULTADO POSIBLE C — No tienes acceso al panel de Hotmart:
→ Contactar a Miguel directamente con screenshot de esta tarea
→ Pedirle que confirme el Pixel ID configurado en el panel
</action>
<acceptance_criteria>
- El panel de Hotmart muestra el Pixel ID 2277921389363777 en la sección de rastreo/pixels
- El estado del pixel es "Activo" o equivalente
</acceptance_criteria>
</task>

<task id="2">
<title>TRACK-02: Test Purchase con Meta Pixel Helper o Events Manager</title>
<type>execute</type>
<autonomous>false</autonomous>
<read_first>
- .planning/STATE.md (blocker TRACK-02)
</read_first>
<action>
[ACCIÓN MANUAL — requiere Chrome]

OPCIÓN A (más rápido — Meta Events Manager Test Events):
1. Abrir: https://www.facebook.com/events_manager2/pixel/2277921389363777/test_events/
2. En la pestaña "Test en el navegador": ingresar URL https://pay.hotmart.com/H105380102G?bid=1776180424703
3. Hacer clic en "Abrir sitio web" (abre en nueva pestaña con código de test activo)
4. Simular un checkout en Hotmart (llegar hasta la página de pago — no hace falta completar)
5. Volver a Events Manager → verificar si aparece algún evento (Purchase, InitiateCheckout, PageView)

OPCIÓN B (Meta Pixel Helper en Chrome):
1. Instalar Meta Pixel Helper desde Chrome Web Store (si no está instalado)
2. Ir a https://pay.hotmart.com/H105380102G?bid=1776180424703
3. Iniciar proceso de checkout
4. Hacer clic en el icono de Pixel Helper → ver qué eventos disparan
5. Intentar llegar hasta la página de confirmación (si es posible con tarjeta de prueba)

EVENTO ESPERADO:
- event_name: "Purchase"
- Puede incluir: value, currency
- Dispara en: página de confirmación/agradecimiento de Hotmart (después del pago exitoso)

NOTA IMPORTANTE: El evento InitiateCheckout que dispara en la landing (ya configurado y limpio) es diferente. Lo que necesitamos verificar es el Purchase que dispara DENTRO de Hotmart después del pago.
</action>
<acceptance_criteria>
- Meta Pixel Helper O Meta Events Manager muestra el evento Purchase con event_name="Purchase" para el Pixel 2277921389363777
- El evento dispara en la URL de confirmación de Hotmart (no en la landing de MDI)
</acceptance_criteria>
</task>

<task id="3">
<title>TRACK-02: Documentar resultado y actualizar STATE.md</title>
<type>execute</type>
<autonomous>false</autonomous>
<read_first>
- .planning/STATE.md (sección "Blockers")
</read_first>
<action>
Actualizar .planning/STATE.md sección "Blockers" con el resultado:

SI TRACK-02 PASA ✅:
Reemplazar la línea de TRACK-02 con:
- **TRACK-02 ✅ RESUELTO [fecha]**: Pixel Purchase verificado en Hotmart. Evento dispara en página de confirmación. Meta Events Manager / Pixel Helper confirma event_name="Purchase". Phase 2 puede usar OUTCOME_SALES.

SI TRACK-02 FALLA ❌:
Reemplazar la línea de TRACK-02 con:
- **TRACK-02 ❌ BLOQUEADO [fecha]**: Pixel Purchase NO dispara en Hotmart. Causa: [descripción exacta]. 
  Fallback Phase 2: usar OUTCOME_TRAFFIC con URL de destino directo a Hotmart checkout. El CPA será mayor (~3-5x) pero el tráfico llegará al checkout sin necesitar señal Purchase.

Luego hacer commit:
git add .planning/STATE.md && git commit -m "track(TRACK-02): verificación pixel Purchase Hotmart — [PASS/FAIL]"
</action>
<acceptance_criteria>
- .planning/STATE.md contiene resultado de TRACK-02 con ✅ o ❌ y fecha
- Si ✅: STATE.md menciona "Phase 2 puede usar OUTCOME_SALES"
- Si ❌: STATE.md menciona el fallback OUTCOME_TRAFFIC y la causa del fallo
- git log --oneline -1 muestra commit con "TRACK-02"
</acceptance_criteria>
</task>

</tasks>

<verification>
1. .planning/STATE.md contiene resultado documentado de TRACK-02 (✅ o ❌)
2. git log muestra commit con resultado de TRACK-02
3. Si ✅: Hotmart tiene Pixel 2277921389363777 configurado y activo
4. Si ❌: Causa documentada + decisión sobre fallback para Phase 2
</verification>

<success_criteria>
- [ ] Se verificó si el evento Purchase dispara en Hotmart (resultado documentado)
- [ ] STATE.md actualizado con resultado ✅ o ❌
- [ ] Si ❌: decisión tomada sobre OUTCOME_SALES vs OUTCOME_TRAFFIC para Phase 2
</success_criteria>

<must_haves>
- Resultado documentado: saber si Purchase funciona (permite Phase 2 tomar la decisión correcta de objetivo de campaña)
- Si el pixel falla, el proyecto no se bloquea — Phase 2 puede avanzar con fallback OUTCOME_TRAFFIC
</must_haves>

<decision_gate>
El resultado de TRACK-02 determina la estrategia de Phase 2:
- Purchase ✅ → Phase 2 usa OUTCOME_SALES (óptimo, Meta optimiza para compradores reales)
- Purchase ❌ → Phase 2 usa OUTCOME_TRAFFIC con URL destino Hotmart directo (subóptimo pero funciona)
Esta decisión debe estar documentada en STATE.md antes de ejecutar Phase 2.
</decision_gate>
