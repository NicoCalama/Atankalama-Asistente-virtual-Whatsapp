# 📑 Índice de Documentación — WhatsApp Asistente
Mapa de contenidos y rutas de navegación

---

## 📖 Documentos Principales

### Para Empezar
| Documento | Líneas | Propósito | Tiempo |
|-----------|--------|----------|--------|
| [README.md](README.md) | 150 | Descripción general del sistema | 2 min |
| [TROUBLESHOOTING.md](TROUBLESHOOTING.md) | 370 | **LEER PRIMERO SI ALGO FALLA** | 10-15 min |

### Documentación Técnica
| Documento | Líneas | Propósito | Audiencia | Cuándo |
|-----------|--------|----------|-----------|--------|
| [ARCHITECTURE.md](ARCHITECTURE.md) | 280 | Diagrama de nodos + flujo (v1.6.4) | Desarrolladores | Entender cómo funciona |
| [AGENTS_SPEC.md](AGENTS_SPEC.md) | 240 | **SOT para prompts del agente** | Developers/Prompt engineers | Cambiar instrucciones |
| [PROMPT.md](PROMPT.md) | 150 | Análisis detallado del sistema | Product | Optimizar comportamiento |
| [ARCHITECTURE_V2.md](ARCHITECTURE_V2.md) | 380 | Multi-agente (FASE 2 - BORRADOR) | Dev team | Desarrollo v2.0 |

### Registros y Reportes
| Documento | Propósito | Cuándo |
|-----------|----------|--------|
| [CHANGELOG.md](CHANGELOG.md) | Historial de todas las versiones | Ver qué cambió |
| [ISSUES.md](ISSUES.md) | Bugs conocidos + estado | Buscar issue activo |
| [AUDIT_REPORT.md](AUDIT_REPORT.md) | Auditoría de documentación (6 Abr) | Entender problemas técnicos |
| [CLEANUP_PLAN.md](CLEANUP_PLAN.md) | Plan de implementación de fixes | Ejecutar cambios |

---

## 🎯 Rutas de Navegación por Caso de Uso

### 1️⃣ "Algo está roto, ¿qué hago?"
```
TROUBLESHOOTING.md (Error 1-8)
  ├─ Qué síntoma específico?
  ├─ Leer "Cómo verificar"
  └─ Leer "Cómo resolver"
  
Si error ≈ bug conocido:
  ├─ ISSUES.md (bugs resueltos)
  └─ CHANGELOG.md (cuándo se resolvió)
```

**Tiempo**: 10-15 min

---

### 2️⃣ "¿Qué cambió en v1.6.4?"
```
CHANGELOG.md
  ├─ Líneas 10-44: v1.6.4 (notificación Slack simplificada)
  ├─ Líneas 47-100: v1.6.3 (resumen LLM)
  └─ Líneas [continuar]: versiones anteriores

Cambios impactados:
  ├─ ARCHITECTURE.md líneas 110-118 (Slack)
  ├─ PROMPT.md línea 70 (resumen)
  └─ README.md líneas 94-105 (Fase 2 status)
```

**Tiempo**: 5-10 min

---

### 3️⃣ "¿Cómo funciona el sistema?"
```
README.md (descripción general)
  ↓ (5 min)
ARCHITECTURE.md (diagrama + nodos)
  ├─ Líneas 11-75: diagrama y flujo
  ├─ Líneas 132-195: detalle de nodos
  └─ Líneas 198-233: flujos de datos por caso
  ↓ (10 min)
TROUBLESHOOTING.md líneas 19-72 (arquitectura visual)
  ↓ (5 min)
Total entendimiento: 20 min
```

---

### 4️⃣ "Quiero cambiar el prompt del agente"
```
AGENTS_SPEC.md [SOT — editar aquí PRIMERO]
  ├─ Líneas 30-130: System message (copiar/editar)
  └─ Líneas 132-144: instrucciones para actualizar
  
Luego sincronizar:
  ├─ PROMPT.md (mismo contenido)
  └─ ARCHITECTURE_V2.md si afecta sub-agentes

Validar cambios:
  ├─ CHANGELOG.md (registrar cambio)
  └─ COMMIT con cambios
```

**Tiempo**: 20 min (incluyendo testing)

---

### 5️⃣ "Estoy desarrollando v2.0"
```
ARCHITECTURE_V2.md (descripción completa)
  ├─ Líneas 1-43: visión general
  ├─ Líneas 45-201: especificación sub-agentes
  ├─ Líneas 204-350: problemas Cloudbeds API
  └─ Líneas 369-377: relación con v1.5.5
  
Detalles específicos:
  ├─ AGENTS_SPEC.md (prompts sub-agentes)
  └─ PROMPT.md (main agent de referencia)

Testing:
  ├─ TROUBLESHOOTING.md (leyendo errores en ejecuciones)
  └─ CHANGELOG.md (viendo cambios recientes)
```

