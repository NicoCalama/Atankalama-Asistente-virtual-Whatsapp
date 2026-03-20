# Changelog — Drive to Supabase Atankalama

## [v2.0] — 2026-03-19
### Cambiado
- **Chunking por secciones**: Reemplazados los nodos `textSplitterRecursiveCharacterTextSplitter` (500 chars) por nodos Code que dividen por secciones logicas (`SECCION N --`)
- Agregados 3 niveles de fallback: secciones → separador ___ → parrafos ~1000 chars
- Agregados "Splitter passthrough" (chunkSize 50000) porque el Default Data Loader requiere un text splitter conectado
- Nodos eliminados: `Dividir texto` (780234e1), `Dividir texto1` (8b35d6f8)
- Nodos agregados: `Dividir por secciones` (9b869cc9), `Dividir por secciones1` (4fac0171), `Splitter passthrough` y `Splitter passthrough1`

### Motivo
El splitter de 500 chars cortaba el texto sin respetar la estructura del documento, generando chunks desordenados e inutiles para el agente de IA. Los documentos tienen secciones claramente definidas que deben mantenerse como unidades logicas.

### Backup
- Archivo: `docs/backups/drive_to_supabase_backup_v19.json`
- Version ID: `e5c44238-0b93-4abb-8de3-37b39f2f8d7b` (v19)

---

## [v1.0] — 2026-02-07
### Inicial
- Workflow creado con Google Drive triggers (nuevo + actualizado)
- Chunking por caracteres (500 chars, 50 overlap, modo markdown)
- Vectorizacion con OpenAI embeddings + Supabase pgvector
- Tabla: `faq_atankalama`
