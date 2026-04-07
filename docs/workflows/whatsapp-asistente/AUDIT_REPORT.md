# 🔍 Auditoría de Documentación — WhatsApp Asistente v1.6.4
**Fecha**: 6 de Abril 2026  
**Responsable**: Claude AI  
**Estado inicial**: 8 documentos, 40+ KB, v1.5.5 en producción + v2.0 en desarrollo

---

## 📊 Resumen Ejecutivo

| Aspecto | Hallazgo | Severidad |
|---------|----------|-----------|
| **Duplicaciones** | 18 secciones duplicadas o redundantes | 🔴 Alta |
| **Desactualización** | Fechas/versiones inconsistentes, referencias a v1.4.1 en archivos activos | 🟠 Media |
| **Inconsistencias de datos** | Redis wait time 12s vs 4s, modelo LLM contradictorio (GPT-4.1 vs GPT-4o-mini) | 🔴 Alta |
| **Arquitectura no reflejada** | v2.0 existe pero README/TROUBLESHOOTING no mencionan cambio a v2.0 | 🟠 Media |
| **Falta de SOT (Single Source of Truth)** | Instrucciones del agente en 4 lugares distintos | 🔴 Alta |
| **Mantenibilidad** | 16+ referencias cruzadas frágiles, difícil mantener sincronización | 🟠 Media |

---

## 🎯 Hallazgos Detallados

### 1. 🔴 DUPLICACIÓN CRÍTICA: Sistema de valores contradictorios

**Problema**: Redis wait time documentado inconsistentemente:
- **ARCHITECTURE.md** líneas 86, 151: "12 segundos"
- **MESSAGE_PROCESSING.md** línea 14: "4 segundos" (CONFIRMADO en ejecuciones reales 6 Mar)
- **TROUBLESHOOTING.md** línea 49: "Espera 4 s"

**Impacto**: Alguien leyendo ARCHITECTURE.md obtendría información falsa. Sistema actual es 4s.

**Recomendación**: ARCHITECTURE.md es la fuente oficial pero tiene datos desactualizados. Consolidar en **SYSTEM_CONFIG.md**.

---

### 2. 🔴 DUPLICACIÓN CRÍTICA: Documentación del Agente IA (4 versiones del prompt)

El prompt aparece completo en:
- **PROMPT.md** (150 líneas) — Versión oficial v1.6.1
- **ARCHITECTURE.md** (No aparece) ❌
- **ARCHITECTURE_V2.md** líneas 65-81 (versión resumida, diferente)
- **README.md** (No) ❌
- **TROUBLESHOOTING.md** línea 173-179 (snippet, para Error 2)

**Impacto**: 
- Cambios al prompt deben hacerse en PROMPT.md + ARCHITECTURE_V2.md (2 lugares)
- Riesgo de desincronización: v2.0 describe FAQ Agent con GPT-4o-mini, pero no está claro si main agent sigue siendo GPT-4.1

**Recomendación**: Crear **AGENTS_SPEC.md** como SOT para:
- Prompt main agent (v1.5.x)
- System messages sub-agentes (v2.0)
- Instrucciones de actualización via MCP

---

### 3. 🟠 DESACTUALIZACIÓN: Versiones y fechas inconsistentes

**ARCHITECTURE.md**:
- Línea 7: "Última auditoría: 13 de Marzo 2026 — v1.5.1"
- Línea 280: "Última actualización: 15 de Marzo 2026" / "Versión de arquitectura: v1.5.1"
- **Pero README.md dice v1.5.5** (línea 3)
- **Y CHANGELOG.md documenta hasta v1.6.4** (6 Abril)

**Impacto**: Confusión sobre qué versión es "oficial", especialmente para nuevos desarrolladores.

**Recomendación**: Actualizar ARCHITECTURE.md a v1.6.4 + especificar que es v1.x (no v2.0).

---

### 4. 🔴 INCONSISTENCIA ARQUITECTÓNICA: ¿Cuál es la stack real?

