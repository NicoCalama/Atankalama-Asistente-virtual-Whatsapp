# Guía de Resolución de Problemas — Asistente Virtual WhatsApp

Este documento está escrito para cualquier persona que necesite entender, mantener o reparar el workflow del Asistente Virtual de Hotel Atankalama, aunque no tenga contexto previo del proyecto.

---

## ¿Qué hace este sistema?

El asistente es un bot de WhatsApp con IA que responde automáticamente a los clientes del hotel. Cuando alguien escribe o envía un audio por WhatsApp, el sistema:

1. Recibe el mensaje via Chatwoot
2. Transcribe el audio si corresponde (Whisper)
3. Lo procesa con un agente de IA (GPT-4.1)
4. Consulta la base de conocimiento del hotel y el CRM
5. Envía una respuesta de vuelta al cliente en WhatsApp

---

## Arquitectura del flujo (verificada 7 Marzo 2026)

```
CLIENTE (WhatsApp)
        |
        v
   CHATWOOT
        |-- webhook POST -->
                            Webhook (n8n)
                                |
                               If
                     [message_created + incoming]
                                |
                     CHATWOOT Obtener Etiqueta
                     (verifica si conv. tiene etiqueta "humano")
                                |
                             Filter
                     (descarta si etiqueta = "humano")
                                |
                          Edit Fields
                     (extrae: telefono, mensaje, tipo_mensaje, ID Chatwoot)
                                |
                             Switch
                     __________|__________
                    |                     |
                 [audio]               [texto]
                    |                     |
           descargar audio           Redis (push)
                    |              (acumula mensajes en rafaga)
         Transcribe (Whisper)             |
                    |                Espera 4 s
             Edit Fields2                |
          (campo: "mensaje")        Redis6 (get)
                    |                    |
                    |               Switch4
                    |          __________|____________
                    |         |           |           |
                    |     [ignorar]  [Continuamos] [Esperar]
                    |                    |           |
                    |              Redis7 (delete)  Espera 4 s
                    |                    |           |
                    +--------------------+-----------+
                                         |
                                    Edit Fields6
                                  (une mensajes acumulados)
                                         |
                                      Merge1
                                         |
                                      Agente IA
                                (GPT-4.1 + herramientas)
                                         |
                                    HTTP Request
                              (envia respuesta a Chatwoot)
```

**Nota importante**: Los mensajes de voz NO pasan por Redis. Se procesan de forma directa e inmediata al llegar.

---

## Herramientas disponibles del Agente IA

El agente tiene acceso a estas herramientas que llama según el contexto:

| Herramienta | Tipo | Para qué sirve |
|---|---|---|
| **Think** | Interna | Razonar antes de actuar. Su contenido es invisible para el cliente. |
| **Consultar contactos** | Airtable | Busca si el cliente existe en el CRM por número de teléfono. Siempre se llama primero. |
| **Crear / Actualizar contacto** | Airtable | Crea o actualiza el registro del cliente con datos nuevos o tipo de consulta. |
| **Base de datos** | Supabase Vector Store | Busca información del hotel (servicios, políticas, actividades, zona). |
| **Contactar Humano** | Gmail | Envía email al equipo para escalar la conversación a un humano. |
| **reporte de preguntas sin respuesta** | Google Sheets | Registra preguntas que el agente no pudo responder. |
| **registrar_feedback_encuesta** | Airtable | Guarda la calificación y razón de nota al cerrar una conversación. |
| **Calculator** | Interna | Cálculos simples si son necesarios. |

---

## Cómo verificar que el sistema está funcionando

### 1. Enviar un mensaje de prueba

- Abre el workflow en n8n en modo test
- Activa "Test workflow" (escucha el webhook de prueba)
- Envía un WhatsApp al número del hotel
- Observa la ejecución en tiempo real en el editor

### 2. Revisar ejecuciones pasadas

En n8n: **Executions** → filtrar por workflow "Atankalama Asistente virtual Whatsapp"

Una ejecución correcta se ve así:
```
Webhook          ✅  (recibió el mensaje)
If               ✅  (validó que es mensaje entrante)
CHATWOOT         ✅  (obtuvo etiquetas)
Filter           ✅  (no tiene etiqueta "humano")
Edit Fields      ✅  (extrajo datos)
Switch           ✅  (detectó tipo: texto o audio)
[Redis o Whisper]✅
Merge1           ✅  (mensaje listo con campo "mensaje")
Agente IA        ✅  (generó respuesta, tiempo > 1 seg)
HTTP Request     ✅  (enviado a Chatwoot)
```

Si algún nodo está en rojo, ese es el punto de falla.

### 3. Señales de que funciona correctamente

- El cliente recibe respuesta en menos de 15 segundos
- La respuesta NO incluye texto como "Think:" al inicio
- La respuesta NO dice "No prompt specified"
- Los mensajes de voz se transcriben correctamente

---

