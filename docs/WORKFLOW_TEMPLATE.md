# 📋 [Nombre del Workflow] - Template de Documentación

**Workflow ID**: `XXXXXXXXXXXXXXXXX`
**Estado**: 🟢 Activo / 🟡 En desarrollo / 🔴 Inactivo / 🧪 Testing
**Creado**: [Fecha]
**Última actualización**: [Fecha]

---

## 📋 Descripción General

[Descripción breve de 2-3 líneas sobre qué hace el workflow]

---

## 🎯 Objetivo

[Explicación del problema que resuelve y objetivos específicos]

**Problema que resuelve**:
- [Punto 1]
- [Punto 2]
- [Punto 3]

**Objetivos**:
- [Objetivo 1]
- [Objetivo 2]
- [Objetivo 3]

---

## 🏗️ Stack Técnico

### Infraestructura
- **n8n**: [versión / configuración]
- [Otros servicios de infraestructura]

### Integraciones Principales
- **[Servicio 1]**: [Función]
- **[Servicio 2]**: [Función]
- **[Servicio 3]**: [Función]

### APIs y Servicios Externos
- **[API 1]**: [Uso]
- **[API 2]**: [Uso]

### Almacenamiento
- **[Base de datos / Storage]**: [Qué guarda]

---

## 🔄 Flujo del Workflow

### Vista General

```
[Diagrama de flujo en ASCII o descripción paso a paso]

Ejemplo:
Trigger → Procesamiento → Validación → Acción → Notificación
```

### Fases Detalladas

#### Fase 1: [Nombre]
[Descripción de qué ocurre en esta fase]

**Nodos involucrados**:
- [Nodo 1]: [Función]
- [Nodo 2]: [Función]

**Input**:
```json
{
  "ejemplo": "de datos de entrada"
}
```

**Output**:
```json
{
  "ejemplo": "de datos de salida"
}
```

---

#### Fase 2: [Nombre]
[Descripción]

---

## ✨ Características Actuales

### Capacidades
- ✅ [Característica 1]
- ✅ [Característica 2]
- ✅ [Característica 3]

### Limitaciones Conocidas
- ⚠️ [Limitación 1]
- ⚠️ [Limitación 2]

---

## 🧩 Arquitectura de Nodos

**Total de nodos**: [Número]

### Nodos Principales

#### 1. [Nombre del Nodo]
**Tipo**: `[Tipo de nodo en n8n]`
**Función**: [Descripción breve]

**Configuración**:
```yaml
[Configuración relevante del nodo]
```

**Input/Output**: [Descripción de datos]

---

#### 2. [Nombre del Nodo]
[Igual que arriba]

---

## 🗄️ Bases de Datos y Almacenamiento

### [Nombre de Base de Datos]

**Tablas**:

#### `nombre_tabla`
```sql
CREATE TABLE nombre_tabla (
  id SERIAL PRIMARY KEY,
  campo1 VARCHAR(255),
  campo2 TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);
```

**Índices**:
```sql
CREATE INDEX idx_campo ON nombre_tabla(campo);
```

---

## 🔌 Integraciones Externas

### 1. [Nombre del Servicio]
- **Endpoint**: `[URL]`
- **Auth**: [Tipo de autenticación]
- **Uso**: [Para qué se usa]
- **Rate Limits**: [Si aplica]

---

## 🔐 Variables de Entorno

```bash
# [Categoría]
NOMBRE_VARIABLE=valor_ejemplo
OTRA_VARIABLE=valor_ejemplo

# [Otra Categoría]
VARIABLE3=valor_ejemplo
```

---

## 🎯 Casos de Uso

### Caso 1: [Nombre del Caso]
**Trigger**: [Qué inicia el flujo]
**Acción**: [Qué hace el workflow]
**Resultado**: [Qué se logra]

**Ejemplo**:
```
[Descripción paso a paso de un caso real de uso]
```

---

## 📊 Métricas y Performance

### Targets
- [Métrica 1]: [Valor objetivo]
- [Métrica 2]: [Valor objetivo]
- [Métrica 3]: [Valor objetivo]

### Actuales
- [Métrica 1]: [Valor real] (actualizado: [fecha])
- [Métrica 2]: [Valor real]

---

## 🐛 Problemas Conocidos

Ver archivo completo de issues: `ISSUES.md` (si existe)

### Activos
1. **[Nombre del issue]** - Prioridad: [Alta/Media/Baja]
   - Descripción breve
   - Estado: [En análisis / En progreso / Bloqueado]

---

## 🔧 Mantenimiento

### Tareas Regulares
- [ ] [Tarea 1]: Cada [frecuencia]
- [ ] [Tarea 2]: Cada [frecuencia]

