# ⚙️ Análisis de Procesamiento de Mensajes - WhatsApp Asistente

**Issue relacionado**: [#002 - Procesamiento de mensajes puede optimizarse](ISSUES.md#002---procesamiento-de-mensajes-puede-optimizarse)
**Prioridad**: 🟡 Media
**Estado**: 🔍 En análisis - Requiere feedback de Nico
**Última actualización**: 5 de Marzo 2026

---

## 🎯 Objetivo

Analizar el flujo actual de procesamiento de mensajes (texto y voz) para identificar:
- Cuellos de botella en performance
- Oportunidades de optimización
- Mejores prácticas a implementar
- Puntos de fallo potenciales

---

## 📋 Análisis del Flujo Actual

### Flujo de Mensajes de Texto

```
1. Webhook recibe mensaje de Chatwoot
   ├─ Tiempo: < 100ms
   └─ Riesgo: Bajo

2. Extracción de datos del contacto (Code Node)
   ├─ Tiempo: < 50ms
   └─ Riesgo: Bajo

3. [PARALELO] Construcción de contexto:
   ├─ 3a. Consulta PostgreSQL (historial conversación)
   │    ├─ Query: SELECT últimos 10 mensajes
   │    ├─ Tiempo estimado: ?
   │    └─ ⚠️ REQUIERE VALIDACIÓN
   │
   └─ 3b. Consulta Supabase (búsqueda vectorial RAG)
        ├─ Embedding del mensaje + búsqueda similitud
        ├─ Tiempo estimado: ?
        └─ ⚠️ REQUIERE VALIDACIÓN

4. Clasificación de cliente y detección de intención (Code Node)
   ├─ Tiempo: < 100ms
   └─ Riesgo: Bajo

5. Construcción del prompt completo (Code Node)
   ├─ Tiempo: < 50ms
   └─ Riesgo: Bajo

6. Llamada a OpenAI GPT-4.1-mini
   ├─ Tiempo estimado: ?
   ├─ Tokens promedio: ?
   └─ ⚠️ REQUIERE VALIDACIÓN

7. Validación de respuesta (Code Node)
   ├─ Tiempo: < 50ms
   └─ Riesgo: Bajo

8. Envío a Chatwoot (HTTP Request)
   ├─ Tiempo estimado: ?
   └─ ⚠️ REQUIERE VALIDACIÓN

9. [BACKGROUND] Post-procesamiento:
   ├─ Guardar en PostgreSQL
   ├─ Actualizar Airtable
   └─ Notificaciones condicionales

TIEMPO TOTAL ESTIMADO: 2-5 segundos
```

---

### Flujo de Mensajes de Voz

```
1-2. [Igual que texto hasta clasificación]

3. Descarga del audio desde Chatwoot
   ├─ Tamaño promedio: ?
   ├─ Tiempo estimado: ?
   └─ ⚠️ REQUIERE VALIDACIÓN

4. Transcripción con Whisper
   ├─ Duración promedio de audios: ?
   ├─ Tiempo de transcripción: ?
   ├─ Tasa de éxito: ?
   └─ ⚠️ REQUIERE VALIDACIÓN

5. Guardar audio en Google Drive
   ├─ ¿Se hace en paralelo con transcripción?
   ├─ Tiempo estimado: ?
   └─ ⚠️ REQUIERE VALIDACIÓN

6-N. [Continúa como flujo de texto]

TIEMPO TOTAL ESTIMADO: 5-15 segundos (+ duración del audio)
```

---

## ❓ Preguntas para Nico (Requiere Feedback)

### 📊 Performance y Métricas

#### 1. Tiempos de Respuesta Actuales
- ¿Cuál es el tiempo promedio de respuesta para mensajes de texto?
- ¿Y para mensajes de voz?
- ¿Hay variabilidad significativa? ¿Picos de lentitud?
- ¿Los clientes se quejan de lentitud?

#### 2. Volumen de Mensajes
- ¿Cuántos mensajes se procesan por día en promedio?
- ¿Cuál es el pico máximo de mensajes simultáneos?
- ¿Qué proporción son de texto vs voz?
- ¿Hay horarios de mayor tráfico?

---

### 🗄️ Base de Datos PostgreSQL

#### 3. Historial de Conversación
- ¿Cuántos mensajes se recuperan del historial? (configurado en 10 según docs)
- ¿Es suficiente o a veces se pierde contexto?
- ¿La query es rápida? ¿Hay índices optimizados?
- ¿Se podría cachear para conversaciones activas?

#### 4. Almacenamiento
- ¿Cuánto crece la tabla `conversation_history` por mes?
- ¿Hay estrategia de limpieza de conversaciones antiguas?
- ¿Se hace backup regularmente?

---

### 🔍 Supabase Vector Search (RAG)

#### 5. Base de Conocimiento
- ¿Cuántos documentos hay en la base de conocimiento?
- ¿Qué tan actualizada está?
- ¿Cuántos embeddings se consultan por búsqueda? (docs dicen 5)
- ¿La relevancia de los resultados es buena?

#### 6. Optimización de Búsqueda
- ¿El threshold de similitud (0.7) es apropiado?
- ¿Hay muchos casos donde no se encuentra info relevante?
- ¿Se podría mejorar la forma de hacer embeddings?

---

### 🎤 Procesamiento de Voz

#### 7. Whisper Performance
- ¿Cuánto tarda en promedio una transcripción?
- ¿La duración promedio de los audios es...? (10seg, 30seg, 1min?)
- ¿Qué tan precisa es la transcripción?
- ¿Hay errores frecuentes en la transcripción?

#### 8. Manejo de Audios
- ¿El audio se procesa mientras se descarga o secuencialmente?
- ¿Se guarda en Google Drive ANTES o DESPUÉS de transcribir?
- ¿Es necesario guardar TODOS los audios o solo algunos?
- ¿Hay límite de tamaño/duración de audio?

---

### 🤖 OpenAI GPT

#### 9. Configuración Actual
- ¿Qué parámetros usa? (temperature, max_tokens, etc.)
- ¿Cuántos tokens consume en promedio por respuesta?
- ¿Hay límite de rate limiting que afecte?
- ¿Cuánto cuesta aproximadamente por mes?

#### 10. Calidad de Respuestas
- ¿Qué % de respuestas son buenas sin intervención?
- ¿Cuántas requieren intervención humana?
- ¿Hay casos donde GPT inventa información?
- ¿Las respuestas son consistentes?

---

### 🔔 Notificaciones y Post-Procesamiento

#### 11. Sistema de Notificaciones
- ¿Cuántas notificaciones se envían por día?
- ¿El equipo está sobrecargado de alertas?
- ¿Las notificaciones son útiles o hay mucho ruido?
- ¿Se pierde alguna notificación importante?

#### 12. Airtable CRM
- ¿La actualización de Airtable es lenta?
- ¿Hay duplicación de leads?
- ¿La información que se captura es completa?

---

### 🐛 Errores y Manejo de Fallos

#### 13. Casos de Error
- ¿Qué errores son más frecuentes?
- ¿Cómo se manejan actualmente?
- ¿Hay logs de errores accesibles?
- ¿Se notifican los errores al equipo técnico?

#### 14. Casos Edge
- ¿Qué pasa si Chatwoot no responde?
- ¿Qué pasa si OpenAI da error?
- ¿Qué pasa si Whisper falla en transcribir?
- ¿Hay respuesta de fallback al cliente?

---

## 🎯 Oportunidades de Optimización Identificadas

### 🟢 Optimizaciones de Bajo Riesgo (Rápidas de Implementar)

#### 1. Paralelización de Post-Procesamiento
**Actual**: Post-procesamiento puede bloquear
**Propuesta**: Asegurar que guardado en DB, Airtable y notificaciones sean 100% asíncronos

**Beneficio estimado**: -500ms en tiempo de respuesta
**Esfuerzo**: Bajo
**Riesgo**: Muy bajo

---

#### 2. Caché de Historial para Conversaciones Activas
**Actual**: Query a PostgreSQL en cada mensaje
**Propuesta**: Cachear historial de conversaciones activas en memoria (Redis o n8n cache)

**Beneficio estimado**: -200ms por mensaje en conversaciones activas
**Esfuerzo**: Medio
**Riesgo**: Bajo

---

#### 3. Optimización de Prompt (Reducir Tokens)
**Actual**: Prompt puede ser muy largo
**Propuesta**: Ver [PROMPT.md - Propuesta C](PROMPT.md) (prompt minimalista)

**Beneficio estimado**:
- -30% tokens = menor costo
- -500ms en latencia de GPT
**Esfuerzo**: Bajo (solo cambiar prompt)
**Riesgo**: Bajo (testear antes)

---

#### 4. Índices de Base de Datos
**Actual**: Desconocido si están optimizados
**Propuesta**: Verificar y crear índices en:
- `conversation_history.conversation_id`
- `conversation_history.created_at`

**Beneficio estimado**: -100ms en queries
**Esfuerzo**: Muy bajo
**Riesgo**: Muy bajo

---

### 🟡 Optimizaciones de Riesgo Medio (Requieren Testing)

#### 5. Procesamiento Paralelo de Descarga + Transcripción de Voz
**Actual**: Desconocido si es secuencial o paralelo
**Propuesta**: Stream del audio mientras se transcribe

**Beneficio estimado**: -2-5 segundos en audios largos
**Esfuerzo**: Alto
**Riesgo**: Medio (requiere cambios significativos)

---

#### 6. Smart RAG (Búsqueda Condicional)
**Actual**: Búsqueda vectorial en cada mensaje
**Propuesta**: Solo buscar en RAG si el mensaje es pregunta (no saludos, confirmaciones simples)

**Beneficio estimado**: -500ms en 30% de mensajes
**Esfuerzo**: Medio
**Riesgo**: Medio (puede afectar calidad)

---

#### 7. Respuestas Pre-Cacheadas para FAQs
**Actual**: GPT responde todo desde cero
**Propuesta**: Detectar preguntas ultra-frecuentes y responder sin llamar a GPT

**Beneficio estimado**:
- -2 segundos en FAQs
- -80% costo en esos mensajes
**Esfuerzo**: Alto
**Riesgo**: Medio

---

### 🔴 Optimizaciones de Alto Impacto (Largo Plazo)

#### 8. Modelo Local para Clasificación
**Actual**: Todo pasa por GPT
**Propuesta**: Usar modelo pequeño local para clasificar tipo cliente y intención, GPT solo para respuesta

**Beneficio estimado**:
- -50% costo
- -1 segundo en latencia
**Esfuerzo**: Muy alto
**Riesgo**: Alto

---

#### 9. Streaming de Respuestas
**Actual**: Espera respuesta completa de GPT
**Propuesta**: Stream la respuesta a medida que GPT genera (si Chatwoot lo soporta)

**Beneficio estimado**: Percepción de -50% tiempo de respuesta
**Esfuerzo**: Alto
**Riesgo**: Medio

---

## 📊 Propuesta de Roadmap de Optimización

### Fase 1: Quick Wins (1-2 semanas)
- [ ] Validar índices de base de datos
- [ ] Asegurar post-procesamiento asíncrono
- [ ] Implementar Propuesta C de prompt (testing)

**Impacto esperado**: -30% tiempo de respuesta

---

### Fase 2: Optimizaciones Medias (1 mes)
- [ ] Implementar caché de historial
- [ ] Smart RAG (búsqueda condicional)
- [ ] Optimizar procesamiento de voz

**Impacto esperado**: -40% tiempo de respuesta total

---

### Fase 3: Mejoras Estructurales (2-3 meses)
- [ ] Sistema de métricas completo
- [ ] Respuestas pre-cacheadas para FAQs
- [ ] Evaluación de modelo local para clasificación

**Impacto esperado**: -50% costo, +30% velocidad

---

## 🔍 Análisis de Riesgos

### Puntos de Fallo Potenciales

#### 1. Dependencia de APIs Externas
**Riesgo**: Si OpenAI o Chatwoot caen, todo el workflow para
**Mitigación actual**: ¿Hay retry logic? ¿Timeout configurado?
**Propuesta**: Implementar circuit breaker pattern

#### 2. Rate Limiting
**Riesgo**: Límites de OpenAI pueden afectar en picos de tráfico
**Mitigación actual**: Desconocida
**Propuesta**: Queue de mensajes con rate limiting controlado

#### 3. Crecimiento de Base de Datos
**Riesgo**: `conversation_history` puede crecer infinitamente
**Mitigación actual**: Desconocida
**Propuesta**: Archivado automático de conversaciones > 90 días

#### 4. Calidad de Transcripciones
**Riesgo**: Whisper puede fallar con audios de mala calidad
**Mitigación actual**: Desconocida
**Propuesta**: Validación post-transcripción + opción de re-intentar

---

## 📝 Checklist de Validación Técnica

### Para Completar con Nico

#### Performance
- [ ] Tiempo promedio mensajes texto: _______
- [ ] Tiempo promedio mensajes voz: _______
- [ ] Mensajes/día promedio: _______
- [ ] Pico máximo simultáneo: _______

#### Base de Datos
- [ ] Índices actuales en PostgreSQL: _______
- [ ] Tamaño actual de `conversation_history`: _______
- [ ] Estrategia de limpieza: _______

#### Supabase RAG
- [ ] Número de documentos en KB: _______
- [ ] Relevancia de búsquedas (1-10): _______
- [ ] Threshold de similitud actual: _______

#### OpenAI
- [ ] Tokens promedio por respuesta: _______
- [ ] Costo mensual aproximado: _______
- [ ] Rate limits configurados: _______

#### Errores
- [ ] Error más frecuente: _______
- [ ] Frecuencia de errores: _______
- [ ] Sistema de logging actual: _______

---

## 🎯 Próximos Pasos

### 1. Feedback de Nico
- Responder preguntas de este documento
- Compartir métricas actuales si las tiene
- Identificar pain points principales

### 2. Priorización
- Decidir qué optimizaciones implementar primero
- Basado en impacto vs esfuerzo

### 3. Implementación
- Crear plan detallado de cada optimización
- Testing exhaustivo antes de producción
- Documentar en CHANGELOG.md

---

## 📚 Referencias

- **[ISSUES.md #002](ISSUES.md)**: Issue relacionado
- **[ARCHITECTURE.md](ARCHITECTURE.md)**: Arquitectura detallada
- **[PROMPT.md](PROMPT.md)**: Optimización de prompt
- **[README.md](README.md)**: Documentación general

---

## 💡 Notas Finales

Este análisis está basado en la documentación disponible y suposiciones técnicas.
Para optimizaciones concretas, necesitamos:

1. **Datos reales** de performance actual
2. **Logs** de ejecuciones del workflow
3. **Métricas** de uso y errores
4. **Feedback** de Nico sobre pain points

Una vez tengamos esta información, podemos crear un plan de optimización específico y medible.

---

**Última actualización**: 5 de Marzo 2026
**Estado**: Esperando feedback técnico de Nico
**Documentado por**: Claude AI
