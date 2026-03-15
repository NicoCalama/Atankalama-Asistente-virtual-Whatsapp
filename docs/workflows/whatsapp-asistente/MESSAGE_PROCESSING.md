# Procesamiento de Mensajes — Hechos Confirmados

**Estado**: ✅ Verificado con ejecuciones reales (#1229, #1232, #1233) el 6 de Marzo 2026

---

## Comportamiento confirmado

### Acumulación de mensajes (Redis)
- **Redis push**: almacena cada mensaje como JSON `{message, sessionID, date_time}` en lista por número de teléfono
- **Redis get**: devuelve el array completo correctamente
- **Switch4**: si el último mensaje tiene < 4s → espera, si > 4s → procesa
- **Resultado real**: múltiples mensajes enviados en ráfaga se acumulan y el agente recibe el texto combinado en un solo campo `mensaje`
- **Redis wait**: cambiado de 12s → **4s** (evita timeout de Chatwoot)

### Línea de voz
- **Switch**: detecta audio via `body.conversation.messages[0].attachments[0]?.file_type`
- **descargar audio**: usa `body.attachments[0].data_url`
- **Whisper**: transcripción correcta y rápida (~5s para audio corto)
- **La voz NO pasa por Redis** — se procesa inmediatamente, sin acumulación

### Tiempos reales
- Texto (con acumulación 4s): ~24s total
- Voz: ~22s total (Whisper + agente)
- Eventos filtrados (status updates, outgoing): ~0s — son correctamente ignorados por el nodo `If`

### Ejecuciones 0s
No son mensajes perdidos. Son eventos de Chatwoot (status updates, outgoing) filtrados correctamente.

---

**Última actualización**: 6 de Marzo 2026