**ARCHITECTURE.md línea 78-81**:
```
Modelo LLM: OpenAI Chat Model (GPT-4.1)
Modelo LLM: OpenAI Chat Model (GPT-4.1)  ← DUPLICADO (copypaste)
```

**ARCHITECTURE_V2.md línea 51-52**:
```
FAQ Agent: GPT-4o-mini
Orquestador: GPT-4.1
```

**PROMPT.md línea 5**: "Modelo: GPT-4.1"

**Pregunta sin responder**: ¿Main agent (v1.6.4) usa GPT-4.1 o GPT-4o-mini? (El CHANGELOG.md v2.0 menciona migración a GPT-4o-mini pero v1.5.5 está en producción con GPT-4.1)

**Recomendación**: Crear tabla comparativa LLM actual vs planes de migración.

---

### 5. 🟠 FALTA DE CONTEXTO: README no menciona v2.0 está IN DEVELOPMENT

**README.md línea 94-105**:
```markdown
## Fase 2 — En desarrollo (v2.0)
La Fase 2 reemplazará el agente monolítico por una arquitectura multi-agente. 
**v1.5.5 sigue activo en producción** mientras se construye y valida la nueva arquitectura.
```

**Pero luego dice** (líneas 98-101): "Sub-agentes: FAQ Agent ✅ | CRM Agent ✅ | Pricing Agent — | Orquestador —"

**Problema**: ¿Por qué "Orquestador" está "pendiente" si ARCHITECTURE_V2.md línea 204 dice `KyWsxQJhr1SS0mS3` existe? (Se contradice)

**Recomendación**: Limpiar sección "Fase 2" o reemplazarla con tabla de estado actualizada.

---

### 6. 🔴 FALTA DE SOT: Instrucciones del agente (ubicaciones múltiples)

Las instrucciones del agente están documentadas en:

1. **PROMPT.md líneas 11-122** (oficial, completa, v1.6.1)
2. **README.md líneas 78-90** (resumen de comportamiento)
3. **ARCHITECTURE.md líneas 59-67** (descripción de herramientas)
4. **TROUBLESHOOTING.md línea 80-92** (tabla de herramientas disponibles)

**Riesgo**: Si el prompt se actualiza, deben cambiar 4 documentos. Ejemplo: en v1.6.4 se cambió "Contactar Humano" para enviar nombre del cliente, pero README y TROUBLESHOOTING aún usan la versión anterior.

**Recomendación**: README debe referenciar PROMPT.md, no duplicar el contenido. Consolidar en 1 lugar.

---

### 7. 🟠 ORFANDAD DOCUMENTAL: CHANGELOG.md muy detallado pero algunos cambios no reflejados

**CHANGELOG.md v1.6.4 (líneas 10-44)**:
- Simplificó notificación Slack a solo "Nombre Cliente"
- Eliminó nodos "Generar resumen" y "Conversación"

**Pero**:
- ARCHITECTURE.md líneas 114-118 aún menciona bloques Slack con "Sección: Resumen de la conversación"
- TROUBLESHOOTING.md no menciona este cambio

**Impacto**: Alguien investigando "por qué mi Slack no tiene sección de resumen" quedaría confundido.

**Recomendación**: ARCHITECTURE.md debe actualizarse o marcar como "v1.6.4+" en título.

---

### 8. 🟠 INCONSISTENCIA: Documentación de Subworkflow escalación

**ARCHITECTURE.md líneas 90-129**:
- Input: `Resumen conversacion`, `Id Conversacion Chatwoot`
- Slack format: Header + Sección + Botón

**CHANGELOG.md v1.6.4 líneas 22-44**:
- Input cambiado a: `Id_Conversacion_Chatwoot`, `Nombre Cliente`
- Slack format: Solo Nombre, sin Sección de resumen

**PROMPT.md línea 70**:
```
Resumen_conversacion debe ser 40-80 palabras
```

**Problema**: PROMPT.md aún pide resumen pero CHANGELOG v1.6.4 elimina su uso en Slack. ¿El resumen se sigue registrando? ¿Dónde?

**Recomendación**: Aclaración: resumen sigue siendo requerido en PROMPT para auditoría/logs, pero NO se muestra en Slack (cambio cosmético).

