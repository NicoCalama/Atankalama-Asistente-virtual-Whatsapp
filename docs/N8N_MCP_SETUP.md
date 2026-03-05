# 🔧 Configuración de n8n-MCP para Claude Desktop

**Fecha de instalación**: 5 de Marzo 2026
**Estado**: ✅ n8n-mcp instalado correctamente

---

## ✅ Instalación Completada

### Software Instalado
- ✅ **Node.js**: v24.14.0
- ✅ **npm**: v11.9.0
- ✅ **n8n-mcp**: Instalado globalmente (115 paquetes)

### Verificación
```bash
# Verificar que n8n-mcp funciona
npx n8n-mcp --version
```

**Resultado esperado**: El servidor se inicia y muestra:
- 20 tools disponibles
- 1236 nodes cargados
- Base de datos inicializada

---

## 🔐 Credenciales

Las credenciales están guardadas de forma segura en `.env` (NO en Git):

```bash
N8N_API_URL=https://n8n-n8n.pvwkqy.easypanel.host
N8N_API_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI3OTQ1YjEyNy0xZjUzLTRjZDktOGEyMS1jOGNlMjFiOTEyZTEiLCJpc3MiOiJuOG4iLCJhdWQiOiJwdWJsaWMtYXBpIiwiaWF0IjoxNzcyNTgyNzYzfQ.F9kKJbG2ho3jB2Jlec9NnctwsNK7UqKjNahq9DItK7A
```

---

## 📝 Configuración de Claude Desktop

### Ubicación del Archivo de Configuración

**Windows**:
```
C:\Users\[TU_USUARIO]\AppData\Roaming\Claude\claude_desktop_config.json
```

En tu caso:
```
C:\Users\nicoc\AppData\Roaming\Claude\claude_desktop_config.json
```

---

### Opción 1: Configuración Básica (Solo Documentación)

**Sin acceso a tu instancia n8n** - Solo usa la documentación de nodos:

```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "npx",
      "args": ["n8n-mcp"],
      "env": {
        "MCP_MODE": "stdio",
        "LOG_LEVEL": "error",
        "DISABLE_CONSOLE_OUTPUT": "true"
      }
    }
  }
}
```

**Herramientas disponibles**:
- Búsqueda de nodos
- Documentación de nodos
- Propiedades y operaciones
- Templates de workflows

---

### Opción 2: Configuración Completa (Recomendada) ⭐

**Con acceso completo a tu instancia n8n**:

```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "npx",
      "args": ["n8n-mcp"],
      "env": {
        "MCP_MODE": "stdio",
        "LOG_LEVEL": "error",
        "DISABLE_CONSOLE_OUTPUT": "true",
        "N8N_API_URL": "https://n8n-n8n.pvwkqy.easypanel.host",
        "N8N_API_KEY": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI3OTQ1YjEyNy0xZjUzLTRjZDktOGEyMS1jOGNlMjFiOTEyZTEiLCJpc3MiOiJuOG4iLCJhdWQiOiJwdWJsaWMtYXBpIiwiaWF0IjoxNzcyNTgyNzYzfQ.F9kKJbG2ho3jB2Jlec9NnctwsNK7UqKjNahq9DItK7A"
      }
    }
  }
}
```

**Herramientas adicionales**:
- ✅ Listar workflows de tu instancia
- ✅ Obtener detalles de workflows
- ✅ Actualizar workflows parcialmente
- ✅ Validar workflows
- ✅ Deploy de templates

---

## 🚀 Pasos para Configurar

### 1. Cerrar Claude Desktop
Asegúrate de cerrar completamente Claude Desktop antes de modificar la configuración.

### 2. Crear/Editar el Archivo de Configuración

**Opción A - Manual**:
1. Abre File Explorer
2. Pega en la barra de direcciones:
   ```
   %APPDATA%\Claude
   ```
3. Busca o crea el archivo `claude_desktop_config.json`
4. Pega la configuración (Opción 1 o 2 de arriba)
5. Guarda el archivo

**Opción B - PowerShell**:
```powershell
# Crear la carpeta si no existe
New-Item -ItemType Directory -Force -Path "$env:APPDATA\Claude"

# Crear el archivo de configuración (Opción 2 - Completa)
@"
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "npx",
      "args": ["n8n-mcp"],
      "env": {
        "MCP_MODE": "stdio",
        "LOG_LEVEL": "error",
        "DISABLE_CONSOLE_OUTPUT": "true",
        "N8N_API_URL": "https://n8n-n8n.pvwkqy.easypanel.host",
        "N8N_API_KEY": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI3OTQ1YjEyNy0xZjUzLTRjZDktOGEyMS1jOGNlMjFiOTEyZTEiLCJpc3MiOiJuOG4iLCJhdWQiOiJwdWJsaWMtYXBpIiwiaWF0IjoxNzcyNTgyNzYzfQ.F9kKJbG2ho3jB2Jlec9NnctwsNK7UqKjNahq9DItK7A"
      }
    }
  }
}
"@ | Out-File -FilePath "$env:APPDATA\Claude\claude_desktop_config.json" -Encoding UTF8
```

