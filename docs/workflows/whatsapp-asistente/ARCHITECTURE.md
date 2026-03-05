# 🏗️ Arquitectura - Atankalama Asistente Virtual WhatsApp

Documentación técnica detallada de la arquitectura, nodos y flujo de datos del workflow.

**Workflow ID**: `s9A9Al67_R0wSQWf_HY3X`
**Total de Nodos**: 19
**Última actualización**: 5 de Marzo 2026

---

## 📊 Diagrama de Arquitectura General

```
┌─────────────────────────────────────────────────────────────────┐
│                        CLIENTE (WhatsApp)                        │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                          CHATWOOT                                │
│                    (WhatsApp Business API)                       │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ Webhook
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                           N8N WORKFLOW                           │
│                                                                   │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  1. RECEPCIÓN Y VALIDACIÓN                                │  │
│  │     • Webhook Receiver                                    │  │
│  │     • Validación de origen                                │  │
│  │     • Extracción de datos del contacto                    │  │
│  └───────────────────────────────────────────────────────────┘  │
│                             │                                    │
│                             ▼                                    │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  2. CLASIFICACIÓN DE TIPO DE MENSAJE                      │  │
│  │     • ¿Es texto o voz?                                    │  │
│  │     • Switch Node                                         │  │
│  └───────────────────────────────────────────────────────────┘  │
│              │                              │                    │
│              ▼ (texto)                      ▼ (voz)              │
│  ┌──────────────────────┐     ┌──────────────────────────────┐  │
│  │ Procesar directamente│     │  3. PROCESAMIENTO DE VOZ     │  │
│  └──────────────────────┘     │     • Descargar audio        │  │
│              │                │     • OpenAI Whisper API     │  │
│              │                │     • Transcripción          │  │
│              │                │     • Guardar en Drive       │  │
│              │                └──────────────────────────────┘  │
│              │                              │                    │
│              └──────────────┬───────────────┘                    │
│                             ▼                                    │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  4. CONSTRUCCIÓN DE CONTEXTO                              │  │
│  │     • Consulta PostgreSQL (historial)                     │  │
│  │     • Búsqueda Supabase Vector DB (RAG)                   │  │
│  │     • Clasificación tipo cliente (Turista/Empresa)        │  │
│  │     • Identificación de intención                         │  │
│  └───────────────────────────────────────────────────────────┘  │
│                             │                                    │
│                             ▼                                    │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  5. GENERACIÓN DE RESPUESTA                               │  │
│  │     • OpenAI GPT-4.1-mini                                 │  │
│  │     • Prompt + Contexto + Historial + RAG                 │  │
│  │     • Validación de respuesta                             │  │
│  └───────────────────────────────────────────────────────────┘  │
│                             │                                    │
│                             ▼                                    │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  6. RESPUESTA AL CLIENTE                                  │  │
│  │     • HTTP Request a Chatwoot API                         │  │
│  │     • Envío de mensaje                                    │  │
│  └───────────────────────────────────────────────────────────┘  │
│                             │                                    │
│                             ▼                                    │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  7. POST-PROCESAMIENTO                                    │  │
│  │     • Guardar conversación en PostgreSQL                  │  │
│  │     • Actualizar Airtable CRM                             │  │
│  │     • Detectar oportunidades comerciales                  │  │
│  └───────────────────────────────────────────────────────────┘  │
│                             │                                    │
│                             ▼                                    │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  8. NOTIFICACIONES (condicionales)                        │  │
│  │     • Telegram (equipo comercial)                         │  │
│  │     • Triggers: Reservas, Leads, Voz transcrita           │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
           │                    │                    │
           ▼                    ▼                    ▼
    ┌───────────┐      ┌──────────────┐    ┌─────────────┐
    │  Airtable │      │  PostgreSQL  │    │   Telegram  │
    │    CRM    │      │   (Memoria)  │    │(Notificación)│
    └───────────┘      └──────────────┘    └─────────────┘
```

