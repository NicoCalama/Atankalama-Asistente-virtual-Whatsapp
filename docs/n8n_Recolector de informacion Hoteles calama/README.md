# Recolector de Información Hotelera Calama

## Descripción

Workflow automatizado que monitorea semanalmente los precios de la competencia hotelera en Calama, Chile via Booking.com. Genera un reporte estratégico con análisis de IA (Claude) y lo envía por email al equipo de ventas.

**Versión**: 1.1.0 (activo en producción — 15 Mar 2026)
**Estado**: Todos los workflows activos. Primer reporte enviado exitosamente.
**Proyecto independiente** — no tiene conexión con el workflow de WhatsApp Asistente.

---

## Objetivo

Dar al equipo de ventas de Hotel Atankalama y Hotel Atankalama Inn información actualizada sobre el mercado hotelero en Calama para:
- Ajustar precios competitivamente
- Detectar oportunidades (competidores agotados)
- Recibir recomendaciones accionables de pricing

---

## Stack tecnológico

| Servicio | Rol |
|---------|-----|
| **n8n** (self-hosted, EasyPanel) | Orquestador de workflows |
| **Apify** — `voyager/booking-scraper` | Scraping de precios en Booking.com |
| **Supabase** (PostgreSQL) | Almacenamiento de historial de precios |
| **Claude API** (claude-sonnet-4-6) | Análisis estratégico y generación de reporte |
| **Gmail** | Envío del reporte semanal al equipo |
| **Slack** | Integración futura (v2) |

---

## Hoteles monitoreados

| Hotel | Tipo |
|-------|------|
| Hotel Atankalama | Propio (referencia) |
| Hotel Atankalama Inn | Propio (referencia) |
| Geotel Calama | Competidor |
| Park Hotel Calama | Competidor |
| Ibis Calama | Competidor |
| ibis budget Calama | Competidor |
| Hotel Agua del Desierto | Competidor |

**Tipos de habitación**: Doble, Triple, Cuádruple
**Ventana de precios**: próximos 7 días desde la fecha de scraping

---

## Workflows en n8n

| Componente | ID n8n | Estado |
|-----------|--------|--------|
| A — Scraping Semanal | `QBDVpsKWGTHZZikE` | Activo producción (7 hoteles) |
| Subworkflow — Análisis y Reporte | `YXDogRLJrprijeiV` | Activo producción (21 nodos) |
| B — Disparador Manual | `SbI0cfUlQvtLWvxZ` | Activo |
| [UTIL] Postear Botón Slack | `yyaxraPUve3ApgGl` | Manual (ejecutar cuando se quiera postear) |

## Arquitectura resumida

```
[Workflow A: Recolector]  →  [Supabase hotel_prices]
       Lunes 6:00 AM               ↓
                          [Subworkflow: Análisis y Reporte]
                            Claude AI + Gmail semanal
```

Ver [ARCHITECTURE.md](./ARCHITECTURE.md) para el detalle técnico completo.

---

## Presupuesto mensual estimado

| Servicio | Costo/mes |
|---------|-----------|
| Apify Starter | ~$38 USD |
| Claude API | ~$0.50 USD |
| Supabase (existente) | $0-25 USD |
| Gmail / n8n | $0 |
| **TOTAL** | **~$38-65 USD/mes** |

---

## Cómo ejecutar manualmente

Para disparar el análisis sin esperar al lunes:

**Opción 1 — Botón Slack**: En el canal `#ventas-atankalama`, hacer click en el botón "Ejecutar Reporte" (bookmark fijo). Abre el formulario de Workflow B.

**Opción 2 — Formulario directo**: Ir a n8n → Workflow B `SbI0cfUlQvtLWvxZ` → "Test workflow".

El pipeline corre en segundo plano (~10 min) y el email llega al finalizar.

---

## Estructura de carpetas

```
docs/n8n_Recolector de informacion Hoteles calama/
├── README.md          ← este archivo
├── PLAN.md            ← plan de implementación con fases
├── ARCHITECTURE.md    ← arquitectura técnica detallada
├── CHANGELOG.md       ← historial de versiones
└── supabase/
    └── schema.sql     ← SQL para crear las 3 tablas

workflows/recolector/  ← (se crea al exportar desde n8n)
├── recolector-precios.json
├── disparador-standalone.json
└── subworkflow-analisis.json
```

---

## Seguridad

- Las URLs de Booking.com, API keys y emails reales **nunca** se suben al repositorio
- Usar placeholders `[URL protegida]` y `[CONFIGURAR EN N8N]` en todos los archivos
- Las credenciales se configuran directamente en el gestor de credenciales de n8n
- Ver checklist pre-commit en [PLAN.md](./PLAN.md)