### 3. Reiniciar Claude Desktop
1. Abre Claude Desktop
2. Debería aparecer un ícono de 🔌 o "hammer" en la interfaz
3. Click en el ícono para ver las herramientas MCP disponibles

---

## 🧪 Verificar que Funciona

Una vez configurado y reiniciado Claude Desktop, pregunta:

```
¿Qué herramientas MCP tienes disponibles?
```

**Deberías ver**:
- `search_nodes` - Buscar nodos
- `get_node` - Información de nodos
- `list_workflows` - Listar workflows (si usaste Opción 2)
- `get_workflow` - Detalles de workflows (si usaste Opción 2)
- Y más...

---

## 🔧 Troubleshooting

### Problema: Claude Desktop no muestra herramientas MCP

**Soluciones**:
1. Verifica que el archivo JSON esté bien formado (usa jsonlint.com)
2. Asegúrate de cerrar COMPLETAMENTE Claude Desktop antes de editar
3. Verifica que Node.js esté en el PATH del sistema
4. Revisa los logs de Claude Desktop

### Problema: Error "Command not found: npx"

**Solución**:
Usa la ruta completa en lugar de `npx`:

```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "C:\\Program Files\\nodejs\\npx.cmd",
      "args": ["n8n-mcp"],
      ...
    }
  }
}
```

### Problema: Errores de conexión a n8n

**Verificar**:
1. ¿La URL de n8n es correcta?
2. ¿El API key es válido?
3. ¿Puedes acceder a la URL desde el navegador?

---

## 📊 Herramientas MCP Disponibles

Una vez configurado, tendrás acceso a **20 herramientas**:

### Documentación (Siempre Disponibles)
- `search_nodes` - Buscar nodos por palabra clave
- `get_node` - Información detallada de un nodo
- `list_node_properties` - Propiedades de un nodo
- `list_node_operations` - Operaciones disponibles
- `search_templates` - Buscar templates de workflows
- `get_template` - Detalles de un template

### Gestión de Workflows (Solo con API configurada)
- `list_workflows` - Listar tus workflows
- `get_workflow` - Detalles de un workflow específico
- `n8n_update_partial_workflow` - Actualizar parte de un workflow
- `n8n_validate_workflow` - Validar un workflow
- `n8n_deploy_template` - Desplegar un template

### Análisis
- `analyze_workflow` - Analizar estructura de workflow
- `get_workflow_statistics` - Estadísticas de workflows

---

## 🎯 Próximos Pasos

### 1. Configurar Claude Desktop (Hoy)
- [ ] Cerrar Claude Desktop
- [ ] Crear/editar `claude_desktop_config.json`
- [ ] Reiniciar Claude Desktop
- [ ] Verificar que las herramientas MCP aparecen

### 2. Probar las Herramientas (Después de configurar)
- [ ] Buscar nodos: `search_nodes("chatwoot")`
- [ ] Listar workflows: `list_workflows()`
- [ ] Ver workflow de WhatsApp: `get_workflow("s9A9Al67_R0wSQWf_HY3X")`

### 3. Implementar Mejoras de Prompt (Próxima Sesión)
- [ ] Usar `n8n_update_partial_workflow` para modificar el prompt
- [ ] Validar cambios con `n8n_validate_workflow`
- [ ] Hacer testing

---

## 📚 Documentación

- **n8n-mcp GitHub**: https://github.com/czlonkowski/n8n-mcp
- **n8n-mcp Docs**: https://dashboard.n8n-mcp.com
- **MCP Protocol**: https://modelcontextprotocol.io

---

## ⚠️ Seguridad

### ✅ Buenas Prácticas
- ✅ API Key guardada en configuración local (no en Git)
- ✅ Configuración de Claude Desktop es local (no se sincroniza)
- ✅ Nunca edites workflows de producción directamente
- ✅ Siempre haz backup antes de modificar workflows

### ⚠️ Advertencia
El archivo `claude_desktop_config.json` contiene tu API key en texto plano.
**NO compartas este archivo** con nadie ni lo subas a Git.

---

**Última actualización**: 5 de Marzo 2026
**Configurado por**: Claude AI + NicoCalama
**Estado**: ✅ n8n-mcp instalado, pendiente configuración en Claude Desktop
