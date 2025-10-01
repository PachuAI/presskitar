---
description: Crear commits semánticos y recordar documentar con /recap
---

# 💾 Gestión de Commits

Ejecuta commits siguiendo convenciones del proyecto y recuerda documentar.

---

## 📊 Estado del Proyecto

@docs/estado-proyecto.json
@docs/fases/FASE_N_breakdown.md (si existe)

---

## 🎬 Comando Ejecutado

**Argumentos**: {{ARGS}}

---

## 🤖 Instrucciones

### 1. `/commit` (sin argumentos) - COMMIT AUTOMÁTICO

**Flujo completo:**

#### A. Analizar Cambios

```bash
# 1. Verificar que hay cambios
git status --porcelain

# Si no hay cambios:
# Output: "⚠️ No hay cambios para commitear"
# Exit

# 2. Ver archivos modificados
git status --short

# 3. Ver diff de cambios
git diff --staged
git diff
```

#### B. Determinar Contexto

1. **Leer estado del proyecto:**
   - `docs/estado-proyecto.json` → `fase_actual`
   - `docs/fases/FASE_N_breakdown.md` (si existe) → Ver checkboxes recientes

2. **Inferir tipo de cambio:**
   - Nueva funcionalidad → `feat`
   - Bug fix → `fix`
   - Refactor → `refactor`
   - Tests → `test`
   - Documentación → `docs`
   - Configuración → `chore`

3. **Detectar scope:**
   - Si estamos en una fase → `fase-N`
   - Si es config → `config`
   - Si es deps → `deps`

#### C. Generar Mensaje de Commit

**Reglas de generación:**

**Caso 1: Fase completa (muchos cambios, fase al 100%)**
```
feat: completar Fase N - [Nombre de la Fase]
```

**Caso 2: Subtarea específica**
```
feat(fase-N): [descripción breve de la subtarea]
```

**Caso 3: Fix en fase**
```
fix(fase-N): [descripción del bug corregido]
```

**Caso 4: Tests**
```
test(fase-N): [descripción de tests agregados]
```

**Caso 5: Documentación**
```
docs: [descripción del cambio en docs]
```

**Ejemplos reales:**
```
feat: completar Fase 5 - API de Gestión de Perfiles
feat(fase-5): implementar GET /api/perfil con autenticación
feat(fase-8): agregar validación de duración de audio
fix(fase-5): corregir query de alias único en profiles
test(fase-5): agregar tests unitarios de schema Zod
docs: actualizar manual-uso-agentes-comandos.md
chore(deps): actualizar Next.js a 14.2.3
```

**Descripción debe ser:**
- ✅ Clara y específica
- ✅ En español (para este proyecto)
- ✅ Describir QUÉ se hizo (no CÓMO)
- ✅ > 10 caracteres
- ❌ NO vaga ("cambios", "actualización")

#### D. Mostrar Preview

```
📝 Mensaje de commit generado:
"feat(fase-5): implementar GET /api/perfil con autenticación"

📁 Archivos a commitear (5):
M  app/api/perfil/route.ts
A  lib/validations/perfil.ts
M  docs/fases/FASE_5_breakdown.md
M  memoria/estado.md
M  package.json

¿Proceder con commit? (Sí/No - default: Sí)
```

**Si el usuario no responde en 5 segundos o dice Sí:**
→ Proceder con commit

**Si el usuario dice No:**
→ Mostrar: "Commit cancelado. Usa /commit 'mensaje' para mensaje custom"

#### E. Ejecutar Commit

```bash
git add .
git commit -m "feat(fase-5): implementar GET /api/perfil con autenticación

🤖 Generated with Claude Code
Co-Authored-By: Claude <noreply@anthropic.com>"
```

#### F. Output Final

```
✅ Commit realizado exitosamente

📝 Mensaje:
"feat(fase-5): implementar GET /api/perfil con autenticación"

📊 Estado Git:
Branch: main
Commits ahead: 3
Archivos committeados: 5

⚠️ RECORDATORIO IMPORTANTE:
Ejecuta /recap antes de /clear para documentar la sesión

Próximo paso:
- Continuar con siguiente subtarea, o
- /fase complete (si terminaste la fase), o
- /recap (si terminás la sesión)
```

