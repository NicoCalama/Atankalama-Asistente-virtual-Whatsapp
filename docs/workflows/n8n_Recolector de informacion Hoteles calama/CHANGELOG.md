# Changelog — Recolector de Información Hotelera Calama

---

## [v1.3.0] — 2026-03-18

### Workflow C — Agente inteligente: ventana de tiempo y escalabilidad

**Problema**: El tool `consultar_precios_puntuales` no filtraba por `scrape_date` ni por fecha futura. Haiku recibía registros sold_out (17-18 marzo) antes que los datos válidos (19+) y concluía que no había información. A futuro, con meses de datos acumulados, la query devolvería cientos de registros obsoletos mezclados con los actuales.

**Cambio 1 — `consultar_precios_puntuales` (nodo `wc-04-tool-precios`)**:
- Doble query a Supabase:
  1. Obtiene el `scrape_date` más reciente para el hotel
  2. Filtra por ese scrape + `check_in_date >= hoy`
- Resultado: siempre máximo ~21 registros relevantes (7 días × 3 tipos) sin importar cuántos datos históricos haya en la base
- Escalabilidad garantizada — sin degradación con el tiempo

**Cambio 2 — System message `wc-02-agent`**:
- Agregada sección ESTRATEGIA DE CONSULTA con instrucciones explícitas:
  - Pregunta general → analizar TODOS los registros, no detenerse en sold_out
  - Pregunta específica → filtrar mentalmente al dato solicitado
  - Pregunta de tendencia → usar `consultar_tendencia_historica`
- Reforzada regla: nunca concluir "no hay datos" si hay registros con `availability='sold_out'`

**Investigación**: Ventana táctica en hotelería = 3-7 días (revenue managers). Scraping captura 7 días hacia adelante — ventana correcta para consultas rápidas del equipo de ventas vía Slack.

---

## [v1.2.0] — 2026-03-16

### Workflow C — Agente Market Intel (Slack DM) — ACTIVO

**Bot dedicado "Atankalama Mercado"** — lanza el ciclo completo: Slack DM → IA → respuesta con datos reales.

- **ID n8n**: `TlckRzWvHtO7QPGJ` | 9 nodos | Activo en producción
- **Flujo**: `Slack Trigger` → `IF anti-loop` → `AI Agent (claude-sonnet-4-6)` → `Slack — Responder DM`
- **3 herramientas** (`toolCode` v1.3 con `$fromAI()`):
  - `consultar_precios_puntuales` — precios actuales por hotel
  - `consultar_tendencia_historica` — historial por hotel desde una fecha
  - `get_ultimo_snapshot` — último análisis estratégico semanal

**Bug crítico resuelto — toolHttpRequest incompatible con n8n v2.3.6**:
El nodo `toolHttpRequest` v1.1 lanza "Invalid URL" para cualquier URL en esta versión de n8n (bug del motor). Solución: reemplazar por `toolCode` con `$helpers.httpRequest()` y Supabase anon key embebida en código.

**Slack App "Atankalama Mercado"**:
- Scopes: `chat:write`, `im:history`, `im:read`, `im:write`
- Events: `message.im` (DMs) + `message.channels` (canales)
- Credencial n8n: `uKOVvZfS46uT8weR`

**Schedule Workflow A**: cambiado a **jueves 6:00 AM** (era lunes — conflicto con reunión semanal de ventas).

**Sticky notes**: rediseñadas en todos los workflows (A, B, C, Subworkflow) para lenguaje no técnico.

---

## [v1.1.0] — 2026-03-15

### Lanzamiento a producción

- **Primer reporte enviado exitosamente** ✅ — análisis Claude + HTML + email al equipo
- **Workflow A modo producción**: quitado `.filter()` en `Code — Build Apify input` → scraping 7 hoteles activos (147 iteraciones/semana: 7 hoteles × 7 días × 3 tipos)
- **Email destinatarios**: eliminado email de test (`zurk.calameno@gmail.com`), sujeto sin prefijo `[TEST]`
- **Fixes de schema** `price_snapshots`: agregadas columnas `analysis`, `html`, `scrape_date`; `snapshot_date DEFAULT CURRENT_DATE`; `summary_json` nullable
- **Guards Supabase**: `alwaysOutputData: true` en los 3 nodos Supabase del subworkflow — evita corte de cadena con 0 rows
- **SplitInBatches subworkflow**: corregida conexión `loop` → Gmail (estaba invertida a `done`)

