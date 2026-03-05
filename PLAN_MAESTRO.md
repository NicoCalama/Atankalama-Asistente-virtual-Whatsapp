# 📋 PLAN MAESTRO - Proyecto N8N Ayudante

**Fecha de Creación**: 5 de Marzo, 2026
**Proyecto**: Atankalama - Asistente Virtual WhatsApp
**Colaboradores**: NicoCalama + Claude AI Assistant

---

## 🎯 MISIÓN DEL PROYECTO

### Propósito General
Mejorar y documentar workflows de n8n para automatización de procesos, con foco inicial en el **Asistente Virtual de WhatsApp** para Hotel Atankalama.

### Rol del Asistente IA (Claude)
**✅ LO QUE SÍ HACE**:
- Ayudar a mejorar workflows existentes
- Arreglar errores y bugs
- Optimizar rendimiento y código
- Probar cambios en ambiente seguro
- Sugerir mejores prácticas
- Documentar todo el proceso

**❌ LO QUE NO HACE**:
- Crear proyectos completos sin guía
- Aplicar cambios sin testing previo
- Tomar decisiones arquitectónicas sin consultar
- Documentar en GitHub sin aprobación
- Subir información sensible

---

## 🔄 METODOLOGÍA DE TRABAJO

### 1. 💡 Lluvia de Ideas
- Explorar problemas y oportunidades
- Discutir posibles soluciones
- Identificar prioridades

### 2. 📋 Planificación
- Definir objetivos claros
- Crear plan de acción detallado
- Establecer entregables
- Documentar el plan (como este documento)

### 3. 🔧 Manos a la Obra
- Implementar en ambiente seguro
- Testing exhaustivo
- Validación de resultados
- Ajustes iterativos

### 4. ✅ Revisión
- Validar con Nico
- Confirmar que no hay datos sensibles expuestos
- Aprobar cambios antes de producción

### 5. 📚 Documentación
- Registrar cambios en CHANGELOG
- Actualizar documentación técnica
- Commit a GitHub (con aprobación)
- Mantener historial de versiones

---

## 🏨 CONTEXTO DEL NEGOCIO

### Áreas de Proyectos
1. **Hoteles** (Foco actual)
   - Hotel Atankalama
   - Atankalama Inn
   - Gestión de reservas y atención al cliente

2. **Finanzas** (Futuro)
   - Por definir

3. **Estudios de Mercado** (Futuro)
   - Scraping de hoteles en Calama
   - Análisis de competencia

### Workflows Existentes en n8n
1. **Atankalama Asistente Virtual WhatsApp** ⭐ **PRIORITARIO**
   - Estado: 🟢 Activo
   - ID: `s9A9Al67_R0wSQWf_HY3X`
   - Última actualización: 3 de Marzo 2026

2. **Recolector Información Hotelera Calama**
   - Estado: 🔴 Inactivo
   - ID: `-oE10yLNkz21D63rXd401`
   - Scraping con Apify

3. **Clasificador de Sentimientos y Trazabilidad**
   - Estado: 🔴 Inactivo
   - ID: `AeXLrNiy_qTUbQ01AQh3P`
   - Análisis de reseñas

---

## 🎯 OBJETIVOS DEL PLAN ACTUAL

### Objetivo Principal
**Documentar y mejorar el Workflow de WhatsApp** para que sea más natural, eficiente y mantenible.

### Objetivos Específicos

#### 1. **Documentación Completa** 📚
- Crear estructura de docs/ organizada
- Documentar arquitectura técnica
- Registrar stack completo
- Crear templates reutilizables
- Establecer sistema de versionado

#### 2. **Optimizar Procesamiento de Mensajes** 🔍
- Analizar flujo de texto y voz
- Identificar puntos de fallo
- Optimizar sincronización
- Mejorar manejo de sesiones
- Reducir latencia

#### 3. **Mejorar Naturalidad del Agente** ✨
- Rediseñar prompt actual (muy robótico)
- Crear 3 versiones alternativas
- Testing en ambiente seguro
- Implementar la mejor versión

#### 4. **Seguridad y Control** 🔒
- Proteger información sensible
- Actualizar .gitignore
- Crear guía de seguridad
- Checklist pre-commit

---

## 📂 ESTRUCTURA DE DOCUMENTACIÓN

### Árbol de Archivos a Crear

```
N8N_ayudante/
├── README.md                          # ✅ Actualizar
├── PLAN_MAESTRO.md                    # ✅ Este archivo (referencia permanente)
├── .gitignore                         # ✅ Actualizar
├── .env                               # (Ya existe, protegido)
├── N8N_SKILLS_REFERENCE.md           # (Ya existe)
│
├── docs/
│   ├── SECURITY.md                    # 🆕 Guía de seguridad general
│   ├── WORKFLOW_TEMPLATE.md          # 🆕 Template para futuros workflows
│   │
│   └── workflows/
│       └── whatsapp-asistente/
│           ├── README.md              # 🆕 Overview principal
│           ├── CHANGELOG.md           # 🆕 Historial de versiones
│           ├── ISSUES.md              # 🆕 Problemas conocidos
│           ├── ARCHITECTURE.md        # 🆕 Arquitectura técnica
│           ├── PROMPT.md              # 🆕 Análisis y propuestas de prompt
│           ├── MESSAGE_PROCESSING.md  # 🆕 Análisis de mensajes
│           └── TESTING.md             # 🆕 Plan de pruebas
│
└── n8n-skills/                        # (Ya existe, referencia)
```

---

## 📝 CONTENIDO DE CADA DOCUMENTO

### 1. README.md (Principal)

**Secciones**:
- Título y descripción del proyecto
- 🤝 Metodología de trabajo (5 fases)
- 👤 Información del proyecto (sin URL real)
- 🎯 Áreas de proyectos (Hoteles, Finanzas, Mercados)
- 📊 Workflows activos (foco en WhatsApp)
- 🛠️ Superpoderes del asistente (MCP + Skills)
- 🔒 Seguridad
- 📝 Registro de cambios
- 🚀 Próximos pasos

**Información a ocultar**:
- ❌ URL real de n8n → usar `[URL protegida]`
- ❌ API Keys completas
- ❌ Datos reales de clientes

---

### 2. docs/workflows/whatsapp-asistente/README.md

**Secciones**:

#### 2.1 Overview
- Nombre del workflow
- Objetivo de negocio
- Estado actual (🟢 Activo desde Feb 2026)
- ID: `s9A9Al67_R0wSQWf_HY3X`
- Última actualización

#### 2.2 ¿Qué Hace Este Workflow?
Descripción funcional en lenguaje simple:
- Responde automáticamente consultas de WhatsApp
- Clasifica clientes (B2C vs B2B)
- Gestiona reservas y cotizaciones
- Almacena leads en CRM
- Notifica al equipo sobre oportunidades
- Transcribe mensajes de voz
- Mantiene memoria conversacional

#### 2.3 Stack Técnico

**Servicios Externos**:
- 🔹 Chatwoot (frontend WhatsApp)
- 🔹 OpenAI (GPT-4.1-mini para chat + Whisper para voz)
- 🔹 Airtable (CRM de leads)
- 🔹 Supabase (base de conocimiento vectorial - RAG)
- 🔹 PostgreSQL (memoria conversacional)
- 🔹 Telegram (notificaciones - temporal, migración a Slack pendiente)

**Nodos n8n** (19 identificados):

**Trigger y Control de Flujo**:
1. Webhook Trigger - Recibe mensajes de Chatwoot
2. Filter - Filtra mensajes que no requieren humano
3. Edit Fields - Extrae datos del webhook
4. Switch - Detecta audio vs texto
5. Merge1 - Sincroniza ramas de audio y texto

