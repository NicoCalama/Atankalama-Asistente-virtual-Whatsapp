# ⚙️ Configuración del Sistema
**Centralización de parámetros, timeouts, modelos y IDs clave**

---

## 🕐 Timeouts y Delays

| Parámetro | Valor | Ubicación | Propósito | Confirmado |
|-----------|-------|-----------|----------|-----------|
| Redis wait | 4 segundos | Main Workflow, nodo "Espera 4s" | Acumular mensajes en ráfaga | ✅ 6 Mar 2026 |
| Webhook test timeout | — | Chatwoot → n8n | Depende de Chatwoot | — |
| HTTP timeout (Chatwoot) | — | n8n config | Riesgo de timeout si > 4s | ⚠️ Ver nota |

### Nota de Timeout Redis
**Histórico**: Fue 12s → reducido a 4s en v1.5.3 para evitar timeout de Chatwoot.  
**Confirmado con ejecuciones reales**: 6 Mar 2026 (#1229, #1232, #1233)  
**Referencia**: [MESSAGE_PROCESSING.md](MESSAGE_PROCESSING.md) línea 14 (ahora archivado)

---

## 🤖 Modelos LLM

### Producción (v1.5.x)

| Componente | Modelo | Tokens | Propósito | Costo | Credencial |
|-----------|--------|--------|----------|-------|-----------|
| Main Agent | GPT-4.1 | ~620 | Orquestación principal | ~$0.02/msg | OpenAI |
| Transcripción | Whisper v1 | — | Audio → texto | ~$0.01/min | OpenAI |

### Desarrollo (v2.0)

| Componente | Modelo | Tipo | Propósito | Costo | Credencial |
|-----------|--------|------|----------|-------|-----------|
| Orquestador | GPT-4.1 | Agent | Main v2.0 | ~$0.02 | OpenAI |
| FAQ Agent | GPT-4o-mini | Agent + Vector Store | Búsqueda FAQ | ~$0.004 | OpenAI |
| CRM Agent | GPT-4o-mini | Agent + Airtable tools | Gestión contactos | ~$0.004 | OpenAI |
| Pricing Agent | GPT-4o-mini | LLM Chain | Formato disponibilidad | ~$0.002 | OpenAI |

**Nota**: v2.0 usa GPT-4o-mini en sub-agentes para reducir costo (~60%). Main sigue siendo GPT-4.1.

---

## 🔗 Integraciones Externas

### APIs Críticas

| Servicio | Endpoint | Versión | Propósito | Status |
|----------|----------|---------|----------|--------|
| Chatwoot | REST API | v2.0 | Webhooks + Send message | ✅ Activo |
| Cloudbeds | REST API | v1.2 | getRoomTypes, getRatePlans | ✅ Validado |
| Cloudbeds | REST API | v1.2 | postReservation (Fase 3) | ⏳ No implementado |
| Airtable | REST API | v0 | CRUD contactos + feedback | ✅ Activo |
| Supabase | pgvector | — | Vector Store FAQ | ✅ Activo |
| PostgreSQL | psycopg2 | — | Chat Memory | ✅ Activo |
| Redis | redis-py | 6.0+ | Acumulación de mensajes | ✅ Activo |
| OpenAI | REST API | v1 | GPT-4.1 + Whisper | ✅ Activo |
| Google Sheets | REST API | v4 | Preguntas sin respuesta | ✅ Activo |
| Gmail | SMTP | — | Notificación errores | ✅ Activo |
| Slack | Webhook | — | Escalación humana | ✅ Activo |

---

## 🏢 Proyectos y Recursos

### Supabase

| Recurso | Identificador | Proyecto | Propósito | Estado |
|---------|---------------|----------|----------|--------|
| Vector Store | `faq_atankalama` | Atankalama Corp | Base de conocimiento | ✅ Activo |
| Chat Memory | (PostgreSQL nativo) | Atankalama Corp | Historial conversaciones | ✅ Activo |

**Proyecto**: "Atankalama Corp" (ref: `zxzimlqrjnmigblulthg`)

### Airtable

| Recurso | Base ID | Tabla ID | Propósito |
|---------|---------|----------|----------|
| CRM | `applhMgcR7lh6UDlT` | `tblSTBwvHaxvSd5Fq` | Contactos + leads |
| Feedback | (misma base) | (tabla feedback) | Calificaciones |

### Google Drive

| Recurso | Folder ID | Propósito |
|---------|-----------|----------|
| FAQ source files | `1SYGbCj0PIf-yVb2DzyX5aPWILV7wAbQy` | Drive → Supabase vectorization |

---

## 🔐 Credenciales (Protegidas)

Todas las credenciales están en **n8n → Settings → Credentials**. Nunca en código.

| Servicio | Credencial | Tipo | Estado |
|----------|-----------|------|--------|
| OpenAI | [Protegida] | API Key | ✅ Activo |
| Chatwoot | [Protegida] | API Key | ✅ Activo |
| Airtable | [Protegida] | API Key | ✅ Activo |
| Supabase | [Protegida] | Anon Key | ✅ Activo |
| PostgreSQL | [Protegida] | Connection String | ✅ Activo |
| Google Sheets | [Protegida] | OAuth 2.0 | ✅ Activo |
| Gmail | [Protegida] | OAuth 2.0 | ✅ Activo |
| Slack | [Protegida] | Webhook | ✅ Activo |
| Google Drive | [Protegida] | OAuth 2.0 | ✅ Activo |

**Nota**: Todos los IDs reales son protegidos en repositorio. Ver ARCHITECTURE.md línea 254+ para referencia.

---

## 📍 IDs de Workflow Principales

```
Producción (v1.5.x):
  Main:                    s9A9Al67_R0wSQWf_HY3X  (40 nodos)
  Escalación Humano:       K3WrelHxg7k9EePiD5-2S  (5 nodos)

Desarrollo (v2.0):
  Orquestador:             KyWsxQJhr1SS0mS3  (en testing)
  FAQ Agent:               Mq8XkIXWvZIyF2sZ  (validado)
  CRM Agent:               LPeOJLQadME2Nbgv  (validado)
  Pricing Agent:           X22IjZoUYkFxKyjw  (validado)
```

---

## 🎯 Nodos Críticos (Main v1.6.4)

| Nodo | ID | Tipo | Crítico |
|------|-----|------|---------|
| Agente IA | `b6432bcb-0ded-4e43-aeec-169cdf3eac97` | AI Agent | 🔴 SÍ |
| Contactar Humano | `2964a976-4304-41a8-ba67-703a9f13912b` | toolWorkflow | 🟠 Escalación |
| Webhook | (main trigger) | Webhook | 🔴 SÍ |
| Filter | (bloquea etiqueta "humano") | Filter | 🟠 Control flow |

---

## 📊 Monedas Soportadas

**Por prefijo telefónico** (para links Cloudbeds):

| Prefijo | Moneda | País |
|---------|--------|------|
| +56 | CLP | Chile |
| +55 | BRL | Brasil |
| +54 | ARS | Argentina |
| +34, +33, +49, +39 | EUR | Europa (España, Francia, Alemania, Italia) |
| Otros | USD | Default |

---

## 🔄 Links de Reserva Cloudbeds

| Hotel | URL | Parámetros |
|-------|-----|-----------|
| Atankalama | `https://hotels.cloudbeds.com/es/reservation/kKhFdN/` | `?currency=XXX&checkin=YYYY-MM-DD&checkout=YYYY-MM-DD&adults=N` |
| Atankalama Inn | `https://hotels.cloudbeds.com/es/reservation/bcvkBy/` | `?currency=XXX&checkin=YYYY-MM-DD&checkout=YYYY-MM-DD&adults=N` |

**Nota**: Links pre-llenados generados en v2.0 Pricing Agent.

---

## ⚡ Parámetros de Rendimiento

| Métrica | Valor | Target |
|---------|-------|--------|
| Tiempo respuesta texto (con Redis) | ~5-15 seg | < 20 seg |
| Tiempo respuesta voz (Whisper) | ~8-20 seg | < 30 seg |
| Tokens por mensaje promedio | ~620 (main) | < 800 |
| Ejecuciones/día típico | ? | — |
| Costo mensual estimado | ~$80-120 | < $200 |

---

## 🐛 Bugs Conocidos

| Bug | Versión | Workaround | Estado |
|-----|---------|-----------|--------|
| `$fromAI()` en workflowInputs falla | n8n v2.3.6 | Usar `$('Trigger').item.json.campo` | ⚠️ Workaround aplicado |
| toolHttpRequest v1.1 crashea | n8n v2.3.6 | Usar toolCode con `$helpers.httpRequest()` | ⚠️ Workaround aplicado |
| updateNode borra credenciales | n8n v2.3.6 | Actualizar credentials en paso separado | ⚠️ Documentado |

Ver [TROUBLESHOOTING.md](TROUBLESHOOTING.md) para detalles.

---

## 📅 Próximas Migraciones/Actualizaciones

| Tarea | Versión | Fecha estimada | Estado |
|-------|---------|-----------------|--------|
| Cutover v2.0 (Fase 2) | v2.0 | TBD | ⏳ Testing |
| Implementar Fase 3 (Reservas) | v3.0 | TBD | 📋 Planificado |
| Soporte multiidioma | v2.5 | TBD | 💡 Idea |
| Analytics + métricas | v2.2 | TBD | 💡 Idea |

---

## 🔗 Referencias

- [AGENTS_SPEC.md](AGENTS_SPEC.md) — Prompts del agente
- [ARCHITECTURE.md](ARCHITECTURE.md) — Diagrama y flujo
- [TROUBLESHOOTING.md](TROUBLESHOOTING.md) — Debugging
- [INDEX.md](INDEX.md) — Mapa de navegación

---

**Última actualización**: 6 Abril 2026  
**Próxima revisión**: 4 Mayo 2026  
**Responsable**: NicoCalama