---

### 2. `/commit "mensaje custom"` - COMMIT CON MENSAJE MANUAL

**Flujo:**

1. Verificar que hay cambios (igual que `/commit`)

2. Validar formato del mensaje (opcional - solo advertir):
   ```
   ⚠️ NOTA: El mensaje no sigue conventional commits

   Tu mensaje:
   "fixed bug"

   Formato recomendado:
   "fix(fase-N): [descripción del bug]"

   ¿Proceder de todos modos? (Sí/No)
   ```

3. Si procede, ejecutar commit:
   ```bash
   git add .
   git commit -m "[mensaje provisto]

   🤖 Generated with Claude Code
   Co-Authored-By: Claude <noreply@anthropic.com>"
   ```

4. Output (igual que `/commit` automático)

---

### 3. `/commit --suggest` - SOLO SUGERIR (NO COMMITEAR)

**Flujo:**

1. Analizar cambios (igual que `/commit`)
2. Generar mensaje sugerido
3. Mostrar y salir (NO commitear)

**Output:**
```
💡 Mensaje de commit sugerido:
"feat(fase-5): implementar GET /api/perfil con autenticación"

📁 Archivos detectados (5):
M  app/api/perfil/route.ts
A  lib/validations/perfil.ts
M  docs/fases/FASE_5_breakdown.md
M  memoria/estado.md
M  package.json

Para commitear:
- /commit (usa este mensaje)
- /commit "otro mensaje"
- git commit -m "mensaje manual"
```

---

## ⚙️ Convenciones de Commit (Conventional Commits)

**Formato estándar:**
```
<tipo>(<scope>): <descripción>

[cuerpo opcional]

🤖 Generated with Claude Code
Co-Authored-By: Claude <noreply@anthropic.com>
```

### Tipos Permitidos

| Tipo | Uso | Ejemplo |
|------|-----|---------|
| `feat` | Nueva funcionalidad | `feat(fase-5): agregar endpoint de perfil` |
| `fix` | Corrección de bug | `fix(fase-8): corregir validación de audio` |
| `refactor` | Refactoring sin cambiar funcionalidad | `refactor(fase-10): simplificar lógica de upload` |
| `test` | Agregar o modificar tests | `test(fase-5): agregar tests de API perfil` |
| `docs` | Cambios en documentación | `docs: actualizar README con instrucciones` |
| `style` | Formato, prettier, linting | `style: aplicar prettier a archivos` |
| `chore` | Tareas de mantenimiento | `chore(deps): actualizar dependencias` |
| `perf` | Mejoras de performance | `perf(fase-15): optimizar queries de DB` |

### Scopes Comunes

| Scope | Uso |
|-------|-----|
| `fase-N` | Cambios en fase específica (más común) |
| `config` | Configuración del proyecto |
| `deps` | Dependencias |
| (ninguno) | Cambios globales o completar fase |

### Ejemplos por Escenario

**Completar una fase:**
```
feat: completar Fase 5 - API de Gestión de Perfiles
```

**Subtarea de fase:**
```
feat(fase-5): implementar PATCH /api/perfil con validación Zod
feat(fase-8): agregar blur placeholders para imágenes
feat(fase-12): crear componente AudioUploader con progress
```

**Fix durante implementación:**
```
fix(fase-5): corregir query JOIN en endpoint de archivos
fix(fase-8): resolver problema de timeout en upload de audio
```

**Tests:**
```
test(fase-5): agregar tests unitarios de perfilSchema
test(fase-8): agregar tests E2E de upload de archivos
```

**Refactor:**
```
refactor(fase-10): extraer lógica de validación a helper
refactor(fase-15): simplificar componente Editor con custom hook
```

**Docs:**
```
docs: actualizar manual-uso-agentes-comandos.md con /commit
docs: agregar ejemplos a CLAUDE.md
docs(fase-5): documentar endpoint GET /api/perfil
```

**Chores:**
```
chore(deps): actualizar Next.js a 14.2.3
chore(config): agregar regla de ESLint para imports
chore: agregar .env.example con variables requeridas
```

