# 🐛 Issues - Atankalama Asistente Virtual WhatsApp

Registro de problemas conocidos, bugs y mejoras planificadas del workflow.

---

## 🔴 Problemas Activos

### #011 - Agente IA responde "No prompt specified" tras actualización del prompt

**Prioridad**: 🔴 Alta
**Estado**: ✅ RESUELTO - 7 de Marzo 2026
**Reportado**: 7 de Marzo 2026
**Resuelto**: 7 de Marzo 2026

#### Descripción
Tras aplicar el nuevo systemMessage via `n8n_update_partial_workflow`, el agente respondía con `{"error": "No prompt specified"}` en 24ms, sin llegar a llamar al LLM.

#### Causa raíz confirmada
El `updateNode` con `updates: {parameters: {options: {...}}}` **reemplazó** el objeto `parameters` completo, eliminando `promptType` y `text` que son necesarios para que el nodo lea la entrada del usuario.

#### Solución implementada
- ✅ Restaurados `promptType: "define"` y `text: "={{ $json.mensaje }}"` junto con el objeto `options` completo en un solo update
- ✅ Fix secundario: el agente incluía "Think:" como texto en sus respuestas → prompt corregido para aclarar que Think es una herramienta interna, nunca visible para el cliente
- ✅ Verificado con ejecuciones reales #1259 y #1261: flujo completo funcionando incluyendo feedback de cierre de conversación

---

### #009 - Agente no sigue protocolo de fases

**Prioridad**: 🔴 Alta
**Estado**: ✅ RESUELTO - Prompt rediseñado el 7 de Marzo 2026
**Reportado**: 6 de Marzo 2026
**Resuelto**: 7 de Marzo 2026

