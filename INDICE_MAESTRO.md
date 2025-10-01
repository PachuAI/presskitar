# INDICE MAESTRO - Preskit.AR

**Proyecto**: Preskit.AR - SaaS para DJs y productores de música electrónica
**Stack**: Next.js 14 + Supabase + Vercel
**Última actualización**: 2025-09-30

---

## 🎯 INICIO RÁPIDO

**Nuevo en el proyecto?**
1. Lee `.claude/CLAUDE.md` (reglas del proyecto)
2. Lee `docs/PRD.md` (qué estamos construyendo)
3. Lee `docs/arquitectura.md` (cómo lo construimos)
4. Lee `docs/manual-uso-agentes-comandos.md` (workflow oficial)
5. Ejecuta `/arranca` para contexto completo

**Retomando sesión?**
- Ejecuta `/arranca` → resumen ejecutivo + próximo paso

**Antes de hacer `/clear`?**
- Ejecuta `/recap` → documenta sesión completa

---

## 📂 ESTRUCTURA DEL PROYECTO

```
presskit.ar/
├── .claude/                          # Sistema de comandos
│   ├── CLAUDE.md                     # Reglas del proyecto (NUEVO)
│   └── commands/                     # Comandos disponibles
│       ├── arranca.md                # /arranca - Onboarding
│       ├── recap.md                  # /recap - Documentar sesión
│       └── fase.md                   # /fase - Gestión de fases (NUEVO)
│
├── docs/                             # Documentación técnica
│   ├── PRD.md                        # Product Requirements (1042 líneas)
│   ├── arquitectura.md               # Arquitectura técnica (2046 líneas)
│   ├── plan-implementacion.md        # Plan de 26 fases (NUEVO)
│   ├── manual-uso-agentes-comandos.md # Workflow oficial (NUEVO)
│   ├── estado-proyecto.json          # Estado técnico (NUEVO)
│   ├── fases/                        # Breakdowns por fase (NUEVO)
│   │   └── FASE_N_breakdown.md       # Generado por /fase start
│   ├── mapas/                        # Mapas técnicos (post-implementación)
│   │   ├── mapa-de-apis.md
│   │   ├── mapa-de-types.md
│   │   ├── mapa-de-components.md
│   │   ├── mapa-de-hooks.md
│   │   └── mapa-de-utils.md
│   └── decisiones/                   # Architecture Decision Records
│       └── boilerplate-elegido.md
│
├── memoria/                          # Memoria entre sesiones
│   ├── estado.md                     # Estado actual del proyecto
│   ├── proximos-pasos.md             # Qué hacer ahora
│   └── recaps/                       # Snapshots de sesiones
│       └── YYYY-MM-DD.md
│
├── app/                              # Next.js App Router (futuro)
├── components/                       # React components (futuro)
├── lib/                              # Utilities y helpers (futuro)
│
├── INDICE_MAESTRO.md                 # Este archivo
├── bitacora-inicio-proyecto.md       # Historial de setup
└── [archivos de configuración]       # .env, package.json, etc.
```

---

## 📋 DOCUMENTACIÓN CRÍTICA

### 🎯 Planificación y Diseño
| Archivo | Descripción | Estado |
|---------|-------------|--------|
| `docs/PRD.md` | Product Requirements (1042 líneas) | ✅ Finalizado |
| `docs/arquitectura.md` | Arquitectura técnica (2046 líneas) | ✅ Finalizado |
| `docs/plan-implementacion.md` | Plan de 26 fases, 6 hitos | ✅ Finalizado |
| `docs/manual-uso-agentes-comandos.md` | Workflow oficial completo | ✅ Finalizado |
| `.claude/CLAUDE.md` | Reglas y convenciones del proyecto | ✅ Finalizado |
| `docs/decisiones/boilerplate-elegido.md` | ADR del boilerplate | ✅ Finalizado |

### 🧠 Sistema de Memoria
| Archivo | Descripción | Actualización |
|---------|-------------|---------------|
| `docs/estado-proyecto.json` | Estado técnico (fase actual, progreso) | Automática vía `/fase` |
| `memoria/estado.md` | Estado narrativo del proyecto | Automática vía `/fase` y `/recap` |
| `memoria/proximos-pasos.md` | Qué hacer en próxima sesión | Automática vía `/fase` y `/recap` |
| `memoria/recaps/YYYY-MM-DD.md` | Snapshots diarios | Automática vía `/recap` |

