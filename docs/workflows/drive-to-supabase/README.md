# Drive to Supabase Atankalama

**Version actual**: v2.0 (chunking por secciones)
**Estado**: Activo en produccion
**Ultima modificacion**: 19 de Marzo 2026
**Workflow ID en n8n**: `kU1GhReCw5RuzDlqiEpXU`

---

## Que es esto?

Un workflow de n8n que automatiza la vectorizacion de documentos de Google Drive en Supabase (pgvector). Cuando se sube o modifica un archivo en una carpeta especifica de Drive, el workflow lo descarga, extrae el texto, lo divide en chunks inteligentes por secciones y lo almacena como embeddings en la tabla `faq_atankalama`.

Esto alimenta la base de conocimiento del asistente virtual de WhatsApp (RAG).

---

## Que puede hacer?

- Detectar archivos nuevos en la carpeta Drive "SupaBase"
- Detectar archivos modificados y re-vectorizar (elimina version anterior primero)
- Dividir documentos por secciones logicas (`SECCION N --`) en lugar de cortes arbitrarios
- Fallback inteligente para documentos sin formato de secciones
- Evitar duplicados en la base de datos

---

## Stack tecnico

| Componente | Tecnologia |
|-----------|------------|
| Trigger | Google Drive Trigger (poll cada minuto) |
| Almacenamiento | Google Drive (carpeta "SupaBase") |
| Extraccion | extractFromFile (text/plain) |
| Chunking | Code node (split por secciones + fallbacks) |
| Embeddings | OpenAI (ada-002) |
| Vector Store | Supabase pgvector (tabla `faq_atankalama`) |
| Funcion busqueda | `match_documents` (RPC) |

---

## Arquitectura — Dos flujos paralelos

### Flujo 1: Archivo NUEVO
```
Nuevo archivo → Guardar ID → Descarga archivo → Extraer texto
  → Dividir por secciones → Supabase Vector Store
                              ├── Default Data Loader (ai_document)
                              │     └── Splitter passthrough (ai_textSplitter, 50000 chars)
                              └── Embeddings OpenAI (ai_embedding)
```

### Flujo 2: Archivo MODIFICADO
```
Archivo actualizado → Elimina contenido (Supabase DELETE) → Guardar ID1
  → Evita duplicados → Descarga archivo1 → Extraer texto1
  → Dividir por secciones1 → Supabase Vector Store1
                               ├── Default Data Loader1 (ai_document)
                               │     └── Splitter passthrough1 (ai_textSplitter, 50000 chars)
                               └── Embeddings OpenAI1 (ai_embedding)
```

---

## Nodos clave

| Nodo | ID | Tipo | Notas |
|------|-----|------|-------|
| Nuevo archivo | `1327973f` | googleDriveTrigger | event: fileCreated |
| Archivo actualizado | `811a76c0` | googleDriveTrigger | event: fileUpdated |
| Elimina contenido | `ab565e1b` | supabase (DELETE) | Borra por metadata file_id |
| Dividir por secciones | `9b869cc9` | code (v2) | runOnceForAllItems |
| Dividir por secciones1 | `4fac0171` | code (v2) | runOnceForAllItems |
| Supabase Vector Store | `80bd73d6` | vectorStoreSupabase | tabla: faq_atankalama |
| Supabase Vector Store1 | `5edbef87` | vectorStoreSupabase | tabla: faq_atankalama |

---

## Logica del chunking (Code node)

El campo de entrada es `$input.first().json.data` (output del nodo extractFromFile).

**3 niveles de split:**

1. **Por secciones**: Regex `SECCION N --` (soporta \r\n y variantes de guion)
2. **Fallback por separador**: Split por `________________` (3+ underscores)
3. **Fallback por parrafos**: Agrupa parrafos hasta ~1000 chars

Filtro: chunks menores a 50 caracteres se descartan.
Output: `json.text` (campo que espera el Default Data Loader).

---

## Credenciales

| Servicio | Credential ID |
|----------|--------------|
| Google Drive | `Y8YpGW6aSOdaKWmD` |
| OpenAI | `PFi2O7hEC5a75nv7` |
| Supabase | `bFOo7cpHxcNnPXVD` |

---

## Configuracion

- **Carpeta Drive**: `1SYGbCj0PIf-yVb2DzyX5aPWILV7wAbQy` (nombre: "SupaBase")
- **Tabla Supabase**: `faq_atankalama`
- **Query name**: `match_documents`
- **Timezone**: America/Santiago
- **Error workflow**: `Kyas0a078PTQLvseQLkcz` (Alerta de errores - Global)
- **Poll interval**: cada minuto

---

## Notas tecnicas importantes

1. **extractFromFile** pone el texto en `json.data`, NO en `json.text`
2. **Default Data Loader requiere un Text Splitter** conectado via `ai_textSplitter` — por eso existen los "Splitter passthrough" con chunkSize 50000
3. Los line endings de Google Docs son `\r\n` (Windows) — el regex contempla esto
4. El flujo de archivo modificado borra PRIMERO los registros anteriores en Supabase (por metadata `file_id`) antes de re-vectorizar