## Errores comunes y cómo resolverlos

---

### Error 1: Agente responde "No prompt specified"

**Síntoma**: El Agente IA responde en menos de 1 segundo con `{"error": "No prompt specified"}` sin llegar a llamar al LLM.

**Causa**: Los parámetros `promptType` y `text` del nodo "Agente IA" fueron eliminados al hacer una actualización parcial con la API de n8n. Esto ocurre cuando se actualiza solo el campo `options` y n8n reemplaza el objeto `parameters` completo.

**Cómo verificar**:
En n8n, abre el nodo "Agente IA" → pestaña "Parameters". Debe tener:
- **Prompt**: "Define below"
- **Text**: `={{ $json.mensaje }}`

Si esos campos están vacíos o en blanco, ese es el problema.

**Cómo resolver**:
1. En n8n, abre el nodo "Agente IA"
2. En el campo **Prompt**, selecciona "Define below"
3. En el campo **Text**, escribe: `={{ $json.mensaje }}`
4. Guarda el workflow
5. Envía un mensaje de prueba para verificar

---

### Error 2: El agente escribe su razonamiento en el mensaje al cliente

**Síntoma**: El cliente recibe mensajes que empiezan con algo como "Think: consulta de hotel, necesito llamar Consultar contactos..." antes de la respuesta real.

**Causa**: El agente interpretó las instrucciones del prompt como un formato de texto a escribir, en lugar de llamar la herramienta Think como tool call.

**Cómo verificar**:
Revisa la ejecución en n8n → nodo "Agente IA" → output. Si el campo `output` contiene "Think:" al inicio, el problema es el prompt.

**Cómo resolver**:
Revisar el systemMessage del nodo "Agente IA". El ejemplo del prompt debe dejar claro que Think es una herramienta a llamar, no un texto a escribir. La instrucción correcta es:

```
Llama la herramienta "Think" para razonar internamente.
Tu razonamiento NUNCA debe aparecer en el mensaje al cliente.
```

Si el prompt tiene un ejemplo con "Think: ..." como texto plano, reemplazarlo por:
```
[Herramienta Think → razonamiento interno, nunca visible para el cliente]
```

---

### Error 3: El agente no llama herramientas (responde sin consultar nada)

**Síntoma**: El agente responde directamente sin llamar "Consultar contactos" ni "Base de datos". En las ejecuciones se ve que el Agente IA solo tiene un paso, sin sub-llamadas a herramientas.

**Causa posible A**: El prompt tiene demasiadas reglas prohibitivas en lugar de instrucciones positivas de secuencia.

**Causa posible B**: El campo `promptType` está en "auto" en lugar de "define", por lo que el agente no recibe el mensaje del usuario correctamente.

**Cómo resolver**:
1. Verificar que `promptType = define` y `text = {{ $json.mensaje }}` (ver Error 1)
2. Verificar que el nodo Merge1 entrega un objeto con campo `mensaje` al Agente IA
3. Si el prompt tiene más de 500 tokens de instrucciones, considerar simplificarlo

---

### Error 4: Mensajes de texto no llegan al agente

**Síntoma**: La ejecución para antes de llegar a Merge1. Los nodos Redis funcionan pero Switch4 descarta el mensaje.

**Causa**: Switch4 evalúa si el mensaje debe procesarse o esperar. Si el timeout de Redis no expiró o la condición de "Continuamos" no se cumple, el mensaje se descarta.

**Cómo verificar**:
Revisar el output del nodo Switch4 en la ejecución:
- Output 0 (ignorar): el mensaje fue descartado
- Output 1 (Continuamos): procesamiento directo
- Output 2 (Esperar): esperó 4 segundos y reintentó

**Cómo resolver**:
Generalmente no es un error sino el comportamiento esperado. Si los mensajes se están perdiendo consistentemente, revisar la lógica de Switch4 y el tiempo de espera de Redis.

---

### Error 5: El flujo no se dispara cuando llega un mensaje

**Síntoma**: El cliente envía un mensaje y no hay ninguna ejecución nueva en n8n.

**Causas posibles**:
1. El workflow está inactivo (toggle apagado en n8n)
2. El webhook de Chatwoot apunta a la URL de test en lugar de la URL de producción
3. Chatwoot no tiene configurado el webhook hacia n8n

**Cómo verificar**:
- En n8n: confirmar que el workflow tiene el toggle en "Active" (verde)
- En Chatwoot: Settings → Integrations → Webhooks → verificar que la URL sea la URL de producción del webhook (no la de test)

**URL de producción vs test**:
- Producción: `https://[instancia-n8n]/webhook/[id]`
- Test: `https://[instancia-n8n]/webhook-test/[id]`

Chatwoot siempre debe usar la URL de **producción**.

---

### Error 6: Mensajes de voz no se transcriben

**Síntoma**: El switch detecta tipo "audio" pero el nodo "Transcribe a recording" falla.

