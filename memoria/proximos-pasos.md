# Próximos Pasos

**Última actualización**: 2025-09-30
**Sistema**: Plan de Implementación v1.2 (26 fases)

---

## 🔥 Inmediato (Siguiente Sesión)

### 1. Ejecutar `/fase start` para iniciar Fase 0

**La Fase 0 incluye:**
- Setup de Next.js 14 con App Router
- Configuración de Supabase (proyecto + variables de entorno)
- Instalación de dependencias base
- Verificación de build exitoso

**Comando a ejecutar:**
```
/fase start
```

Esto generará automáticamente:
- `docs/fases/FASE_0_breakdown.md` con subtareas detalladas
- TodoList con todas las subtareas
- Actualización de memoria/estado.md

---

## 📚 Contexto

### Sistema de Gestión de Fases

**Acabamos de crear en esta sesión:**
- ✅ Comando `/fase` (gestión completa de fases)
- ✅ Manual de uso (`docs/manual-uso-agentes-comandos.md`)
- ✅ Reglas del proyecto (`.claude/CLAUDE.md`)
- ✅ Estado técnico (`docs/estado-proyecto.json`)
- ✅ Estructura de memoria (`memoria/`)

**Workflow:**
1. `/arranca` → Ver estado actual
2. `/fase start` → Iniciar fase (genera breakdown + todos)
3. Implementar subtareas (actualizar checkboxes)
4. `/fase complete` → Completar fase
5. `/recap` → Documentar sesión
6. `/clear` → Limpiar contexto

**Leer el manual completo:**
Ver `docs/manual-uso-agentes-comandos.md` para casos de uso detallados

---

## 📖 Referencias

**Plan maestro:**
- `docs/plan-implementacion.md` - 26 fases, 6 hitos

**Planificación:**
- `docs/PRD.md` - Product Requirements
- `docs/arquitectura.md` - Decisiones técnicas

**Sistema:**
- `docs/estado-proyecto.json` - Estado actual (Fase 0, 0% completado)
- `memoria/estado.md` - Estado narrativo
- `.claude/CLAUDE.md` - Reglas y convenciones

---

## 🎯 Objetivo de la Fase 0

**Setup de Infraestructura:**
- Proyecto Next.js 14 funcionando
- Supabase conectado
- Variables de entorno configuradas
- Primer build exitoso

**Después de Fase 0:**
- Ejecutar `/fase complete`
- Commit: "feat: completar Fase 0 - Setup Infraestructura"
- Ejecutar `/fase start` para Fase 1 (Database Schema)

---

**Este archivo se actualiza automáticamente con `/fase start`, `/fase complete` y `/recap`**
