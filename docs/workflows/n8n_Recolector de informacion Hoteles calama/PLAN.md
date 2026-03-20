# Plan de Implementación — Recolector de Información Hotelera Calama

**Fecha de aprobación**: 13 de Marzo 2026
**Versión**: 1.0.0

---

## Contexto y objetivo

Hotel Atankalama necesita monitorear semanalmente los precios de la competencia en Booking.com para que el equipo de ventas tome decisiones informadas de pricing. Este workflow:

1. Scrapea ~8 hoteles en Calama via Apify cada lunes
2. Almacena el historial en Supabase (PostgreSQL)
3. Genera un análisis estratégico con Claude AI
4. Envía un reporte HTML profesional por email al equipo de ventas

---

## Diagrama de flujo

```
┌─────────────────────────────────────────────────────────┐
│  WORKFLOW A — "Recolector de Precios Hoteleros"          │
│  Schedule: Lunes 6:00 AM (UTC-4 / Santiago)              │
│                                                          │
│  [Schedule Trigger]                                      │
│       ↓                                                  │
│  [Set Configuración] ← today, date_window, room_types    │
│       ↓                                                  │
│  [HTTP GET Supabase] ← hotels_catalog activos            │
│       ↓                                                  │
│  [SplitInBatches] ← Loop por hotel                       │
│       ↓                                                  │
│  [Code JS] ← Construir URL Booking.com                   │
│       ↓                                                  │
│  [HTTP POST Apify] ← Iniciar actor scraper               │
│       ↓                                                  │
│  [Wait 90s] ← Esperar resultado                          │
│       ↓                                                  │
│  [HTTP GET Apify] ← Obtener dataset items                │
│       ↓                                                  │
│  [IF] ← ¿Hay resultados?                                 │
│       ↓ SÍ                                               │
│  [Code JS] ← Transformar a schema hotel_prices           │
│       ↓                                                  │
│  [HTTP POST Supabase] ← Insert hotel_prices              │
│       ↓ (fin loop)                                       │
│  [Merge] ← Recolectar todos los resultados               │
│       ↓                                                  │
│  [Code JS] ← Resumen: success/error counts               │
│       ↓                                                  │
│  [IF] ← ¿Errores críticos (>50%)?                        │
│    ↓ SÍ              ↓ NO                                │
│  [Gmail Alert]   [Execute Workflow] → SUBWORKFLOW        │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│  SUBWORKFLOW — "Análisis y Reporte de Precios"           │
│  (reutilizable por Workflow B y futuro Slack v2)         │
│                                                          │
│  [Execute Workflow Trigger] ← { scrape_date, mode }      │
│       ↓                                                  │
│  [HTTP GET Supabase] ← Precios semana actual             │
│  [HTTP GET Supabase] ← Precios semana anterior           │
│  [HTTP GET Supabase] ← Últimas 4 semanas                 │
│       ↓                                                  │
│  [Code JS] ← Preparar contexto: deltas, avg, sold-out    │
│       ↓                                                  │
│  [HTTP POST Claude API] ← Análisis estratégico           │
│       ↓                                                  │
│  [Code JS] ← Parsear respuesta JSON de Claude            │
│       ↓                                                  │
│  [Code JS] ← Renderizar HTML del email                   │
│       ↓                                                  │
│  [HTTP POST Supabase] ← Guardar en price_snapshots       │
│       ↓                                                  │
│  [Set] ← Lista de emails destinatarios                   │
│       ↓                                                  │
│  [SplitInBatches] ← Loop por email                       │
│       ↓                                                  │
│  [Gmail] ← Enviar reporte semanal                        │
│       ↓ (fin loop)                                       │
│  [Respond to Webhook] ← Preparado para Slack v2          │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│  WORKFLOW B — "Disparador Standalone"  (3 nodos)         │
│  Para re-ejecuciones manuales sin re-scrapear            │
│                                                          │
│  [Manual Trigger]                                        │
│       ↓                                                  │
│  [Set] ← scrape_date = hoy, mode = "standalone"          │
│       ↓                                                  │
│  [Execute Workflow] → SUBWORKFLOW                        │
└─────────────────────────────────────────────────────────┘
```

---