**Procesamiento de Audio**:
6. Edit Fields1 - Extrae datos de archivo de audio
7. Convert to File - Prepara audio para transcripción
8. Transcribe a recording (OpenAI Whisper) - Convierte voz a texto
9. Edit Fields2 - Normaliza texto transcrito

**Agente IA y Memoria**:
10. AI Agent (LangChain) - Cerebro del asistente
11. OpenAI Chat Model (GPT-4.1-mini) - Modelo de lenguaje
12. Postgres Chat Memory - Memoria por conversación

**Herramientas del Agente** (7 herramientas):
13. Think - Razonamiento interno
14. Calculator - Cálculos matemáticos
15. Base de datos (Vector Store + Supabase) - Consulta FAQ
16. Consultar contactos (Airtable) - Lee CRM
17. Crear/Actualizar contacto (Airtable) - Escribe CRM
18. Contactar Humano (Telegram) - Notificaciones al equipo
19. Registrar Feedback - Guarda calificaciones

**Base de Conocimiento (RAG)**:
- Embeddings OpenAI - Vectoriza consultas
- Supabase Vector Store - Almacena y recupera FAQ
- OpenAI Chat Model1 - Modelo para RAG

#### 2.4 Flujo de Conversación

**Protocolo de 6 Fases**:

**Fase 1: Identificación Silenciosa**
- Ejecuta automáticamente "Consultar contactos"
- Busca al usuario por número de teléfono en Airtable
- Determina si es cliente nuevo o recurrente

**Fase 2: Contacto Inicial**
- Si es nuevo: Se presenta + responde consulta + pide nombre al final
- Si ignora la pregunta del nombre: Continúa sin bloquearse
- Usa el nombre solo una vez si lo da

**Fase 3: Clasificación de Cliente**
- Detecta si es:
  - **Turista (B2C)**: Persona natural buscando reserva
  - **Empresa (B2B)**: Agencia, tour operador o empresa

**Fase 4: Reserva B2C (Turistas)**
- Pregunta hotel de interés (Atankalama o Atankalama Inn)
- Entrega enlace de Cloudbeds con moneda local según prefijo:
  - +56 (Chile) → CLP
  - +55 (Brasil) → BRL
  - +54 (Argentina) → ARS
  - Europa → EUR
  - Otros → USD
- Guarda lead en Airtable (Prioridad: Alta)

**Fase 5: Protocolo B2B (Empresas)**
- Recopilación SECUENCIAL de datos (una pregunta a la vez):
  1. Nombre de persona y empresa
  2. Email de contacto
  3. Fechas de estadía
  4. Cantidad y tipo de habitaciones
  5. Cantidad de huéspedes
- DOBLE ACCIÓN OBLIGATORIA:
  - Guarda en Airtable (Segmento: Corporativo)
  - Notifica a equipo vía Telegram con resumen completo

**Fase 6: Feedback y Cierre**
- Se activa cuando:
  - Acaba de entregar enlaces (B2C)
  - Confirmó envío de datos a recepción (B2B)
  - Cliente se despide explícitamente
- Pregunta: "¿Calificarías mi atención del 1 al 5? ¿Y por qué esa nota?"
- Ejecuta "Registrar Feedback" si responde
- Cierra conversación sin reabrir el feedback

#### 2.5 Casos de Uso de Notificaciones

**Actualmente usa Telegram** (migración a Slack pendiente)

**3 Tipos de Notificaciones**:

1. **Oportunidades B2B Corporativas**
   - Empresas solicitando cotización
   - Datos completos de contacto
   - Detalles de reserva
   - Prioridad automática

2. **Escalamiento a Humano**
   - Cliente solicita hablar con persona
   - Casos sensibles o quejas
   - Situaciones que requieren intervención manual

3. **Conocimiento Faltante**
   - Preguntas que el bot no puede responder
   - Gaps en la base de datos de FAQ
   - Sugerencias para mejorar conocimiento

**Plan Futuro**: Migrar a Slack cuando el equipo lo apruebe
- Canal #ventas-corporativas
- Canal #soporte-urgente
- Canal #conocimiento-bot

#### 2.6 Problemas Conocidos

**Link a**: `ISSUES.md`

**Problemas identificados a investigar**:
1. ⚠️ **Prompt suena poco natural y robótico**
   - Tono técnico ("Ejecuta herramienta...")
   - Demasiadas reglas rígidas
   - Pierde hilo conversacional

2. ⚠️ **Procesamiento de mensajes de texto y voz necesita revisión**
   - Por determinar problemas específicos
   - Requiere análisis detallado

#### 2.7 Próximas Mejoras

**Prioridad Alta**:
- ✅ Rediseñar prompt (más natural)
- ✅ Optimizar procesamiento de mensajes
- ⏳ Migración a Slack (pendiente aprobación del equipo)

**Prioridad Media**:
- Ampliar base de conocimiento (FAQ)
- Métricas y analytics de conversaciones
- A/B testing de respuestas

**Prioridad Baja**:
- Soporte multiidioma (inglés)
- Integración con más canales

---

### 3. docs/workflows/whatsapp-asistente/PROMPT.md

**Estructura**:

#### 3.1 Prompt Actual (v0 - En Producción)

**Prompt completo**: [Se documentará el prompt actual extraído del workflow]

**Análisis de Problemas**:

❌ **Problema 1: Lenguaje Técnico**
```
Ejemplo actual:
"Ejecuta 'Consultar contactos' con el teléfono del usuario."

Por qué es malo:
- Suena robótico
- Expone lógica interna
- No es natural
```

❌ **Problema 2: Tono Rígido**
```
Ejemplo actual:
"TIENES ESTRICTAMENTE PROHIBIDO compartir datos personales"

Por qué es malo:
- Demasiado agresivo
- Hace al modelo defensivo
- Genera respuestas poco naturales
```

❌ **Problema 3: Exceso de Reglas**
```
Problema:
- Demasiadas "PROHIBICIONES"
- Reglas contradictorias
- El modelo se confunde

Resultado:
- Pierde el hilo de la conversación
- Se bloquea en situaciones no contempladas
- Respuestas muy estructuradas y poco humanas
```

❌ **Problema 4: Falta de Personalidad**
```
Problema:
- No tiene tono definido
- Suena genérico
- Falta calidez humana

Resultado:
- Cliente percibe frialdad
- Experiencia poco memorable
```

#### 3.2 Propuesta A: Prompt Natural con Personalidad

**Filosofía**:
- Tono cálido y humano
- Primera persona ("Voy a buscar...")
- Menos reglas, más ejemplos
- Personalidad definida: amable, eficiente, proactivo

**Características**:
- ✅ Lenguaje natural y conversacional
- ✅ Reduce "PROHIBIDO" → usa guidelines
- ✅ Explica acciones de forma humana
- ✅ Mantiene calidez sin perder profesionalismo
- ✅ Personalidad consistente

**Estructura del Prompt**:
```
[ROL Y PERSONALIDAD]
Tono y forma de comunicarse

[OBJETIVO]
Qué lograr (orientado a resultados, no a procesos)

[HERRAMIENTAS]
Descripción breve sin exponer lógica técnica

[FLUJO DE CONVERSACIÓN]
Narrado como guía, no como checklist

[EJEMPLOS DE CONVERSACIONES]
2-3 ejemplos de interacciones perfectas

[REGLAS CRÍTICAS]
Solo 5 reglas máximo, redactadas positivamente
```

