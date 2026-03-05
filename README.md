# 🏨 N8N Ayudante - Proyectos de Automatización

Documentación y mejora continua de proyectos n8n para automatización de procesos hoteleros, financieros y de análisis de mercado.

---

## 🎯 Metodología de Trabajo

Trabajamos de forma colaborativa siguiendo un proceso estructurado de 5 fases:

### 1. 💡 Lluvia de Ideas
Exploramos problemas, oportunidades y posibles soluciones juntos.

### 2. 📋 Planificación
Definimos objetivos claros, creamos plan de acción detallado y documentamos todo (ver [PLAN_MAESTRO.md](PLAN_MAESTRO.md)).

### 3. 🔧 Manos a la Obra
Implementamos en ambiente seguro, testing exhaustivo, validación de resultados.

### 4. ✅ Revisión
Validación completa, confirmación de seguridad, aprobación antes de producción.

### 5. 📚 Documentación
Registro en CHANGELOG, actualización técnica, commit a GitHub (con aprobación).

---

## 👤 Información del Proyecto

- **Usuario**: NicoCalama
- **Instancia n8n**: [URL protegida - ver .env]
- **Asistente IA**: Claude Code + n8n Skills

---

## 🤝 Cómo Trabajamos Juntos

### Rol del Asistente IA

**✅ LO QUE SÍ HACE**:
- Ayudar a mejorar workflows existentes
- Arreglar errores y optimizar código
- Probar cambios en ambiente seguro
- Sugerir mejores prácticas
- Documentar todo el proceso

**❌ LO QUE NO HACE**:
- Crear proyectos completos sin guía
- Aplicar cambios sin testing previo
- Tomar decisiones arquitectónicas solo
- Documentar en GitHub sin aprobación
- Subir información sensible

---

## 📊 Áreas de Proyectos

### 🏨 Hoteles (Foco Actual)
Automatización para Hotel Atankalama y Atankalama Inn:
- Asistente virtual de WhatsApp ⭐ **PRIORITARIO**
- Análisis de sentimientos en reseñas
- Estudio de mercado hotelero

### 💰 Finanzas (Futuro)
Por definir

### 📈 Estudios de Mercado (Futuro)
Análisis de competencia y tendencias

---

## 🔧 Workflows Activos

### ⭐ 1. Atankalama Asistente Virtual WhatsApp **[PRIORITARIO]**
- **Estado**: 🟢 Activo desde Febrero 2026
- **Función**: Asistente IA para atención de clientes por WhatsApp
- **Stack**: Chatwoot, OpenAI GPT-4.1, Airtable CRM, Supabase, PostgreSQL
- **Documentación**: [Ver docs completos →](docs/workflows/whatsapp-asistente/README.md)
- **Última actualización**: 3 de Marzo 2026

**Características**:
- Responde automáticamente consultas 24/7
- Clasifica clientes (Turistas vs Empresas)
- Gestiona reservas y cotizaciones
- Transcribe mensajes de voz
- Almacena leads en CRM
- Notifica oportunidades al equipo

**En Desarrollo**:
- Optimización de procesamiento de mensajes
- Mejora de naturalidad del agente (rediseño de prompt)
- Migración a Slack (pendiente aprobación)

---

### 2. Recolector Información Hotelera Calama
- **Estado**: 🔴 Inactivo (en revisión)
- **Función**: Scraping de datos de hoteles competencia en Calama
- **Stack**: Apify, Google Sheets, Google Gemini
- **Documentación**: Pendiente

---

### 3. Clasificador de Sentimientos y Trazabilidad
- **Estado**: 🔴 Inactivo (en revisión)
- **Función**: Análisis automático de reseñas de clientes
- **Stack**: Google Drive, Gmail, Google Gemini, Google Sheets
- **Documentación**: Pendiente

---

## 🛠️ Superpoderes del Asistente

