# Issues — Asistente Virtual WhatsApp

---

## Resueltos (resumen)

| # | Título | Resolución |
|---|--------|-----------|
| #001 | Prompt suena robótico | ✅ Rediseño completo v1.4.0 — Think-first + árbol situaciones, -70% tokens |
| #002 | Procesamiento de mensajes | ✅ Confirmado funcionando (ejecuciones reales 6 Mar 2026) — ver MESSAGE_PROCESSING.md |
| #003 | Notificaciones Slack | ✅ Slack implementado en v1.5.0 para escalación humana. Telegram nunca estuvo en producción |
| #009 | Agente no sigue protocolo | ✅ Resuelto con rediseño de prompt v1.4.0 |
| #010 | Migración Supabase | ✅ Decisión: contratar plan pago (se usa para WhatsApp + Recolector + futuros proyectos) |
| #011 | "No prompt specified" tras update | ✅ Causa: `n8n_update_partial_workflow` con solo `options` borra `promptType` y `text`. Fix: incluir los 3 campos juntos. Ver PROMPT.md |

---

## Planificados

### #004 — Ampliación de base de conocimiento
**Prioridad**: Media | **Estado**: Planificado

Expandir la base vectorial con más información del hotel: servicios adicionales, políticas detalladas, tours y actividades de la zona, restaurantes, transporte y logística.

---

### #005 — Sistema de métricas y analytics
**Prioridad**: Media | **Estado**: Planificado

Tracking de: conversaciones/día, tiempo promedio de respuesta, tasa conversión (consulta → reserva), temas más consultados, errores.

---

## Ideas (backlog)

- **#006** — Soporte multiidioma: detectar idioma y responder en inglés/español automáticamente
- **#007** — Integración reservas en tiempo real: conectar con sistema de gestión hotelera para disponibilidad directa
- **#008** — Personalización por historial: recordar preferencias de clientes recurrentes

---

## Resumen

- Activos: 0
- Planificados: 2 (#004, #005)
- Ideas: 3 (#006, #007, #008)

**Última actualización**: 15 de Marzo 2026