**Tiempo**: 30+ min (complejo)

---

### 6️⃣ "¿Cuál es el estado actual?"
```
README.md líneas 94-105 (Fase 2 table)
  ↓
ARCHITECTURE_V2.md líneas 273-281 (plan implementación)
  ↓
ISSUES.md (bugs conocidos)
  ↓
CHANGELOG.md (cambios recientes)
```

**Tiempo**: 5 min

---

## 📊 Mapa de Versiones

```
Producción actual (v1.5.x):
  └─ ARCHITECTURE.md (nodos, flujo)
     + PROMPT.md (instrucciones agente)
     + AGENTS_SPEC.md (SOT para cambios)
     + TROUBLESHOOTING.md (debugging)

En desarrollo (v2.0):
  └─ ARCHITECTURE_V2.md (diseño multi-agente)
     + AGENTS_SPEC.md (sub-agentes)
     + Testing en progreso
```

---

## 🔍 Búsqueda Rápida

### Por palabra clave

**Redis wait time**:
→ ARCHITECTURE.md línea 86 (ACTUALIZADO a 4s en v1.6.4)

**Notificación Slack**:
→ ARCHITECTURE.md líneas 110-118 (ACTUALIZADO en v1.6.4)
→ CHANGELOG.md líneas 10-44 (v1.6.4)

**Prompt del agente**:
→ AGENTS_SPEC.md líneas 30-130 (SOT)
→ PROMPT.md líneas 10-122 (análisis)

**Sub-agentes**:
→ ARCHITECTURE_V2.md líneas 45-201 (especificación)
→ AGENTS_SPEC.md líneas 148-176 (config)

**Cloudbeds API**:
→ ARCHITECTURE_V2.md líneas 125-200 (Pricing Agent)
→ ARCHITECTURE_V2.md líneas 343-362 (problemas conocidos)

**Escalación humana**:
→ ARCHITECTURE.md líneas 90-129 (subworkflow)
→ PROMPT.md línea 69 (instrucciones)

**Credenciales**:
→ ARCHITECTURE.md líneas 254-268 (protegidas)
→ ARCHITECTURE_V2.md líneas 89-121 (protegidas)

---

## 📋 Checklist de Lectura (Onboarding nuevo dev)

- [ ] 5 min: [README.md](README.md) — qué es esto
- [ ] 10 min: [ARCHITECTURE.md](ARCHITECTURE.md) líneas 11-75 — diagrama principal
- [ ] 10 min: [TROUBLESHOOTING.md](TROUBLESHOOTING.md) líneas 19-72 — arquitectura visual
- [ ] 5 min: [README.md](README.md) líneas 78-90 — comportamiento del agente
- [ ] 5 min: [AGENTS_SPEC.md](AGENTS_SPEC.md) líneas 1-50 — cómo cambiar prompt
- [ ] ⏸️ STOP aquí si solo necesitas mantener v1.6.4
- [ ] 15 min: [ARCHITECTURE_V2.md](ARCHITECTURE_V2.md) líneas 1-43 — visión v2.0
- [ ] 20 min: Reste de ARCHITECTURE_V2.md — detalles técnicos

**Total**: 30-60 min (depende de rol)

---

## 🔗 Referencias Directas

### IDs Clave de Workflow

| ID | Workflow | Propósito |
|----|----------|----------|
| `s9A9Al67_R0wSQWf_HY3X` | Main (v1.6.4) | Producción |
| `K3WrelHxg7k9EePiD5-2S` | Escalación | Humano alert |
| `Mq8XkIXWvZIyF2sZ` | FAQ Agent | v2.0 FAQ |
| `LPeOJLQadME2Nbgv` | CRM Agent | v2.0 CRM |
| `X22IjZoUYkFxKyjw` | Pricing Agent | v2.0 Precios |
| `KyWsxQJhr1SS0mS3` | Orquestador | v2.0 Main |

Ver [AGENTS_SPEC.md](AGENTS_SPEC.md) líneas 154-170 para tabla completa.

---

## 📅 Actualización de Documentos

| Documento | Última actualización | Responsable |
|-----------|---------------------|------------|
| README.md | 6 Abr 2026 | Claude AI |
| ARCHITECTURE.md | 6 Abr 2026 | Claude AI |
| AGENTS_SPEC.md | 6 Abr 2026 (NUEVO) | Claude AI |
| PROMPT.md | 6 Abr 2026 | Claude AI |
| ARCHITECTURE_V2.md | 20 Mar 2026 | Original |
| CHANGELOG.md | 6 Abr 2026 | Original |
| ISSUES.md | 15 Mar 2026 | Original (actualizar) |
| TROUBLESHOOTING.md | 7 Mar 2026 | Original |

---

**Índice generado**: 6 Abril 2026  
**Próxima actualización recomendada**: 4 Mayo 2026
