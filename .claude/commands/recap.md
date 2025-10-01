---
description: Documentar sesión completa antes de hacer clear (invoca agente docs-creator)
---

# 📝 Documentar Sesión

Invocar al agente **docs-creator** para documentar todo lo trabajado en esta sesión antes de hacer `/clear`.

---

## 📋 Contexto Actual

### Estado de memoria:
@memoria/estado.md

@memoria/proximos-pasos.md

### Commits recientes:
!git log --oneline --since="today" 2>/dev/null || git log --oneline -5 2>/dev/null || echo "⚠️ No hay commits o no es un repositorio git"

### Estado del repositorio:
!git status --short 2>/dev/null || echo "⚠️ No es un repositorio git"

---

## 🎯 Tarea

Usar el agente **docs-creator** para:

1. Leer toda la conversación de esta sesión
2. Analizar el progreso realizado
3. Crear/actualizar los archivos de memoria:
   - `memoria/recaps/YYYY-MM-DD.md` (nuevo)
   - `memoria/estado.md` (actualizar)
   - `memoria/proximos-pasos.md` (actualizar)

---

## ⚠️ Verificación Pre-Invocación

Antes de invocar al agente, verificar:
- ¿Hay trabajo significativo para documentar?
- ¿Los archivos de memoria existen? (si no, el agente los creará)

---

## 🚀 Acción

Invocar agente **docs-creator** ahora.