---

## 🧩 Detalle de Nodos

### 1. Webhook Receiver (Chatwoot)
**Tipo**: `Webhook`
**Función**: Recibe eventos desde Chatwoot cuando llega un mensaje nuevo

**Configuración**:
- HTTP Method: `POST`
- Authentication: API Key validation
- Response: 200 OK

**Output**:
```json
{
  "conversation_id": "string",
  "contact": {
    "id": "number",
    "name": "string",
    "phone_number": "string"
  },
  "message": {
    "content": "string",
    "message_type": "incoming",
    "content_type": "text|audio",
    "attachments": []
  }
}
```

---

### 2. Data Extractor (Contacto)
**Tipo**: `Code Node (JavaScript)`
**Función**: Extrae y normaliza datos del contacto desde el payload de Chatwoot

**Lógica**:
```javascript
// Extrae:
- conversation_id
- contact_id
- contact_name
- phone_number (normalizado)
- message_type (text/audio)
- message_content
- timestamp
```

---

### 3. Switch: Tipo de Mensaje
**Tipo**: `Switch Node`
**Función**: Enruta el flujo según el tipo de mensaje

**Rutas**:
- **Route 1 (text)**: `message.content_type === 'text'`
- **Route 2 (audio)**: `message.content_type === 'audio'`

---

### 4. Download Audio (Voz)
**Tipo**: `HTTP Request`
**Función**: Descarga el archivo de audio desde Chatwoot

**Configuración**:
- Method: `GET`
- URL: `{{ $json.message.attachments[0].data_url }}`
- Response Format: `Binary`

---

### 5. Transcribe Audio (Whisper)
**Tipo**: `OpenAI Node`
**Función**: Transcribe audio a texto usando Whisper

**Configuración**:
- Resource: `Audio`
- Operation: `Transcribe`
- Model: `whisper-1`
- Input: Binary audio file
- Language: `es` (Español)

**Output**:
```json
{
  "text": "Transcripción del audio"
}
```

---

### 6. Save Audio to Drive
**Tipo**: `Google Drive Node`
**Función**: Guarda el audio original en Google Drive para referencia

**Configuración**:
- Resource: `File`
- Operation: `Upload`
- Folder: `/n8n/whatsapp-audios/`
- Filename: `{{ $json.conversation_id }}_{{ $now.format('YYYY-MM-DD_HHmmss') }}.ogg`

---

### 7. PostgreSQL: Get Conversation History
**Tipo**: `PostgreSQL Node`
**Función**: Recupera historial de conversación para contexto

**Query**:
```sql
SELECT
  role,
  content,
  created_at
FROM conversation_history
WHERE conversation_id = $1
ORDER BY created_at DESC
LIMIT 10
```

**Variables**:
- `$1`: `{{ $json.conversation_id }}`

**Output**: Array de mensajes previos

---

### 8. Supabase: Vector Search (RAG)
**Tipo**: `Supabase Node`
**Función**: Búsqueda semántica en base de conocimiento

**Configuración**:
- Resource: `Vector Search`
- Table: `knowledge_base`
- Match count: `5`
- Similarity threshold: `0.7`

**Proceso**:
1. Genera embedding del mensaje actual
2. Busca documentos similares
3. Retorna top 5 más relevantes

**Output**:
```json
[
  {
    "content": "Información relevante del hotel",
    "metadata": { "category": "rooms", "topic": "pricing" },
    "similarity": 0.89
  }
]
```

---

### 9. Code: Classify Client Type
**Tipo**: `Code Node (JavaScript)`
**Función**: Clasifica el tipo de cliente según patrones

**Lógica**:
```javascript
// Análisis de:
- Número de personas mencionadas
- Tipo de consulta (corporativa vs vacacional)
- Palabras clave ("empresa", "factura", "familia", "tour")
- Historial previo

// Output:
return {
  client_type: "tourist" | "business",
  confidence: 0.0-1.0
}
```