### 🗺️ Mapas de Referencia (Post-Implementación)
| Archivo | Descripción | Estado |
|---------|-------------|--------|
| `docs/mapas/mapa-de-apis.md` | API routes de Next.js | Pendiente |
| `docs/mapas/mapa-de-types.md` | Tipos TypeScript | Pendiente |
| `docs/mapas/mapa-de-components.md` | Componentes React | Pendiente |
| `docs/mapas/mapa-de-hooks.md` | Custom hooks | Pendiente |
| `docs/mapas/mapa-de-utils.md` | Funciones utility | Pendiente |

---

## 🧩 SISTEMA DE COMANDOS

### `/arranca`
**Propósito**: Onboarding al inicio de sesión
**Ubicación**: `.claude/commands/arranca.md`
**Lee**: CLAUDE.md, estado.md, proximos-pasos.md, git log, recaps
**Output**: Resumen ejecutivo + próximo paso inmediato

### `/fase`
**Propósito**: Gestión del Plan de Implementación (26 fases)
**Ubicación**: `.claude/commands/fase.md`
**Comandos**:
- `/fase` → Ver estado actual
- `/fase start` → Iniciar fase (genera breakdown + todos)
- `/fase complete` → Completar fase (verifica checkboxes)
- `/fase list` → Ver todas las fases
- `/fase N` → Saltar a fase específica

### `/commit`
**Propósito**: Crear commits semánticos durante desarrollo
**Ubicación**: `.claude/commands/commit.md`
**Comandos**:
- `/commit` → Commit automático con mensaje generado
- `/commit "mensaje"` → Commit con mensaje custom
- `/commit --suggest` → Solo sugerir mensaje (no commitea)
**Recuerda**: Ejecutar `/recap` antes de `/clear`

### `/recap`
**Propósito**: Documentar sesión antes de `/clear`
**Ubicación**: `.claude/commands/recap.md`
**Invoca**: Agente `docs-creator` (si existe)
**Genera**: Recap del día + actualiza memoria/

---

## 🔄 WORKFLOW DE DESARROLLO

### Ciclo de Sesión
```
1. /arranca → Contexto completo
2. /fase start → Iniciar fase (si es nueva)
3. Implementar subtareas → Actualizar checkboxes
4. /commit → Commitear cambios (después de cada subtarea)
5. /fase complete → Completar fase (cuando esté 100%)
6. /commit → Commit de fase completa
7. /recap → Documentar sesión
8. /clear → Limpiar contexto
9. VOLVER A 1
```

### Gestión de Fases (Plan v1.2)
- **Total**: 26 fases divididas en 6 hitos
- **Estado técnico**: `docs/estado-proyecto.json`
- **Estado narrativo**: `memoria/estado.md`
- **Workflow detallado**: `docs/manual-uso-agentes-comandos.md`

---

## 🛠️ STACK TÉCNICO

### Frontend
- **Framework**: Next.js 14 (App Router)
- **UI**: shadcn/ui + Tailwind CSS
- **Lenguaje**: TypeScript (strict mode)
- **Estado**: Zustand + Immer
- **Drag & Drop**: dnd-kit

### Backend
- **BaaS**: Supabase
  - Auth (email/password + OAuth Google)
  - PostgreSQL con RLS
  - Storage (archivos multimedia)
- **API**: Next.js API Routes
- **Validación**: Zod

### Infraestructura
- **Hosting**: Vercel
- **Rate Limiting**: Vercel KV
- **Image Processing**: Sharp (blur placeholders)

**Detalles completos**: Ver `docs/arquitectura.md`

---

## 📊 ESTADO ACTUAL DEL PROYECTO

### Fase Actual
**Fase**: 0 - Setup de Infraestructura
**Estado**: ⏸️ NO INICIADA
**Progreso**: 0/26 fases (0%)

### Completado Hasta Ahora
- ✅ PRD detallado (1042 líneas)
- ✅ Elección de boilerplate (ADR completo)
- ✅ Arquitectura técnica (2046 líneas)
- ✅ Plan de Implementación v1.2 (26 fases, 2 auditorías GPT5)
- ✅ Sistema de gestión de fases (`/fase`, manual, reglas)
- ✅ Estructura de memoria y documentación

