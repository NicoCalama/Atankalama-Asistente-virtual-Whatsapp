# 📱 Atankalama Asistente Virtual WhatsApp

**Workflow ID**: `s9A9Al67_R0wSQWf_HY3X`
**Estado**: 🟢 Activo desde Febrero 2026
**Ultima auditoria**: 6 de Marzo 2026

---

## 📋 Descripción General

Asistente virtual de IA que atiende automáticamente consultas de clientes a través de WhatsApp 24/7. Integrado con Chatwoot como frontend de mensajería, utiliza GPT-4.1-mini para generar respuestas personalizadas basadas en el contexto del hotel, el historial de conversaciones y una base de conocimiento vectorizada.

---

## 🎯 Objetivo

Automatizar la atención al cliente por WhatsApp para:
- Responder consultas frecuentes instantáneamente
- Clasificar leads (Turistas vs Empresas)
- Capturar información de contacto en CRM
- Gestionar solicitudes de reservas y cotizaciones
- Notificar al equipo sobre oportunidades comerciales
- Transcribir y responder mensajes de voz

---

## 🏗️ Stack Técnico

### Infraestructura
- **n8n**: Motor de automatización (self-hosted)
- **Chatwoot**: Frontend de WhatsApp y gestión de conversaciones
- **PostgreSQL**: Base de datos para memoria conversacional

### Inteligencia Artificial
- **OpenAI GPT-4.1**: Generacion de respuestas conversacionales (agente)
- **OpenAI Whisper**: Transcripcion de mensajes de voz
- **Supabase Vector DB**: Knowledge base RAG (migracion pendiente)

### Acumulacion de Mensajes
- **Redis**: Acumula mensajes enviados en rafaga (ventana de 12 segundos)

### CRM y Almacenamiento
- **Airtable**: CRM para gestion de leads y contactos
- **Google Sheets**: Registro de preguntas sin respuesta

### Notificaciones y Escalacion
- **Gmail**: Escalacion a humano (B2B, alertas de seguridad, solicitudes)
- **Gmail**: Notificacion de errores de Chatwoot

---

## 🔄 Flujo de Conversación

### Fase 1: Recepción del Mensaje
1. Cliente envía mensaje por WhatsApp
2. Chatwoot recibe el mensaje y dispara webhook a n8n
3. n8n valida el origen y extrae información del contacto

### Fase 2: Procesamiento de Entrada
- **Mensajes de texto**: Se procesan directamente
- **Mensajes de voz**:
  - Se descarga el audio
  - Se transcribe con Whisper
  - Se guarda en Google Drive
  - Se continúa con el texto transcrito

### Fase 3: Construcción de Contexto
1. Recupera historial de conversación (últimos 10 mensajes)
2. Busca información relevante en base de conocimiento vectorizada
3. Clasifica tipo de cliente (Turista/Empresa)
4. Identifica intención del mensaje

### Fase 4: Generación de Respuesta
1. Envía contexto completo + prompt al GPT-4.1-mini
2. IA genera respuesta personalizada
3. Sistema valida coherencia y formato

### Fase 5: Acciones Post-Respuesta
- Guarda conversación en memoria PostgreSQL
- Actualiza información del contacto en Airtable
- Detecta oportunidades comerciales

### Fase 6: Notificaciones
Si se detecta:
- Solicitud de reserva → Notifica al equipo comercial
- Lead calificado → Registra en CRM
- Información de contacto nueva → Actualiza base de datos

---

## ✨ Características Actuales

### Capacidades del Asistente
- ✅ Respuesta automática 24/7
- ✅ Transcripción de mensajes de voz
- ✅ Memoria conversacional (contexto de chat)
- ✅ Búsqueda en base de conocimiento (RAG)
- ✅ Clasificación automática de clientes
- ✅ Detección de intenciones
- ✅ Personalización por tipo de cliente
- ✅ Captura de datos de contacto

### Integraciones Activas
- ✅ Chatwoot (WhatsApp Business API)
- ✅ OpenAI (GPT-4.1-mini + Whisper)
- ✅ Airtable CRM
- ✅ Supabase Vector Database
- ✅ PostgreSQL Database
- ✅ Google Drive
- ✅ Telegram Notifications

