# Issues — Asistente Virtual WhatsApp

---

## Resueltos (resumen)

| # | Título | Resolución |
|---|--------|-----------|
| #001 | Prompt suena robótico | ✅ Rediseño completo v1.4.0 — Think-first + árbol situaciones, -70% tokens |
| #002 | Procesamiento de mensajes | ✅ Confirmado funcionando (ejecuciones reales 6 Mar 2026) |
| #003 | Notificaciones Slack | ✅ Slack implementado v1.5.0, simplificado v1.6.4 (solo nombre cliente) |
| #009 | Agente no sigue protocolo | ✅ Resuelto con rediseño de prompt v1.4.0 |
| #010 | Migración Supabase | ✅ Plan pago contratado (v1.6.0+), se usa para WhatsApp + Recolector + futuros proyectos |
| #011 | "No prompt specified" tras update | ✅ Causa: `n8n_update_partial_workflow` con solo `options` borra `promptType` y `text`. Fix: incluir los 3 campos juntos. Ver AGENTS_SPEC.md |
| #012 | Duplicación y desactualización de documentación | ✅ Auditoría completada 6 Abr 2026. Planes de limpieza en CLEANUP_PLAN.md |

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

---

## Issues v2.0 (FASE 2 - EN DESARROLLO)

### #200 — Testing y validación Orquestador v2.0
**Prioridad**: Alta | **Estado**: ⏳ En progreso | **Responsable**: Dev team

Testing piloto con números específicos antes de cutover.

### #201 — Investigar Cloudbeds API con habitaciones isPrivate
**Prioridad**: Media | **Estado**: ⏳ Bloqueante para Fase 3 | **Contexto**: Atankalama Inn tiene `isPrivate: true`

Investigar si `postReservation` funciona con estas habitaciones antes de implementar Fase 3 (reservas con cobro).

---

## Resumen

- Resueltos: 12
- Planificados: 2 (#004, #005)
- Ideas: 3 (#006, #007, #008)
- v2.0 en progreso: 2 (#200, #201)

**Última actualización**: 6 de Abril 2026
