# 🔒 Guía de Seguridad - Proyecto N8N Ayudante

## 🎯 Principio Fundamental

**NUNCA subir información sensible a repositorios públicos**

---

## ✅ QUÉ SÍ SE DOCUMENTA PÚBLICAMENTE

- ✅ Arquitectura de workflows y lógica de negocio
- ✅ Nombres de nodos, tipos y stack tecnológico
- ✅ Código JavaScript/Python (sin credenciales)
- ✅ Prompts y configuraciones del AI Agent
- ✅ Datos de ejemplo ficticios

---

## ❌ QUÉ NUNCA SE SUBE A GITHUB

### Credenciales
- ❌ API Keys completas
- ❌ Tokens de autenticación
- ❌ Passwords de bases de datos

### URLs y Endpoints
- ❌ URL real de instancia n8n
- ❌ URLs de webhooks con tokens

### Datos Reales
- ❌ Nombres, emails, teléfonos reales de clientes
- ❌ RUTs/DNIs
- ❌ Información personal identificable (PII)

---

## 🔄 USO DE PLACEHOLDERS

**URLs**:
```markdown
✅ BIEN: URL: [URL protegida - ver .env]
❌ MAL: URL: https://n8n-real-url.com
```

**API Keys**:
```markdown
✅ BIEN: API_KEY: sk-****...****
❌ MAL: API_KEY: sk-proj-abc123...completa
```

---

## ✅ CHECKLIST PRE-COMMIT

Antes de hacer `git push`:

- [ ] ¿Hay API keys completas?
- [ ] ¿URLs reales expuestas?
- [ ] ¿Nombres reales de clientes?
- [ ] ¿Datos de producción en ejemplos?
- [ ] `.env` está en `.gitignore`

---

## 🚨 SI SE SUBE INFO SENSIBLE

1. **NO hacer commits "corrigiendo"** (queda en historial)
2. **Rotar TODAS las credenciales expuestas**
3. **Documentar el incidente**
4. **Considerar repo privado temporalmente**

---

**Última actualización**: 5 de Marzo 2026
**Mantenedor**: NicoCalama + Claude AI Assistant