**Ejemplo de Reescritura**:
```
❌ ANTES:
"Ejecuta 'Consultar contactos' con el teléfono del usuario.
Si no existe, ejecuta Fase 2."

✅ DESPUÉS:
"Primero voy a revisar si ya nos conocemos de conversaciones
anteriores, así puedo personalizar mejor mi ayuda. Si es tu
primera vez hablando conmigo, encantado de conocerte."
```

#### 3.3 Propuesta B: Prompt Estructurado Conversacional

**Filosofía**:
- Balance entre estructura y naturalidad
- Mantiene fases pero con lenguaje amigable
- Reglas claras sin ser rígido
- Prioridades en lugar de prohibiciones

**Características**:
- ✅ Estructura clara (mantiene fases)
- ✅ Lenguaje amigable
- ✅ "Idealmente..." en lugar de "PROHIBIDO"
- ✅ Flexibilidad ante situaciones imprevistas

**Estructura del Prompt**:
```
[IDENTIDAD]
Quién eres y para qué existes

[CAPACIDADES]
Qué puedes hacer (no cómo lo haces)

[PROTOCOLO DE ATENCIÓN]
Guía de flujo con lenguaje positivo

[PRIORIDADES]
Qué es más importante

[MANEJO DE EXCEPCIONES]
Qué hacer si algo sale del guión
```

**Ejemplo de Reescritura**:
```
❌ ANTES:
"TIENES ESTRICTAMENTE PROHIBIDO compartir datos personales
de otros clientes, información interna..."

✅ DESPUÉS:
"Protege siempre la privacidad de los clientes. La información
de cada persona es confidencial y solo debe usarse para
ayudarla a ella."
```

#### 3.4 Propuesta C: Prompt Minimalista (GPT-4 Optimizado)

**Filosofía**:
- Aprovecha capacidades nativas de GPT-4.1-mini
- Few-shot prompting (aprender por ejemplos)
- Menos instrucciones, más confianza en el modelo
- Énfasis en resultados, no en proceso

**Características**:
- ✅ Mínimas instrucciones técnicas
- ✅ Ejemplos de conversaciones reales
- ✅ Confianza en razonamiento del modelo
- ✅ Solo reglas críticas de seguridad

**Estructura del Prompt**:
```
[IDENTIDAD Y OBJETIVO]
1 párrafo claro

[HERRAMIENTAS DISPONIBLES]
Lista con descripción de 1 línea cada una

[EJEMPLOS DE CONVERSACIONES EXITOSAS]
3-4 ejemplos completos de:
- Cliente B2C → Reserva exitosa
- Cliente B2B → Cotización enviada
- Pregunta sin respuesta → Escalamiento
- Cliente confundido → Aclaración efectiva

[REGLAS CRÍTICAS]
Máximo 5 reglas, solo lo absolutamente esencial:
1. Nunca inventar información
2. Proteger privacidad
3. Ser cálido y profesional
4. Ser proactivo en ventas
5. Escalar cuando sea necesario
```

**Ejemplo de Conversación (Few-shot)**:
```
EJEMPLO 1: Cliente B2C
━━━━━━━━━━━━━━━━━━━━━━━━━━━
Cliente: Hola, necesito saber precios para 2 personas
del 15 al 20 de marzo

Asistente: ¡Hola! Con gusto te ayudo. Para que veas
las tarifas exactas y disponibilidad en tiempo real,
¿te interesa Atankalama o Atankalama Inn?

Cliente: Atankalama

Asistente: Perfecto, aquí puedes ver todas las opciones
con precios en pesos chilenos: [enlace]. Ahí puedes
reservar directamente. Por cierto, ¿me dices tu nombre
para personalizar nuestra charla?

Cliente: Soy María

Asistente: Encantado, María. Cualquier duda sobre las
habitaciones o servicios, aquí estoy para ayudarte.
```

#### 3.5 Comparativa de las 3 Propuestas

| Aspecto | Prompt A (Natural) | Prompt B (Estructurado) | Prompt C (Minimalista) |
|---------|-------------------|------------------------|----------------------|
| **Naturalidad** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Control** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| **Flexibilidad** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Facilidad de mantener** | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Tono humano** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Previene errores** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| **Longitud** | Media | Media-Larga | Corta |

**Recomendación Inicial**: Empezar con **Propuesta B** (balance óptimo)

#### 3.6 Plan de Testing

**Metodología**:
1. Duplicar workflow actual (ambiente de testing)
2. Implementar Propuesta B primero
3. Probar con casos reales documentados
4. Medir resultados
5. Ajustar según feedback
6. Si funciona, probar A o C para comparar

**Casos de Prueba**:

**Test 1: Cliente B2C Simple**
- Input: "Hola, quiero saber precios"
- Esperado: Respuesta cálida + pregunta por hotel + enlace

**Test 2: Cliente B2B**
- Input: "Somos de Turismo Atacama, necesitamos 10 habitaciones"
- Esperado: Recopilación secuencial + notificación a equipo

**Test 3: Mensaje de Voz**
- Input: Audio con consulta
- Esperado: Transcripción + respuesta coherente

**Test 4: Cambio de Tema**
- Input: Pregunta sobre piscina → luego sobre precios
- Esperado: Mantiene contexto sin bloquearse

**Test 5: Cliente Confundido**
- Input: Preguntas desordenadas o contradictorias
- Esperado: Aclara con paciencia, guía suavemente

**Test 6: Pregunta sin Respuesta**
- Input: "¿Tienen servicio de lavandería?"
- Esperado: Intenta buscar en FAQ → si no existe, notifica equipo + ofrece contacto humano

**Test 7: Cliente Molesto**
- Input: Queja o tono negativo
- Esperado: Empatía + escala a humano rápidamente

**Métricas a Evaluar**:
- ✅ Naturalidad (1-10, evaluación humana)
- ✅ Efectividad (¿logró el objetivo?)
- ✅ Retención de contexto (¿recordó info previa?)
- ✅ Tasa de escalamiento a humano (¿apropiada?)
- ✅ Tiempo de respuesta
- ✅ Satisfacción del cliente (si hay feedback)

---

### 4. docs/workflows/whatsapp-asistente/MESSAGE_PROCESSING.md

**Contenido**:

#### 4.1 Análisis del Flujo Actual

**Diagrama ASCII del Flujo**:
```
Chatwoot Webhook
    ↓
Filter (¿es para el bot?)
    ↓
Edit Fields (extrae datos)
    ↓
Switch (¿audio o texto?)
    ├─→ [AUDIO]
    │   ├─→ Edit Fields1
    │   ├─→ Convert to File
    │   ├─→ Transcribe (Whisper)
    │   └─→ Edit Fields2
    │
    └─→ [TEXTO]
        └─→ Continúa directamente

Merge1 (une ambas ramas)
    ↓
AI Agent (procesa con GPT-4.1-mini)
    ↓
[Respuesta al cliente]
```

#### 4.2 Preguntas para Investigar

**Necesito tu feedback sobre**:

**Sobre Audios**:
- [ ] ¿Los audios se transcriben correctamente?
- [ ] ¿Qué % de éxito aproximado?
- [ ] ¿Hay delay notable para el usuario?
- [ ] ¿Falla más con audios largos o cortos?
- [ ] ¿Problemas con acentos o ruido?

**Sobre Textos**:
- [ ] ¿El bot responde a todos los mensajes de texto?
- [ ] ¿Hay respuestas duplicadas?
- [ ] ¿Se pierden mensajes?
- [ ] ¿Problemas con emojis o caracteres especiales?

