# Atankalama Asistente Virtual WhatsApp

**Versión actual**: v1.5.5
**Estado**: Activo en producción
**Última verificación**: 19 de Marzo 2026
**Workflow ID en n8n**: `s9A9Al67_R0wSQWf_HY3X`

---

## ¿Qué es esto?

Un asistente virtual con IA que atiende automáticamente a los clientes del Hotel Atankalama por WhatsApp, las 24 horas. El cliente escribe (o manda un audio) y el bot responde en segundos usando información real del hotel, historial de la conversación y el CRM.

Cuando el bot no puede ayudar, escala automáticamente a un humano del equipo.

---

## ¿Qué puede hacer?

- Responder preguntas sobre el hotel (habitaciones, servicios, actividades, zona)
- Transcribir y responder mensajes de voz
- Dar links de reserva con la moneda correcta según el país del cliente
- Registrar y actualizar contactos en el CRM (Airtable)
- Registrar preguntas que no supo responder (para mejorar la base de conocimiento)
- Pedir feedback al cerrar una conversación
- Derivar a un agente humano cuando el cliente lo pide o cuando hay una alerta de seguridad

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

## Cómo funciona (flujo simplificado)

```
Cliente envía mensaje por WhatsApp
        |
        v
Chatwoot lo recibe y notifica a n8n
        |
        v
n8n valida que es un mensaje real (no un evento del sistema)
n8n verifica que la conversación no esté marcada como "humano"
        |
        |-- Si es AUDIO --> Descarga y transcribe con Whisper
        |-- Si es TEXTO --> Acumula en Redis por 4 segundos
        |                  (por si el cliente manda varios mensajes seguidos)
        v
Agente IA recibe el mensaje
        |
        +--> Llama "Consultar contactos" para saber si el cliente existe en CRM
        +--> Llama "Base de datos" si hay preguntas sobre el hotel
        +--> Llama "Crear/Actualizar contacto" si hay datos nuevos
        +--> Llama "Contactar Humano" si el cliente lo pide
        |
        v
Respuesta enviada de vuelta al cliente via Chatwoot
```

**Tiempo de respuesta típico**: 5-15 segundos

---

## Comportamiento del agente

El agente sigue este orden de prioridad:

1. **Siempre primero**: llama "Consultar contactos" para saber con quién está hablando
2. **Cliente nuevo**: se presenta ("Soy el Asistente Virtual de Atankalama")
3. **Cliente conocido**: saluda por nombre
4. **Preguntas del hotel**: consulta "Base de datos" antes de responder
5. **Reservas/precios/disponibilidad**: no está en la base de datos — deriva a Cloudbeds con link y moneda correcta
6. **No sabe responder**: registra la pregunta y ofrece contacto humano
7. **Al cerrar conversación**: pide feedback del 1 al 5

El agente razona internamente con la herramienta Think antes de responder. Ese razonamiento es invisible para el cliente.

---

## Documentación disponible

| Documento | Contenido |
|---|---|
| [TROUBLESHOOTING.md](TROUBLESHOOTING.md) | Guía de problemas comunes y cómo resolverlos. **Empezar aquí si algo falla.** |
| [CHANGELOG.md](CHANGELOG.md) | Historial completo de versiones y cambios |
| [ISSUES.md](ISSUES.md) | Bugs conocidos, estado y soluciones aplicadas |
| [ARCHITECTURE.md](ARCHITECTURE.md) | Detalle técnico de cada nodo |
| [PROMPT.md](PROMPT.md) | Análisis del prompt del agente |

---

## Problemas activos

| Issue | Descripción | Prioridad |
|---|---|---|
| #010 | Migrar base de conocimiento de Supabase (la DB gratuita será eliminada) | Alta |

Ver historial completo en [ISSUES.md](ISSUES.md).

---

## Historial de versiones

| Versión | Fecha | Descripción |
|---|---|---|
| v1.4.1 | 7 Mar 2026 | Fix: prompt sin `promptType`, Think visible en respuestas |
| v1.4.0 | 7 Mar 2026 | Rediseño completo del prompt (Think-first, -70% tokens) |
| v1.3.0 | 6 Mar 2026 | Auditoría técnica completa, arquitectura verificada |
| v1.2.0 | 5 Mar 2026 | Documentación completa del workflow |
| v1.0.0 | Feb 2026 | Lanzamiento inicial |

Ver detalle completo en [CHANGELOG.md](CHANGELOG.md).

---

## Responsable

**NicoCalama** — Hotel Atankalama
