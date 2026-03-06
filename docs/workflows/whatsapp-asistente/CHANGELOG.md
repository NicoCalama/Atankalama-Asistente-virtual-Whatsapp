# 📋 Changelog - Atankalama Asistente Virtual WhatsApp

Registro de todas las versiones y cambios significativos del workflow.

Formato basado en [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/)
Versionado siguiendo [Semantic Versioning](https://semver.org/lang/es/)

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

**Última actualización**: 5 de Marzo 2026
**Versión actual**: v1.2.0
**Mantenedores**: NicoCalama + Claude AI Assistant
