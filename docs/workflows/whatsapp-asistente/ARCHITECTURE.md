# 🏗️ Arquitectura - Atankalama Asistente Virtual WhatsApp

Documentación técnica detallada de la arquitectura, nodos y flujo de datos del workflow.

**Workflow ID**: `s9A9Al67_R0wSQWf_HY3X`
**Total de Nodos**: 40
**Última auditoría**: 13 de Marzo 2026 — v1.5.1

---

## 📊 Diagrama de Arquitectura Real (verificado 2026-03-06)

```
CLIENTE (WhatsApp)
       │
       ▼
  CHATWOOT ──webhook──▶ Webhook (n8n)
                              │
                         If (filtro)
                    message_created + Contact + incoming
                              │ TRUE
                    CHATWOOT Obtener Etiqueta
                              │
                         Filter
                    (excluye etiqueta "humano")
                              │
                        Edit Fields
                    (extrae: telefono, mensaje, tipo, ID Chatwoot)
                              │
                          Switch
                    ┌─────────┴──────────┐
               Audio│                    │texto (fallback)
                    ▼                    ▼
           descargar audio         Redis (push)
                    │            mensaje a lista por telefono
          Transcribe (Whisper)          │
                    │             Espera 12s
             Edit Fields2               │
            (texto transcrito)    Redis6 (get lista)
                    │                   │
                    └──────┬────────────┘
                           │
                        Merge1
                           │
                        Switch4
               ┌───────────┼──────────────┐
          Ignoran      Esperar         Continuamos
          (otro           │                │
         session)    Espera 12s      Redis7 (delete)
                          │                │
                          └────────────────┘
                                 │
                           Edit Fields6
                         (join mensajes acumulados)
                                 │
                            Agente IA
                    ┌────────────┼────────────────┐
              Herramientas del agente:
              • Think (razonamiento)
              • Consultar contactos (Airtable)
              • Crear/Actualizar contacto (Airtable)
              • Base de datos (Supabase Vector Store)
              • Contactar Humano (toolWorkflow → subworkflow etiqueta chatwoot)
              • reporte preguntas sin respuesta (Google Sheets)
              • registrar_feedback_encuesta (Airtable)
              • Calculator
                                 │
                        HTTP Request
                    (envia respuesta a Chatwoot)
                                 │
                    ┌────────────┴────────────┐
               error chatwoot?          mensaje OK
                    │
              Gmail (error)
```

**Memoria del agente**: Postgres Chat Memory (historial de conversacion)
**Modelo LLM**: OpenAI Chat Model (GPT-4.1)

**Memoria del agente**: Postgres Chat Memory (historial de conversacion)
**Modelo LLM**: OpenAI Chat Model (GPT-4.1)

**Notas**:
- La línea de voz NO pasa por Redis — se procesa inmediatamente al llegar.
- Redis wait: 4 segundos (cambiado de 12s para evitar timeout de Chatwoot).
- El diagrama muestra "Espera 12s" en dos lugares — el valor real activo es 4s.

---

## 🔀 Subworkflow: Escalación a Humano

**ID**: `K3WrelHxg7k9EePiD5-2S`
**Nombre**: subworkflow etiqueta chatwoot
**Activado por**: Nodo "Contactar Humano" (toolWorkflow) del flujo principal

### Flujo interno

```
Agente IA llama "Contactar Humano"
       │
       ▼
When Executed by Another Workflow
  inputs: Resumen conversacion, Id Conversacion Chatwoot
       │
       ▼
Edit Fields (mapea los inputs)
       │
       ▼
Slack — Send a message (Block Kit)
  • Header: 🚨 Solicitud de atención humana — WhatsApp
  • Sección: Resumen de la conversación
  • Botón verde: 💬 Abrir chat en Chatwoot → link directo
       │
       ▼
Etiqueta Humano (HTTP Request → Chatwoot API)
  POST /api//accounts/150504/conversations/{Id}/custom_atributes
  body: { "custom_atributes": { "humano": "Activado" } }
```

### Inputs esperados

| Campo | Tipo | Origen |
|---|---|---|
| `Resumen conversacion` | string | Generado por el modelo (`$fromAI()`) |
| `Id Conversacion Chatwoot` | string | `$('Webhook').item.json.body.data.chatwootConversationId` |

### Pendiente
- `channelId` del nodo Slack debe configurarse con el canal de recepción de la empresa

---

## 🧩 Detalle de Nodos (arquitectura real)

> Los nodos están organizados por sección del flujo. Los IDs de nodo son los internos de n8n.

### Entrada y filtros

| Nodo | Tipo | Función |
|------|------|---------|
| Webhook | `Webhook` | Recibe eventos de Chatwoot (POST) |
| If | `IF` | Filtra: solo `message_created` + `Contact` + `incoming` |
| CHATWOOT Obtener Etiqueta | `HTTP Request` | Consulta etiquetas de la conversación |
| Filter | `Filter` | Excluye conversaciones con etiqueta "humano" |
| Edit Fields | `Set` | Extrae: teléfono, mensaje, tipo de contenido, ID Chatwoot |

### Línea de texto (Redis)

| Nodo | Tipo | Función |
|------|------|---------|
| Redis (push) | `Redis` | Acumula mensaje en lista por teléfono |
| Espera 4s | `Wait` | Espera que lleguen mensajes adicionales |
| Redis6 (get lista) | `Redis` | Recupera todos los mensajes acumulados |
| Switch4 | `Switch` | Decide: Ignoran (otra sesión) / Esperar / Continuamos |
| Espera 4s (2) | `Wait` | Segunda espera si Switch4 = Esperar |
| Redis7 (delete) | `Redis` | Limpia la lista tras leer |
| Edit Fields6 | `Set` | Junta mensajes acumulados en campo `mensaje` |