## Competidores a monitorear

| Hotel | Tipo | Precio aprox | Notas |
|-------|------|--------------|-------|
| Hotel Atankalama | Propio | — | Referencia principal |
| Hotel Atankalama Inn | Propio | — | Referencia secundaria |
| Geotel Calama | Competidor | ~$57 USD/noche | 3.5★, céntrico |
| Park Hotel Calama | Competidor | ~$45 USD/noche | 3.5★, business |
| Ibis Calama | Competidor | ~$65 USD/noche | 3★, cadena |
| ibis budget Calama | Competidor | ~$45-65 USD/noche | 3★, económico |
| Hotel Agua del Desierto | Competidor | ~$54-66 USD/noche | 3★, spa |
| *(a confirmar)* | Competidor | — | El usuario define al construir |

---

## Presupuesto mensual

| Servicio | Plan | Costo/mes | Cálculo |
|---------|------|-----------|---------|
| Apify | Starter | ~$38 USD | $29 base + 24 CU overage × $0.40 |
| Claude API | claude-sonnet-4-6 | ~$0.50 USD | 4 llamadas × 3k in + 1.5k out |
| Supabase | Existente | $0-25 USD | Tablas nuevas en proyecto existente |
| Gmail / n8n | Existente | $0 | Ya configurados |
| **TOTAL** | | **~$38-65 USD/mes** | |

> Estimación de uso Apify: 8 hoteles × 1 run/semana × ~3 CU = 24 CU/semana = 96 CU/mes.
> Plan Starter incluye ~72 CU → overage de ~24 CU × $0.40 = ~$9.60 adicionales.

---

## Fases de implementación

### Fase 0 — Preparación Supabase ✅ COMPLETADA (2026-03-13)
**Objetivo**: Crear las 3 tablas nuevas e insertar el catálogo de hoteles.

**Resultado**: Tablas creadas via Management API (PAT token). Seed de 7 hoteles insertado.
Proyecto Supabase renombrado a "Atankalama Corp" (ref: `zxzimlqrjnmigblulthg`).

---

### Fase 1 — Subworkflow "Análisis y Reporte" ✅ LISTO PARA TEST (2026-03-15)
**Objetivo**: Pipeline que genera y envía el reporte email semanal.

**ID n8n**: `YXDogRLJrprijeiV`, activo. **21 nodos**.

**Arquitectura v2.0 — serial con Guards**:
```
[Trigger] → [Supabase actual] → [Guard1]
          → [Supabase anterior] → [Guard2]
          → [Supabase 4sem] → [Guard3]
          → [Code contexto IA]
          → [Claude LLM Chain ← Anthropic]
          → [Code parsear] → [Code HTML] → [Supabase snapshot]
          → [Code destinatarios] → [SplitInBatches] → [Gmail]
```

**Guards** (3 nuevos nodos Code): evitan que la cadena se corte cuando no hay historial previo (primera ejecución). Cada guard pasa 1 item máximo para que cada Supabase corra solo una vez.

**Code — Preparar contexto IA** ✅: lógica real — agrupación por hotel+tipo, deltas, promedios de mercado, contexto estructurado para Claude.

**Code — Renderizar HTML** ✅: template HTML/CSS inline compatible Gmail. Secciones: resumen ejecutivo, alertas, oportunidades, posicionamiento, cambios vs semana anterior, recomendaciones, footer.

**Pendiente**:
- ⚠️ Verificar que `price_snapshots` acepta los campos `html`, `analysis`, `scrape_date` (autoMapInputData puede fallar si columnas difieren → ajustar nodo si es necesario)

**Criterio de éxito**: Email recibido con análisis de Claude coherente + diseño HTML legible.

---

### Fase 2 — Workflow A "Scraping Semanal" ✅ ACTIVO EN PRODUCCIÓN (2026-03-15)
**Objetivo**: Scraping automático de competidores (7 días) y guardado en Supabase.

**ID n8n**: `QBDVpsKWGTHZZikE`, inactivo.

#### Historia de versiones

**v1 (descartada)**: Loop por hotel usando nodo comunitario `@apify/n8n-nodes-apify` — inestable, requería instalación adicional.