**Sobre Memoria/Sesión**:
- [ ] ¿El bot recuerda conversaciones previas del mismo cliente?
- [ ] ¿Se confunde entre diferentes clientes?
- [ ] ¿Pierde contexto si hay pausas largas (ej: 1 hora)?
- [ ] ¿Funciona bien el ID de conversación de Chatwoot?

**Sobre Sincronización**:
- [ ] ¿El nodo Merge funciona correctamente?
- [ ] ¿Hay race conditions?
- [ ] ¿Problemas si llegan mensajes muy seguidos?

#### 4.3 Puntos de Fallo Potenciales

**A revisar**:

1. **Switch Logic**
   ```javascript
   Condición actual:
   attachments[0]?.file_type === "audio"

   Riesgos:
   - ¿Qué pasa si attachments está vacío?
   - ¿Otros tipos de archivos (imagen, video)?
   - ¿Audio sin extensión reconocida?
   ```

2. **Transcripción con Whisper**
   ```
   Posibles problemas:
   - Timeout si audio muy largo
   - Formato de audio no soportado
   - Calidad de audio baja
   - Idioma no detectado correctamente
   ```

3. **Merge de Ramas**
   ```
   Riesgo:
   - Si una rama falla, ¿la otra continúa?
   - ¿Se mantiene el orden correcto?
   ```

4. **Postgres Memory**
   ```
   Key usado: conversation_id de Chatwoot

   Riesgos:
   - ¿Se limpia memoria vieja?
   - ¿Límite de tamaño de historial?
   - ¿Qué pasa si cambia el conversation_id?
   ```

#### 4.4 Propuestas de Optimización

**Se definirán después de tu feedback**

---

### 5. docs/workflows/whatsapp-asistente/CHANGELOG.md

**Template**:

```markdown
# Changelog - Atankalama WhatsApp Assistant

Todos los cambios notables en este workflow serán documentados aquí.

Formato basado en [Keep a Changelog](https://keepachangelog.com/).

---

## [Sin Versionar] - Estado Actual (5 de Marzo 2026)

### ℹ️ Información
- Workflow funcional en producción
- Creado originalmente: 1 de Febrero 2026
- Última modificación: 3 de Marzo 2026

### 📦 Stack Tecnológico
- **Frontend**: Chatwoot (WhatsApp)
- **LLM**: OpenAI GPT-4.1-mini
- **STT**: OpenAI Whisper
- **CRM**: Airtable
- **Knowledge Base**: Supabase (Vector Store)
- **Memory**: PostgreSQL
- **Notifications**: Telegram (temporal)

### 🔍 Estado Documentado
- Nodos identificados: 19
- Fases de conversación: 6
- Herramientas del agente: 7

### ⚠️ Problemas Conocidos
- Prompt suena robótico y poco natural
- Procesamiento de mensajes requiere revisión
- Migración a Slack pendiente

---

## Template para Futuras Versiones

### [v1.1.0] - YYYY-MM-DD

#### 🎉 Añadido
- Nueva funcionalidad X
- Nuevo nodo Y

#### 🔧 Modificado
- Mejora en Z
- Optimización de A

#### 🐛 Corregido
- Bug en B
- Error de C

#### 🗑️ Eliminado
- Feature obsoleta D

#### ⚡ Rendimiento
- Reducción de latencia en E

#### 🔒 Seguridad
- Mejora de seguridad en F

#### 👤 Autor(es)
- NicoCalama + Claude AI Assistant

#### 📝 Notas
- Información adicional relevante

---

## Leyenda de Emojis

- 🎉 **Añadido**: Nuevas funcionalidades
- 🔧 **Modificado**: Cambios en funcionalidades existentes
- 🐛 **Corregido**: Bugs resueltos
- 🗑️ **Eliminado**: Funcionalidades removidas
- ⚡ **Rendimiento**: Mejoras de performance
- 🔒 **Seguridad**: Mejoras de seguridad
- 📝 **Documentación**: Cambios en docs
```

---

### 6. docs/workflows/whatsapp-asistente/ISSUES.md

**Template**:

```markdown
# Problemas Conocidos - WhatsApp Assistant

## 🟢 Estado: 0 Issues Activos Críticos

---

## 🔍 Issues en Investigación

### [MEDIA] Prompt suena robótico y poco natural
**Fecha reportado**: 5 de Marzo 2026
**Reportado por**: NicoCalama
**Estado**: 🟡 En Análisis
**Prioridad**: Media-Alta

**Descripción**:
El agente IA utiliza lenguaje muy técnico y estructurado, lo que resulta en conversaciones que suenan artificiales. Además, pierde el hilo de la conversación en situaciones complejas.

**Ejemplos**:
- Usa frases como "Ejecuta herramienta X"
- Demasiadas reglas "PROHIBIDO"
- Falta de personalidad humana
- Se bloquea ante situaciones no contempladas

**Impacto**:
- Experiencia de usuario subóptima
- Cliente percibe frialdad
- Puede afectar conversión

**Solución Propuesta**:
Rediseñar prompt con 3 enfoques alternativos:
- Propuesta A: Natural con personalidad
- Propuesta B: Estructurado conversacional
- Propuesta C: Minimalista (GPT-4 optimizado)

Ver detalles en: `PROMPT.md`

**Próximos Pasos**:
1. Crear 3 versiones del prompt
2. Testing en ambiente seguro
3. Comparar resultados
4. Implementar mejor versión

---

### [MEDIA] Procesamiento de mensajes de texto y voz requiere revisión
**Fecha reportado**: 5 de Marzo 2026
**Reportado por**: NicoCalama
**Estado**: 🟡 En Investigación
**Prioridad**: Media

**Descripción**:
Necesita revisión técnica del flujo de procesamiento de mensajes (tanto texto como audio) para identificar puntos de mejora.

**Áreas a Investigar**:
- Transcripción de audios (tasa de éxito, latencia)
- Manejo de textos (respuestas duplicadas, mensajes perdidos)
- Memoria/sesión (retención de contexto)
- Sincronización de ramas (Merge)

**Impacto**:
Por determinar según análisis

**Solución Propuesta**:
Pendiente de análisis técnico detallado

**Próximos Pasos**:
1. Recopilar feedback específico de Nico
2. Analizar nodos críticos (Switch, Merge, Transcribe)
3. Identificar puntos de fallo
4. Proponer optimizaciones

Ver detalles en: `MESSAGE_PROCESSING.md`

---

## ⏳ Mejoras Futuras (No son Issues)

### Migración de Telegram a Slack
**Tipo**: Mejora
**Prioridad**: Baja (pendiente aprobación del equipo)

**Razón**:
Slack ofrece mejor organización por canales, seguridad empresarial y contexto rico para notificaciones.

**Canales Propuestos**:
- #ventas-corporativas
- #soporte-urgente
- #conocimiento-bot

**Status**: Documentado, esperando decisión del equipo hotelero

---

## 📋 Template para Reportar Nuevos Issues

### [PRIORIDAD] Título Descriptivo del Problema
**Fecha reportado**: YYYY-MM-DD
**Reportado por**: Nombre
**Estado**: 🔴 Abierto | 🟡 En Progreso | 🟢 Resuelto
**Prioridad**: Crítica | Alta | Media | Baja

**Descripción**:
Explicación clara del problema

**Pasos para Reproducir**:
1. Paso 1
2. Paso 2
3. Resultado observado

**Comportamiento Esperado**:
Qué debería pasar

**Comportamiento Actual**:
Qué está pasando realmente

**Impacto**:
- En clientes
- En negocio
- En sistema

**Solución Propuesta**:
Ideas de cómo resolverlo

**Notas Adicionales**:
Información extra relevante

**Enlaces Relacionados**:
- Link a documentación
- Link a código
- Link a conversaciones

---

## 🏷️ Etiquetas de Prioridad

- **🔴 Crítica**: Afecta funcionalidad principal, requiere atención inmediata
- **🟠 Alta**: Problema importante pero hay workaround temporal
- **🟡 Media**: Mejora deseable, no bloquea operación
- **🟢 Baja**: Nice to have, no urgente

## 🏷️ Etiquetas de Estado

- **🔴 Abierto**: Identificado, no iniciado
- **🟡 En Progreso**: Se está trabajando en solución
- **🔵 En Testing**: Solución implementada, en pruebas
- **🟢 Resuelto**: Completado y verificado
- **⚪ Cerrado**: Resuelto y documentado
```