### Línea de voz (directa, sin Redis)

| Nodo | Tipo | Función |
|------|------|---------|
| descargar audio | `HTTP Request` | Descarga el archivo de audio desde Chatwoot |
| Transcribe (Whisper) | `OpenAI` | Transcribe audio a texto (`whisper-1`) |
| Edit Fields2 | `Set` | Mapea el texto transcrito al campo `mensaje` |

### Merge y Agente IA

| Nodo | Tipo | Función |
|------|------|---------|
| Merge1 | `Merge` | Une salidas de línea de texto y de voz |
| Agente IA | `AI Agent` | Genera respuesta. Modelo: GPT-4.1. nodeId: `b6432bcb-...` |
| Postgres Chat Memory | `PostgreSQL` | Historial de conversación (sub-nodo del Agente) |
| OpenAI Chat Model | `OpenAI Chat Model` | Motor LLM del agente (GPT-4.1) |

### Herramientas del Agente IA

| Herramienta | Tipo | Función |
|-------------|------|---------|
| Think | `toolThink` | Razonamiento interno (nunca visible para el cliente) |
| Consultar contactos | `Airtable` | Busca el contacto en el CRM |
| Crear/Actualizar contacto | `Airtable` | Crea o actualiza el contacto |
| Base de datos | `Supabase Vector Store` | Búsqueda semántica en conocimiento del hotel |
| Contactar Humano | `toolWorkflow` | Llama subworkflow de escalación (Slack + Chatwoot) |
| reporte preguntas sin respuesta | `Google Sheets` | Registra preguntas que el agente no pudo responder |
| registrar_feedback_encuesta | `Airtable` | Guarda el feedback de cierre de conversación |
| Calculator | `toolCalculator` | Cálculos matemáticos básicos |

### Salida

| Nodo | Tipo | Función |
|------|------|---------|
| HTTP Request | `HTTP Request` | Envía respuesta al cliente vía Chatwoot API |
| (IF error) | `IF` | Detecta si el envío falló |
| Gmail (error) | `Gmail` | Notifica error de Chatwoot al equipo técnico |

---

## 🔄 Flujo de Datos

### Caso 1: Mensaje de texto

```
Webhook → If → CHATWOOT Obtener Etiqueta → Filter
→ Edit Fields → Switch (texto) → Redis push → Espera 4s
→ Redis get → Switch4 → Continuamos → Redis delete
→ Edit Fields6 → Merge1 → Agente IA → HTTP Request → FIN
```

**Tiempo típico**: 5-15 segundos

### Caso 2: Mensaje de voz

```
Webhook → If → CHATWOOT Obtener Etiqueta → Filter
→ Edit Fields → Switch (audio) → descargar audio
→ Transcribe (Whisper) → Edit Fields2 → Merge1
→ Agente IA → HTTP Request → FIN
```

**Tiempo típico**: 8-20 segundos

### Caso 3: Escalación humana

```
[Agente decide escalar]
→ Contactar Humano (toolWorkflow)
→ Subworkflow K3WrelHxg7k9EePiD5-2S
  → Slack (Block Kit con resumen + botón Chatwoot)
  → Chatwoot (custom_attribute: humano = "Activado")
→ Agente responde confirmación fija (sin preguntas adicionales)
→ HTTP Request → FIN
```

---

## 🗄️ Bases de Datos

### PostgreSQL (Chat Memory)
- Gestionado por el sub-nodo `Postgres Chat Memory` del Agente IA
- Clave de sesión: `conversation_id` de Chatwoot
- Cada conversación nueva tiene `conversation_id` distinto → memoria limpia

### Supabase (Vector Store)
- Tabla: `knowledge_base` (información del hotel)
- Credencial: `bFOo7cpHxcNnPXVD` ("Supabase account")
- Proyecto: "Atankalama Corp" (ref: `zxzimlqrjnmigblulthg`)

### Airtable (CRM)
- Herramientas: Consultar / Crear / Actualizar contacto, registrar_feedback_encuesta
- Campos clave: Nombre, Teléfono, Estado, Notas

---

## 🔌 Credenciales utilizadas

Todas las credenciales están en **n8n → Settings → Credentials**, nunca en el código.

| Servicio | Uso |
|---------|-----|
| OpenAI | GPT-4.1 (Agente) + Whisper (transcripción) |
| Chatwoot | Obtener etiquetas + enviar mensajes |
| Airtable | CRM (3 herramientas) |
| Supabase | Vector Store (base de conocimiento) |
| PostgreSQL | Chat Memory (historial de conversación) |
| Google Sheets | Reporte de preguntas sin respuesta |
| Slack | Escalación humana (subworkflow) |
| Gmail | Notificación de errores de Chatwoot |

---

## 📚 Referencias

- **[README.md](README.md)**: Documentación general
- **[PROMPT.md](PROMPT.md)**: Análisis del prompt
- **[MESSAGE_PROCESSING.md](MESSAGE_PROCESSING.md)**: Procesamiento de mensajes
- **[CHANGELOG.md](CHANGELOG.md)**: Historial de versiones

---

**Última actualización**: 15 de Marzo 2026
**Versión de arquitectura**: v1.5.1
**Documentado por**: Claude AI + NicoCalama