---

### 10. Code: Detect Intent
**Tipo**: `Code Node (JavaScript)`
**Función**: Identifica la intención del mensaje

**Intenciones detectadas**:
- `booking_request`: Solicitud de reserva
- `pricing_inquiry`: Consulta de precios
- `availability_check`: Consulta disponibilidad
- `general_info`: Información general
- `complaint`: Reclamo o problema
- `other`: Otro

---

### 11. Code: Build AI Prompt
**Tipo**: `Code Node (JavaScript)`
**Función**: Construye el prompt completo para GPT

**Estructura del Prompt**:
```javascript
{
  system: "Prompt de sistema con rol e instrucciones",
  context: {
    client_type: "tourist|business",
    conversation_history: [...],
    relevant_knowledge: [...],
    hotel_info: {...}
  },
  user_message: "Mensaje actual del cliente"
}
```

**Ver**: [PROMPT.md](PROMPT.md) para análisis detallado del prompt

---

### 12. OpenAI: Generate Response
**Tipo**: `OpenAI Node`
**Función**: Genera respuesta conversacional con GPT-4.1-mini

**Configuración**:
- Model: `gpt-4.1-mini`
- Temperature: `0.7`
- Max tokens: `500`
- Top P: `0.9`

**Input**:
```json
{
  "messages": [
    { "role": "system", "content": "..." },
    { "role": "user", "content": "..." }
  ]
}
```

**Output**: Respuesta del asistente

---

### 13. Validate Response
**Tipo**: `Code Node (JavaScript)`
**Función**: Valida que la respuesta sea apropiada

**Validaciones**:
- No contiene información sensible
- Longitud apropiada (< 500 chars para WhatsApp)
- Tono adecuado
- No hay URLs sospechosas

---

### 14. Chatwoot: Send Message
**Tipo**: `HTTP Request`
**Función**: Envía respuesta al cliente vía Chatwoot API

**Configuración**:
- Method: `POST`
- URL: `{{ $env.CHATWOOT_URL }}/api/v1/accounts/{{ $env.ACCOUNT_ID }}/conversations/{{ $json.conversation_id }}/messages`
- Headers:
  - `api_access_token`: `{{ $env.CHATWOOT_TOKEN }}`
- Body:
```json
{
  "content": "{{ $json.ai_response }}",
  "message_type": "outgoing",
  "private": false
}
```

---

### 15. PostgreSQL: Save Conversation
**Tipo**: `PostgreSQL Node`
**Función**: Guarda la conversación en la base de datos

**Query**:
```sql
INSERT INTO conversation_history
  (conversation_id, role, content, created_at, metadata)
VALUES
  ($1, 'user', $2, NOW(), $3),
  ($1, 'assistant', $4, NOW(), $5)
```

**Datos guardados**:
- Mensaje del usuario
- Respuesta del asistente
- Metadata (tipo cliente, intención, etc.)

---

### 16. Airtable: Update Lead
**Tipo**: `Airtable Node`
**Función**: Crea o actualiza registro en CRM

**Configuración**:
- Base: `Hotel CRM`
- Table: `Leads`
- Operation: `Upsert` (por phone_number)

**Campos actualizados**:
```json
{
  "Name": "{{ $json.contact_name }}",
  "Phone": "{{ $json.phone_number }}",
  "Client Type": "{{ $json.client_type }}",
  "Last Message": "{{ $json.message_content }}",
  "Last Interaction": "{{ $now }}",
  "Status": "Active"
}
```

---

### 17. IF: Detect Commercial Opportunity
**Tipo**: `IF Node`
**Función**: Detecta si hay oportunidad comercial que requiere notificación

**Condiciones**:
```javascript
{{ $json.intent === 'booking_request' }} OR
{{ $json.intent === 'pricing_inquiry' && $json.client_type === 'business' }} OR
{{ $json.message_content.includes('reserva') }}
```