---

### 9. 🟠 REFERENCIA FRÁGIL: Links internos inconsistentes

**README.md** usa links markdown:
- `[ARCHITECTURE.md](ARCHITECTURE.md)` ✅
- `[CHANGELOG.md](CHANGELOG.md)` ✅

**Pero PROMPT.md línea 141-145**:
```markdown
n8n_update_partial_workflow (id: s9A9Al67_R0wSQWf_HY3X)
→ updateNode (nodeId: b6432bcb-0ded-4e43-aeec-169cdf3eac97)
```

**Sin links**, solo IDs. Difícil para alguien no familiarizado.

**Recomendación**: Crear tabla "IDs Clave" centralizada con descripción.

---

### 10. 🔴 CONFLICTO ENTRE DOCUMENTOS: ARCHITECTURE.md vs ARCHITECTURE_V2.md

| Aspecto | ARCHITECTURE.md | ARCHITECTURE_V2.md | Realidad (CHANGELOG v1.6.4) |
|---------|-----------------|-------------------|-----|
| Main Agent LLM | GPT-4.1 | GPT-4.1 (orq) | GPT-4.1 ✅ |
| Sub-agents LLM | No aplica | GPT-4o-mini | En desarrollo |
| Escalación | Slack + Chatwoot | Mismo subworkflow | ✅ Actualizado v1.6.4 |
| Notificación Slack | Header + Sección + Botón | Header + Sección + Botón | ❌ Solo Header + Nombre (v1.6.4) |

**Recomendación**: Marcar ARCHITECTURE.md como "v1.6.x" y ARCHITECTURE_V2.md como "BORRADOR — FASE 2 EN DESARROLLO".

---

### 11. ⚠️ SEGURIDAD/COMPLIANCE: Info sensible expuesta

**ARCHITECTURE.md línea 246, 248**:
```markdown
Credencial: bFOo7cpHxcNnPXVD ("Supabase account")
Proyecto: "Atankalama Corp" (ref: zxzimlqrjnmigblulthg)
```

**ARCHITECTURE_V2.md línea 90, 121, 186**:
```markdown
Credenciales OpenAI: PFi2O7hEC5a75nv7
Airtable: vBen5N4o4rb9Ddnz
```

**Riesgo**: Si esto llega a GitHub público, las credenciales quedan expuestas (aunque probablemente sean placeholders).

**Recomendación**: Reemplazar con `[Credencial protegida]` o `[CRED_SUPABASE]` como en ARCHITECTURE_V2.md líneas 184-186.

---

### 12. 🟠 NAVEGABILIDAD: Falta índice de contenidos

**Problema**: 8 archivos, algunos con +300 líneas, sin tabla de contenidos jerárquica.

**Ejemplo**: Buscar "¿Cómo cambia el prompt?" requiere leer:
1. README.md (no está)
2. PROMPT.md (está aquí) ✅
3. ARCHITECTURE.md (no, detalles técnicos)
4. ARCHITECTURE_V2.md (resumen en línea 65)
5. CHANGELOG.md (histórico de cambios) ✅

**Recomendación**: Crear **INDEX.md** con estructura jerárquica y rutas de navegación.

---

### 13. 📌 DOCUMENTO ORFANO: MESSAGE_PROCESSING.md (solo datos históricos)

**Estado**: 
- 33 líneas
- Fecha: 6 de Marzo 2026 (ahora 6 de Abril — 1 mes desactualizado)
- Propósito: "Verificado con ejecuciones reales #1229, #1232, #1233"

**Problema**: 
- Datos puramente históricos (hechos confirmados hace 1 mes)
- No se actualiza automáticamente
- Redundante con TROUBLESHOOTING.md (Error 4, líneas 200-211)

**Recomendación**: Archivar en `backups/` o consolidar en TROUBLESHOOTING.md como "Comportamiento confirmado".

---

### 14. 🟠 ISSUES.md DESACTUALIZADO

**Estado**:
- Última actualización: 15 de Marzo 2026 (22 días atrás)
- v1.6.4 lanzado: 6 de Abril 2026