---

### 7. docs/workflows/whatsapp-asistente/ARCHITECTURE.md

**Contenido**:

#### 7.1 Arquitectura General

```
┌─────────────────────────────────────────────────────┐
│              CLIENTE (WhatsApp)                      │
└────────────────────┬────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────┐
│              CHATWOOT (Frontend)                     │
│  - Gestión de conversaciones                         │
│  - Interfaz de agentes humanos                       │
└────────────────────┬────────────────────────────────┘
                     │ Webhook
                     ▼
┌─────────────────────────────────────────────────────┐
│              N8N WORKFLOW                            │
│                                                       │
│  ┌──────────────────────────────────────────────┐  │
│  │  1. RECEPCIÓN Y FILTRADO                     │  │
│  │     - Webhook Trigger                        │  │
│  │     - Filter (excluir escalados a humano)    │  │
│  │     - Edit Fields (extracción de datos)      │  │
│  └──────────────────────────────────────────────┘  │
│                     │                                 │
│                     ▼                                 │
│  ┌──────────────────────────────────────────────┐  │
│  │  2. CLASIFICACIÓN DE MENSAJE                 │  │
│  │     - Switch (audio vs texto)                │  │
│  └──────┬─────────────────────────────┬─────────┘  │
│         │                               │             │
│    [AUDIO]                         [TEXTO]          │
│         │                               │             │
│  ┌──────▼───────────────────┐         │             │
│  │  3A. PROCESAMIENTO AUDIO  │         │             │
│  │   - Edit Fields1          │         │             │
│  │   - Convert to File       │         │             │
│  │   - Transcribe (Whisper)  │         │             │
│  │   - Edit Fields2          │         │             │
│  └──────┬───────────────────┘         │             │
│         │                               │             │
│         └───────────┬───────────────────┘            │
│                     ▼                                 │
│  ┌──────────────────────────────────────────────┐  │
│  │  4. SINCRONIZACIÓN                           │  │
│  │     - Merge1 (une audio y texto)             │  │
│  └──────────────────┬───────────────────────────┘  │
│                     ▼                                 │
│  ┌──────────────────────────────────────────────┐  │
│  │  5. AGENTE IA (Cerebro)                      │  │
│  │                                               │  │
│  │  ┌────────────────────────────────────────┐ │  │
│  │  │   OpenAI Chat Model (GPT-4.1-mini)     │ │  │
│  │  └────────────────────────────────────────┘ │  │
│  │                     │                         │  │
│  │  ┌─────────────────▼──────────────────────┐ │  │
│  │  │   Postgres Chat Memory                  │ │  │
│  │  │   (memoria por conversation_id)         │ │  │
│  │  └─────────────────────────────────────────┘ │  │
│  │                                               │  │
│  │  HERRAMIENTAS (7):                           │  │
│  │  ┌────────────────────────────────────────┐ │  │
│  │  │ 1. Think (razonamiento)                │ │  │
│  │  │ 2. Calculator (cálculos)               │ │  │
│  │  │ 3. Base de datos (FAQ - Vector Store)  │ │  │
│  │  │ 4. Consultar contactos (Airtable)      │ │  │
│  │  │ 5. Crear/Actualizar contacto (Airtable)│ │  │
│  │  │ 6. Contactar Humano (Telegram)         │ │  │
│  │  │ 7. Registrar Feedback                  │ │  │
│  │  └────────────────────────────────────────┘ │  │
│  └──────────────────┬───────────────────────────┘  │
│                     │                                 │
└─────────────────────┼─────────────────────────────┘
                      │
         ┌────────────┼────────────┐
         ▼            ▼             ▼
    ┌────────┐  ┌─────────┐  ┌──────────┐
    │Airtable│  │Supabase │  │Telegram  │
    │  CRM   │  │Vector DB│  │(Notif.)  │
    └────────┘  └─────────┘  └──────────┘
```

#### 7.2 Detalle de Nodos

**GRUPO 1: Recepción y Control**

**Nodo: Webhook Trigger**
- **Tipo**: Trigger
- **Función**: Recibe webhooks de Chatwoot
- **Configuración**:
  - Path: `/webhook/whatsapp`
  - Método: POST
- **Output**: Datos completos del mensaje de Chatwoot

**Nodo: Filter**
- **Tipo**: Filter
- **Función**: Excluye mensajes que contienen "humano" en payload
- **Lógica**: `payload NOT contains "humano"`
- **Razón**: Si el cliente solicitó humano, no debe responder el bot

**Nodo: Edit Fields**
- **Tipo**: Set (Edit Fields)
- **Función**: Extrae y normaliza datos del webhook
- **Campos extraídos**:
  ```javascript
  {
    Numero_Telefono: $('Webhook').json.body.conversation.messages[0].sender.phone_number,
    Mensaje: $('Webhook').json.body.conversation.messages[0].content,
    Tipo_Mensaje: $('Webhook').json.body.conversation.messages[0].attachments[0].file_type,
    ID: $('Webhook').json.body.conversation.messages[0].conversation.assignee_id,
    ID_Chatwoot: $('Webhook').json.body.conversation.messages[0].conversation_id,
    serverUrl: "https://app.chatwoot.com"
  }
  ```

---

**GRUPO 2: Clasificación**

**Nodo: Switch**
- **Tipo**: Switch
- **Función**: Detecta si el mensaje es audio o texto
- **Lógica**:
  ```javascript
  Condición: attachments[0]?.file_type === "audio"
  Si TRUE → Rama "Audio"
  Si FALSE → Rama "texto"
  ```
- **Outputs**:
  - Audio: Procesa con Whisper
  - texto (default): Continúa directo

---

**GRUPO 3: Procesamiento de Audio**

**Nodo: Edit Fields1**
- **Función**: Extrae URL del archivo de audio

**Nodo: Convert to File**
- **Función**: Descarga y prepara archivo de audio para transcripción

**Nodo: Transcribe a recording (OpenAI)**
- **Tipo**: OpenAI (Whisper)
- **Función**: Transcribe audio a texto
- **Configuración**:
  - Resource: audio
  - Operation: transcribe
  - Model: whisper-1
- **Output**: `{ text: "transcripción..." }`

**Nodo: Edit Fields2**
- **Función**: Normaliza texto transcrito
- **Campo creado**: `mensaje` (contiene la transcripción)

---

**GRUPO 4: Sincronización**

**Nodo: Merge1**
- **Tipo**: Merge
- **Función**: Une las ramas de audio y texto
- **Modo**: Merge By Position (asume orden correcto)
- **Output**: Datos unificados con campo `mensaje` siempre presente

---

**GRUPO 5: Agente IA y Herramientas**

