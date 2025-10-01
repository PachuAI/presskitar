# Próximos Pasos

**Última actualización**: 2025-09-30
**Sistema**: Plan de Implementación v1.2 (26 fases)

---

## 🔥 Inmediato (Siguiente Sesión)

### 1. Implementar Subtarea 1 de Fase 0

**Buscar y seleccionar boilerplate Next.js 14 + Supabase**

Requisitos del boilerplate:
- Next.js 14 con App Router
- TypeScript configurado
- shadcn/ui compatible o pre-instalado
- OAuth pre-configurado o fácil de integrar

Opciones a evaluar:
1. `launch-mvp-stripe-nextjs-supabase` (ya documentado en ADR)
2. Otros boilerplates populares en GitHub

**Documento de referencia:**
Ver `docs/ADR/boilerplate-elegido.md` para decisión previa

**Breakdown completo:**
Ver `docs/fases/FASE_0_breakdown.md` para todas las subtareas

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