### Próximo Paso Inmediato
**Ejecutar `/fase start` para iniciar Fase 0**

Esto generará automáticamente:
- `docs/fases/FASE_0_breakdown.md` con subtareas
- TodoList de tracking
- Actualización de estado

**Estado detallado**: Ver `memoria/estado.md`

---

## 🎨 FEATURES PRINCIPALES DEL MVP

### Core (MUST HAVE)
1. **Autenticación robusta** (email + OAuth Google)
2. **Editor de Perfil** (drag & drop visual)
3. **Perfiles Públicos** (preskit.ar/nombreartista)
4. **Contenido Multimedia** (audio, video, PDF, imágenes)
5. **Privacidad** (archivos privados con JWT)

**Features completas**: Ver `docs/PRD.md`

---

## 🗄️ BASE DE DATOS (SUPABASE)

### Tablas Principales
- `profiles` - Datos del artista
- `files` - Archivos multimedia
- `profile_components` - Componentes del perfil
- `profile_alias_history` - Historial de cambios de alias
- `storage_usage` - Control de cuotas

### Storage Buckets
- `profile-images` - Fotos de perfil
- `audio-files` - Archivos MP3
- `pdf-files` - Press kits

**Schema completo**: Ver `docs/arquitectura.md` sección 4

---

## 🚀 COMANDOS ÚTILES

### Desarrollo
```bash
npm run dev          # Servidor de desarrollo
npm run build        # Build de producción
npm run type-check   # Verificar tipos
npm run lint         # Linting
npm test             # Tests
```

### Git
```bash
git log --oneline --graph -10    # Últimos 10 commits
git status --short               # Estado resumido
```

### Claude Code
```bash
/arranca          # Inicio de sesión
/fase             # Ver estado de fases
/fase start       # Iniciar fase actual
/fase complete    # Completar fase actual
/fase list        # Ver todas las fases
/commit           # Commit automático
/commit "msg"     # Commit con mensaje custom
/commit --suggest # Sugerir mensaje (no commitea)
/recap            # Documentar sesión
/clear            # Limpiar contexto
```

---

## 📞 RECURSOS Y REFERENCIAS

### Documentación Externa
- [Next.js 14 Docs](https://nextjs.org/docs)
- [Supabase Docs](https://supabase.com/docs)
- [shadcn/ui Docs](https://ui.shadcn.com)
- [Tailwind CSS Docs](https://tailwindcss.com/docs)

### Boilerplate (Futuro)
- [launch-mvp-stripe-nextjs-supabase](https://github.com/ShenSeanChen/launch-mvp-stripe-nextjs-supabase)

---

## 🎯 OBJETIVOS DEL PROYECTO

### Objetivos de Negocio
- Portafolio destacado de ITERA (mi agencia)
- Networking con escena de música electrónica
- Lead magnet para servicios premium

### Objetivos Técnicos
- Testbed para sistema de gestión de fases
- Base replicable para futuros SaaS
- Contenido educativo (YouTube)

### Métricas de Éxito (MVP)
- 15 perfiles activos (primer mes)
- 100 perfiles creados (3 meses)
- Consultas para servicios premium

**Detalles completos**: Ver `docs/PRD.md` sección 2

---

## ⚡ QUICK LINKS

| Necesito... | Ir a... |
|-------------|---------|
| Entender qué construimos | `docs/PRD.md` |
| Entender cómo lo construimos | `docs/arquitectura.md` |
| Ver el plan de implementación | `docs/plan-implementacion.md` |
| Entender el workflow | `docs/manual-uso-agentes-comandos.md` |
| Ver reglas del proyecto | `.claude/CLAUDE.md` |
| Ver el estado actual | `memoria/estado.md` |
| Saber qué hacer ahora | `memoria/proximos-pasos.md` |
| Ver estado técnico | `docs/estado-proyecto.json` |
| Ver historial de setup | `bitacora-inicio-proyecto.md` |
| Ver decisiones técnicas | `docs/decisiones/` |
| Configurar comandos | `.claude/commands/` |

---

**Mantenimiento**: Actualizar cuando cambie estructura o workflow

**Última actualización**: 2025-09-30 (Sistema de gestión de fases completo)
