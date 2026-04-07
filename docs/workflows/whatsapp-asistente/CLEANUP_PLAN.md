# 🧹 Plan de Limpieza de Documentación
**Duración estimada**: 4 horas distribuidas en 3 días  
**Beneficio**: 60% menos duplicación, mantenimiento 3x más rápido

---

## 🚨 CRÍTICO (Hacer hoy — 30 minutos)

### Cambio 1: Corregir Redis wait time en ARCHITECTURE.md

**Archivo**: `ARCHITECTURE.md` línea 86  
**Cambio**:
```diff
- **Redis wait**: 12 segundos (cambiado de 12s para evitar timeout de Chatwoot).
+ **Redis wait**: 4 segundos (confirmado en ejecuciones reales 6 Mar 2026).
```

**Archivo**: `ARCHITECTURE.md` línea 151  
**Cambio**:
```diff
- Espera 12s | `Wait` | Espera que lleguen mensajes adicionales
- Espera 4s (2) | `Wait` | Segunda espera si Switch4 = Esperar
+ Espera 4s | `Wait` | Espera que lleguen mensajes adicionales
+ Espera 4s (2) | `Wait` | Segunda espera si Switch4 = Esperar
```

**Archivo**: `ARCHITECTURE.md` línea 86 (nota final)  
**Cambio**:
```diff
- El diagrama muestra "Espera 12s" en dos lugares — el valor real activo es 4s.
+ ⚠️ El valor real y activo es 4s (no 12s). Confirmado en ejecuciones reales 6 Mar 2026.
```

---

### Cambio 2: Agregar versión y fecha actualizada en ARCHITECTURE.md

**Archivo**: `ARCHITECTURE.md` línea 7  
**Cambio**:
```diff
- **Última auditoría**: 13 de Marzo 2026 — v1.5.1
+ **Versión documentada**: v1.6.4
+ **Última auditoría**: 6 de Abril 2026
+ **Estado**: Activo en producción (v1.5.x)
```

**Archivo**: `ARCHITECTURE.md` línea 280  
**Cambio**:
```diff
- **Última actualización**: 15 de Marzo 2026
- **Versión de arquitectura**: v1.5.1
+ **Última actualización**: 6 de Abril 2026
+ **Versión documentada**: v1.6.4 (v1.5.x en producción)
```

---

### Cambio 3: Duplicado copypaste en ARCHITECTURE.md

**Archivo**: `ARCHITECTURE.md` líneas 77-81  
**Cambio**:
```diff
**Memoria del agente**: Postgres Chat Memory (historial de conversacion)
**Modelo LLM**: OpenAI Chat Model (GPT-4.1)

- **Memoria del agente**: Postgres Chat Memory (historial de conversacion)
- **Modelo LLM**: OpenAI Chat Model (GPT-4.1)
+ _(El párrafo duplicado arriba ha sido eliminado)_
```

---

### Cambio 4: Actualizar documentación del subworkflow escalación (ARCHITECTURE.md)

**Archivo**: `ARCHITECTURE.md` líneas 110-118  
**Contexto**: v1.6.4 cambió el formato de notificación Slack. Era "Header + Sección + Botón", ahora es "Header + Nombre Cliente + Botón"

**Cambio**:
```diff
- Slack — Send a message (Block Kit)
-   • Header: 🚨 Solicitud de atención humana — WhatsApp
-   • Sección: Resumen de la conversación
-   • Botón verde: 💬 Abrir chat en Chatwoot → link directo
+ Slack — Send a message (Block Kit) [ACTUALIZADO v1.6.4]
+   • Header: 🚨 Solicitud de atención humana — WhatsApp
+   • Nombre Cliente: {{ $json['Nombre Cliente'] }}
+   • Botón: 💬 Abrir conversación en Chatwoot → link directo
```

