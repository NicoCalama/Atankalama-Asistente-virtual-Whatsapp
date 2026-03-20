# Arquitectura Técnica — Recolector de Información Hotelera Calama

---

## Componentes

| Componente | ID n8n | Estado | Descripción |
|-----------|--------|--------|-------------|
| Workflow A | `QBDVpsKWGTHZZikE` | Activo producción (7 hoteles) | Scraping semanal — Loop 147 iteraciones (7 hoteles × 7 días × 3 tipos) |
| Subworkflow | `YXDogRLJrprijeiV` | Activo producción | Análisis IA + email (21 nodos) |
| Workflow B | `SbI0cfUlQvtLWvxZ` | Activo | Disparador manual (Form Trigger + Execute Workflow A) |
| Workflow C | `TlckRzWvHtO7QPGJ` | Activo producción | Agente consultor vía Slack DM (bot "Atankalama Mercado") |

---

## Workflow A — Scraping Semanal

```
Cron: 0 10 * * 4  (Jueves 10:00 UTC = 6:00 AM Santiago)
PRODUCCIÓN: 7 hoteles activos, 147 iteraciones/semana
```

### Flujo

```
[Schedule / Execute Trigger] → [Set config (scrape_date)]
→ [Supabase — Hoteles activos (active=eq.true)]
→ [Code — Generar 21 configs]   ← 7 días × 3 tipos (doble/triple/cuádruple)
→ [SplitInBatches — Loop v2]    ← batchSize=1, typeVersion 3
  → output[1] (loop): [Code — Build Apify input]
    → [HTTP — Lanzar Actor Apify]   ← POST waitForFinish=300
    → [HTTP — Obtener Items Dataset]
    → [Code — Transformar schema]   ← Math.round(price_clp), sin campo rating, fallback no_data
    → [HTTP — Insertar en Supabase] ← REST API, Prefer: return=minimal
    → (loop-back a SplitInBatches)
  → output[0] (done): [Code — Preparar subworkflow]
    → [Execute — Subworkflow análisis (YXDogRLJrprijeiV)]
```

### Notas críticas

**SplitInBatches v3 — output INVERTIDO**:
- `output[0]` = **done** (post-loop, cuando terminaron todos los batches)
- `output[1]` = **loop** (batch actual, cuerpo del loop)
- Al revés de v1/v2. Si cambias versión del nodo, verificar conexiones.

**Supabase insert — usar HTTP Request, no nodo nativo v1**:
El nodo Supabase v1 nativo pierde la config `fieldsUi` al abrir/guardar en UI. Se reemplazó por HTTP POST directo:
```
URL: https://zxzimlqrjnmigblulthg.supabase.co/rest/v1/hotel_prices
Auth: supabaseApi (bFOo7cpHxcNnPXVD)
Body: "={{ $json }}"  ← NO usar JSON.stringify($json), causa error PGRST204
Header: Prefer: return=minimal
```

**raw_data debe ser objeto JS** (no string) — PostgREST lo inserta como JSONB nativo.

---

## Subworkflow — Análisis y Reporte (21 nodos, v2.0)

```
Input: { scrape_date: "YYYY-MM-DD", mode: "scheduled" | "standalone" }
```

### Flujo con guards

```
[Trigger] → [Supabase actual]
→ [Guard1]  ← reduce a 1 item (evita N ejecuciones en downstream)
→ [Supabase anterior]
→ [Guard2]  ← pasa 1 item OR {_guard:true} si vacío (primera ejecución)
→ [Supabase 4sem]
→ [Guard3]  ← ídem
→ [Code — Preparar contexto IA]  ← lee los 3 Supabase por $('nodo').all()
→ [Claude — Análisis Estratégico]  ← Basic LLM Chain + Anthropic sub-nodo
→ [Code — Parsear respuesta Claude]  ← $json.text del chain
→ [Code — Renderizar HTML]  ← template Gmail inline CSS
→ [Supabase — Guardar snapshot]  ← autoMapInputData → price_snapshots
→ [Code — Lista de destinatarios]  ← retorna N items (1 por email)
→ [SplitInBatches] → [Gmail — Enviar reporte] → loop-back
```

**Por qué guards**: cada Supabase corre una vez por item recibido. Sin guards, Supabase anterior corría N veces (N = registros de semana actual). Los guards reducen a 1 item para que cada nodo corra exactamente una vez.

**Primera ejecución**: Supabase anterior y 4sem devuelven 0 items. Los guards insertan `{_guard:true}` para no cortar la cadena. Code contexto filtra `_guard` y maneja arrays vacíos con gracia.

### Credenciales vinculadas
- Anthropic: `nY5IaQTAreX8ULxT` ("Anthropic account") ✅
- Gmail: `tRN6oegZdk974irw` ("Gmail account") ✅
- Supabase: `bFOo7cpHxcNnPXVD` ("Supabase account") ✅