**Nodo: AI Agent (LangChain)**
- **Tipo**: @n8n/n8n-nodes-langchain.agent
- **Versión**: 1.7
- **Función**: Cerebro del asistente, procesa la conversación
- **Configuración**:
  - Prompt Type: define
  - Text input:
    ```
    Mensaje: {{ $json.mensaje }}
    Conversacion ID: {{ $('Edit Fields').json['ID Chatwoot'] }}
    Numero de Telefono: {{ $('Edit Fields').json.Numero_Telefono }}
    ```
  - System Message: [Ver PROMPT.md para prompt completo]
  - Max Iterations: 15
  - On Error: continueRegularOutput

**Sub-nodo: OpenAI Chat Model**
- **Modelo**: gpt-4.1-mini
- **Función**: Motor de lenguaje principal
- **Conectado a**: AI Agent (ai_languageModel)

**Sub-nodo: Postgres Chat Memory**
- **Tipo**: @n8n/n8n-nodes-langchain.memoryPostgresChat
- **Función**: Mantiene memoria de conversación
- **Session Key**: `conversation_id` de Chatwoot
- **Conectado a**: AI Agent (ai_memory)
- **Almacena**: Historial completo de mensajes por sesión

**Sub-nodo: Think**
- **Tipo**: @n8n/n8n-nodes-langchain.toolThink
- **Función**: Permite al agente "pensar" internamente
- **Conectado a**: AI Agent (ai_tool)

**Sub-nodo: Calculator**
- **Tipo**: @n8n/n8n-nodes-langchain.toolCalculator
- **Función**: Realiza cálculos matemáticos
- **Conectado a**: AI Agent (ai_tool)

**Sub-nodo: Base de datos (Vector Store)**
- **Tipo**: @n8n/n8n-nodes-langchain.toolVectorStore
- **Nombre**: "faq_antankalam"
- **Descripción**: "Utiliza esta herramienta para obtener TODA la información oficial de los hoteles Atankalama..."
- **Componentes**:
  - Supabase Vector Store (tabla: faq_antankalama)
  - Embeddings OpenAI
  - OpenAI Chat Model1 (para generación de respuesta RAG)
- **Función**: Consulta base de conocimiento sobre hoteles
- **Conectado a**: AI Agent (ai_tool)

**Sub-nodo: Consultar contactos (Airtable)**
- **Tipo**: n8n-nodes-base.airtableTool
- **Operación**: search
- **Base**: Atankalama leads (applhMgcR7lh6UDlT)
- **Tabla**: CRM_Leads_Chatbot (tblSTBwvHaxvSd5Fq)
- **Filtro**: `Numero de Telefono = {{ Numero_Telefono }}`
- **Función**: Busca si el cliente ya existe en el CRM
- **Conectado a**: AI Agent (ai_tool)

**Sub-nodo: Crear / Actualizar contacto (Airtable)**
- **Tipo**: n8n-nodes-base.airtableTool
- **Operación**: upsert
- **Base**: Atankalama leads
- **Tabla**: CRM_Leads_Chatbot
- **Matching Column**: Numero de Telefono
- **Función**: Crea o actualiza registro del lead
- **Campos** (18 campos mapeados con $fromAI):
  - Nombre Completo
  - Email
  - RUT/DNI
  - Check-in / Check-out
  - Huéspedes
  - Hotel de Interés
  - Tipo de Consulta
  - Status
  - Fuente del Lead (fijo: "Whatsapp")
  - Segmento de Lead
  - Idioma Preferido
  - Priorización de Lead
  - Empresa
  - Notas Internas
- **Conectado a**: AI Agent (ai_tool)

**Sub-nodo: Contactar Humano (Telegram)**
- **Tipo**: Custom tool (probablemente HTTP Request a Telegram Bot API)
- **Función**: Envía notificación al equipo
- **Casos de uso**:
  - Cliente solicita hablar con humano
  - Oportunidad B2B detectada
  - Pregunta sin respuesta en FAQ
- **Conectado a**: AI Agent (ai_tool)
- **Nota**: Migración a Slack pendiente

**Sub-nodo: Registrar Feedback**
- **Tipo**: Airtable upsert
- **Función**: Guarda calificación del cliente (1-5)
- **Campos actualizados**:
  - Satisfacción Conversación (AI): número 1-5
  - Comentario feedback chatbot: texto opcional
- **Conectado a**: AI Agent (ai_tool)

#### 7.3 Flujo de Datos

**Estructura de datos entre nodos**:

```javascript
// Después de Edit Fields:
{
  Numero_Telefono: "+56912345678",
  Mensaje: "Hola, quiero reservar", // o vacío si es audio
  Tipo_Mensaje: "text" | "audio",
  ID: "assignee_id",
  ID_Chatwoot: "12345",
  serverUrl: "https://app.chatwoot.com"
}

// Después de Switch → Audio → Edit Fields2:
{
  ...datos_previos,
  mensaje: "texto transcrito del audio"
}

// Después de Merge1:
{
  Numero_Telefono: "+56912345678",
  mensaje: "contenido del mensaje (texto o transcrito)",
  ID_Chatwoot: "12345",
  ...otros_campos
}

// Input al AI Agent:
Texto del prompt:
"Mensaje: {mensaje}
Conversacion ID: {ID_Chatwoot}
Numero de Telefono: {Numero_Telefono}"
```

#### 7.4 Integraciones Externas

**Airtable (CRM)**
- **Base**: Atankalama leads (ID: applhMgcR7lh6UDlT)
- **Tabla**: CRM_Leads_Chatbot (ID: tblSTBwvHaxvSd5Fq)
- **Autenticación**: OAuth2 (Personal Access Token)
- **Operaciones**: search, upsert
- **Campos clave**:
  - Numero de Telefono (matching key)
  - Status, Tipo de Consulta, Segmento de Lead
  - Check-in, Check-out, Huéspedes
  - Satisfacción Conversación (AI)

**Supabase (Vector Database)**
- **Tabla**: faq_antankalama
- **Función**: Almacena embeddings de FAQ de hoteles
- **Query**: match_documents
- **Autenticación**: Supabase API Key
- **Uso**: RAG (Retrieval Augmented Generation)

**OpenAI**
- **Modelos usados**:
  - gpt-4.1-mini: Chat principal
  - whisper-1: Transcripción de audio
  - text-embedding-ada-002: Embeddings (implícito)
- **Autenticación**: API Key

**PostgreSQL (Memoria)**
- **Función**: Almacena historial de conversaciones
- **Key**: conversation_id de Chatwoot
- **Tipo**: Postgres Chat Memory (LangChain)
- **Persistencia**: Indefinida (hasta limpieza manual)

**Telegram (Notificaciones)**
- **Uso actual**: Notificaciones al equipo
- **Casos**: Escalamiento, oportunidades B2B, conocimiento faltante
- **Plan**: Migrar a Slack

**Chatwoot (Frontend)**
- **Función**:
  - Interfaz con WhatsApp
  - Gestión de conversaciones
  - Webhooks a n8n
- **Conexión**: Webhook POST a n8n

#### 7.5 Consideraciones de Escalabilidad

**Límites Actuales**:
- Max iterations del AI Agent: 15
- Memoria Postgres: Sin límite definido (riesgo de crecimiento)
- Timeout de transcripción: Por defecto (puede fallar con audios muy largos)

**Puntos de Mejora**:
1. Implementar limpieza de memoria antigua en Postgres
2. Agregar timeout explícito para transcripciones
3. Manejo de rate limits de OpenAI
4. Circuit breaker para servicios externos

---

### 8. docs/SECURITY.md