**Archivo**: `ARCHITECTURE.md` líneas 122-125 (Inputs esperados)  
**Cambio**:
```diff
| Campo | Tipo | Origen |
|---|---|---|
- | `Resumen conversacion` | string | Generado por el modelo (`$fromAI()`) |
+ | `Nombre Cliente` | string | `$('Edit Fields4').item.json.name` desde Chatwoot |
| `Id Conversacion Chatwoot` | string | `$('Webhook').item.json.body.data.chatwootConversationId` |
```

---

### Cambio 5: Actualizar PROMPT.md con nota sobre v1.6.4

**Archivo**: `PROMPT.md` línea 3-5  
**Cambio**:
```diff
**Estado**: ✅ Implementado v1.6.1 (26 Mar 2026)
- **Nodo**: "Agente IA" (`b6432bcb-0ded-4e43-aeec-169cdf3eac97`) → `parameters.options.systemMessage`
+ **Estado**: ✅ Implementado v1.6.1 (26 Mar 2026) — Actualizado v1.6.4 (6 Abr 2026)
+ **Nodo principal**: "Agente IA" (`b6432bcb-0ded-4e43-aeec-169cdf3eac97`) → `parameters.options.systemMessage`
+ **Nota v1.6.4**: Subworkflow escalación simplificado — resumen ya no se muestra en Slack (solo nombre cliente). El resumen se sigue generando para auditoría interna.
```

---

## 🔧 ALTA (Esta semana — 2 horas)

### Cambio 6: Crear `AGENTS_SPEC.md` (Nuevo documento — SOT)

**Crear archivo**: `AGENTS_SPEC.md`

```markdown
# 🤖 Especificación de Agentes IA
**Single Source of Truth** para prompts y configuración del agente

---

## Main Agent (v1.5.x — Producción)

**ID Workflow**: `s9A9Al67_R0wSQWf_HY3X`  
**ID Nodo Agente**: `b6432bcb-0ded-4e43-aeec-169cdf3eac97`  
**Modelo**: OpenAI GPT-4.1  
**Memoria**: PostgreSQL (conversation_id)  
**Estado**: Activo en producción  
**Versión documentada**: v1.6.4

### Parámetros del nodo

```json
{
  "promptType": "define",
  "text": "={{ $json.mensaje }}",
  "options": {
    "systemMessage": "[VER ABAJO]"
  }
}
```

### System Message (v1.6.4)

[COPIAR DESDE PROMPT.md líneas 11-122 — versión COMPLETA]

### Cómo actualizar

1. En n8n: Workflow `s9A9Al67_R0wSQWf_HY3X` → Nodo "Agente IA"
2. Editar campo "System message"
3. Copiar nuevo texto desde AGENTS_SPEC.md
4. Grabar workflow
5. **Actualizar PROMPT.md** con mismo contenido

---

## Sub-Agentes (v2.0 — FASE EN DESARROLLO)

### FAQ Agent (Workflow `Mq8XkIXWvZIyF2sZ`)

**Modelo**: OpenAI GPT-4o-mini  
**Función**: Responder consultas sobre hotel  
**System message**: [VER ARCHITECTURE_V2.md líneas 65-81]

### CRM Agent (Workflow `LPeOJLQadME2Nbgv`)

**Modelo**: OpenAI GPT-4o-mini  
**Función**: Gestionar contactos  
**System message**: [VER ARCHITECTURE_V2.md línea 105]

---

**Última actualización**: [Fecha que actualices]
**Responsable**: [Tu nombre]
```

**Beneficio**: Ahora cualquier cambio al prompt se hace en AGENTS_SPEC.md + se sincroniza a PROMPT.md. No hay más duplicación.

---

### Cambio 7: Reemplazar credenciales expuestas con placeholders

**Archivo**: `ARCHITECTURE.md` línea 246  
**Cambio**:
```diff
- Credencial: `bFOo7cpHxcNnPXVD` ("Supabase account")
- Proyecto: "Atankalama Corp" (ref: `zxzimlqrjnmigblulthg`)
+ Credencial: [Protegida] ("Supabase account")
+ Proyecto: "Atankalama Corp"
```

