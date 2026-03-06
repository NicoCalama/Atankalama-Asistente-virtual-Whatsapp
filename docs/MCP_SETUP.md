# MCP Setup - N8N Ayudante

**Configurado**: 6 de Marzo, 2026
**Estado**: Operativo

---

## Que es n8n-mcp

El MCP (Model Context Protocol) es el canal por el que Claude Code se conecta directamente a tu instancia de n8n. Sin MCP, Claude solo puede sugerir cambios. Con MCP, puede leer, validar y modificar workflows en tiempo real.

**Repositorio**: [czlonkowski/n8n-mcp](https://github.com/czlonkowski/n8n-mcp)

---

## Capacidades habilitadas

Con n8n-mcp activo, Claude Code puede:

| Herramienta | Funcion |
|---|---|
| `search_nodes` | Buscar nodos por keyword |
| `get_node` | Obtener info detallada de cualquier nodo |
| `validate_node` / `validate_workflow` | Validar configuraciones |
| `n8n_create_workflow` | Crear workflows nuevos |
| `n8n_update_partial_workflow` | Editar workflows incrementalmente |
| `n8n_autofix_workflow` | Auto-reparar errores comunes |
| `n8n_deploy_template` | Deployar templates |
| `n8n_workflow_versions` | Ver historial y hacer rollback |
| `search_templates` | Buscar templates de la comunidad |

Total: 20 herramientas disponibles, 1236 nodos en base de datos.

---

## Instalacion actual

### Paquete npm (global)
```
n8n-mcp instalado en: C:\Users\nicoc\AppData\Roaming\npm\node_modules\n8n-mcp
```

Instalado via:
```bash
npm install -g n8n-mcp
```

### Configuracion para Claude Code
Archivo: `.mcp.json` (en raiz del proyecto, excluido de git)

```json
{
  "mcpServers": {
    "n8n": {
      "command": "npx",
      "args": ["n8n-mcp"],
      "env": {
        "MCP_MODE": "stdio",
        "LOG_LEVEL": "error",
        "DISABLE_CONSOLE_OUTPUT": "true",
        "N8N_API_URL": "[URL protegida - ver .env]",
        "N8N_API_KEY": "[API Key - ver .env]"
      }
    }
  }
}
```

### Configuracion para Claude Desktop
Archivo: `%APPDATA%\Claude\claude_desktop_config.json` (fuera del repo)

Misma estructura que .mcp.json. Se configuro en sesiones anteriores.

---

## Instancia n8n

- **Host**: EasyPanel VPN server
- **URL**: [URL protegida - ver .env]
- **Autenticacion**: API Key (JWT)
- **Acceso**: HTTPS publico (necesario para webhooks de Chatwoot/WhatsApp)

Variables en `.env` (nunca en git):
```bash
N8N_API_URL=...
N8N_API_KEY=...
```

---

## Skills de n8n (complemento al MCP)

Las skills estan clonadas localmente en `n8n-skills/` (excluido de git).
Son guias de comportamiento que Claude Code activa automaticamente segun el contexto:

| Skill | Cuando se activa |
|---|---|
| n8n-expression-syntax | Al escribir expresiones `{{ }}` |
| n8n-mcp-tools-expert | Al buscar o usar nodos |
| n8n-workflow-patterns | Al disenar flujos |
| n8n-validation-expert | Al validar o corregir errores |
| n8n-node-configuration | Al configurar nodos especificos |
| n8n-code-javascript | Al escribir codigo JS en nodos Code |
| n8n-code-python | Al escribir codigo Python en nodos Code |

**Repositorio**: [czlonkowski/n8n-skills](https://github.com/czlonkowski/n8n-skills)

---

## Seguridad

| Archivo | En git | Razon |
|---|---|---|
| `.mcp.json` | No (gitignored) | Contiene API key |
| `.env` | No (gitignored) | Contiene credenciales |
| `n8n-skills/` | No (gitignored) | Repo externo clonado |
| `%APPDATA%/.../claude_desktop_config.json` | No (fuera del repo) | Credenciales del sistema |

---

## Como actualizar el API Key

Si se rota el API Key desde la UI de n8n:

1. Actualizar `.env` con el nuevo valor
2. Actualizar `.mcp.json` con el nuevo valor
3. Actualizar `claude_desktop_config.json` con el nuevo valor
4. Reiniciar Claude Desktop y Claude Code

> Nunca crear un commit para "corregir" un key expuesto. Ver [docs/SECURITY.md](SECURITY.md).

---

## Troubleshooting

**"n8n API: not configured"**
- Verificar que `.mcp.json` existe y tiene los valores correctos
- Verificar que la URL de n8n es accesible (`curl https://[url]/healthz`)

**"1236 nodes loaded" pero falla la conexion a n8n**
- La base de datos local de nodos funciona aunque no haya conexion a n8n
- La conexion a n8n solo es necesaria para crear/editar/deployar workflows

**Actualizar la base de datos de nodos**
```bash
npm update -g n8n-mcp
```

---

**Ultima actualizacion**: 6 de Marzo, 2026
**Mantenedor**: NicoCalama + Claude AI Assistant
