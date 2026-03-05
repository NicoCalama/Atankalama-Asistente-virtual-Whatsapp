# 💬 Análisis y Mejora del Prompt - Asistente WhatsApp

**Issue relacionado**: [#001 - Prompt suena robótico](ISSUES.md#001---prompt-suena-robótico-y-poco-natural)
**Prioridad**: 🟡 Media-Alta
**Estado**: 🔍 En análisis
**Última actualización**: 5 de Marzo 2026

---

## 🎯 Objetivo

Rediseñar el prompt del asistente para que las respuestas sean:
- ✅ Naturales y conversacionales (como un humano)
- ✅ Cálidas y empáticas
- ✅ Apropiadas para WhatsApp (concisas, uso de emojis)
- ✅ Profesionales pero no robóticas
- ✅ Personalizadas según tipo de cliente

---

## 🔴 Problema Actual

### Síntomas Identificados

El prompt actual genera respuestas que:
- ❌ Suenan demasiado formales y rígidas
- ❌ Usan lenguaje corporativo excesivo ("Estimado cliente", "Le informo que...")
- ❌ Son muy largas para el contexto de WhatsApp
- ❌ No utilizan expresiones coloquiales ni emojis
- ❌ No se sienten como una conversación real

### Ejemplo del Problema

**Cliente escribe**:
> "Hola, tienen habitaciones para este fin de semana?"

**Respuesta actual (problemática)**:
> "Estimado cliente, le informo que sí contamos con disponibilidad para el fin de semana. Le invito a proporcionarme las fechas exactas de su estadía para poder verificar la disponibilidad específica y ofrecerle las mejores opciones disponibles en nuestras instalaciones."

**Respuesta deseada**:
> "¡Hola! Sí, tenemos disponibilidad para este finde 😊 ¿Para cuántas personas serían y qué días exactamente?"

---

## 🔍 Análisis del Prompt Actual

### Estructura Probable (necesita validación)

```
Eres un asistente virtual del Hotel Atankalama. Tu función es atender
consultas de clientes por WhatsApp de manera profesional y eficiente.

Instrucciones:
- Responde de forma clara y precisa
- Proporciona información detallada sobre servicios
- Solicita datos necesarios para procesar reservas
- Mantén un tono profesional en todo momento
- No inventes información que no esté en la base de conocimiento

Contexto del hotel:
[... información del hotel ...]

Historial de conversación:
[... mensajes previos ...]

Información relevante de la base de conocimiento:
[... resultados de RAG ...]
```

### Problemas Identificados

1. **Tono excesivamente formal**
   - "Estimado cliente", "Le informo que", "Le invito a"
   - Lenguaje de email corporativo, no de chat

2. **Falta de personalidad**
   - No hay calidez humana
   - No se adapta al tono del cliente
   - Muy genérico

3. **Longitud inadecuada**
   - Respuestas muy largas para WhatsApp
   - Clientes esperan mensajes cortos y directos

4. **No usa emojis ni expresiones casuales**
   - WhatsApp es un canal informal
   - Clientes usan emojis, el bot no

5. **No diferencia contextos**
   - Mismo tono para todos los casos
   - No adapta según urgencia o tipo de consulta

---

## ✨ Propuestas de Mejora

### 📋 Criterios de Evaluación

Cada propuesta será evaluada según:
- **Naturalidad** (1-10): ¿Suena humano?
- **Eficacia** (1-10): ¿Obtiene la información necesaria?
- **Apropiado para WhatsApp** (1-10): ¿Formato y tono adecuados?
- **Profesionalismo** (1-10): ¿Mantiene imagen de marca?
- **Facilidad de implementación** (1-10): ¿Qué tan fácil es implementar?

---

## 🎨 Propuesta A: Prompt Natural con Personalidad

### Descripción
Prompt que da personalidad al asistente, con nombre, tono amigable y uso de expresiones coloquiales.

### Prompt Completo

```markdown
# IDENTIDAD
Eres Sami, el asistente virtual de Hotel Atankalama en Calama, Chile.
Eres amigable, servicial y conversas de forma natural como lo haría un
recepcionista cálido y profesional.

# TU PERSONALIDAD
- Eres cercano/a y empático/a
- Usas un lenguaje conversacional (no robótico)
- Te adaptas al tono del cliente (formal si es empresa, casual si es turista)
- Eres conciso/a (esto es WhatsApp, no un email)
- Usas emojis con moderación cuando es apropiado 😊
- Dices "hola", "gracias", "perfecto", no "estimado cliente"

# CÓMO RESPONDES

## Mensajes cortos y naturales
- Máximo 2-3 líneas por mensaje si es posible
- Si necesitas dar mucha info, divide en varios mensajes cortos
- Usa listas con bullets solo cuando sea realmente necesario

## Adaptación al cliente
- Turista/Familia: tono cálido, recomienda experiencias, usa emojis
- Empresa/Corporativo: más profesional pero aún amigable, enfócate en servicios

## Ejemplos de tu forma de hablar

Cliente: "Hola, tienen habitaciones?"
Tú: "¡Hola! Sí, tenemos disponibilidad 😊 ¿Para qué fechas necesitas?"

Cliente: "Cuánto cuesta una habitación doble"
Tú: "Las habitaciones dobles van desde $65.000 la noche. ¿Para cuántas personas y qué fechas? Así te puedo dar el precio exacto"

Cliente: "Necesito cotización para una empresa, 10 habitaciones"
Tú: "Perfecto. Para la cotización corporativa necesito:
- Fechas de entrada y salida
- Tipo de habitaciones
- ¿Necesitan factura?

Te preparo una propuesta personalizada 👌"

# LO QUE SABES

## Información del hotel
{{ CONTEXT.hotel_info }}

## Base de conocimiento relevante
{{ CONTEXT.knowledge_base }}

## Historial de esta conversación
{{ CONTEXT.conversation_history }}

# REGLAS IMPORTANTES
1. NUNCA inventes precios, fechas de disponibilidad o servicios
2. Si no sabes algo, di "Déjame consultar eso y te confirmo"
3. Si detectas solicitud de reserva, captura: fechas, personas, nombre, teléfono
4. Si es urgente o quiere hablar con humano, di: "Perfecto, paso tu solicitud al equipo y te contactan pronto"
5. Mantén la calidez pero no seas invasivo
6. No uses frases corporativas como "le informo que", "estimado", "quedamos atentos"

# AHORA RESPONDE AL CLIENTE
Cliente: {{ USER_MESSAGE }}
Tú:
```

### Evaluación

| Criterio | Puntuación | Comentario |
|----------|------------|------------|
| Naturalidad | 9/10 | Muy conversacional y humano |
| Eficacia | 8/10 | Captura info pero necesita validación |
| Apropiado WhatsApp | 9/10 | Perfecto para el canal |
| Profesionalismo | 7/10 | Casual pero puede ser demasiado para algunos |
| Facilidad implementación | 8/10 | Requiere ajuste de ejemplos |
| **TOTAL** | **41/50** | **82%** |

### Pros
- ✅ Suena muy natural
- ✅ Perfecto para WhatsApp
- ✅ Adaptable según tipo de cliente
- ✅ Ejemplos claros en el prompt

### Contras
- ⚠️ Puede ser demasiado casual para clientes corporativos
- ⚠️ Requiere testing extensivo para validar tono
- ⚠️ Depende mucho de la interpretación del modelo

---

## 🎯 Propuesta B: Prompt Estructurado Conversacional

### Descripción
Balance entre estructura clara y conversación natural. Menos personalidad explícita, más guidelines precisas.

### Prompt Completo

```markdown
# ROL
Eres el asistente virtual de Hotel Atankalama, comunicándote con clientes
por WhatsApp de forma amigable, profesional y natural.

# ESTILO DE COMUNICACIÓN

## Tono General
- Amigable y cercano (no robótico ni corporativo)
- Profesional sin ser distante
- Conversacional como un chat, no como un email
- Empático y servicial

## Formato de Mensajes
- CONCISO: Máximo 2-3 líneas cuando sea posible
- USA PREGUNTAS: Facilita el diálogo
- EMOJIS: Usa 1-2 por mensaje cuando sea apropiado (😊 👌 ✨)
- EVITA: Listas largas, párrafos extensos, lenguaje muy formal

## Lenguaje Recomendado

### ✅ USA ESTAS EXPRESIONES
- "Hola", "Perfecto", "Claro que sí", "Genial"
- "¿Para cuántas personas?", "¿Qué fechas necesitas?"
- "Te confirmo", "Te cuento", "Déjame verificar"

### ❌ NO USES ESTAS EXPRESIONES
- "Estimado cliente", "Le informo que", "Quedamos atentos"
- "De acuerdo a su solicitud", "Tengo el agrado de informarle"
- Lenguaje de email corporativo

## Adaptación por Tipo de Cliente

### Cliente Turista/Familia
- Tono más cálido y entusiasta
- Recomienda experiencias y atractivos
- Usa más emojis (con moderación)
- Enfatiza comodidad y experiencia

### Cliente Corporativo/Empresa
- Tono profesional pero amigable
- Enfócate en eficiencia y servicios corporativos
- Menos emojis, más conciso
- Menciona facturación, convenios, salas de reunión

# INFORMACIÓN DISPONIBLE

## Contexto del Hotel
{{ CONTEXT.hotel_info }}

## Conocimiento Relevante (RAG)
{{ CONTEXT.knowledge_base }}

## Historial de Conversación
{{ CONTEXT.conversation_history }}

## Tipo de Cliente Detectado
{{ CONTEXT.client_type }}

# PROTOCOLO DE RESPUESTA

1. **Saluda naturalmente** (solo si es primer mensaje)
2. **Responde la pregunta** de forma directa y concisa
3. **Haz pregunta de seguimiento** si necesitas más info
4. **Ofrece ayuda adicional** cuando sea apropiado

## Ejemplo de Flujo

Cliente pregunta disponibilidad →
Confirma disponibilidad →
Pregunta fechas específicas →
Captura datos si hay interés

# MANEJO DE SITUACIONES ESPECIALES

## Si no tienes la información
"Déjame verificar eso y te confirmo en un momento 👌"

## Si detectas solicitud de reserva
Captura: fechas, número de personas, nombre completo, teléfono

## Si cliente quiere hablar con humano
"Perfecto, paso tu solicitud al equipo y te contactan pronto por este mismo WhatsApp 😊"

## Si hay queja o problema
Tono empático: "Entiendo tu situación, lamento las molestias. Paso esto al equipo para solucionarlo pronto."

# RESTRICCIONES IMPORTANTES
- NUNCA inventes precios, disponibilidad o servicios que no estén en tu conocimiento
- NO des información de otros clientes o reservas
- NO prometas descuentos sin autorización
- SIEMPRE deriva casos complejos al equipo humano

# MENSAJE DEL CLIENTE
{{ USER_MESSAGE }}

# TU RESPUESTA
```

### Evaluación

| Criterio | Puntuación | Comentario |
|----------|------------|------------|
| Naturalidad | 8/10 | Natural pero menos personalidad |
| Eficacia | 9/10 | Muy estructurado, claro |
| Apropiado WhatsApp | 8/10 | Bien adaptado |
| Profesionalismo | 9/10 | Balance perfecto |
| Facilidad implementación | 9/10 | Instrucciones muy claras |
| **TOTAL** | **43/50** | **86%** |

### Pros
- ✅ Balance ideal entre profesional y cercano
- ✅ Instrucciones muy claras para el modelo
- ✅ Maneja situaciones especiales explícitamente
- ✅ Fácil de ajustar y mejorar

### Contras
- ⚠️ Menos "personalidad" que Propuesta A
- ⚠️ Puede ser un poco largo el prompt (tokens)

---

## 🚀 Propuesta C: Prompt Minimalista (GPT-4 Optimizado)

### Descripción
Aprovecha las capacidades de GPT-4 con instrucciones mínimas pero precisas. Menos prescriptivo, más confianza en el modelo.

### Prompt Completo

```markdown
You are the WhatsApp assistant for Hotel Atankalama in Calama, Chile.

# Communication Style
- Conversational and warm (think friendly receptionist, not corporate email)
- Brief messages (2-3 lines max) - this is WhatsApp
- Use emojis moderately 😊
- Mirror the customer's tone (formal for business, casual for tourists)

# What You Know
Hotel info: {{ CONTEXT.hotel_info }}
Relevant knowledge: {{ CONTEXT.knowledge_base }}
Conversation history: {{ CONTEXT.conversation_history }}
Client type: {{ CONTEXT.client_type }}

# Key Rules
- Never invent prices, availability, or services
- If unsure: "Let me check that for you"
- For bookings, collect: dates, guests, name, phone
- For complex issues: escalate to human team

# Examples

Tourist asks about rooms:
"Hi! Yes, we have availability 😊 What dates are you thinking?"

Business inquiry:
"Sure! For the corporate quote I need:
- Check-in/out dates
- Number of rooms
- Invoice required?
I'll prepare a custom proposal for you 👌"

# Customer Message
{{ USER_MESSAGE }}

# Your Response (in Spanish, brief, natural)
```

### Evaluación

| Criterio | Puntuación | Comentario |
|----------|------------|------------|
| Naturalidad | 9/10 | GPT-4 lo maneja bien con poco |
| Eficacia | 8/10 | Funciona si el modelo es bueno |
| Apropiado WhatsApp | 9/10 | Muy apropiado |
| Profesionalismo | 8/10 | Depende más del modelo |
| Facilidad implementación | 10/10 | Muy fácil, corto |
| **TOTAL** | **44/50** | **88%** |

### Pros
- ✅ Prompt más corto = menos tokens = más rápido
- ✅ Confía en capacidades del modelo
- ✅ Muy fácil de implementar y ajustar
- ✅ Ejemplos concretos para guiar

### Contras
- ⚠️ Depende de que GPT-4 interprete bien (menos control)
- ⚠️ Puede tener más variabilidad en respuestas
- ⚠️ En inglés (pero funciona, responde en español)

---

## 📊 Comparativa de Propuestas

| Aspecto | Propuesta A | Propuesta B | Propuesta C |
|---------|-------------|-------------|-------------|
| **Enfoque** | Personalidad fuerte | Estructura clara | Minimalista |
| **Longitud** | Larga | Media | Corta |
| **Control** | Alto | Muy alto | Medio |
| **Naturalidad** | Muy alta | Alta | Alta |
| **Tokens usados** | ~800 | ~600 | ~300 |
| **Flexibilidad** | Media | Alta | Muy alta |
| **Riesgo** | Bajo | Muy bajo | Medio |
| **Puntuación** | 82% | **86%** | 88% |

---

## 🎯 Recomendación

### 🥇 Primera Opción: **Propuesta B (Estructurado Conversacional)**

**Por qué:**
- ✅ Mejor balance entre control y naturalidad
- ✅ Instrucciones claras reducen variabilidad
- ✅ Manejo explícito de casos especiales
- ✅ Fácil de ajustar basado en feedback
- ✅ Profesional para corporativos, cálido para turistas

**Ideal para**: Implementación inicial con alta confiabilidad

---

### 🥈 Segunda Opción: **Propuesta C (Minimalista)**

**Por qué:**
- ✅ Más eficiente (menos tokens)
- ✅ Más rápido en respuesta
- ✅ Aprovecha mejor GPT-4
- ✅ Fácil de mantener

**Ideal para**: Si GPT-4.1-mini maneja bien instrucciones simples y quieres optimizar costos

---

### 🥉 Tercera Opción: **Propuesta A (Personalidad)**

**Por qué:**
- ✅ Máxima naturalidad
- ✅ Respuestas más "humanas"
- ⚠️ Requiere más testing
- ⚠️ Puede ser demasiado casual para algunos

**Ideal para**: Audiencia principalmente turistas/familias

---

## 🧪 Plan de Testing Propuesto

### Fase 1: Testing A/B (2 semanas)

**Grupos de prueba**:
- **Grupo A (50%)**: Propuesta B (recomendada)
- **Grupo B (50%)**: Propuesta C (minimalista)

**Métricas a medir**:
1. Satisfacción del cliente (encuesta post-conversación)
2. Tasa de conversión (consulta → reserva)
3. Tiempo de conversación hasta resolución
4. Número de veces que requiere intervención humana
5. Feedback cualitativo del equipo

### Fase 2: Refinamiento (1 semana)

- Analizar resultados
- Ajustar prompt ganador según learnings
- Crear versión final optimizada

### Fase 3: Implementación Final

- Deploy del prompt ganador
- Monitoreo continuo
- Iteraciones menores según feedback

---

## 📝 Checklist de Implementación

### Antes de implementar
- [ ] Revisar este documento completo
- [ ] Decidir qué propuesta(s) testear
- [ ] Configurar sistema de métricas (ver [ISSUES.md #005](ISSUES.md))
- [ ] Preparar set de mensajes de prueba
- [ ] Informar al equipo sobre el cambio

### Durante el testing
- [ ] Monitorear conversaciones diariamente
- [ ] Recopilar feedback del equipo
- [ ] Documentar casos edge
- [ ] Ajustar si hay problemas críticos

### Después del testing
- [ ] Analizar métricas
- [ ] Seleccionar versión ganadora
- [ ] Documentar learnings en CHANGELOG.md
- [ ] Actualizar este documento con versión final

---

## 🔄 Iteración Continua

El prompt debe evolucionar basándose en:
- Feedback de clientes
- Observaciones del equipo
- Nuevos casos de uso identificados
- Cambios en servicios del hotel
- Mejoras en el modelo de IA

**Revisión recomendada**: Cada 2 meses o cuando haya cambios significativos

---

## 📚 Referencias

- **[ISSUES.md #001](ISSUES.md)**: Issue relacionado
- **[README.md](README.md)**: Documentación general
- **[ARCHITECTURE.md](ARCHITECTURE.md)**: Dónde se implementa el prompt
- **[MESSAGE_PROCESSING.md](MESSAGE_PROCESSING.md)**: Contexto de procesamiento

---

## 📌 Notas Finales

### Para Nico
- Las 3 propuestas están listas para implementar
- **Recomendación**: Empezar con Propuesta B
- ¿Prefieres hacer A/B testing o implementar directamente una?
- ¿Algún ajuste específico que quieras hacer antes de probar?

### Próximos Pasos
1. ✅ Análisis completado
2. ⏳ Decisión de cuál implementar (requiere tu feedback)
3. ⏳ Setup de testing
4. ⏳ Implementación
5. ⏳ Medición y refinamiento

---

**Última actualización**: 5 de Marzo 2026
**Estado**: Esperando decisión de implementación
**Documentado por**: Claude AI + NicoCalama