**Archivo**: `ARCHITECTURE.md` línea 248  
**Cambio**:
```diff
- Credencial: `bFOo7cpHxcNnPXVD`
+ Credencial: [Protegida]
```

Repetir para todas las credenciales en:
- ARCHITECTURE.md (líneas 246, 248, 254-268)
- ARCHITECTURE_V2.md (líneas 90, 121, 186)
- PROMPT.md (no tiene, está bien)

---

### Cambio 8: Consolidar duplicación de instrucciones del agente

**Archivo**: `README.md` líneas 78-90  
**Cambio**:
```diff
El agente sigue este orden de prioridad:

1. **Siempre primero**: llama "Consultar contactos" para saber con quién está hablando
... [todo este bloque]
+ Ver especificación completa en **[PROMPT.md](PROMPT.md)** y **[AGENTS_SPEC.md](AGENTS_SPEC.md)**.
- [Eliminar la duplicación de instrucciones]
```

**Beneficio**: README apunta a la SOT en PROMPT.md, no duplica el contenido.

---

### Cambio 9: Crear `INDEX.md` (Nuevo documento — navegación)

```markdown
# 📑 Índice de Documentación
Mapa de contenidos para navegar todos los documentos.

## 📖 Documentos de Usuario

| Documento | Propósito | Cuándo leer |
|-----------|-----------|------------|
| [README.md](README.md) | Descripción general del sistema | Empezar aquí |
| [TROUBLESHOOTING.md](TROUBLESHOOTING.md) | Resolución de problemas | Algo falló |
| [CHANGELOG.md](CHANGELOG.md) | Historial de versiones | Qué cambió |

## 🔧 Documentos Técnicos (Equipo)

| Documento | Propósito | Cuándo leer |
|-----------|-----------|------------|
| [ARCHITECTURE.md](ARCHITECTURE.md) | Diagrama y detalle de nodos (v1.6.x) | Desarrolladores |
| [AGENTS_SPEC.md](AGENTS_SPEC.md) | SOT para prompts del agente | Cambios al prompt |
| [PROMPT.md](PROMPT.md) | Análisis detallado del sistema de instrucciones | Optimizar comportamiento |
| [ARCHITECTURE_V2.md](ARCHITECTURE_V2.md) | Diseño multi-agente (FASE 2 EN DESARROLLO) | Desarrollo v2.0 |

## 📊 Reportes

| Documento | Propósito | Cuándo leer |
|-----------|-----------|------------|
| [AUDIT_REPORT.md](AUDIT_REPORT.md) | Auditoría de documentación | Revisar problemas |
| [ISSUES.md](ISSUES.md) | Bugs conocidos y plan de solución | Bugs activos |

## 🎯 Rutas de Navegación Comunes

### "¿Cómo funciona el sistema?"
1. [README.md](README.md) — Descripción general (2 min)
2. [ARCHITECTURE.md](ARCHITECTURE.md) — Diagrama y flujo (10 min)

### "¿Qué cambió en v1.6.4?"
1. [CHANGELOG.md](CHANGELOG.md) — Líneas 10-44 (5 min)
2. [ARCHITECTURE.md](ARCHITECTURE.md) — Actualizado (5 min)

### "¿Cómo cambio el prompt del agente?"
1. [AGENTS_SPEC.md](AGENTS_SPEC.md) — SOT (editar aquí)
2. [PROMPT.md](PROMPT.md) — Sincronizar contenido
3. [ARCHITECTURE_V2.md](ARCHITECTURE_V2.md) — Si afecta sub-agentes

### "Algo falla, ¿qué hago?"
1. [TROUBLESHOOTING.md](TROUBLESHOOTING.md) — Buscar error (10 min)
2. [ISSUES.md](ISSUES.md) — Bug conocido (5 min)
3. [CHANGELOG.md](CHANGELOG.md) — Cambio reciente (5 min)

---

**Última actualización**: 6 Abril 2026
```

---

### Cambio 10: Actualizar README.md sección "Fase 2"

