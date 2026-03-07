# Atankalama — Asistente Virtual WhatsApp

Bot de IA que atiende automáticamente a los clientes del Hotel Atankalama por WhatsApp, las 24 horas. El cliente escribe o manda un audio, y el bot responde en segundos usando información real del hotel, el historial de la conversación y el CRM.

**Estado**: Activo en producción — v1.4.1 (7 Marzo 2026)
**Workflow ID en n8n**: `s9A9Al67_R0wSQWf_HY3X`

---

## ¿Qué puede hacer el asistente?

- Responder preguntas sobre el hotel (habitaciones, servicios, actividades, zona)
- Transcribir y responder mensajes de voz
- Dar links de reserva con la moneda correcta según el país del cliente
- Registrar y actualizar contactos en el CRM (Airtable)
- Anotar preguntas que no supo responder (para mejorar la base de conocimiento)
- Pedir feedback al cerrar una conversación
- Derivar a un agente humano cuando el cliente lo pide o ante una alerta de seguridad

---

## Cómo funciona

```
Cliente envía mensaje (texto o audio) por WhatsApp
        |
        v
Chatwoot lo recibe y notifica a n8n via webhook
        |
        v
n8n verifica que es un mensaje real y que la conversación
no está marcada como "humano" (si lo está, el bot no interviene)
        |
        |-- Si es AUDIO --> Descarga y transcribe con Whisper
        |-- Si es TEXTO --> Acumula en Redis 4 segundos
        |                  (por si el cliente manda varios mensajes seguidos)
        v
Agente IA recibe el mensaje y ejecuta este orden:
  1. Llama "Consultar contactos" → saber si el cliente está en el CRM
  2. Saluda por nombre si es conocido, se presenta si es nuevo
  3. Consulta "Base de datos" para preguntas del hotel
  4. Da link de Cloudbeds con moneda correcta para reservas/precios
  5. Escala a humano si el cliente lo pide o si no tiene respuesta
  6. Pide feedback al despedirse
        |
        v
Respuesta enviada al cliente via Chatwoot
```

**Tiempo de respuesta típico**: 5–15 segundos

---

## Stack técnico

| Componente | Tecnología |
|---|---|
| Motor de automatización | n8n (self-hosted) |
| Frontend WhatsApp | Chatwoot |
| Agente de IA | OpenAI GPT-4.1 |
| Transcripción de voz | OpenAI Whisper |
| Base de conocimiento | Supabase Vector Store |
| Memoria conversacional | PostgreSQL |
| CRM | Airtable |
| Acumulación de mensajes | Redis |
| Preguntas sin respuesta | Google Sheets |
| Escalación humana | Gmail |

---

## Si algo falla — Errores comunes y soluciones

### El agente responde "No prompt specified"

**Causa**: Los campos `promptType` y `text` del nodo "Agente IA" fueron eliminados por una actualización parcial de la API de n8n.

**Solución**: En n8n, abrir el nodo "Agente IA" y verificar:
- Campo **Prompt**: debe decir "Define below"
- Campo **Text**: debe tener `={{ $json.mensaje }}`

Si están vacíos, completarlos y guardar.

---

### El bot escribe su razonamiento interno en el mensaje al cliente

**Síntoma**: El cliente recibe mensajes que empiezan con "Think: ..." antes de la respuesta real.

**Causa**: El agente interpretó las instrucciones del prompt como texto a escribir en lugar de llamar la herramienta Think.

**Solución**: Revisar el systemMessage del nodo "Agente IA". La instrucción debe decir explícitamente:

> "Llama la herramienta Think para razonar internamente. Tu razonamiento NUNCA debe aparecer en el mensaje al cliente."

---

### El bot no responde cuando llega un mensaje

**Causas posibles**:
1. El workflow está inactivo → verificar que el toggle en n8n esté en verde (Active)
2. Chatwoot está apuntando a la URL de test en lugar de la de producción

**Diferencia entre URLs**:
- Producción: `https://[instancia-n8n]/webhook/[id]` ← Chatwoot debe usar esta
- Test: `https://[instancia-n8n]/webhook-test/[id]` ← solo para pruebas en el editor

---

### El bot no responde en una conversación específica

**Causa probable**: La conversación tiene la etiqueta "humano" en Chatwoot — el bot se detiene para que un agente tome el control.

**Solución**: En Chatwoot, abrir la conversación y remover la etiqueta "humano".

---

### El HTTP Request final falla y el cliente no recibe respuesta

**Causa probable**: La credencial de Chatwoot venció o fue regenerada.

**Solución**: En n8n → Settings → Credentials → buscar la credencial de Chatwoot → hacer "Test" para verificar.

---

## Cómo verificar que el sistema está funcionando

En n8n, ir a **Executions** y filtrar por este workflow. Una ejecución correcta se ve así:

```
Webhook                   ✅  recibió el mensaje
If                        ✅  validó que es mensaje entrante
CHATWOOT Obtener Etiqueta ✅  consultó etiquetas
Filter                    ✅  no tiene etiqueta "humano"
Edit Fields               ✅  extrajo datos del mensaje
Switch                    ✅  detectó tipo (texto o audio)
[Redis o Whisper]         ✅
Merge1                    ✅  mensaje listo con campo "mensaje"
Agente IA                 ✅  generó respuesta (tiempo > 1 seg)
HTTP Request              ✅  enviado a Chatwoot
```

Si algún nodo está en rojo, ese es el punto de falla.

---

## Dónde están las credenciales

Todas las credenciales están en **n8n → Settings → Credentials**, nunca en el código ni en este repositorio.

Credenciales utilizadas: OpenAI, Chatwoot, Airtable, Supabase, PostgreSQL, Google Sheets, Gmail.

---

## Documentación detallada

| Documento | Contenido |
|---|---|
| [TROUBLESHOOTING.md] | Guía completa de errores con diagrama del flujo real |
| [CHANGELOG.md] | Historial de versiones |
| [ISSUES.md] | Bugs conocidos y estado |
| [ARCHITECTURE.md] | Detalle técnico de nodos |

---

## Historial de versiones

| Versión | Fecha | Descripción |
|---|---|---|
| v1.4.1 | 7 Mar 2026 | Fix: prompt sin `promptType`, Think visible en respuestas, nombre en email de escalación |
| v1.4.0 | 7 Mar 2026 | Rediseño completo del prompt (Think-first, -70% tokens) |
| v1.3.0 | 6 Mar 2026 | Auditoría técnica completa, arquitectura verificada |
| v1.0.0 | Feb 2026 | Lanzamiento inicial |

---

**Responsable**: NicoCalama — Hotel Atankalama