---

## 📝 Reglas Importantes

### ✅ HACER

1. **SIEMPRE** hacer commit después de completar una subtarea o grupo lógico
2. **SIEMPRE** usar conventional commits
3. **SIEMPRE** incluir scope `(fase-N)` cuando sea relevante
4. **SIEMPRE** recordar hacer `/recap` al terminar sesión
5. **Descripción clara** (qué se hizo, no cómo)
6. **Commits frecuentes** mejor que commits gigantes

### ❌ NO HACER

1. **NO** commitear sin mensaje descriptivo
2. **NO** usar mensajes vagos ("cambios", "update", "fix")
3. **NO** commitear código comentado o console.logs
4. **NO** commitear archivos de configuración local (.env)
5. **NO** hacer commits gigantes con 50+ archivos sin relación
6. **NO** hacer `/clear` sin antes hacer `/recap`

---

## 🔄 Flujo de Trabajo Recomendado

### Durante Implementación

```
1. Implementar subtarea 3
   ↓
2. Actualizar checkbox en breakdown.md
   - [x] 3. Implementar PATCH /api/perfil
   ↓
3. /commit
   → "feat(fase-5): implementar PATCH /api/perfil"
   ↓
4. Continuar con subtarea 4...
```

### Al Completar Fase

```
1. /fase complete
   → "Sugerencia: feat: completar Fase 5 - API..."
   ↓
2. /commit
   → Usa mensaje sugerido por /fase complete
   ↓
3. Continuar o terminar sesión
```

### Al Terminar Sesión

```
1. Asegurar que último cambio está commiteado
   → /commit (si hay cambios pendientes)
   ↓
2. /recap
   → Documenta toda la sesión
   ↓
3. /clear
```

---

## 🚫 Qué NO Hace Este Comando

- ❌ NO ejecuta `/recap` automáticamente (es tu decisión)
- ❌ NO hace `git push` (solo commit local)
- ❌ NO invoca agentes (release-conductor, docs-creator)
- ❌ NO valida todo el proyecto (eso es para release-conductor)
- ❌ NO hace deploy (eso es para release-conductor)

---

## 🔗 Diferencia con release-conductor

| Aspecto | `/commit` | `release-conductor` |
|---------|-----------|---------------------|
| Propósito | Commits diarios | Releases a producción |
| Frecuencia | Múltiples por día | Por hito completado |
| Validaciones | Básicas (formato) | Exhaustivas (todo el proyecto) |
| Deploy | ❌ No | ✅ Sí |
| Smoke tests | ❌ No | ✅ Sí |
| CHANGELOG | ❌ No actualiza | ✅ Valida y actualiza |
| Bump version | ❌ No | ✅ Sí (semver) |

**Usar `/commit` para:** Desarrollo día a día
**Usar `release-conductor` para:** Deploy a producción

---

## 📚 Referencias

**Manual de workflow:**
@docs/manual-uso-agentes-comandos.md

**Reglas del proyecto:**
@.claude/CLAUDE.md

**Para releases y deployment:**
@.claude/agents/release-conductor.md

---

## 💡 Tips

### Commits Atómicos
Hacer commits pequeños y frecuentes:
```
✅ BIEN:
- commit 1: feat(fase-5): crear schema Zod
- commit 2: feat(fase-5): implementar GET /api/perfil
- commit 3: feat(fase-5): implementar PATCH /api/perfil

❌ MAL:
- commit 1: feat(fase-5): implementar todo el endpoint (50 archivos)
```

### Commits Descriptivos
```
✅ BIEN:
feat(fase-8): agregar validación de duración máxima 7 min para audio

❌ MAL:
feat(fase-8): validación
feat(fase-8): cambios en audio
feat(fase-8): update
```

### Usar Scope
```
✅ BIEN:
fix(fase-5): corregir query JOIN en endpoint de archivos

❌ ACEPTABLE (pero menos claro):
fix: corregir query en endpoint
```

---

**Recordatorio:** Este comando facilita commits semánticos durante desarrollo. Para releases a producción, usar `release-conductor`.
