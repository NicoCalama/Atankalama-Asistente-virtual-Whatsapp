# 📋 Changelog - Atankalama Asistente Virtual WhatsApp

Registro de todas las versiones y cambios significativos del workflow.

Formato basado en [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/)
Versionado siguiendo [Semantic Versioning](https://semver.org/lang/es/)

---

## [1.6.1] - 2026-03-26

### Fix: Resumen Slack solo mostraba último mensaje + preguntas parciales no registradas

#### Problema 1: Resumen en Slack era solo el último mensaje del cliente

**Contexto**: Al escalar a recepción vía "Contactar Humano", el mensaje en Slack (campo "Motivo") mostraba solo el último mensaje del cliente (ej: "Sí, por favor") en vez de un resumen de toda la conversación.

**Causa raíz**:
1. El schema del tool "Contactar Humano" (`2964a976`) pedía "Máximo 20 palabras explicando por qué necesita atención humana" — demasiado breve y orientado al motivo, no al resumen.
2. El prompt del Agente IA no especificaba formato ni longitud del resumen, ni prohibía enviar solo el último mensaje.
3. Cuando el LLM no proporcionaba `Resumen_conversacion`, el fallback caía a `$('Edit Fields').item.json['Mensaje']` que es solo el mensaje actual.

**Fixes aplicados**:
1. **Tool schema** (`2964a976`): `Resumen_conversacion` description cambiada a "Resumen completo de la conversación (40-80 palabras). Incluye: 1) Qué consultó el cliente desde el inicio. 2) Qué información se le entregó. 3) Por qué necesita atención humana ahora." + ejemplo concreto.
2. **System prompt** (`b6432bcb`): Nueva sección "CÓMO USAR Contactar Humano — RESUMEN OBLIGATORIO" con ejemplo correcto vs incorrecto. Regla: NUNCA enviar solo el último mensaje.

#### Problema 2: Pregunta con respuesta parcial no se registraba en Excel

**Contexto**: Roberto preguntó "¿cuentan con servicio de desayuno buffet?". El agente consultó "Base de datos", encontró "desayuno incluido" (sin especificar tipo), respondió correctamente que no sabía si era buffet y ofreció contacto humano — pero NO llamó "reporte de preguntas sin respuesta" para registrar la pregunta en el Excel.

**Causa raíz**: El prompt decía "PREGUNTA SIN RESPUESTA EN Base de datos → Llama reporte". El agente encontró una respuesta *parcial* (sí hay desayuno) y no la interpretó como "sin respuesta", por lo que no activó el reporte.

**Ejecución analizada**: #2152 (26 Mar 2026, 14:54) — nodos ejecutados: Consultar contactos ✅, Base de datos ✅, reporte de preguntas sin respuesta ❌ (no ejecutado).

**Fixes aplicados**:
1. **System prompt** (`b6432bcb`): Sección renombrada a "PREGUNTA SIN RESPUESTA **O CON RESPUESTA PARCIAL**". Nueva regla: si la respuesta encontrada es incompleta y no responde exactamente lo que el cliente preguntó, también debe llamar al reporte.
2. **Nuevo ejemplo en prompt**: Caso concreto del desayuno buffet como referencia para el LLM.

#### Nodos modificados

| Nodo | ID | Cambio |
|------|-----|--------|
| Contactar Humano | `2964a976-4304-41a8-ba67-703a9f13912b` | Schema description ampliado (20→40-80 palabras, con ejemplo) |
| Agente IA | `b6432bcb-0ded-4e43-aeec-169cdf3eac97` | +3 secciones en prompt: resumen obligatorio, respuesta parcial, ejemplo buffet |

---

## [2.0.0-dev] - 2026-03-23 (multi-credencial + precisión datos + Fase 3 diseño)

### Multi-credencial Cloudbeds (Pricing Sub-workflow)

#### Cambiado
- **Routing por hotel**: Nodo `If Hotel Inn` separa flujo en 2 ramas, cada una con su propia credencial Header Auth (Atankalama / Atankalama Inn)
- **4 HTTP Request** (2 por rama) en vez de 2 compartidos — evita problema de credencial única
- **3 Merge nodes**: Merge Atk + Merge Inn (combineByPosition) + Merge Final (append)
- **Code Formatear**: detecta rama activa vía try/catch, no muestra cantidades exactas de habitaciones
- **LLM Chain prompt**: instrucción de nunca mencionar cantidades exactas, siempre incluir link como fuente de verdad
- **Edit Fields**: agrega `startDate`/`endDate` (extraídos de `fechas_mencionadas` o default hoy/mañana)
- **Total nodos**: 13 (antes 8)

#### Agregado
- Nodo `If Hotel Inn` (routing por propiedad)
- Nodos HTTP duplicados por rama (getRoomTypes/getRatePlans × 2)
- Merge Inn + Merge Final
- Campo `hotel` como `required: true` en tool del orquestador

### Precisión de datos de disponibilidad

#### Investigado
- `getRoomTypes.roomsAvailable` = solo fecha actual, no acepta parámetros de fecha
- `getRatePlans.roomsAvailable` = por rate plan, no total de habitaciones reales
- `getAvailableRoomTypes` (v1.3) = retorna vacío para habitaciones `isPrivate: true`
- Dashboard Cloudbeds puede mostrar 10 disponibles mientras API muestra 0

#### Cambiado
- Code node usa "disponible" / "disponibilidad limitada" en vez de números exactos
- LLM Chain nunca dice cantidades, siempre apunta al link de reserva
- Link de reserva es la fuente de verdad para disponibilidad exacta

### Prompt del Orquestador

#### Cambiado
- Normalización obligatoria de `hotel` → `"atankalama"` o `"atankalama_inn"` antes de llamar Pricing
- Si hotel no está claro, preguntar al cliente antes de llamar al sub-agente
- Sección CUANDO LLAMES SUB-AGENTES actualizada con campos y valores exactos

### Fase 3 — Diseño Pre-Reserva (documentado, no implementado)

#### Documentado
- Patrón: POST /postReservation (pending) → link de pago → confirmación/cancelación
- Pre-reserva valida disponibilidad real y bloquea habitación durante pago
- Flujo determinístico (no agente) para operaciones financieras
- Prerequisito: investigar `isPrivate` antes de implementar
- Arquitectura documentada en ARCHITECTURE_V2.md

### Decisión Arquitectónica — Agente vs Flujo vs LLM Chain

| Situación | Solución |
|-----------|----------|
| Datos listos, solo interpretar/formatear | LLM Chain (Pricing) |
| Pasos fijos, operaciones críticas (dinero) | Flujo determinístico (Reservation, Fase 3) |
| Debe elegir entre herramientas según contexto | Agente (FAQ, CRM) |

---

## [2.0.0-dev] - 2026-03-21 (refactor Pricing + prompt orquestador)

### Refactor Pricing Sub-workflow

#### Cambiado
- **Pricing Agent → LLM Chain**: Reemplazado nodo Agent (con toolCode) por flujo determinístico: HTTP Request nativos + Code + LLM Chain (GPT-4o-mini)
- **Endpoints Cloudbeds corregidos**: `GET /getRoomTypes` y `GET /getRatePlans` (los anteriores `/rooms`, `/availability`, `/rates` no existían)
- **Multi-propiedad**: Soporte para Atankalama (209760) y Atankalama Inn (209761) via Edit Fields → propertyID
- **API key segura**: Migrada de hardcoded en toolCode a credencial Header Auth nativa
- **Link pre-llenado**: URL de reserva con `?currency=CLP&checkin=...&checkout=...&adults=...` para que el cliente solo confirme y pague

#### Eliminado
- Nodo `consultar_disponibilidad` (toolCode con endpoint inexistente)
- Nodo `Calculadora` (toolCalculator, innecesario con datos pre-procesados)
- Nodo `Pricing Agent` (Agent v1.7, reemplazado por LLM Chain)

#### Agregado
- `Edit Fields - Resolver Hotel` (hotel → propertyID, link, moneda por teléfono)
- `HTTP getRoomTypes` + `HTTP getRatePlans` (paralelo, con Header Auth)
- `Merge` (combineByPosition) + `Code - Formatear Disponibilidad`
- `LLM Chain - Pricing` (interpreta datos + formatea respuesta natural)

### Prompt del Orquestador

#### Cambiado
- Sección PRECIO/DISPONIBILIDAD/RESERVA: recopilación natural (sin formulario), confirmación antes de consultar, manejo de "sin disponibilidad" con alternativas
- Sección CUANDO LLAMES SUB-AGENTES: actualizada para reflejar campos del Pricing sub-workflow

---

## [2.0.0-dev] - 2026-03-20

### Fase 2 — Arquitectura Multi-Agente (en desarrollo)

#### Contexto

El agente monolítico v1.5.x (GPT-4.1 + 7 herramientas en un solo workflow) funciona bien en producción pero tiene límites de escala: cada nueva capacidad suma complejidad al prompt y carga al único LLM. La Fase 2 introduce un **orquestador + sub-agentes especializados** para separar responsabilidades.

**v1.5.x sigue activo en producción** mientras se construye y valida v2.0 en paralelo.

#### Arquitectura nueva

```
Orquestador (GPT-4.1)
  ├─ FAQ_Agent ─────► Sub-WF: GPT-4o-mini + Supabase Vector Store
  ├─ CRM_Agent ─────► Sub-WF: GPT-4o-mini + Airtable
  ├─ Pricing_Agent ─► Sub-WF: GPT-4o-mini + Cloudbeds API
  ├─ Contactar_Humano ► Sub-WF existente (K3WrelHxg7k9EePiD5-2S)
  ├─ reporte_preguntas ► Google Sheets (directo)
  └─ registrar_feedback ► Airtable (directo)
```

#### Estado actual — Fases 2A + 2B + 2C completadas, Testing en curso

**Workflows creados (activos, paralelos a producción):**

| Workflow | ID | Estado |
|---|---|---|
| FAQ Agent - Atankalama v2.0 | `Mq8XkIXWvZIyF2sZ` | Creado ✅, funcional ✅ |
| CRM Agent - Atankalama v2.0 | `LPeOJLQadME2Nbgv` | Creado ✅, funcional ✅ |
| Pricing Agent - Atankalama v2.0 | `X22IjZoUYkFxKyjw` | Creado ✅, funcional ✅ |
| Orquestador WhatsApp v2.0 | `KyWsxQJhr1SS0mS3` | Creado ✅, funcional ✅ |
| TEST Sub-Agentes v2 | `4YvYOs5lmiQpgIfc` | Creado ✅ (webhook: `test-subagentes-v2`) |

#### Decisiones técnicas

- **Sub-agentes**: GPT-4o-mini (`PFi2O7hEC5a75nv7`) — más barato que Haiku (~40-52% menos por token), sin bugs de credenciales en sub-nodos
- **Orquestador**: GPT-4.1 — razonamiento fuerte para routing y síntesis
- **Memoria**: solo en el orquestador (PostgreSQL Chat Memory), sub-agentes son stateless
- **Escalación + feedback**: herramientas directas del orquestador (no necesitan LLM propio)
- **Pricing Agent**: Cloudbeds API real (credencial `9ubhJlDJWQpKaJHE`, Header Auth x-api-key) — base para fase 3 (reservas + cobros)
- **Workarounds n8n v2.3.6 aplicados**: `inputSource: "workflowInputs"`, `$('Trigger').item.json.campo` en lugar de `$fromAI()` en workflowInputs, `toolDescription` en toolVectorStore v1.1, `toolCode` con `$helpers.httpRequest()` en lugar de `toolHttpRequest` (crashea en v2.3.6)
- **Ciclo Redis**: validator reporta "infinite loop" en Switch4 → Espera 4s → Redis6 → Switch4 — es intencional (acumulación de mensajes), idéntico a v1.5.5 producción

#### Cambios post-creación (misma fecha)

**Migración de modelos — Haiku → GPT-4o-mini en sub-agentes**
- Motivo: GPT-4o-mini es ~40-52% más barato que Claude Haiku 3 (input: $0.15 vs $0.25/1M, output: $0.60 vs $1.25/1M), y evita el bug `notice` de `lmChatAnthropic` v1.3 en n8n v2.3.6.
- Afectó: `Anthropic Haiku (Agent)` en FAQ, `Anthropic Haiku (CRM)` en CRM, `Anthropic Haiku (Pricing)` en Pricing — reemplazados por `OpenAI GPT-4o-mini` en los tres workflows.
- Credencial usada: `PFi2O7hEC5a75nv7` (misma que embeddings y Drive-to-Supabase).

**Bug retriever FAQ Agent — `lmChatAnthropic` v1.3 en toolVectorStore**
- Causa: `lmChatAnthropic` typeVersion 1.3 inyecta automáticamente `notice: ""` en sus parámetros. En contexto de sub-nodo (`getInputConnectionData`), este parámetro extra lanza `"Could not get parameter"` al intentar recuperar documentos del vector store.
- Fix: Reemplazado `Anthropic Haiku (Retriever)` por `OpenAI GPT-4o-mini (Retriever)` en el nodo `toolVectorStore` del FAQ Agent — idéntico al enfoque de producción v1.5.5.

**Prompt FAQ Agent — reescritura**
- Prompt anterior (demasiado restrictivo): respondía `NO_INFO` incluso cuando había datos en Supabase, porque la instrucción "responde exactamente: NO_INFO" suprimía cualquier elaboración.
- Prompt nuevo: extraído y adaptado del prompt de producción v1.5.5 (`b6432bcb`), manteniendo solo la sección de consultas a "Base de datos". Instrucción corregida: `NO_INFO` solo si realmente no se encuentra información. Límite: no mencionar precios ni CRM (los maneja el orquestador).
- También corregido: nombre de tabla Supabase `faq_antankalama` → `faq_atankalama` (typo, corregido manualmente en UI).

**Contexto enriquecido para sub-agentes (21 Mar 2026)**

Decisión arquitectónica: **NO compartir memoria PostgreSQL** entre orquestador y sub-agentes. Se evaluaron 4 opciones (stateless, memoria R/W compartida, memoria read-only, contexto enriquecido). La memoria compartida R/W corrompe el historial (sub-agentes escriben respuestas internas que el cliente nunca vio). Read-only no es nativo en n8n v2.3.6. Solución elegida: **contexto enriquecido vía workflowInputs** — el orquestador pasa contexto específico a cada sub-agente según lo necesite.

Cambios aplicados:
- **FAQ Agent**: nuevo input `contexto_conversacion` (resumen breve para desambiguar consultas vagas). Prompt actualizado para usarlo.
- **CRM Agent**: sin inputs nuevos — el agente consulta Airtable directamente para obtener contexto. Prompt reforzado: "usa datos de la consulta previa como contexto, no pidas lo que ya existe en el CRM".
- **Pricing Agent**: nuevos inputs `fechas_mencionadas`, `num_huespedes`, `tipo_habitacion`. Prompt actualizado para usar parámetros directos sin volver a preguntar.
- **Orquestador**: nueva sección `CUANDO LLAMES SUB-AGENTES` en el prompt (~80 tokens). `toolDescription` y `workflowInputs` con `$fromAI()` configurados en los 4 nodos toolWorkflow. Notas descriptivas agregadas a cada nodo toolWorkflow.

**Seguridad y RGPD (21 Mar 2026)**
- Nueva sección `SEGURIDAD Y PRIVACIDAD (RGPD)` en el prompt del orquestador: minimización de datos, prohibición de datos sensibles, derechos de acceso/supresión → Contactar_Humano, anti-prompt-injection.

#### Pendiente

- Fase 2D: Testing end-to-end + período piloto con filtro por número de teléfono
- Cutover: desactivar v1.5.5, activar v2.0

Ver detalles de arquitectura en [ARCHITECTURE_V2.md](ARCHITECTURE_V2.md).

---

## [1.5.5] - 2026-03-19

### Mejora: Notificación Slack de escalación con motivo específico

#### Problema resuelto

**Mensaje Slack de escalación siempre mostraba texto genérico**
- Causa 1: Las instrucciones al agente decían "llama Contactar Humano con un resumen" sin especificar formato ni longitud — el agente generaba textos vagos como "Cliente solicitó atención humana".
- Causa 2: El field `Resumen_conversacion` en el tool schema tenía `required: false`, lo que permitía al modelo omitirlo o rellenarlo con texto genérico.
- Causa 3: El fallback en el tool (`$json.query?.Resumen_conversacion ?? ... ?? 'Cliente solicitó atención humana'`) activaba el texto hardcoded cuando el modelo no proveía el campo.

**Fixes aplicados**:
1. **System prompt** (`b6432bcb`): Las 2 instrucciones "Contactar Humano" ahora especifican explícitamente el formato y proveen ejemplos concretos:
   - *"pasando Resumen_conversacion = máximo 20 palabras del motivo (ej: 'cliente solicita cotización para evento corporativo de 40 personas')"*
2. **Tool schema** (`2964a976`): `Resumen_conversacion` → `required: true` + descripción con ejemplos del formato esperado.
3. **Fallback mejorado**: Cuando el modelo no provee el campo, cae al último mensaje real del cliente (`$('Edit Fields').item.json['Mensaje']`) en vez del texto hardcoded genérico.
4. **Slack message** (`K3WrelHxg7k9EePiD5-2S`):
   - Agregado campo `📅 Fecha` con timestamp (mantenido de versión anterior)
   - Etiqueta `👤 Motivo:` en vez de `👤 Resumen:`
   - Link de Chatwoot como texto clicable `<url|texto>` con `unfurl_links: false` — elimina el preview de marketing de Chatwoot que aparecía bajo el mensaje
   - Removido campo teléfono (no requerido por recepción)

**Resultado**: Recepción recibe en Slack un mensaje como:
```
🚨 Solicitud de atención humana — WhatsApp
📅 Fecha: 19/03/2026 14:35 hrs
👤 Motivo: cliente solicita cotización para evento corporativo de 40 personas
💬 Abrir conversación en Chatwoot
```

---

## [1.5.4] - 2026-03-17

### Fix: Subworkflow escalación — valores nulos, claves con espacios, credenciales y mensaje Slack

#### Problemas resueltos

**Bug: Valores nulos en subworkflow trigger (`Resumen_conversacion` / `Id_Conversacion_Chatwoot`)**
- Causa: `$fromAI()` falla silenciosamente en n8n v2.3.6 cuando se usa dentro de `workflowInputs.value` en nodos `toolWorkflow`. Los valores llegaban como `null` al trigger del subworkflow.
- Fix: Expresiones en el trigger cambiadas a `$json.query?.Resumen_conversacion ?? $json.Resumen_conversacion ?? 'Sin resumen'` para leer directamente del `$json` que entrega n8n en su lugar.

**Bug: Error "Invalid parameter key" — claves con espacios en toolWorkflow**
- Causa: Los keys `"Resumen conversacion"` y `"Id Conversacion Chatwoot"` tienen espacios, lo cual es inválido en n8n v2.3.6 para inputs de toolWorkflow.
- Fix: Renombradas a `Resumen_conversacion` e `Id_Conversacion_Chatwoot` (underscore) en ambos workflows:
  - Nodo "Contactar Humano" (flujo principal) → `workflowInputs`
  - Subworkflow: `ExecuteWorkflowTrigger`, `Edit Fields`, nodo Slack, URL Chatwoot

**Bug: Credenciales perdidas en nodos tras updateNode via MCP**
- Causa: `n8n_update_partial_workflow → updateNode` con `updates.parameters` reemplaza el objeto `parameters` completo, eliminando las credenciales vinculadas.
- Afectó: "Enviar Respuesta" (Chatwoot HTTP), "Mensaje de error de Chatwoot" (Gmail), "Contactar Humano" (toolWorkflow).
- Fix: Restaurar credenciales en paso separado con `updates.credentials`, y siempre incluir todos los campos de `parameters` en un solo update.

**Bug: Mensaje Slack con texto duplicado / formato HTML**
- Causa: Nodo Slack en Block Kit con `text` fallback mostraba el texto plano duplicado. Previsualización devolvía HTML en vez del mensaje formateado.
- Fix: Cambiado a **Simple Text Message** con texto plano + iconos:
  ```
  🚨 *Solicitud de atención humana — WhatsApp*
  👤 *Resumen:* {{ $json['Resumen_conversacion'] }}
  💬 Abrir chat: https://app.chatwoot.com/app/accounts/150504/conversations/{{ $json['Id_Conversacion_Chatwoot'] }}
  ```

**Bug: URL Chatwoot con `=` literal en parámetro JSON**
- Causa: El campo URL tenía `"=https://..."` — el `=` dentro de un string JSON es literal, no prefijo de expresión n8n.
- Fix: Eliminado el `=` inicial de la URL.

#### Estado final del subworkflow (`K3WrelHxg7k9EePiD5-2S`)
- Flujo: Trigger → Edit Fields → Slack (texto plano) → Chatwoot (custom_attribute humano) ✅
- Probado en producción: Slack recibe mensaje con resumen e ID de conversación ✅

---

## [1.5.3] - 2026-03-16

### Fix: Bot inventaba reservas en vez de enviar link de Cloudbeds

#### Problema resuelto

**Bug: El bot decía "ya está preparada tu reserva" sin enviar el link**
- Causa: La instrucción `Llama "Crear / Actualizar contacto"` al final de la sección PRECIO/RESERVA era interpretada por el modelo como "completar el registro de la reserva". El bot nunca entregaba el link de Cloudbeds y fingía haber hecho una reserva que no existe.
- Fix 1: Se aclaró que `"Crear / Actualizar contacto"` solo registra interés en el CRM — no crea ninguna reserva.
- Fix 2: Se marcó la entrega del link Cloudbeds como OBLIGATORIA y nunca omitible.
- Fix 3: Prohibición explícita: `NUNCA digas que "preparaste", "creaste" o "registraste" una reserva`.
- Fix 4: Agregado ejemplo de referencia correcto para consultas de reserva (con link, sin afirmar reserva creada).

---

## [1.5.2] - 2026-03-16

### Fix: Email y teléfono proactivos — escalar a humano cuando falta información

#### Problemas resueltos

**Bug: El bot ofrecía `reservas@atankalama.com` sin que el cliente lo pidiera**
- Causa: No existía regla explícita que prohibiera compartir datos de contacto del hotel proactivamente. El modelo los entregaba como "fallback" cuando no encontraba información.
- Fix: Nueva regla en `LÍMITES` — el bot no puede inventar ni compartir teléfonos, emails ni datos de contacto; si el cliente los solicita y no están en "Base de datos", debe ofrecer contacto humano.

**Bug: El bot inventó un número de teléfono que no existe en la base de datos**
- Causa: El límite de "no inventes" solo cubría precios/disponibilidad/servicios, no datos de contacto.
- Fix: La nueva regla de LÍMITES cubre explícitamente teléfonos y emails.

**Bug: Ante preguntas sin respuesta, el bot daba el email en vez de escalar**
- Causa: La sección `PREGUNTA SIN RESPUESTA` indicaba ofrecer contacto humano, pero no prohibía explícitamente el email como alternativa — el modelo lo agregaba por cuenta propia.
- Fix: Agregada línea final en esa sección: `NUNCA sugieras email ni teléfono como alternativa — solo el contacto humano.`

#### Prompt actualizado (v1.5.2)
- `PREGUNTA SIN RESPUESTA`: nueva línea de cierre que prohíbe sugerir email/teléfono
- `LÍMITES`: nueva regla sobre datos de contacto del hotel

---

## [1.5.1] - 2026-03-13

### Fix: Doble presentación y saludo incorrecto mid-conversación

#### Problemas resueltos

**Bug: El bot se presentaba más de una vez en la misma conversación**
- Causa: La instrucción de CLIENTE NUEVO se aplicaba en cada mensaje, no solo en el primero. Si el usuario no había dado su nombre aún, el bot lo seguía clasificando como "nuevo" y repetía "Soy el Asistente Virtual de Atankalama, un gusto en conocerte."
- Fix: Se añadió condición explícita — la presentación solo ocurre si no hay historial previo en esa conversación (memoria PostgreSQL vacía para el `conversation_id`).

**Bug: "qué bueno que estés de vuelta" incorrecto mid-conversación**
- Causa: El bot llama "Consultar contactos" en cada mensaje. Cuando el usuario daba su nombre en el mensaje 3 y el bot lo guardaba en Airtable, el mensaje 4 ya encontraba el contacto en el CRM y activaba el saludo de "cliente conocido" — aunque la persona nunca había tenido una conversación anterior.
- Fix: El saludo de cliente conocido ahora también se restringe al primer mensaje (sin historial previo). Cambio de "qué bueno que estés de vuelta" → "¡qué gusto verte de nuevo!" (más neutral, no asume hospedaje previo).

#### Mecanismo de detección "primer mensaje"
El Postgres Chat Memory usa `conversation_id` de Chatwoot como clave de sesión. Cada conversación nueva en Chatwoot genera un `conversation_id` distinto → memoria vacía → el agente infiere que es el primer mensaje al no encontrar historial en su contexto.

#### Prompt actualizado (v1.5.1)
- CLIENTE NUEVO: presentación condicional según historial
- CLIENTE CONOCIDO: saludo condicional según historial + frase más neutral
- Añadido segundo ejemplo de referencia: cliente nuevo en segundo mensaje (sin presentación)

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

## 📚 Referencias

- **[README.md](README.md)**: Documentación principal
- **[ISSUES.md](ISSUES.md)**: Problemas conocidos
- **[PROMPT.md](PROMPT.md)**: Análisis y propuestas de prompt
- **[ARCHITECTURE.md](ARCHITECTURE.md)**: Arquitectura técnica

---

**Última actualización**: 26 de Marzo 2026 (fix resumen Slack + reporte preguntas parciales)
**Versión producción**: v1.6.1 | **En desarrollo**: v2.0.0-dev
**Mantenedores**: NicoCalama + Claude AI Assistant