### Última Revisión Técnica
**Fecha**: [Fecha]
**Responsable**: [Nombre]
**Resultado**: [Resumen]

---

## 📝 Historial de Versiones

Ver archivo completo: `CHANGELOG.md` (si existe)

### [Versión Actual] - [Fecha]
**Cambios principales**:
- [Cambio 1]
- [Cambio 2]

---

## 🚀 Próximas Mejoras

### En Desarrollo
- 🔄 [Mejora 1]
- 🔄 [Mejora 2]

### Planificado
- ⏳ [Mejora 3]
- ⏳ [Mejora 4]

### Ideas (Backlog)
- 💡 [Idea 1]
- 💡 [Idea 2]

---

## 🔐 Seguridad

### Medidas Implementadas
- ✅ [Medida 1]
- ✅ [Medida 2]

### Consideraciones
- ⚠️ [Consideración 1]
- ⚠️ [Consideración 2]

### Datos Sensibles
**NO almacenados en Git**:
- [Tipo de dato 1]
- [Tipo de dato 2]

---

## 🧪 Testing

### Casos de Prueba
1. **[Nombre del test]**
   - Input: [Datos de entrada]
   - Expected: [Resultado esperado]
   - Status: ✅ Pasa / ❌ Falla / ⏳ Pendiente

### Testing Manual
**Checklist**:
- [ ] [Paso 1]
- [ ] [Paso 2]
- [ ] [Paso 3]

---

## 📚 Documentación Relacionada

### Archivos Locales
- **README.md**: Documentación general del proyecto
- **SECURITY.md**: Guía de seguridad
- **[Otro archivo]**: [Descripción]

### Enlaces Externos
- [Documentación de API]: [URL]
- [Recurso útil]: [URL]

---

## 🆘 Troubleshooting

### Problema: [Descripción del problema común]
**Causa**: [Por qué ocurre]
**Solución**: [Cómo resolverlo]

### Problema: [Otro problema]
**Causa**: [...]
**Solución**: [...]

---

## 💡 Notas para Desarrolladores

[Información adicional relevante para quien mantenga o modifique este workflow]

**Consideraciones importantes**:
- [Punto 1]
- [Punto 2]

**Mejores prácticas**:
- [Práctica 1]
- [Práctica 2]

---

## 📞 Contacto y Responsables

**Creador**: [Nombre]
**Mantenedor actual**: [Nombre]
**Equipo**: [Nombre del equipo]
**Última revisión**: [Fecha]

---

## 📄 Licencia y Uso

[Si aplica, información sobre licencia o restricciones de uso]

---

## 🔖 Tags y Categorías

**Categorías**: [Categoría1], [Categoría2]
**Tags**: `tag1`, `tag2`, `tag3`
**Prioridad del proyecto**: [Alta/Media/Baja]

---

**Última actualización**: [Fecha]
**Versión de documentación**: v1.0
**Documentado por**: [Nombre]

---

## 📌 Guía de Uso de este Template

### Instrucciones para Usar este Template

1. **Copiar este archivo** para crear documentación de nuevo workflow
2. **Reemplazar todos los placeholders** entre `[corchetes]`
3. **Eliminar secciones no aplicables** (no todas las secciones son necesarias para todos los workflows)
4. **Agregar secciones adicionales** según necesidad del workflow específico
5. **Mantener formato consistente** con otros documentos del proyecto

### Secciones Obligatorias
- Descripción General
- Objetivo
- Stack Técnico
- Flujo del Workflow
- Características Actuales

### Secciones Opcionales (según relevancia)
- Arquitectura de Nodos (para workflows complejos)
- Bases de Datos (si aplica)
- Testing (altamente recomendado)
- Troubleshooting (si hay problemas comunes)

### Mantener Actualizado
- Revisar este documento cada vez que se actualice el workflow
- Actualizar campo "Última actualización" en cada cambio
- Documentar en CHANGELOG.md los cambios significativos
- Agregar issues nuevos en ISSUES.md

---

## ✅ Checklist de Documentación Completa

- [ ] Título y metadata llenos
- [ ] Descripción clara del objetivo
- [ ] Stack técnico documentado
- [ ] Flujo descrito paso a paso
- [ ] Variables de entorno listadas
- [ ] Integraciones documentadas
- [ ] Casos de uso con ejemplos
- [ ] Problemas conocidos registrados
- [ ] Próximas mejoras planificadas
- [ ] Información de contacto actualizada
- [ ] Fecha de última actualización correcta

---

**Este template está diseñado para ser flexible y adaptable a diferentes tipos de workflows de n8n.**