**Contenido**:

```markdown
# 🔒 Guía de Seguridad - Proyecto N8N Ayudante

## 🎯 Principio Fundamental

**NUNCA subir información sensible a repositorios públicos**

---

## ✅ QUÉ SÍ SE DOCUMENTA PÚBLICAMENTE

### Información Técnica Segura
- ✅ Arquitectura de workflows (diagramas, flujos)
- ✅ Nombres de nodos y tipos
- ✅ Lógica de negocio
- ✅ Stack tecnológico (nombres de servicios)
- ✅ Estructura de datos (schema sin valores reales)
- ✅ IDs de workflows (no son sensibles por sí solos)
- ✅ Código JavaScript/Python (sin credenciales)
- ✅ Prompts y configuraciones del AI Agent

### Datos de Ejemplo
- ✅ Nombres ficticios: "María González", "Juan Pérez"
- ✅ Emails ficticios: "ejemplo@email.com"
- ✅ Teléfonos ficticios: "+56912345678"
- ✅ IDs genéricos: "ID123", "abc-def-ghi"

---

## ❌ QUÉ NUNCA SE SUBE A GITHUB

### Credenciales y Accesos
- ❌ API Keys completas (OpenAI, Airtable, Supabase, etc.)
- ❌ Tokens de autenticación
- ❌ Passwords de bases de datos
- ❌ OAuth2 secrets
- ❌ Webhooks con tokens sensibles

### URLs y Endpoints
- ❌ URL real de instancia n8n
- ❌ URLs de webhooks con tokens
- ❌ Endpoints internos de APIs
- ❌ IPs de servidores

### Datos Reales de Clientes
- ❌ Nombres reales de clientes
- ❌ Emails reales
- ❌ Teléfonos reales
- ❌ RUTs/DNIs
- ❌ Datos de tarjetas de crédito
- ❌ Información personal identificable (PII)

### Información de Negocio
- ❌ Precios confidenciales
- ❌ Acuerdos comerciales
- ❌ Datos financieros
- ❌ Información estratégica

---

## 🔄 USO DE PLACEHOLDERS

### En Documentación

**URLs**:
```markdown
❌ MAL:
URL: https://n8n-n8n.pvwkqy.easypanel.host

✅ BIEN:
URL: [URL protegida - ver .env]
```

**API Keys**:
```markdown
❌ MAL:
API_KEY: sk-proj-abc123def456...completa

✅ BIEN:
API_KEY: sk-****...****  (almacenada en .env)
```

**Datos de Clientes**:
```markdown
❌ MAL:
Cliente: Roberto Silva - roberto.silva@empresa.cl - +56987654321

✅ BIEN:
Cliente: [Nombre Cliente] - [email@example.com] - +56912345678
```

---

## 📁 ARCHIVOS PROTEGIDOS

### .env
**Contenido**:
```bash
# n8n Instance
N8N_API_URL=https://[tu-url-real].easypanel.host
N8N_API_KEY=eyJhbGc....[token completo]

# OpenAI
OPENAI_API_KEY=sk-proj-[tu-key-completa]

# Airtable
AIRTABLE_TOKEN=pat[tu-token-completo]

# Supabase
SUPABASE_URL=https://[tu-proyecto].supabase.co
SUPABASE_KEY=[tu-key-completa]

# PostgreSQL
POSTGRES_HOST=[host]
POSTGRES_USER=[usuario]
POSTGRES_PASSWORD=[password]
POSTGRES_DB=[database]

# Telegram Bot (temporal)
TELEGRAM_BOT_TOKEN=[token]
TELEGRAM_CHAT_ID=[id]
```

**Protección**: SIEMPRE en .gitignore

---

## 🛡️ .gitignore COMPLETO

```gitignore
# Environment variables
.env
.env.local
.env.production
.env.development
*.env

# Credentials
credentials.json
secrets/
keys/
*.pem
*.key
*.cert

# n8n specific
.n8n/
workflows-backup/
exports/

# Backups con datos reales
backups/
dumps/
*.sql
*.dump

# Logs con información sensible
logs/
*.log
npm-debug.log*

# Testing data real
test-data/
mock-conversations/
real-client-data/

# OS files
.DS_Store
Thumbs.db
desktop.ini

# IDE
.vscode/settings.json
.idea/
*.swp
*.swo

# Node
node_modules/
package-lock.json (opcional)

# Temporal
tmp/
temp/
*.tmp
```

---

## 🧪 PROTOCOLO DE TESTING SEGURO

### Ambiente de Testing

**Regla de Oro**: NUNCA testear con datos reales de producción

1. **Duplicar Workflow**
   - Crear copia del workflow con sufijo `-test`
   - Usar credenciales de sandbox cuando sea posible
   - Configurar webhooks de prueba separados

2. **Datos de Prueba Ficticios**
   ```json
   {
     "nombre": "Test User",
     "email": "test@example.com",
     "telefono": "+56911111111",
     "empresa": "Test Company",
     "rut": "11.111.111-1"
   }
   ```

3. **Notificaciones de Testing**
   - Crear canal separado (ej: #bot-testing)
   - No enviar a canales de producción
   - Marcar claramente como "TEST" en mensajes

### Checklist Pre-Testing

- [ ] Workflow duplicado (no modificar producción)
- [ ] Datos ficticios preparados
- [ ] Credenciales de sandbox configuradas (si aplica)
- [ ] Canal de notificaciones de testing creado
- [ ] Backup del workflow original guardado

---

## ✅ CHECKLIST PRE-COMMIT

**Antes de hacer commit y push a GitHub**:

### Paso 1: Revisar Archivos Modificados
```bash
git status
git diff
```

### Paso 2: Verificar Contenido Sensible
- [ ] ¿Hay API keys completas?
- [ ] ¿URLs reales expuestas?
- [ ] ¿Nombres reales de clientes?
- [ ] ¿Emails o teléfonos reales?
- [ ] ¿Datos de producción en ejemplos?

### Paso 3: Validar Placeholders
- [ ] URLs reemplazadas por `[URL protegida]`
- [ ] API keys parcialmente ocultas: `sk-****...****`
- [ ] Datos de ejemplo ficticios

### Paso 4: Verificar .gitignore
- [ ] .env está listado
- [ ] Carpetas sensibles están excluidas
- [ ] No hay archivos sensibles en staging

### Paso 5: Commit Seguro
```bash
# Revisar qué se va a commitear
git diff --staged

# Si todo OK, commit
git commit -m "Descripción clara del cambio"

# Doble check antes de push
git log -1 --stat

