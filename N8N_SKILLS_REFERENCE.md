# 📚 Referencia de n8n Skills

Esta guía resume las 7 skills de n8n disponibles para ayudarte en el desarrollo de workflows.

## 🎯 Las 7 Skills Disponibles

### 1. **n8n Expression Syntax**
Sintaxis correcta de expresiones n8n.

**Cuándo usar**: Escribir expresiones, usar `{{}}`, acceder a variables `$json/$node`

**Funciones clave**:
- Variables core: `$json`, `$node`, `$now`, `$env`
- ⚠️ **IMPORTANTE**: Los datos de webhook están en `$json.body`
- Catálogo de errores comunes con soluciones

**Ejemplo**:
```javascript
// ✅ Correcto
{{ $json.body.name }}

// ❌ Incorrecto
{{ $json.name }}  // En webhooks esto falla
```

---

### 2. **n8n MCP Tools Expert** 🌟 (MÁXIMA PRIORIDAD)
Guía experta para usar herramientas n8n-mcp.

**Cuándo usar**: Buscar nodos, validar configuraciones, gestionar workflows

**Herramientas principales**:
- `search_nodes` - Buscar nodos por palabra clave
- `get_node` - Información detallada de nodos
- `validate_node` - Validación de configuración
- `n8n_update_partial_workflow` - Actualización incremental
- `n8n_validate_workflow` - Validación completa
- `n8n_deploy_template` - Desplegar plantillas

**Patrón más común**:
```
search_nodes → get_node (18s promedio entre pasos)
```

---

### 3. **n8n Workflow Patterns**
Patrones arquitectónicos probados para workflows.

**Cuándo usar**: Crear workflows, conectar nodos, diseñar automatización

**Los 5 patrones**:
1. **Webhook Processing** - Recibir y procesar datos externos
2. **HTTP API** - Consumir APIs REST
3. **Database** - Operaciones CRUD con bases de datos
4. **AI Agent** - Workflows con IA (LangChain, OpenAI, etc.)
5. **Scheduled** - Tareas programadas (cron, intervalos)

**Para tu proyecto de hoteles Calama**:
- Patrón principal: **Scheduled** (Schedule Trigger)
- Patrón secundario: **HTTP API** (Apify)
- Almacenamiento: **Database** (Google Sheets)

---

### 4. **n8n Validation Expert**
Interpretar y solucionar errores de validación.

**Cuándo usar**: Validación falla, debugging, manejar falsos positivos

**Funciones**:
- Catálogo de errores reales
- Sistema de auto-sanitización
- Perfiles de validación (minimal/runtime/strict)
- Workflow de solución de errores

**Patrón de validación más común**:
```
n8n_update_partial_workflow → n8n_validate_workflow
(Promedio: 23s pensando, 58s corrigiendo)
```

---

### 5. **n8n Node Configuration**
Configuración de nodos según operación.

**Cuándo usar**: Configurar nodos, entender dependencias de propiedades

**Funciones**:
- Reglas de dependencias (ej: `sendBody` → `contentType`)
- Requisitos específicos por operación
- 8 tipos de conexiones AI para workflows con IA
- Patrones comunes de configuración

---

### 6. **n8n Code JavaScript**
Escribir código JavaScript efectivo en nodos Code.

**Cuándo usar**: Escribir JS en Code nodes, hacer requests HTTP, trabajar con fechas

**Funciones clave**:
- Patrones de acceso a datos: `$input.all()`, `$input.first()`, `$input.item`
- ⚠️ **IMPORTANTE**: Datos de webhook en `$json.body`
- Formato de retorno correcto: `[{json: {...}}]`
- Funciones built-in: `$helpers.httpRequest()`, `DateTime`, `$jmespath()`
- Top 5 patrones de error (cubren 62%+ de fallos)

**Ejemplo para tu proyecto**:
```javascript
// Procesar datos de Apify
for (const item of $input.all()) {
  const hotel = item.json;

  // Extraer información relevante
  const hotelData = {
    nombre: hotel.name,
    precio: hotel.price,
    rating: hotel.rating,
    direccion: hotel.address,
    ciudad: 'Calama'
  };

  item.json = hotelData;
}

return $input.all();
```

---

### 7. **n8n Code Python**
Python en nodos Code (con limitaciones).

**Cuándo usar**: Casos específicos que requieren Python

**⚠️ IMPORTANTE**:
- **Usar JavaScript en 95% de casos**
- ❌ NO hay librerías externas (requests, pandas, numpy)
- ✅ Solo standard library (json, datetime, re, etc.)

**Variables Python**:
- `_input` - Datos de entrada
- `_json` - JSON del item actual
- `_node` - Datos de otros nodos

---

## 🚀 Cómo Usar las Skills

Las skills se activan **automáticamente** cuando haces preguntas relevantes:

```
"¿Cómo escribo expresiones en n8n?"
→ Activa: n8n Expression Syntax

"Búscame un nodo de Apify"
→ Activa: n8n MCP Tools Expert

"Construye un workflow programado"
→ Activa: n8n Workflow Patterns

"¿Por qué falla la validación?"
→ Activa: n8n Validation Expert

"¿Cómo configuro el nodo HTTP Request?"
→ Activa: n8n Node Configuration

"¿Cómo accedo a datos de webhook en Code node?"
→ Activa: n8n Code JavaScript

"¿Puedo usar pandas en Python?"
→ Activa: n8n Code Python
```

---

## 🔗 Skills Trabajan Juntas

**Ejemplo**: "Construir workflow de scraping de hoteles con Apify"

1. **n8n Workflow Patterns** → Identifica patrón "Scheduled + HTTP API"
2. **n8n MCP Tools Expert** → Busca nodos: Schedule Trigger, Apify, Google Sheets
3. **n8n Node Configuration** → Configura cada nodo correctamente
4. **n8n Code JavaScript** → Procesa y transforma datos de Apify
5. **n8n Expression Syntax** → Mapea campos entre nodos
6. **n8n Validation Expert** → Valida el workflow completo

---

## 📊 Estadísticas de Uso

- **525+** nodos soportados
- **2,653+** plantillas de workflow disponibles
- **10** patrones de Code node probados en producción
- **99.0%** tasa de éxito con `n8n_update_partial_workflow`
- **56 segundos** promedio entre edits

---

## 🎯 Para Tu Proyecto de Hoteles Calama

**Skills más relevantes**:
1. ✅ **n8n MCP Tools Expert** - Para buscar y configurar nodos Apify
2. ✅ **n8n Code JavaScript** - Para procesar datos de scraping
3. ✅ **n8n Workflow Patterns** - Patrón Scheduled + Database
4. ✅ **n8n Validation Expert** - Validar el workflow final

**Nodos principales que usarás**:
- Schedule Trigger (programar scraping)
- Apify (scraping de hoteles)
- Code (JavaScript - procesar datos)
- Google Sheets (almacenar resultados)
- HTTP Request (APIs adicionales si necesario)

---

## 📖 Recursos

- **Repositorio**: https://github.com/czlonkowski/n8n-skills
- **MCP Server**: https://github.com/czlonkowski/n8n-mcp
- **Documentación local**: `./n8n-skills/docs/`

---

**Creado por**: Romuald Członkowski - [aiadvisors.pl](https://www.aiadvisors.pl/en)