**v2 (probada, con bugs)**: HTTP Request nativo a la API REST de Apify. Una sola llamada con todos los hoteles como `startUrls`. Apify devolvió 6 hoteles correctamente, pero falló en Supabase insert.

```
[Triggers] → [Set config] → [Supabase hoteles activos]
→ [Code — Preparar input Apify] → [HTTP POST — Run actor (waitForFinish=300)]
→ [HTTP GET — Dataset items] → [Code — Transformar schema]
→ [Supabase — Insertar precios]
→ [Code — Preparar subworkflow] → [Execute subworkflow YXDogRLJrprijeiV]
```

**Bugs identificados en v2**:
- `rating` enviado a Supabase pero columna no existe en `hotel_prices` → error insert
- `price_clp` enviado como float → schema espera INTEGER → fix: `Math.round()`

**v3 — APROBADA, PENDIENTE IMPLEMENTAR**:
Loop de 7 días (D+1 a D+7), todos los hoteles activos (propios + competidores).

```
[Triggers] → [Set config] → [Supabase todos los hoteles activos (active=true)]
→ [Code — Generar 7 fechas]
→ [SplitInBatches — Loop por fecha (size=1)]
    ↓ por iteración (1 fecha)
    [Code — Build Apify input] → [HTTP POST — Run actor]
    → [HTTP GET — Dataset items]
    → [Code — Transformar schema] → [Supabase — Insertar precios]
    → (vuelve al loop)
    ↓ done
[Code — Preparar subworkflow] → [Execute subworkflow YXDogRLJrprijeiV]
```

**Cambios clave en v3 vs v2**:
1. Supabase: filtro solo `active=eq.true` — incluye 2 hoteles propios + 6 competidores = 8 hoteles
2. `Code — Generar 7 fechas` (nuevo): output 7 items `{ date_index, check_in_date, check_out_date }`
3. `SplitInBatches size=1` (nuevo): itera sobre las 7 fechas
4. `Code — Build Apify input` (renombrado): lee hotels de nodo Supabase + fecha del item del loop
5. `Code — Transformar schema` (fix): sin campo `rating`, `price_clp: Math.round(d.price)`
6. `check_in_date`: viene del item del loop, no de config global

**Razón para incluir hoteles propios**: el campo `is_own_hotel` permite al subworkflow (Claude) calcular deltas directos entre precios propios y competencia sin necesidad de cruzar fuentes externas.

**Actor Apify**: `voyager~booking-scraper` vía REST API
- `POST /v2/acts/voyager~booking-scraper/runs?waitForFinish=300`
- `GET /v2/datasets/{defaultDatasetId}/items`
- Devuelve 1 resultado por hotel (precio mínimo), no desglosa por tipo de habitación
- Por ahora: `adults=2, room_type='doble'` (precio mínimo disponible)

**Pendiente de usuario — próxima iteración v4**:
- JSON con parámetros de habitaciones (adults=2/3/4 → doble/triple/cuádruple)
- Cuando llegue ese JSON: hacer 3 llamadas Apify por día (adults 2, 3, 4) → 21 runs/semana

**Criterio de éxito v3**: ~56 filas en `hotel_prices` (8 hoteles × 7 días) + `price_clp` INTEGER + sin campo `rating` + campo `is_own_hotel` correcto por hotel + subworkflow ejecutado.

---

### Fase 3 — Workflow B "Disparador Manual" ✅ ACTIVO (2026-03-15)
**Objetivo**: Permitir re-ejecuciones manuales del análisis sin re-scrapear (y disparar scraping manual vía Slack).

**ID n8n**: `SbI0cfUlQvtLWvxZ`, activo.

**Arquitectura actual**:
```
[Form Trigger]                                          ↘
                                                         → [Set scrape_date] → [Execute Workflow A]
[Webhook Trigger /webhook/disparador-reporte]           ↗
```

**Slack Button** (workflow util separado, `yyaxraPUve3ApgGl` — "[UTIL] Postear Botón Slack"):
- Manual Trigger → HTTP Request → `https://slack.com/api/chat.postMessage`
- Canal: `#ventas-atankalama` (`C0ALJN1RYCD`)
- Block Kit: botón link a `[URL protegida — webhook Workflow B]`
- Ejecutar solo cuando se quiera postear el botón (no automático)