---

## Workflow B — Disparador Manual

```
[Form Trigger /reporte-ventas]   [Webhook /webhook/disparador-reporte]
         └──────────────────────┬────────────────────────────────────┘
                                 ↓
                    [Set scrape_date + mode]
                                 ↓
                    [Execute — Workflow A (QBDVpsKWGTHZZikE)]
```

**Por qué Form Trigger**: el n8n en EasyPanel no registra webhooks externos correctamente. El Form Trigger genera su propia página servida directamente por n8n.

**Acceso del equipo**: bookmark `📊 Ejecutar Reporte` en Slack #ventas-atankalama, apuntando a la Production URL del Form Trigger.

---

## Workflow C — Agente Slack DM

**Estado**: Activo producción | **ID**: `TlckRzWvHtO7QPGJ` | 9 nodos

### Flujo

```
[Slack Trigger]  watchWorkspace:true, trigger:message
      ↓
[IF — Anti-loop]  $json.bot_id → Is Empty
      ↓ TRUE
[AI Agent — claude-sonnet-4-6]
      |── Tool Code: consultar_precios_puntuales   → hotel_prices (hotel + fecha)
      |── Tool Code: consultar_tendencia_historica → hotel_prices (N semanas)
      └── Tool Code: get_ultimo_snapshot           → price_snapshots DESC LIMIT 1
      ↓
[Slack — Responder DM]  channel: ={{ $('Slack Trigger').first().json.channel }}
```

### Notas técnicas

**toolHttpRequest incompatible con n8n v2.3.6**: el nodo `toolHttpRequest` v1.1 lanza "Invalid URL" para cualquier URL en esta versión. Las 3 herramientas se implementaron con `toolCode` (typeVersion 1.3) usando `$helpers.httpRequest()` y la Supabase anon key embebida (es la clave pública — no es un secreto).

**$fromAI() en toolCode**: permite al agente pasar parámetros dinámicos (hotel_slug, fecha_desde) directamente al código JS.

**Slack Trigger**: captura mensajes de canales y DMs. IF anti-loop filtra mensajes del propio bot (`$json.bot_id` → Is Empty).

### Slack App "Atankalama Mercado"

**Bot Token Scopes** (OAuth & Permissions):
- `chat:write`, `im:history`, `im:read`, `im:write`

**Event Subscriptions → Bot Events**:
- `message.im` — DMs entrantes
- `message.channels` — mensajes en canales donde el bot fue invitado

### System Prompt del Agente

```
Eres un asistente de inteligencia de mercado hotelero para Hotel Atankalama.
Respondes consultas sobre precios de la competencia en Calama, Chile.
Hoteles: Atankalama, Atankalama Inn, Geotel Calama, Park Hotel Calama,
Ibis Calama, ibis budget Calama, Hotel Agua del Desierto.
Tipos: Doble, Triple, Cuádruple. Precios en CLP.
Usa tus herramientas para responder con datos reales.
Si el dato no existe, indícalo claramente. Responde siempre en español.
```

### Credenciales

- Anthropic: `nY5IaQTAreX8ULxT` ("Anthropic account") ✅
- Supabase: `bFOo7cpHxcNnPXVD` ("Supabase account") ✅
- Slack: `uKOVvZfS46uT8weR` ("Atankalama Mercado") ✅

---

## Supabase — Tablas del Recolector

**Proyecto**: "Atankalama Corp" | **ref**: `zxzimlqrjnmigblulthg`
**Credencial n8n**: `bFOo7cpHxcNnPXVD` ("Supabase account") — la misma para todos los proyectos

| Tabla | Contenido |
|-------|-----------|
| `hotels_catalog` | 7 hoteles seed (2 propios + 5 competidores), campo `active`, `is_own_hotel`, `booking_url_base` |
| `hotel_prices` | Precios scrapeados. Campos: `hotel_slug`, `room_type`, `check_in_date`, `price_clp` (INTEGER), `availability`, `scrape_date`, `is_own_hotel`, `raw_data` (JSONB) |
| `price_snapshots` | Análisis IA pre-computado por semana. Consultado por Workflow C para evitar re-invocar Claude |

---

## Stack de credenciales

| Servicio | Credencial n8n | Notas |
|---------|---------------|-------|
| Apify | `K8yYXG645t1B1ufI` | Actor: `voyager~booking-scraper` |
| Supabase | `bFOo7cpHxcNnPXVD` | REST API + nodo nativo |
| Anthropic | `nY5IaQTAreX8ULxT` | LangChain sub-nodo |
| Gmail | `tRN6oegZdk974irw` | OAuth2 [email operaciones] |
| Slack | `uKOVvZfS46uT8weR` | Bot "Atankalama Mercado" — Workflow C ✅ |

---

**Última actualización**: 16 de Marzo 2026 (Workflow C activo en producción)