### MCP (Model Context Protocol)
**Repositorio**: [czlonkowski/n8n-mcp](https://github.com/czlonkowski/n8n-mcp)

Acceso directo a documentación de 525+ nodos n8n con herramientas de:
- Validación de workflows
- Búsqueda de nodos
- Gestión y deployment

### Skills de Claude Code
**Repositorio**: [czlonkowski/n8n-skills](https://github.com/czlonkowski/n8n-skills)

7 skills especializadas para n8n:
- n8n Expression Syntax
- n8n MCP Tools Expert
- n8n Workflow Patterns
- n8n Validation Expert
- n8n Node Configuration
- n8n Code JavaScript
- n8n Code Python

📚 **Guía completa**: [N8N_SKILLS_REFERENCE.md](N8N_SKILLS_REFERENCE.md)

---

## 📁 Estructura del Proyecto

```
N8N_ayudante/
├── README.md                      # Este archivo
├── PLAN_MAESTRO.md               # Plan completo del proyecto (referencia)
├── N8N_SKILLS_REFERENCE.md       # Guía de skills de n8n
├── .env                           # Credenciales (no en Git)
├── .gitignore                     # Archivos excluidos
│
├── docs/
│   ├── SECURITY.md                # Guía de seguridad
│   ├── WORKFLOW_TEMPLATE.md      # Template para nuevos workflows
│   │
│   └── workflows/
│       └── whatsapp-asistente/    # Workflow prioritario
│           ├── README.md          # Documentación principal
│           ├── CHANGELOG.md       # Historial de versiones
│           ├── ISSUES.md          # Problemas conocidos
│           ├── ARCHITECTURE.md    # Arquitectura técnica
│           ├── PROMPT.md          # Análisis y mejoras de prompt
│           └── MESSAGE_PROCESSING.md  # Análisis de mensajes
│
└── n8n-skills/                    # Skills clonadas (referencia)
```

---

## 🔒 Seguridad

**Principio fundamental**: NUNCA subir información sensible a GitHub

- ❌ API Keys, tokens, passwords → Solo en `.env`
- ❌ URLs reales de servicios → Usar `[URL protegida]`
- ❌ Datos reales de clientes → Solo ejemplos ficticios
- ✅ Arquitectura, lógica, código → Público y documentado

📖 **Guía completa**: [docs/SECURITY.md](docs/SECURITY.md)

---

## 📝 Registro de Cambios

### 2026-03-05
- ✅ Creación del Plan Maestro (guía completa del proyecto)
- ✅ Documentación completa del workflow WhatsApp
- ✅ Establecimiento de metodología de trabajo
- ✅ Sistema de seguridad y versionado implementado

### 2026-03-03
- ✅ Configuración inicial del repositorio
- ✅ Conexión exitosa con instancia n8n
- ✅ Instalación de n8n-skills local
- ✅ Identificación de workflows existentes

---

## 🚀 Próximos Pasos

### En Progreso
- 🔄 Optimización de procesamiento de mensajes (texto/voz)
- 🔄 Rediseño del prompt del agente WhatsApp (más natural)
- 🔄 Análisis técnico de arquitectura actual

### Planificado
- ⏳ Testing de nuevas versiones de prompt
- ⏳ Migración a Slack (pendiente aprobación del equipo)
- ⏳ Ampliación de base de conocimiento FAQ
- ⏳ Implementación de métricas y analytics

---

## 📚 Documentación

- **[PLAN_MAESTRO.md](PLAN_MAESTRO.md)** - Plan completo y referencia permanente
- **[docs/SECURITY.md](docs/SECURITY.md)** - Guía de seguridad
- **[docs/workflows/whatsapp-asistente/](docs/workflows/whatsapp-asistente/)** - Docs del workflow prioritario
- **[N8N_SKILLS_REFERENCE.md](N8N_SKILLS_REFERENCE.md)** - Referencia de skills

---

## 🔗 Recursos Externos

- [n8n Documentation](https://docs.n8n.io/)
- [n8n-skills GitHub](https://github.com/czlonkowski/n8n-skills)
- [n8n-mcp GitHub](https://github.com/czlonkowski/n8n-mcp)
- [Claude Code](https://claude.com/claude-code)

---

**Proyecto iniciado**: 3 de marzo, 2026
**Última actualización**: 5 de marzo, 2026
**Mantenedores**: NicoCalama + Claude AI Assistant