# Push a GitHub
git push origin main
```

---

## 🚨 QUÉ HACER SI SE SUBE INFO SENSIBLE

### Acción Inmediata

**Si te das cuenta ANTES de que pase mucho tiempo (< 1 hora)**:

1. **NO hacer más commits "corrigiendo"** (la info queda en historial)

2. **Eliminar del historial con BFG Repo Cleaner**:
   ```bash
   # Backup del repo
   cp -r N8N_ayudante N8N_ayudante_backup

   # Usar BFG
   bfg --replace-text passwords.txt N8N_ayudante

   # Force push
   git push --force
   ```

3. **Rotar TODAS las credenciales expuestas**:
   - Regenerar API keys en OpenAI, Airtable, etc.
   - Cambiar passwords de bases de datos
   - Crear nuevos tokens

**Si ya pasó mucho tiempo**:

1. **Rotar credenciales inmediatamente**
2. **Documentar el incidente**
3. **Monitorear por uso no autorizado**
4. **Considerar hacer el repo privado temporalmente**

---

## 🔐 BUENAS PRÁCTICAS ADICIONALES

### Gestión de Credenciales

1. **Variables de Entorno**
   - Usar `.env` para desarrollo local
   - Usar secrets management para producción

2. **Rotación Regular**
   - API keys cada 90 días (mínimo)
   - Passwords cada 60 días

3. **Principio de Menor Privilegio**
   - Dar solo permisos necesarios
   - Usar API keys con scope limitado

### Documentación

1. **Siempre usar ejemplos ficticios**
2. **Marcar claramente qué es público vs privado**
3. **Incluir WARNING en documentos sensibles**

### Comunicación con el Equipo

1. **NUNCA enviar credenciales por email/Slack**
2. **Usar herramientas seguras** (1Password, LastPass)
3. **Comunicar cambios de credenciales al equipo**

---

## 📚 Recursos

- [GitHub: Removing sensitive data](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
- [OWASP: Credential Management](https://owasp.org/www-community/vulnerabilities/Use_of_hard-coded_password)
- [BFG Repo Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)

---

**Última actualización**: 5 de Marzo 2026
**Mantenedor**: NicoCalama + Claude AI Assistant
```

---

### 9. docs/WORKFLOW_TEMPLATE.md

**Template para documentar futuros workflows**:

```markdown
# [Nombre del Workflow]

**ID**: `workflow-id-aqui`
**Estado**: 🟢 Activo | 🔴 Inactivo | 🟡 En Desarrollo
**Creado**: YYYY-MM-DD
**Última modificación**: YYYY-MM-DD

---

## 📋 Overview

### Objetivo
[Descripción clara de qué problema resuelve este workflow]

### Contexto de Negocio
[Por qué existe este workflow, qué proceso automatiza]

---

## 🔧 Stack Técnico

### Servicios Externos
- 🔹 **Servicio 1**: Descripción breve
- 🔹 **Servicio 2**: Descripción breve

### Nodos n8n
Total de nodos: X

#### Triggers
1. [Nombre] - Descripción

#### Procesamiento
2. [Nombre] - Descripción
3. [Nombre] - Descripción

#### Integraciones
4. [Nombre] - Descripción

---

## 🔄 Flujo de Datos

### Diagrama
```
[ASCII diagram del flujo]
```

### Descripción
[Explicación paso a paso del flujo]

---

## 📊 Casos de Uso

### Caso 1: [Título]
**Input**: ...
**Proceso**: ...
**Output**: ...

---

## ⚙️ Configuración

### Variables de Entorno
```bash
# Lista de variables necesarias
VAR_1=descripción
VAR_2=descripción
```

### Credenciales Necesarias
- [ ] Servicio 1 API Key
- [ ] Servicio 2 OAuth2

---

## 🐛 Problemas Conocidos

[Link a ISSUES.md o lista aquí]

---

## 📈 Métricas

- Ejecuciones por día: X
- Tasa de éxito: X%
- Tiempo promedio: X segundos

---

## 🚀 Próximas Mejoras

- [ ] Mejora 1
- [ ] Mejora 2

---

## 📝 Changelog

Ver: [CHANGELOG.md](./CHANGELOG.md)

---

**Última revisión**: YYYY-MM-DD
**Documentado por**: [Nombre]
```

---

## 🚀 PLAN DE EJECUCIÓN

### Fase 1: Documentación (En Curso)

**Status**: ⏸️ Pausado para crear este Plan Maestro

**Próximos pasos cuando se apruebe continuar**:

1. ✅ Crear carpetas:
   ```bash
   mkdir -p docs/workflows/whatsapp-asistente
   ```

2. ✅ Crear archivos base:
   - README.md principal (actualizado)
   - docs/SECURITY.md
   - docs/WORKFLOW_TEMPLATE.md
   - docs/workflows/whatsapp-asistente/README.md
   - docs/workflows/whatsapp-asistente/CHANGELOG.md
   - docs/workflows/whatsapp-asistente/ISSUES.md
   - docs/workflows/whatsapp-asistente/ARCHITECTURE.md
   - docs/workflows/whatsapp-asistente/PROMPT.md
   - docs/workflows/whatsapp-asistente/MESSAGE_PROCESSING.md
   - docs/workflows/whatsapp-asistente/TESTING.md

3. ✅ Actualizar .gitignore

4. ✅ Revisar con Nico

5. ✅ Commit a GitHub

---

### Fase 2: Análisis de Procesamiento de Mensajes

**Objetivo**: Identificar y resolver problemas en el manejo de texto/voz

**Requiere**: Feedback de Nico sobre comportamiento actual

**Entregables**:
- Documento de análisis técnico completo
- Identificación de puntos de fallo
- Propuestas de optimización

---

### Fase 3: Rediseño del Prompt

**Objetivo**: Hacer al agente más natural y menos robótico

**Pasos**:
1. Crear 3 versiones del prompt
2. Presentar a Nico para elección
3. Duplicar workflow para testing
4. Implementar prompt elegido
5. Testing con casos reales
6. Ajustes iterativos
7. Implementación en producción

**Criterios de éxito**:
- Conversaciones suenan naturales
- Mantiene control y no alucina
- No pierde hilo conversacional
- Cliente percibe calidez

---

### Fase 4: Mejoras Futuras (Post-Aprobación)

**Pendientes**:
- Migración a Slack (cuando equipo apruebe)
- Ampliación de base de conocimiento
- Métricas y analytics
- Multiidioma

---

## 📊 MÉTRICAS DE ÉXITO DEL PROYECTO

### Documentación
- ✅ Estructura completa creada
- ✅ Todos los archivos documentados
- ✅ Sin información sensible expuesta
- ✅ Template replicable para futuros workflows

### Optimización Técnica
- ⏳ Problemas de procesamiento identificados
- ⏳ Optimizaciones implementadas
- ⏳ Latencia reducida (meta: <2 segundos)

### Naturalidad del Agente
- ⏳ Prompt rediseñado
- ⏳ Testing completado
- ⏳ Calificación de naturalidad: 8/10 o superior
- ⏳ Reducción de escalamientos innecesarios

---

## 🔄 MANTENIMIENTO DE ESTE DOCUMENTO

**Este es un documento vivo**

### Cuándo Actualizar
- Cada vez que se complete una fase
- Cuando cambien prioridades
- Cuando surjan nuevos problemas
- Al finalizar mejoras importantes

### Responsable
- NicoCalama (decisiones de negocio)
- Claude AI Assistant (actualización técnica)

### Historial de Cambios

**v1.0 - 5 de Marzo 2026**
- Creación inicial del Plan Maestro
- Documentación completa de estructura
- Definición de fases

---

## 📞 CONTACTO Y SOPORTE

**Proyecto**: N8N Ayudante
**Owner**: NicoCalama
**GitHub**: github.com/NicoCalama
**Asistente IA**: Claude Code + n8n Skills

---

## 🎯 RECORDATORIOS CLAVE

### Para Nico:
- ✅ Este documento es tu guía permanente
- ✅ Consulta PLAN_MAESTRO.md antes de cada sesión
- ✅ Actualiza cuando cambien prioridades
- ✅ Usa checklist de seguridad antes de cada commit

### Para Claude:
- ✅ Seguir metodología de 5 fases religiosamente
- ✅ NUNCA subir info sensible
- ✅ Siempre pedir aprobación antes de cambios mayores
- ✅ Documentar TODO en CHANGELOG
- ✅ Mantener este plan actualizado

---

**"El plan es nuestra brújula, la documentación es nuestro mapa."**

---

**FIN DEL PLAN MAESTRO** ✅