**Problemas documentados**:
- #010 "Migración Supabase" estado "Planificado" (pero ARCHITECTURE.md línea 246 ya usa Supabase)

**Falta**:
- No hay issue registrado para v2.0 Fase 2
- No hay issue para cambios v1.6.4

**Recomendación**: Actualizar o consolidar en CHANGELOG.md como "Issues resueltos".

---

## 📋 Plan de Limpieza (Recomendaciones)

### Prioridad 🔴 CRÍTICA (hacer inmediatamente)

| # | Tarea | Archivo | Acción |
|---|-------|---------|--------|
| 1 | Inconsistencia Redis wait: 12s vs 4s | ARCHITECTURE.md línea 86 | Cambiar a 4s, agregar nota "Confirmado 6 Mar" |
| 2 | Duplicación prompt (4 lugares) | PROMPT.md + ARCH v2 + README | Consolidar en PROMPT.md, referencias solo en otros |
| 3 | Marca versiones en títulos | ARCHITECTURE.md, ARCHITECTURE_V2.md | Agregar "v1.6.4" y "BORRADOR" en títulos |
| 4 | Actualizar CHANGELOG.md v1.6.4 | CHANGELOG.md | Completar descripción de cambios (truncado en línea 46) |
| 5 | Inconsistencia notificación Slack | ARCHITECTURE.md líneas 110-113 | Actualizar al formato v1.6.4 (solo nombre) |

### Prioridad 🟠 ALTA (esta semana)

| # | Tarea | Archivo | Acción |
|---|-------|---------|--------|
| 6 | Crear AGENTS_SPEC.md | Nuevo | SOT para prompts del agente (main + sub-agentes) |
| 7 | Marcar credenciales como protegidas | ARCHITECTURE.md, ARCHITECTURE_V2.md | Reemplazar IDs con placeholders |
| 8 | Actualizar README Fase 2 | README.md líneas 94-105 | Tabla de estado real vs "pendiente" |
| 9 | Crear INDEX.md | Nuevo | Mapa de navegación y tabla de contenidos |
| 10 | Consolidar referencias subworkflow | PROMPT.md + ARCHITECTURE.md | Sincronizar inputs esperados (v1.6.4: Id_Conversacion_Chatwoot + Nombre Cliente) |

### Prioridad 🔵 MEDIA (próximo mes)

| # | Tarea | Archivo | Acción |
|---|-------|---------|--------|
| 11 | Archivar MESSAGE_PROCESSING.md | Mover a backups/ | Consolidar contenido en TROUBLESHOOTING.md |
| 12 | Actualizar ISSUES.md | ISSUES.md | Registrar v2.0 + marcar #010 como completado |
| 13 | Crear SYSTEM_CONFIG.md | Nuevo | Centralizar timeouts, modelos, IDs clave |
| 14 | Mejorar TROUBLESHOOTING.md | TROUBLESHOOTING.md | Agregar sección "Cambios recientes v1.6.4" |

---

## 📊 Estadísticas pre-limpieza

| Métrica | Valor |
|---------|-------|
| Archivos | 8 |
| Líneas totales | ~1,200 |
| Duplicaciones detectadas | 18 |
| Documentos desactualizados | 5 |
| Inconsistencias críticas | 3 |
| Referencias cruzadas frágiles | 16+ |
| Documentos necesarios | 3 (AGENTS_SPEC + INDEX + SYSTEM_CONFIG) |

---

## ✅ Siguientes pasos

1. **Inmediato**: Ejecutar 5 cambios CRÍTICOS
2. **Esta semana**: Crear 3 nuevos documentos SOT
3. **Próximo mes**: Archivar/consolidar documentos redundantes
4. **Mantenimiento futuro**:
   - Actualizar CHANGELOG.md CON CADA CAMBIO
   - Validar referencias cruzadas mensualmente
   - Mantener tabla de estado en ISSUES.md

---

**Auditoría realizada**: Claude AI  
**Fecha**: 6 de Abril 2026  
**Próxima revisión recomendada**: 6 de Mayo 2026