**Causas posibles**:
1. El audio de Chatwoot tiene una URL expirada (los links de S3 expiran en 5 minutos)
2. La credencial de OpenAI está vencida o sin crédito
3. El formato del audio no es compatible (Chatwoot envía `.ogg`)

**Cómo verificar**:
En la ejecución, revisar el output del nodo "descargar audio". Si el binary data está vacío o hay un error HTTP, la URL expiró.

**Cómo resolver**:
El audio debe procesarse dentro de los 5 minutos de haber llegado. Si el workflow tiene delays muy largos (Redis timeout alto), el audio puede expirar. El timeout de Redis está configurado en 4 segundos, lo que debería ser suficiente.

---

### Error 7: El nodo Filter bloquea todos los mensajes

**Síntoma**: Las ejecuciones muestran que Filter tiene 0 items de output, es decir, todos los mensajes son descartados.

**Causa**: La conversación en Chatwoot tiene la etiqueta "humano" aplicada, lo que hace que el bot se detenga para que un agente humano tome el control.

**Cómo resolver**:
En Chatwoot, abrir la conversación correspondiente y remover la etiqueta "humano". El bot vuelve a responder automáticamente.

**Nota**: Esto es comportamiento intencional. La etiqueta "humano" sirve para que el equipo pueda tomar el control de una conversación sin que el bot interfiera.

---

### Error 8: HTTP Request falla al enviar respuesta a Chatwoot

**Síntoma**: El Agente IA genera una respuesta correcta pero el HTTP Request final falla. El cliente no recibe nada.

**Causas posibles**:
1. La API key de Chatwoot venció o fue regenerada
2. El ID de conversación en Chatwoot no existe
3. El output del Agente IA no tiene el campo `output` esperado

**Cómo verificar**:
En la ejecución, revisar el input del nodo HTTP Request. El body debe tener `content` con el texto de la respuesta. Si `$json.output` es `undefined`, el agente no generó output correctamente.

**Cómo resolver**:
- Verificar credencial de Chatwoot en n8n Settings → Credentials
- Si el campo `output` está vacío, revisar el estado del nodo Agente IA (puede ser Error 1 o Error 3)

---

## Estructura de los datos en cada nodo clave

### Lo que llega a "Agente IA" (campo mensaje)

```json
{
  "mensaje": "Texto del cliente (texto directo o transcripción de voz)"
}
```

El agente también tiene acceso al contexto de la conversación via Postgres Chat Memory (historial de mensajes anteriores).

### Lo que sale de "Agente IA"

```json
{
  "output": "Texto de la respuesta generada por el agente"
}
```

### Lo que envía "HTTP Request" a Chatwoot

```json
{
  "content": "{{ $json.output }}",
  "message_type": "outgoing",
  "private": false
}
```

---

## Dónde están las credenciales

Todas las credenciales están almacenadas en n8n (no en el código del workflow):

- **n8n** → Settings → Credentials
- Nunca están en el JSON del workflow ni en este repositorio
- Si una credencial falla, ir a n8n → Settings → Credentials → buscar la credencial correspondiente → "Test" para verificar

Credenciales utilizadas por este workflow:
- OpenAI (GPT-4.1 + Whisper)
- Chatwoot (HTTP Request)
- Airtable (CRM)
- Supabase (Vector Store)
- PostgreSQL (Memoria conversacional)
- Google Sheets (Preguntas sin respuesta)
- Gmail (Escalación humana)

---

## Stack técnico resumido

| Componente | Tecnología | Para qué |
|---|---|---|
| Motor de automatización | n8n (self-hosted) | Orquesta todo el flujo |
| Frontend WhatsApp | Chatwoot | Recibe y envía mensajes |
| Agente de IA | OpenAI GPT-4.1 | Genera respuestas |
| Transcripción de voz | OpenAI Whisper | Convierte audio a texto |
| Base de conocimiento | Supabase Vector Store | FAQ y info del hotel (RAG) |
| Memoria conversacional | PostgreSQL | Historial de cada conversación |
| CRM | Airtable | Gestión de contactos y leads |
| Preguntas sin respuesta | Google Sheets | Registro de gaps de conocimiento |
| Escalación humana | Gmail | Notificar al equipo |
| Acumulación de mensajes | Redis | Agrupa mensajes enviados en ráfaga |

---

## Historial de problemas resueltos

Para ver el historial completo de bugs, causas y soluciones aplicadas:

- [ISSUES.md](ISSUES.md) — lista de todos los problemas conocidos
- [CHANGELOG.md](CHANGELOG.md) — qué cambió en cada versión

---

## Contacto

**Responsable del proyecto**: NicoCalama
**Última verificación del sistema**: 7 de Marzo 2026 (v1.4.1)
**Versión actual**: v1.4.1

Para contexto adicional o preguntas técnicas, revisar el historial de commits en GitHub — cada commit describe con detalle qué se cambió y por qué.