---

## [v1.0.0] — 2026-03-15

### Subworkflow: arquitectura guards + Code completo

**Problema**: cadena serial se cortaba silenciosamente si `Supabase — Semana anterior` devolvía 0 items (primera ejecución sin historial). Además, si `Supabase — Semana actual` devolvía N items, todos los nodos downstream corrían N veces.

**Solución — Guards**: 3 nodos Code entre cada par de Supabase. Cada guard reduce a 1 item OR `{_guard:true}` si vacío:
```javascript
const items = $input.all();
return items.length > 0 ? [items[0]] : [{ json: { _guard: true } }];
```
Cadena final: `Trigger → Supa actual → Guard1 → Supa anterior → Guard2 → Supa 4sem → Guard3 → Code contexto → Claude → ...`

**Code — Preparar contexto IA implementado**:
- Lee 3 datasets por nombre: `$('Supabase — Semana actual').all()`, etc.
- Filtra registros `_guard` y `no_data`
- Agrupa por `hotel_slug + room_type` → precio promedio, días agotado
- Calcula deltas vs semana anterior y promedio de mercado (solo competidores)
- Devuelve 1 item con `structured_context` + `system_prompt` + `user_message`

**Code — Renderizar HTML implementado**: template Gmail completo (inline CSS, 620px), secciones: resumen ejecutivo, alertas (rojo), oportunidades (verde), posicionamiento, cambios vs semana anterior, recomendaciones.

Resultado: 21 nodos (18 → 21), credenciales todas vinculadas ✅.

---

## [v0.9.0] — 2026-03-15

### Fix: INSERT via HTTP Request (reemplaza nodo Supabase v1)

El nodo Supabase v1 nativo pierde la config `fieldsUi` al abrir/guardar en UI → campos quedan null → violación NOT NULL. Tras 5 intentos con variantes del nodo nativo, migrado a HTTP Request directo:

```
URL: https://zxzimlqrjnmigblulthg.supabase.co/rest/v1/hotel_prices
Auth: supabaseApi (bFOo7cpHxcNnPXVD)
Body: jsonBody: "={{ $json }}"   ← NO JSON.stringify, causa PGRST204
Header: Prefer: return=minimal
```

**Notas críticas**:
- `raw_data` debe ser objeto JS (no string) — PostgREST inserta como JSONB nativo
- `Postgres account` ≠ Supabase del proyecto — apunta al PostgreSQL del chat memory de WhatsApp

---

## [v0.8.0] — 2026-03-15

### Fix: SplitInBatches v3 output invertido + loop-back + no_data fallback

**SplitInBatches typeVersion 3 — outputs invertidos vs v1/v2**:
- `output[0]` = **done** (fin del loop)
- `output[1]` = **loop** (batch actual)
- Fix: invertir conexiones. Si cambias versión del nodo, re-verificar.

**Loop-back**: `Code — Transformar schema` ahora retorna registro `no_data` cuando Apify no matchea ningún hotel (en vez de `[]`). Array vacío deja el loop colgado indefinidamente.

**Estado tras v0.8.0**: Loop ejecuta correctamente, Apify confirma ejecuciones en dashboard, Supabase inserta precios.

---

## [v0.7.0] — 2026-03-14

### Workflow B: Webhook → Form Trigger

Webhooks en n8n (EasyPanel) no se registran en el router interno aunque la API diga "activo". Reemplazado por `formTrigger` v2.2 — n8n hostea su propia página, sin dependencia externa. Acceso vía bookmark en Slack #ventas-atankalama.

---

## [v0.1.0 – v0.6.0] — 2026-03-13/14

Construcción inicial:
- `v0.2.0`: Tablas Supabase creadas, 7 hoteles seed insertados
- `v0.3.0`: Subworkflow creado (ID `YXDogRLJrprijeiV`, 12 nodos)
- `v0.4.0`: Subworkflow reconstruido con nodos nativos (Supabase, LangChain+Anthropic) — 18 nodos + 5 sticky notes
- `v0.5.0`: Workflow A creado (ID `QBDVpsKWGTHZZikE`, nodo Apify nativo)
- `v0.6.0`: Workflow B creado (ID `SbI0cfUlQvtLWvxZ`) con slash command Slack