---

## 🎯 Casos de Uso de Notificaciones

### 1. Solicitud de Reserva
**Trigger**: Cliente menciona fechas, número de personas o pregunta por disponibilidad
**Acción**: Notificación prioritaria al equipo comercial con:
- Datos del contacto
- Fechas solicitadas
- Número de huéspedes
- Enlace a conversación en Chatwoot

### 2. Cotización Empresarial
**Trigger**: Empresa consulta por tarifas corporativas o eventos
**Acción**: Alerta al área comercial con:
- Tipo de evento/servicio
- Datos de la empresa
- Requerimientos específicos

### 3. Lead Calificado
**Trigger**: Cliente muestra interés genuino y proporciona datos de contacto
**Acción**: Registro automático en CRM con clasificación

### 4. Mensaje de Voz Transcrito
**Trigger**: Se recibe y procesa un audio
**Acción**: Notificación con transcripción completa y enlace al audio

---

## 📊 Arquitectura de Nodos

**Total de nodos**: 40 (verificado 2026-03-06)
**Documentacion detallada**: Ver [ARCHITECTURE.md](ARCHITECTURE.md)

### Nodos Principales
- Webhook receivers (Chatwoot)
- Procesadores de audio (Whisper)
- Consultas a bases de datos (PostgreSQL, Supabase)
- Llamadas a OpenAI (Chat completion)
- Integraciones CRM (Airtable)
- Notificadores (Telegram)
- HTTP Requests (APIs externas)

---

## 🐛 Problemas Conocidos

Ver archivo completo: [ISSUES.md](ISSUES.md)

### Activos
1. **Migracion Supabase urgente** — Supabase elimina la DB gratuita (Prioridad: Alta) → Issue #010

### Resueltos en v1.4.0 (7 Marzo 2026)
- ✅ Issue #009: Agente no seguía protocolo — nuevo prompt Think-first resuelve tool-calling
- ✅ Issue #001: Prompt robótico — nuevo prompt -70% tokens, tono natural

### Resueltos en auditoria 2026-03-06
- ✅ Acumulacion Redis: funciona correctamente
- ✅ Linea de voz: tecnicamente OK (Switch, descarga, Whisper)
- ✅ Filtros Chatwoot: correctos

---

## 📝 Historial de Versiones

Ver archivo completo: [CHANGELOG.md](CHANGELOG.md)

**Version actual**: v1.4.0 (Marzo 2026)

---

## 🔐 Seguridad

- ❌ No se almacenan contraseñas en el workflow
- ✅ API keys en variables de entorno de n8n
- ✅ Webhooks con validación de origen
- ✅ Datos sensibles solo en bases privadas
- ✅ Audios de voz con acceso restringido

---

## 📚 Documentación Relacionada

- **[ARCHITECTURE.md](ARCHITECTURE.md)**: Diagrama y detalle técnico de nodos
- **[PROMPT.md](PROMPT.md)**: Análisis del prompt y propuestas de mejora
- **[MESSAGE_PROCESSING.md](MESSAGE_PROCESSING.md)**: Análisis de procesamiento de mensajes
- **[ISSUES.md](ISSUES.md)**: Lista completa de problemas y mejoras
- **[CHANGELOG.md](CHANGELOG.md)**: Historial detallado de cambios

---

## 🚀 Próximas Mejoras

### En Desarrollo
- 🔄 Testing del nuevo prompt (verificar tool-calling en conversaciones reales)
- 🔄 Migracion Supabase → Google Sheets (urgente)
- 🔄 Mejora del email de escalacion humana

### Planificado
- ⏳ Migracion notificaciones a Slack (pendiente aprobacion)
- ⏳ Ampliacion de base de conocimiento
- ⏳ Metricas y analytics
- ⏳ A/B testing de prompts

---

## 👥 Contacto

**Responsable**: NicoCalama
**Asistente IA**: Claude Code
**Última revisión**: 5 de Marzo 2026
