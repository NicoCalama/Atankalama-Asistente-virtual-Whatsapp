# 📋 Changelog - Atankalama Asistente Virtual WhatsApp

Registro de todas las versiones y cambios significativos del workflow.

Formato basado en [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/)
Versionado siguiendo [Semantic Versioning](https://semver.org/lang/es/)

---

## [1.5.0] - 2026-03-08

### Escalación a Humano — Rediseño completo (subworkflow + Slack)

#### Cambios implementados

**Nuevo subworkflow: "subworkflow etiqueta chatwoot"** (ID: `K3WrelHxg7k9EePiD5-2S`)
- Trigger: `ExecuteWorkflowTrigger` con dos inputs: `Resumen conversacion` + `Id Conversacion Chatwoot`
- Flujo: Trigger → Edit Fields → Slack (Block Kit) → Chatwoot (custom attribute)
- Slack: mensaje con Block Kit — header 🚨 + resumen + botón verde primario "💬 Abrir chat en Chatwoot"
- Chatwoot: POST a endpoint custom con `custom_atributes: { humano: "Activado" }` usando el ID de la conversación

**Reemplazo de herramienta de escalación en flujo principal**
- Eliminado nodo "Contactar Humano" (Gmail tool) — reemplazado por el subworkflow
- Renombrado "Contactar Humano1" → **"Contactar Humano"** (tipo: `toolWorkflow`)
- Configurados Workflow Inputs:
  - `Resumen conversacion` → generado por el modelo vía `$fromAI()`
  - `Id Conversacion Chatwoot` → `$('Webhook').item.json.body.data.chatwootConversationId`

**Prompt actualizado (v1.5.0)**
- `"Contactar Humano"` ahora invoca el subworkflow de Slack/Chatwoot (no email)
- Instrucción explícita post-escalación: responder con confirmación **SIN preguntas adicionales**
  - Ejemplo: `"Listo, ya avisé a recepción. En breve se pondrán en contacto contigo. 😊"`
- La conversación queda en pausa hasta que recepción atienda
- Alerta de seguridad también usa "Contactar Humano" sin preguntas adicionales

#### Pendiente
- Configurar `channelId` del canal de Slack de recepción (subworkflow inactivo hasta que esté configurado)

---

## [1.4.1] - 2026-03-07

### Fix: Bug #011 — Agente IA sin prompt + Think visible en respuestas

#### Problema 1: "No prompt specified" (crítico)
El update del systemMessage en v1.4.0 reemplazó el objeto `parameters` completo del nodo Agente IA, eliminando `promptType` y `text`.

**Fix**: Restaurados `promptType: "define"` y `text: "={{ $json.mensaje }}"` junto al `options` completo en un solo update.

#### Problema 2: Razonamiento interno visible para el cliente
El agente incluía "Think: ..." como texto en sus respuestas al cliente porque el ejemplo del prompt usaba ese formato.

**Fix**: Prompt actualizado para aclarar que Think es una herramienta interna. Ejemplo reformateado como `[Herramienta Think → razonamiento interno, nunca visible para el cliente]`.

#### Verificado con ejecuciones reales
- #1259: Agente completó flujo y solicitó feedback de cierre ✅
- #1261: Agente recibió calificación y se despidió correctamente ✅
- Sin razonamiento interno visible en ninguna respuesta ✅

---

## [1.4.0] - 2026-03-07

### Rediseño completo del prompt del Agente IA (Stage 1)

#### Problema resuelto
- Issue #001: Prompt robótico con lenguaje corporativo excesivo
- Issue #009: Agente no llamaba herramientas antes de responder (confirmado ejecución #1233)

#### Cambios implementados
- **Nuevo systemMessage**: ~260 tokens (vs ~850 anterior = -70% de reducción)
- **Think-first**: el agente usa Think explícitamente antes de actuar
- **Árbol de situaciones**: reemplaza las 6 fases lineales por condiciones independientes
- **Instrucciones positivas**: elimina las prohibiciones en MAYÚSCULAS
- **CRM simplificado**: reglas de Airtable eliminadas del prompt (ya están en los nodos vía $fromAI)
- **B2B simplificado**: todo redirige a Cloudbeds (igual que B2C), sin protocolo de 5 pasos
- **Encuesta mejorada**: incluye razón de la nota ("¿por qué esa nota?")
- **Saludo contextual**: cliente nuevo se presenta; cliente conocido es saludado por nombre

#### Archivos modificados
- Workflow en n8n: nodo "Agente IA" → `parameters.options.systemMessage`
- `workflows/whatsapp-asistente.json` → exportado con prompt nuevo

---

## [1.3.0] - 2026-03-06

### Auditoria Tecnica Completa

#### Hallazgos confirmados (5 tests con datos reales)

**Arquitectura real corregida**
- Total de nodos: 40 (documentacion anterior indicaba 19, estaba desactualizada)
- Stack actualizado: Redis (acumulacion), Gmail Tool (escalacion humana), Google Sheets Tool (preguntas sin respuesta)
- Supabase Vector Store sigue activo (migracion a Excel pendiente)

**Componentes verificados como OK**
- Filtros Chatwoot: funcionan correctamente (If + etiqueta "humano" + Filter)
- Acumulacion Redis: funciona correctamente — push a lista + get devuelve array — multiples mensajes se acumulan y se pasan juntos al agente
- Linea de voz (tecnica): Switch detecta audio, descarga funciona, Whisper transcribe correctamente (test real: 5s audio)
- Ejecuciones 0s: son eventos de Chatwoot correctamente filtrados (no mensajes perdidos)

**Problema critico identificado: Agente no sigue protocolo**
- El agente no ejecuta Fase 1 (Consultar contactos) antes de responder
- En test de voz: respondio directamente sin llamar ninguna herramienta
- No consulto Base de datos antes de decir "no dispongo de esa informacion"
- No ejecuto reporte_preguntas_sin_respuesta como exige el prompt
- Causa raiz: prompt demasiado extenso y prescriptivo → modelo lo ignora parcialmente

#### Pendiente de implementacion
- Rediseno del prompt (metodologia 5 pasos — proxima sesion)
- Migracion Supabase → alternativa (Supabase eliminara la DB)
- Mejora del email de escalacion humana

### Infraestructura
- Repositorio GitHub configurado con remote origin
- Workflow JSON exportado y versionado (punto de recuperacion)
- MCP configurado para Claude Code (.mcp.json)
- n8n-skills clonado localmente

---

## [1.2.0] - 2026-03-05

### 📚 Documentación
- Creación completa de documentación técnica del workflow
- README.md con overview y características
- ARCHITECTURE.md con detalle de nodos
- PROMPT.md con análisis y propuestas de mejora
- MESSAGE_PROCESSING.md con análisis técnico
- ISSUES.md con problemas identificados
- Este CHANGELOG.md

### 🔍 Análisis
- Identificación de problema: prompt suena robótico
- Identificación de área de mejora: procesamiento de mensajes
- Mapeo completo de arquitectura (19 nodos)

---

## [1.1.0] - 2026-03-03

### Añadido
- Integración con Google Drive para almacenar audios
- Mejora en notificaciones de Telegram con más contexto
- Clasificación automática de tipo de cliente (Turista/Empresa)

### Cambiado
- Optimización de consultas a PostgreSQL
- Ajuste de prompt para mejor detección de intenciones

### Documentación
- Identificación del workflow en repositorio n8n-ayudante
- Establecimiento de prioridad como workflow principal

---

## [1.0.0] - 2026-02-15

### 🎉 Lanzamiento Inicial

#### Añadido
- Webhook receiver desde Chatwoot
- Integración con OpenAI GPT-4.1-mini para respuestas
- Transcripción de mensajes de voz con Whisper
- Base de conocimiento vectorizada en Supabase
- Memoria conversacional en PostgreSQL
- Integración con Airtable CRM
- Sistema de notificaciones por Telegram
- Clasificación de tipo de mensaje (texto/voz)
- Procesamiento de contexto conversacional
- Detección de solicitudes de reserva
- Captura automática de datos de contacto

#### Stack Técnico
- n8n (motor de automatización)
- Chatwoot (frontend WhatsApp)
- OpenAI (GPT-4.1-mini + Whisper)
- Supabase (Vector Database)
- PostgreSQL (Memoria conversacional)
- Airtable (CRM)
- Telegram (Notificaciones)

#### Características
- Respuesta automática 24/7
- Soporte para texto y voz
- Búsqueda semántica en base de conocimiento
- Personalización por tipo de cliente
- Alertas al equipo comercial

---

## [0.2.0] - 2026-02-01

### 🧪 Fase de Testing

#### Añadido
- Pruebas de integración con Chatwoot
- Testing de transcripción de voz
- Validación de respuestas de IA
- Pruebas de notificaciones

#### Cambiado
- Ajuste de prompt inicial basado en tests
- Mejora en extracción de datos de contexto
- Optimización de tiempos de respuesta

#### Corregido
- Bug en procesamiento de mensajes sin remitente
- Error en consulta a base vectorial
- Problema con formato de notificaciones

---

## [0.1.0] - 2026-01-15

### 🚧 Prototipo Inicial

#### Añadido
- Estructura básica del workflow
- Conexión inicial con Chatwoot
- Primera integración con OpenAI
- Prueba de concepto de memoria conversacional

---

## 📝 Notas de Versión

### Sobre el Versionado

**MAJOR.MINOR.PATCH**

- **MAJOR**: Cambios incompatibles o rediseño arquitectónico
- **MINOR**: Nueva funcionalidad compatible con versión anterior
- **PATCH**: Correcciones de bugs y ajustes menores

### Tipos de Cambios

- `Añadido`: Nueva funcionalidad
- `Cambiado`: Cambios en funcionalidad existente
- `Obsoleto`: Funcionalidad que será eliminada
- `Eliminado`: Funcionalidad eliminada
- `Corregido`: Corrección de bugs
- `Seguridad`: Parches de seguridad

---

## 🔮 Roadmap

### [1.3.0] - Planificado para Marzo 2026

#### Añadido
- Sistema de métricas y analytics
- A/B testing de prompts
- Nuevo prompt conversacional (ver PROMPT.md)

#### Cambiado
- Optimización de procesamiento de mensajes
- Mejora en clasificación de intenciones

#### Por Definir
- Migración a Slack (pendiente aprobación)
- Ampliación de base de conocimiento

---

## 📚 Referencias

- **[README.md](README.md)**: Documentación principal
- **[ISSUES.md](ISSUES.md)**: Problemas conocidos
- **[PROMPT.md](PROMPT.md)**: Análisis y propuestas de prompt
- **[ARCHITECTURE.md](ARCHITECTURE.md)**: Arquitectura técnica

---

**Última actualización**: 8 de Marzo 2026
**Versión actual**: v1.5.0
**Mantenedores**: NicoCalama + Claude AI Assistant