---

### 18. Telegram: Notify Team
**Tipo**: `Telegram Node`
**Función**: Notifica al equipo comercial sobre oportunidades

**Configuración**:
- Chat ID: `{{ $env.TELEGRAM_CHAT_ID }}`
- Parse mode: `Markdown`

**Mensaje**:
```markdown
🔔 *Nueva Oportunidad Comercial*

👤 *Cliente*: {{ $json.contact_name }}
📱 *Teléfono*: {{ $json.phone_number }}
🏷️ *Tipo*: {{ $json.client_type }}
💬 *Intención*: {{ $json.intent }}

📝 *Mensaje*:
{{ $json.message_content }}

🤖 *Respuesta enviada*:
{{ $json.ai_response }}

🔗 [Ver conversación en Chatwoot]({{ $json.chatwoot_url }})
```

---

### 19. Error Handler
**Tipo**: `Error Workflow`
**Función**: Captura errores y notifica

**Acciones**:
- Log del error en PostgreSQL
- Notificación a Telegram (canal técnico)
- Respuesta genérica al cliente si es posible

---

## 🔄 Flujo de Datos

### Caso 1: Mensaje de Texto Simple

```
Cliente → Chatwoot → Webhook n8n →
Extractor → Switch (texto) →
PostgreSQL (historial) → Supabase (RAG) →
Clasificador → Detector Intención →
Build Prompt → GPT-4.1 → Validador →
Chatwoot (envío) → PostgreSQL (guardar) →
Airtable → FIN
```

**Tiempo estimado**: 2-4 segundos

---

### Caso 2: Mensaje de Voz

```
Cliente → Chatwoot → Webhook n8n →
Extractor → Switch (audio) →
Download Audio → Whisper (transcripción) →
Google Drive (guardar) →
[Continúa como flujo de texto] →
Telegram (notificación de voz transcrita) → FIN
```

**Tiempo estimado**: 5-10 segundos (+ tiempo del audio)

---

### Caso 3: Oportunidad Comercial Detectada

```
[Flujo normal hasta respuesta enviada] →
IF (oportunidad) → TRUE →
Telegram (notificación equipo) →
Airtable (marcar como hot lead) → FIN
```

---

## 🗄️ Bases de Datos

### PostgreSQL Schema

```sql
-- Tabla: conversation_history
CREATE TABLE conversation_history (
  id SERIAL PRIMARY KEY,
  conversation_id VARCHAR(255) NOT NULL,
  role VARCHAR(20) NOT NULL, -- 'user' | 'assistant'
  content TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  metadata JSONB
);

CREATE INDEX idx_conversation_id ON conversation_history(conversation_id);
CREATE INDEX idx_created_at ON conversation_history(created_at DESC);
```

---

### Supabase (Vector Database)

```sql
-- Tabla: knowledge_base
CREATE TABLE knowledge_base (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  content TEXT NOT NULL,
  embedding VECTOR(1536), -- OpenAI embedding dimension
  metadata JSONB,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Índice para búsqueda vectorial
CREATE INDEX ON knowledge_base
USING ivfflat (embedding vector_cosine_ops);
```

---

### Airtable (CRM)

**Base**: Hotel CRM
**Tabla**: Leads

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Name | Single line text | Nombre del contacto |
| Phone | Phone number | Teléfono (unique) |
| Client Type | Single select | Tourist / Business |
| Status | Single select | Active / Cold / Hot / Converted |
| Last Message | Long text | Último mensaje enviado |
| Last Interaction | Date | Última vez que escribió |
| Source | Single select | WhatsApp / Web / Other |
| Assigned To | Collaborator | Vendedor asignado |
| Notes | Long text | Notas del equipo |

---

## 🔌 Integraciones Externas

### 1. Chatwoot
- **Endpoint**: `{{ $env.CHATWOOT_URL }}/api/v1/`
- **Auth**: API Token
- **Uso**: Recepción y envío de mensajes WhatsApp