**Criterio de éxito**: Click en botón Slack → webhook activa Workflow A → 42 filas en Supabase → email recibido.

---

### Fase 4.5 — Workflow C "Agente Precios — Slack DM" ⏳ PENDIENTE (planificado 2026-03-14)
**Objetivo**: Agente de AI con el que el equipo puede conversar por Slack DM para consultar precios, tendencias y recomendaciones — sin depender del reporte del lunes.

**Por qué separado del subworkflow**: el subworkflow es un pipeline determinístico y estable (email crítico). El agente es interactivo y probabilístico — mezclarlos añade fragilidad innecesaria.

**Pasos**:
1. Crear workflow nuevo "Agente Precios — Slack DM" en n8n
2. Agregar `Slack Trigger` con `watchWorkspace: true`
3. Agregar `IF` anti-loop (filtra mensajes del propio bot)
4. Agregar nodo `AI Agent` con:
   - Chat Model: `Anthropic Chat Model Tool` (claude-sonnet-4-6)
   - Tool 1: `consultar_precios_puntuales` (Code Tool → `hotel_prices`)
   - Tool 2: `consultar_tendencia_historica` (Code Tool → `hotel_prices`, N semanas)
   - Tool 3: `get_ultimo_snapshot` (HTTP Tool → `price_snapshots` ORDER BY date DESC LIMIT 1)
5. Agregar nodo `Slack` → responder en DM al usuario
6. Agregar 5 sticky notes con documentación inline
7. Activar workflow → copiar webhook URL del Slack Trigger
8. Configurar Slack App (Event Subscriptions + scope `message.im`)

**Prerrequisitos**:
- Credencial `anthropicApi` creada en n8n (comparte con subworkflow)
- Escopos Slack App: `im:history`, `im:read`, `im:write`, `chat:write`, `message.im`

**Criterio de éxito**:
- DM "¿Cuánto cobra Ibis esta semana para doble?" → respuesta con precio en ≤15 segundos
- DM "¿Qué recomiendas esta semana?" → agente llama Tool 3 y resume recomendaciones
- Bot NO responde a sus propios mensajes (anti-loop activo)

---

### Fase 4 — Activación y monitoreo
**Objetivo**: Activar el schedule y validar en producción.

**Pasos**:
1. Activar Workflow A (schedule lunes 6:00 AM)
2. Esperar primera ejecución real el próximo lunes
3. Verificar datos en Supabase (`hotel_prices`)
4. Verificar email recibido por el equipo de ventas
5. Monitorear 2-3 lunes antes de declarar estable

---

## Notas de configuración de credenciales en n8n

Antes de construir, agregar estas credenciales en n8n UI:

| Credencial | Tipo en n8n | Campo clave |
|-----------|------------|------------|
| Supabase | HTTP Header Auth | `apikey: [SERVICE_ROLE_KEY]` |
| Apify | HTTP Header Auth | `Authorization: Bearer [APIFY_API_TOKEN]` |
| Claude API | HTTP Header Auth | `x-api-key: [ANTHROPIC_API_KEY]` |
| Gmail | OAuth2 | Ya configurado (reutilizar del WhatsApp bot) |

---

## Checklist pre-commit

Antes de hacer `git commit` con cualquier archivo de este proyecto:
- [ ] Sin URLs reales de Booking.com en ningún archivo
- [ ] Sin API keys de Apify, Anthropic, Supabase en ningún archivo
- [ ] Sin emails reales del equipo de ventas
- [ ] JSONs de workflow exportados SIN credenciales incrustadas
- [ ] Variables sensibles marcadas como `[URL protegida]` o `[CONFIGURAR EN N8N]`

---

## Roadmap futuro (v2)

- **Slack integration**: Añadir Webhook Trigger al subworkflow + slash command `/precios`
- **Dashboard**: Visualizar tendencias con Metabase o similar conectado a Supabase
- **Alertas inmediatas**: Notificar vía Slack si un competidor baja precio >15% (sin esperar al lunes)
- **Más fuentes**: Agregar Expedia o Google Hotels para validar precios de Booking