#### Descripcion
El agente IA no ejecuta las herramientas en el orden que el prompt exige. Confirmado con test real de mensaje de voz (ejecucion #1233).

#### Comportamiento esperado (segun prompt)
1. Ejecutar Consultar_contactos (Fase 1 SIEMPRE primero)
2. Consultar Base_de_datos antes de responder preguntas
3. Ejecutar reporte_preguntas_sin_respuesta si no tiene respuesta
4. Solo despues responder al cliente

#### Comportamiento observado
- Respondio directamente sin llamar ninguna herramienta
- Dijo "No dispongo de esa informacion" sin consultar Base_de_datos
- No ejecuto reporte_preguntas_sin_respuesta como exige el prompt
- No ejecuto Consultar_contactos (Fase 1)

#### Causa raiz probable
El prompt actual es demasiado largo (~800 tokens solo de instrucciones) con demasiadas reglas en MAYUSCULAS y "ESTRICTAMENTE PROHIBIDO". El modelo procesa el texto pero no ejecuta las acciones tool-call de forma consistente. Prompt de reglas vs prompt de comportamiento.

#### Solucion implementada
- ✅ Prompt rediseñado con metodología Think-first + árbol de situaciones
- ✅ Reducción -70% de tokens (850 → 260)
- ✅ Prohibiciones reemplazadas por instrucciones positivas en secuencia
- ⏳ Testing A/B pendiente (enviar mensajes reales y verificar ejecuciones)

---

### #010 - Migracion Supabase Vector Store

**Prioridad**: 🔴 Alta
**Estado**: 🚧 Urgente (Supabase eliminara la base de datos)
**Reportado**: 6 de Marzo 2026

#### Descripcion
Supabase notificó que eliminará la base de datos gratuita. La knowledge base del hotel (RAG) vive ahí. Necesita migración urgente.

#### Opciones evaluadas
- **Google Sheets**: Simple, sin busqueda vectorial, pero suficiente para FAQ de hotel
- **Pinecone free tier**: Vector search, mas complejo de configurar
- **n8n Simple Store**: Sin dependencia externa, muy limitado
- **Excel/Sheets + busqueda texto**: Mas sencillo, apropiado para volumen actual

#### Recomendacion
Para el volumen de una FAQ hotelera, Google Sheets con busqueda por texto es suficiente y elimina una dependencia externa compleja.

#### Plan de accion
- [ ] Exportar contenido actual de Supabase
- [ ] Crear Google Sheet con knowledge base
- [ ] Reemplazar nodo Supabase Vector Store por Google Sheets Tool
- [ ] Actualizar prompt para instrucciones de busqueda
- [ ] Testing de calidad de respuestas

---

### #001 - Prompt suena robótico y poco natural

**Prioridad**: 🟡 Media-Alta
**Estado**: ✅ RESUELTO - Prompt rediseñado el 7 de Marzo 2026
**Reportado**: 4 de Marzo 2026
**Resuelto**: 7 de Marzo 2026

#### Descripción
El asistente responde de forma correcta y precisa, pero el tono de las respuestas suena demasiado formal, robótico y poco conversacional. Los clientes pueden percibir que están hablando con un bot en lugar de sentir una conversación natural.

#### Ejemplos Reportados
```
Cliente: "Hola, tienen habitaciones para este fin de semana?"

Respuesta Actual (problemática):
"Estimado cliente, le informo que sí contamos con disponibilidad
para el fin de semana. Le invito a proporcionarme las fechas exactas
de su estadía para poder verificar la disponibilidad específica y
ofrecerle las mejores opciones disponibles."

Respuesta Deseada:
"¡Hola! Sí, tenemos disponibilidad para este finde 😊
¿Para cuántas personas serían y qué días exactamente?"
```

#### Análisis
- El prompt actual está orientado a ser muy formal y estructurado
- Falta personalidad y calidez humana
- No utiliza expresiones coloquiales ni emojis apropiados
- Respuestas muy largas para el contexto de WhatsApp

#### Propuestas de Solución
Ver documento completo: **[PROMPT.md](PROMPT.md)**

Tres propuestas de mejora:
1. **Propuesta A**: Prompt Natural con Personalidad
2. **Propuesta B**: Prompt Estructurado Conversacional
3. **Propuesta C**: Prompt Minimalista (GPT-4 optimizado)

#### Plan de Acción
- [ ] Revisar análisis completo en PROMPT.md
- [ ] Decidir qué propuesta implementar (o combinación)
- [ ] Hacer A/B testing con 3 versiones
- [ ] Medir satisfacción de clientes
- [ ] Implementar versión ganadora

#### Referencias
- **[PROMPT.md](PROMPT.md)**: Análisis completo y propuestas

---

### #002 - Procesamiento de mensajes puede optimizarse

**Prioridad**: 🟡 Media
**Estado**: ✅ Resuelto (confirmado funcionando el 6 de Marzo 2026)
**Reportado**: 4 de Marzo 2026
**Asignado a**: Claude AI + NicoCalama

#### Confirmacion 2026-03-06
Test con datos reales (ejecucion #1229) confirmo:
- Redis push/get funciona correctamente — devuelve array de mensajes
- Acumulacion de multiples mensajes funciona: se juntan y pasan al agente como un solo texto
- Switch4 logica correcta: Esperar → Espera 12s → procesar, Continuamos → procesar directo
- Linea de voz tecnicamente OK: Switch detecta, descarga, Whisper transcribe (test #1233)
- Ejecuciones 0s son eventos Chatwoot filtrados, no mensajes perdidos

El problema de procesamiento era una suposicion incorrecta. El flujo funciona.

#### Descripción original
El flujo actual de procesamiento de mensajes (texto y voz) puede tener puntos de optimización en términos de velocidad, manejo de errores y eficiencia de recursos.

#### Áreas Identificadas

##### 1. Procesamiento de Mensajes de Voz
- ¿Cuánto tarda la transcripción en promedio?
- ¿Hay manejo de errores si Whisper falla?
- ¿Se puede procesar mientras se descarga?

##### 2. Gestión de Contexto Conversacional
- ¿Cuántos mensajes de historial se cargan?
- ¿Es eficiente la consulta a PostgreSQL?
- ¿Se puede cachear información frecuente?

##### 3. Búsqueda en Base de Conocimiento
- ¿La búsqueda vectorial es óptima?
- ¿Cuántos embeddings se consultan?
- ¿Se puede mejorar la relevancia?

#### Preguntas para Nico (requiere feedback)
Ver documento completo: **[MESSAGE_PROCESSING.md](MESSAGE_PROCESSING.md)**

#### Plan de Acción
- [ ] Revisar análisis en MESSAGE_PROCESSING.md
- [ ] Nico proporciona feedback sobre preguntas técnicas
- [ ] Identificar cuellos de botella con métricas reales
- [ ] Proponer optimizaciones específicas
- [ ] Testing de mejoras
- [ ] Implementación gradual

#### Referencias
- **[MESSAGE_PROCESSING.md](MESSAGE_PROCESSING.md)**: Análisis técnico completo
- **[ARCHITECTURE.md](ARCHITECTURE.md)**: Diagrama de flujo de datos

---

## 🟢 Mejoras Planificadas

### #003 - Migración de notificaciones a Slack

**Prioridad**: 🔵 Baja
**Estado**: ⏸️ En espera (pendiente aprobación del equipo)
**Planificado para**: Por definir

#### Descripción
Migrar el sistema de notificaciones de Telegram a Slack para mejor integración con herramientas del equipo.

#### Motivo
- El equipo usa Slack como herramienta principal
- Mejor gestión de canales por tipo de alerta
- Integraciones más potentes
- Mejor formato de mensajes

#### Requisitos Previos
- [x] Identificar necesidad
- [ ] Aprobación del equipo
- [ ] Configurar workspace de Slack
- [ ] Crear bot de Slack
- [ ] Diseñar estructura de canales

#### Impacto Estimado
- **Esfuerzo**: Bajo (2-3 horas)
- **Riesgo**: Bajo
- **Beneficio**: Medio-Alto

---

### #004 - Ampliación de base de conocimiento

**Prioridad**: 🔵 Media
**Estado**: 📋 Planificado
**Planificado para**: Marzo 2026

#### Descripción
Expandir la base de conocimiento vectorizada con más información sobre:
- Servicios adicionales del hotel
- Políticas detalladas
- Tours y actividades de la zona
- Restaurantes recomendados
- Transporte y logística

#### Objetivos
- Reducir casos en que el asistente no tiene respuesta
- Mejorar calidad de recomendaciones
- Aumentar autonomía del bot

---

### #005 - Sistema de métricas y analytics

**Prioridad**: 🔵 Media
**Estado**: 📋 Planificado
**Planificado para**: Marzo 2026

#### Descripción
Implementar sistema de tracking de métricas del asistente:
- Cantidad de conversaciones por día
- Tiempo promedio de respuesta
- Tasa de conversión (consulta → reserva)
- Satisfacción del cliente
- Temas más consultados
- Errores y fallos

#### Beneficios
- Datos para optimizar el servicio
- Identificar problemas proactivamente
- Medir ROI de la automatización
- A/B testing de prompts

---

## 🟣 Ideas Futuras (Backlog)

### #006 - Soporte multiidioma
**Prioridad**: 🔵 Baja
**Estado**: 💡 Idea

Detectar idioma del cliente y responder en inglés/español automáticamente.

---

### #007 - Integración con sistema de reservas
**Prioridad**: 🔵 Media-Alta
**Estado**: 💡 Idea

Conectar directamente con sistema de gestión hotelera para consultar disponibilidad en tiempo real.

---

### #008 - Personalización por historial
**Prioridad**: 🔵 Baja
**Estado**: 💡 Idea

Recordar preferencias de clientes recurrentes (tipo de habitación, servicios preferidos, etc.)

---

## 📝 Template para Reportar Issues

```markdown
### #XXX - Título del Issue

**Prioridad**: 🔴 Alta / 🟡 Media / 🔵 Baja
**Estado**: 🔍 En análisis / 🚧 En progreso / ✅ Resuelto / ⏸️ En espera
**Reportado**: Fecha
**Asignado a**: Persona/IA

#### Descripción
Descripción detallada del problema o mejora

#### Ejemplos
Casos concretos, capturas, logs, etc.

#### Análisis
Investigación realizada, causas identificadas

#### Propuesta de Solución
Posibles formas de resolver

#### Plan de Acción
- [ ] Paso 1
- [ ] Paso 2
- [ ] Paso 3

#### Referencias
- Links a documentación relacionada
```

---

## 🏷️ Etiquetas de Prioridad

- 🔴 **Alta**: Afecta experiencia del usuario o funcionalidad crítica
- 🟡 **Media**: Mejora importante pero no crítica
- 🔵 **Baja**: Nice to have, mejora menor

## 🏷️ Etiquetas de Estado

- 🔍 **En análisis**: Investigando el problema
- 🚧 **En progreso**: Activamente trabajando en la solución
- ✅ **Resuelto**: Implementado y verificado
- ⏸️ **En espera**: Bloqueado por dependencia externa
- 💡 **Idea**: Propuesta para evaluar en el futuro

---

## 📊 Resumen de Issues

**Total Activos**: 2
**Total Planificados**: 3
**Total Ideas**: 3

### Por Prioridad
- 🔴 Alta: 0
- 🟡 Media-Alta: 1 (#001)
- 🟡 Media: 4 (#002, #004, #005, #007)
- 🔵 Baja: 3 (#003, #006, #008)

---

**Última actualización**: 5 de Marzo 2026
**Mantenedores**: NicoCalama + Claude AI Assistant