### 2. OpenAI
- **Endpoints**:
  - `https://api.openai.com/v1/chat/completions` (GPT)
  - `https://api.openai.com/v1/audio/transcriptions` (Whisper)
- **Auth**: Bearer Token
- **Modelos**: `gpt-4.1-mini`, `whisper-1`

### 3. Supabase
- **Endpoint**: `{{ $env.SUPABASE_URL }}/rest/v1/`
- **Auth**: API Key + JWT
- **Uso**: Vector search (RAG)

### 4. Airtable
- **Endpoint**: `https://api.airtable.com/v0/{{ $env.AIRTABLE_BASE_ID }}/`
- **Auth**: Bearer Token
- **Uso**: CRM - gestión de leads

### 5. Google Drive
- **API**: Google Drive v3
- **Auth**: OAuth2
- **Uso**: Almacenamiento de audios

### 6. Telegram
- **Endpoint**: `https://api.telegram.org/bot{{ $env.TELEGRAM_BOT_TOKEN }}/`
- **Auth**: Bot Token
- **Uso**: Notificaciones al equipo

---

## 🔐 Variables de Entorno

```bash
# Chatwoot
CHATWOOT_URL=https://[URL protegida]
CHATWOOT_TOKEN=xxxxxxxxxx
ACCOUNT_ID=xx

# OpenAI
OPENAI_API_KEY=sk-xxxxxxxxxx

# Supabase
SUPABASE_URL=https://[URL protegida]
SUPABASE_KEY=xxxxxxxxxx

# Airtable
AIRTABLE_API_KEY=keyxxxxxxxxxx
AIRTABLE_BASE_ID=appxxxxxxxxxx

# Telegram
TELEGRAM_BOT_TOKEN=xxxxxxxxxx:xxxxxxxxxxx
TELEGRAM_CHAT_ID=-xxxxxxxxxx

# PostgreSQL
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=n8n_db
POSTGRES_USER=n8n_user
POSTGRES_PASSWORD=xxxxxxxxxx
```

---

## ⚡ Optimizaciones Implementadas

### 1. Caching
- ❌ No implementado aún
- 💡 Idea: Cachear respuestas a preguntas frecuentes

### 2. Paralelización
- ✅ Consultas a PostgreSQL y Supabase en paralelo
- ✅ Actualización de CRM en background (no bloquea respuesta)

### 3. Rate Limiting
- ✅ Límite de 10 req/min por conversación (evitar spam)
- ✅ Queue de mensajes si excede límite

### 4. Error Handling
- ✅ Retry automático en fallas de API (max 3 intentos)
- ✅ Fallback a respuesta genérica si GPT falla
- ✅ Notificación técnica en errores críticos

---

## 📊 Métricas de Performance

**Targets actuales**:
- Tiempo de respuesta (texto): < 5 segundos
- Tiempo de respuesta (voz): < 15 segundos
- Uptime: > 99%
- Tasa de éxito de respuestas: > 95%

**Monitoreo**:
- ❌ No implementado (ver [ISSUES.md #005](ISSUES.md))

---

## 🔍 Puntos de Mejora Identificados

Ver análisis completo en:
- **[MESSAGE_PROCESSING.md](MESSAGE_PROCESSING.md)**: Optimización de procesamiento
- **[ISSUES.md](ISSUES.md)**: Lista completa de mejoras

---

## 📚 Referencias

- **[README.md](README.md)**: Documentación general
- **[PROMPT.md](PROMPT.md)**: Análisis del prompt
- **[MESSAGE_PROCESSING.md](MESSAGE_PROCESSING.md)**: Procesamiento de mensajes
- **[CHANGELOG.md](CHANGELOG.md)**: Historial de versiones

---

**Última actualización**: 5 de Marzo 2026
**Versión de arquitectura**: v1.2
**Documentado por**: Claude AI + NicoCalama