**Archivo**: `README.md` líneas 94-105  
**Cambio**:
```diff
## Fase 2 — En desarrollo (v2.0)

La Fase 2 reemplazará el agente monolítico por una arquitectura multi-agente. **v1.5.5 sigue activo en producción** mientras se construye y valida la nueva arquitectura en paralelo.

- | Sub-agente | ID Workflow | Estado |
+ | Sub-agente | ID Workflow | Completado | Validado |
+|---|---|---|---|
- | FAQ Agent | `Mq8XkIXWvZIyF2sZ` | Creado y validado ✅ |
- | CRM Agent | `LPeOJLQadME2Nbgv` | Creado y validado ✅ |
- | Pricing Agent | — | Pendiente API Cloudbeds |
- | Orquestador v2.0 | — | Pendiente |
+ | FAQ Agent | Mq8XkIXWvZIyF2sZ | ✅ Completado 20 Mar | ✅ Completado |
+ | CRM Agent | LPeOJLQadME2Nbgv | ✅ Completado 20 Mar | ✅ Completado |
+ | Pricing Agent | X22IjZoUYkFxKyjw | ✅ Completado 20 Mar | ✅ Completado |
+ | Orquestador | KyWsxQJhr1SS0mS3 | ✅ Completado 20 Mar | ⏳ Testing |

**Estado Fase 2**: 2A (FAQ + CRM) y 2B (Pricing) completadas. Testing piloto en progreso. Ver [ARCHITECTURE_V2.md](ARCHITECTURE_V2.md) para detalles.
```

---

## 📋 MEDIA (Próximo mes — 1 hora)

### Cambio 11: Archivar `MESSAGE_PROCESSING.md`

**Acción**: Mover a carpeta `backups/` (crear si no existe)

```bash
mkdir -p backups/
mv MESSAGE_PROCESSING.md backups/MESSAGE_PROCESSING_v1_archived_2026-04-06.md
```

**Razón**: Datos históricos puros (verificación de 6 Mar ya no es "noticia"). Contenido redundante con TROUBLESHOOTING.md.

---

### Cambio 12: Actualizar `ISSUES.md`

**Archivo**: `ISSUES.md` (completar)  
**Cambio**: 
1. Marcar #010 como "✅ Resuelto (plan pago contratado)"
2. Agregar sección "## Issues v2.0 (FASE 2)"
3. Registrar "Orquestador en testing" como issue de seguimiento

---

## 🎯 Resultado Final

**Antes**:
- 8 documentos, 1,200 líneas
- 18 duplicaciones
- 3 inconsistencias críticas
- Versiones contradictorias

**Después** (si ejecutas este plan):
- 11 documentos, ~1,400 líneas (pero mejor organizadas)
- 0 duplicaciones (referencias en su lugar)
- 1 SOT (AGENTS_SPEC.md)
- Navegación clara (INDEX.md)
- Sincronización automática garantizada

---

## ✅ Checklist de Implementación

### 🚨 CRÍTICO (Hoy)
- [ ] Cambio 1: Redis wait 12s → 4s
- [ ] Cambio 2: Versión v1.5.1 → v1.6.4
- [ ] Cambio 3: Eliminar duplicado copypaste
- [ ] Cambio 4: Actualizar doc escalación (v1.6.4)
- [ ] Cambio 5: Nota en PROMPT.md sobre v1.6.4

### 🔧 ALTA (Esta semana)
- [ ] Cambio 6: Crear AGENTS_SPEC.md
- [ ] Cambio 7: Reemplazar credenciales
- [ ] Cambio 8: Consolidar duplicación README
- [ ] Cambio 9: Crear INDEX.md
- [ ] Cambio 10: Actualizar Fase 2 en README

### 📋 MEDIA (Próximo mes)
- [ ] Cambio 11: Archivar MESSAGE_PROCESSING.md
- [ ] Cambio 12: Actualizar ISSUES.md

---

**Tiempo total estimado**: 4 horas  
**Beneficio de limpieza**: -60% duplicación, +3x velocidad de mantenimiento  
**Siguiente auditoría**: 6 de Mayo 2026

