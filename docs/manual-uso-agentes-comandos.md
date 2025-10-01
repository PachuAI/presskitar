# 📚 Manual de Uso - Agentes y Comandos

**Proyecto**: presskit.ar
**Versión Plan**: 1.2
**Fecha**: 2025-09-30

---

## 🎯 Propósito de este Manual

Este documento define el **workflow oficial** para implementar el Plan de Implementación v1.2 (26 fases) usando:
- Comandos slash (`/arranca`, `/fase`, `/commit`, `/recap`)
- Sistema de tracking de estado
- Agente documentador (docs-creator)

**Lee este documento antes de empezar cualquier fase.**

---

## 📁 Estructura de Archivos del Sistema

```
presskit.ar/
├── docs/
│   ├── estado-proyecto.json          # ✅ Estado técnico (JSON) - automático
│   ├── plan-implementacion.md        # 📖 Plan maestro (26 fases) - READ ONLY
│   ├── manual-uso-agentes-comandos.md # 📚 Este documento
│   └── fases/
│       ├── FASE_0_breakdown.md       # ✅ Generado por /fase start
│       ├── FASE_1_breakdown.md       # ✅ Actualizado con checkboxes
│       └── ...
│
├── memoria/                           # 📝 Estado narrativo (MD)
│   ├── estado.md                      # Estado actual del proyecto
│   ├── proximos-pasos.md              # Lista accionable
│   └── recaps/
│       ├── 2025-09-30.md              # Snapshot de cada sesión
│       └── 2025-10-01.md
│
└── .claude/
    ├── CLAUDE.md                      # 🔧 Reglas del proyecto
    └── commands/
        ├── fase.md                    # Gestión de fases
        ├── arranca.md                 # Onboarding de sesión
        ├── commit.md                  # Commits semánticos
        └── recap.md                   # Documentación pre-clear
```

---

## 🔄 WORKFLOW OFICIAL

### **SESIÓN NUEVA - Inicio**

#### 1️⃣ Ejecutar `/arranca`

**Qué hace:**
- Lee `memoria/estado.md` → "¿En qué fase estoy?"
- Lee `memoria/proximos-pasos.md` → "¿Qué debo hacer ahora?"
- Lee `docs/estado-proyecto.json` → Fase actual técnica
- Lee últimos commits de git
- Lee último recap de `memoria/recaps/`

**Output esperado:**
```
📍 Estado del Proyecto

Fase actual: 5 - API de Gestión de Perfiles
Progreso: 43% (3/7 subtareas)
Última sesión: 2025-09-30

✅ Completado:
- Subtareas 1-3: Schema + GET + PATCH

⏸️ Pendiente:
- Subtarea 4: Validación alias único
- Subtareas 5-7: Tests + Rate limiting + Docs

🚀 Próximo paso inmediato:
Implementar validación de alias único
Ver: docs/fases/FASE_5_breakdown.md
```

**Cuándo ejecutarlo:**
- ✅ Siempre al inicio de cada sesión
- ✅ Después de un `/clear`
- ✅ Si te perdiste y no sabés qué hacer

---

### **INICIAR UNA FASE NUEVA**

#### 2️⃣ Ejecutar `/fase start`

**Cuándo:**
- Cuando `memoria/proximos-pasos.md` diga "Ejecutar /fase start"
- Cuando completaste la fase anterior y querés empezar la siguiente
- **NO ejecutar si la fase ya está iniciada** (verificar con `/fase`)

**Qué hace:**
1. Lee `docs/estado-proyecto.json` → `fase_actual = N`
2. Extrae Fase N de `docs/plan-implementacion.md`
3. Crea `docs/fases/FASE_N_breakdown.md` con:
   - Lista de subtareas con checkboxes `- [ ]`
   - Criterios de aceptación expandidos
   - Esqueleto de tests a crear
   - Lista de archivos a crear/modificar
4. Actualiza `docs/estado-proyecto.json`:
   ```json
   {
     "fecha_inicio_fase": "2025-09-30"
   }
   ```
5. Actualiza `memoria/estado.md`:
   ```markdown
   **Fase**: N - [Nombre]
   **Estado**: 🔄 EN PROGRESO (0% completado)
   **Iniciada**: 2025-09-30
   ```
6. Actualiza `memoria/proximos-pasos.md`:
   ```markdown
   1. Implementar subtarea 1 de Fase N
   2. Marcar checkbox en docs/fases/FASE_N_breakdown.md
   ```
7. Crea TodoWrite con todas las subtareas

**Output esperado:**
```
✅ Fase 5 iniciada

📄 Breakdown: docs/fases/FASE_5_breakdown.md
📝 Checklist creada en TodoList
📅 Iniciada: 2025-09-30

🚀 Arranca con la primera subtarea:
   Crear schema Zod de validación
```

---

### **DURANTE LA IMPLEMENTACIÓN**

#### 3️⃣ Ciclo de Implementación

**Por cada subtarea:**

```
┌─────────────────────────────────────┐
│ 1. Leer docs/fases/FASE_N_breakdown.md │
│    → Ver próxima subtarea sin check  │
├─────────────────────────────────────┤
│ 2. Implementar código                 │
│    → Crear/modificar archivos         │
├─────────────────────────────────────┤
│ 3. Escribir/actualizar tests          │
│    → Si la fase tiene 🧪              │
├─────────────────────────────────────┤
│ 4. Ejecutar tests                     │
│    → npm test (si aplica)             │
├─────────────────────────────────────┤
│ 5. Actualizar breakdown.md            │
│    → Cambiar [ ] a [x]                │
│    → Claude hace esto automáticamente │
├─────────────────────────────────────┤
│ 6. Marcar en TodoWrite como completada│
├─────────────────────────────────────┤
│ 7. Commit con /commit                 │
│    → Automático o con mensaje custom  │
└─────────────────────────────────────┘
```

#### 📝 Comando `/commit` - Commits Durante Desarrollo

**Propósito**: Crear commits semánticos después de completar subtareas.

**Cuándo usar**:
- Después de completar cada subtarea o grupo lógico de subtareas
- Antes de ejecutar `/fase complete`
- Antes de ejecutar `/recap`

**Tres modos de operación**:

1. **`/commit` (automático)**
   - Analiza cambios en git
   - Lee estado actual de la fase
   - Genera mensaje semántico automáticamente
   - Ejemplo: `"feat(fase-5): implementar GET /api/perfil con auth"`

2. **`/commit "mensaje custom"`**
   - Usa el mensaje provisto
   - Advertencia si no sigue conventional commits (opcional)
   - Ejemplo: `/commit "fix(fase-5): corregir query JOIN en archivos"`

3. **`/commit --suggest`**
   - Solo sugiere el mensaje (NO commitea)
   - Útil para ver qué mensaje generaría

**Formato de mensajes**:
```bash
# Subtarea de fase
feat(fase-N): descripción breve de la subtarea
fix(fase-N): descripción del bug corregido
test(fase-N): descripción de tests agregados

# Completar fase (después de /fase complete)
feat: completar Fase N - Nombre de la Fase

# Documentación
docs: actualizar manual con comando /commit
```

**Flujo recomendado**:
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

**Ver detalles completos**: `.claude/commands/commit.md`

---

**Ejemplo de breakdown.md actualizado:**
```markdown
# Fase 5: API de Gestión de Perfiles - Breakdown

## Subtareas
- [x] 1. Crear schema Zod de validación (lib/validations/perfil.ts)
- [x] 2. Implementar GET /api/perfil
- [x] 3. Implementar PATCH /api/perfil
- [ ] 4. Validación de alias único con profile_alias_history
- [ ] 5. Tests de API (vitest)
- [ ] 6. Rate limiting con Vercel KV
- [ ] 7. Documentación de endpoints

## Progreso: 3/7 (43%)
```

---

### **COMPLETAR UNA FASE**

#### 4️⃣ Ejecutar `/fase complete`

**Cuándo:**
- ✅ Todas las subtareas del breakdown tienen `[x]`
- ✅ Todos los criterios de aceptación cumplidos
- ✅ Tests pasan (si la fase tiene 🧪)

**Qué hace:**
1. Lee `docs/fases/FASE_N_breakdown.md`
2. Verifica que todos los checkboxes están marcados
3. Actualiza `docs/estado-proyecto.json`:
   ```json
   {
     "fase_actual": 6,
     "fecha_inicio_fase": null,
     "fases_completadas": [0,1,2,3,4,5]
   }
   ```
4. Actualiza `memoria/estado.md`:
   ```markdown
   **Última fase completada**: 5 - API de Gestión de Perfiles
   **Próxima fase**: 6 - Sistema de Archivos
   ```
5. Actualiza `memoria/proximos-pasos.md`:
   ```markdown
   1. Ejecutar /fase start para iniciar Fase 6
   ```
6. Limpia TodoWrite (marca todo completado)

**Output esperado:**
```
✅ Fase 5 completada
🎉 Progreso: 6/26 fases (23%)

Sugerencia de commit:
"feat: completar Fase 5 - API de Gestión de Perfiles"

➡️ Siguiente: Fase 6 - Sistema de Archivos
Ejecuta /fase start cuando estés listo
```

**Después de `/fase complete`:**
```bash
/commit
# → Usa el mensaje sugerido por /fase complete
# → "feat: completar Fase 5 - API de Gestión de Perfiles"
```

---

### **FIN DE SESIÓN**

#### 5️⃣ Ejecutar `/recap`

**Cuándo:**
- ✅ Antes de cada `/clear`
- ✅ Al terminar una sesión de trabajo
- ✅ SIEMPRE, aunque no hayas completado nada

**Qué hace:**
1. Invoca agente **docs-creator**
2. El agente lee toda la conversación actual
3. Analiza:
   - ¿Qué fases se trabajaron?
   - ¿Qué subtareas se completaron?
   - ¿Qué commits se hicieron?
   - ¿Qué decisiones técnicas se tomaron?
   - ¿Qué quedó pendiente?
4. Crea/actualiza archivos:

**memoria/recaps/2025-09-30.md:**
```markdown
# Sesión 2025-09-30

## Contexto
- Fase trabajada: 5 - API de Gestión de Perfiles
- Tiempo: ~3 horas
- Commits: 3

## Progreso
✅ Completado:
- Subtarea 1: Schema Zod de validación (lib/validations/perfil.ts)
- Subtarea 2: GET /api/perfil con autenticación
- Subtarea 3: PATCH /api/perfil con validación

⏸️ Pendiente:
- Subtareas 4-7 (validación alias, tests, rate limiting, docs)

## Archivos Creados/Modificados
- `lib/validations/perfil.ts` (nuevo)
- `app/api/perfil/route.ts` (nuevo)

## Decisiones Técnicas
- Usamos `perfilSchema.partial().omit({ artist_name: true })` para PATCH
- Rate limiting se pospone hasta tener Vercel KV configurado
- Tests unitarios de schemas van en `lib/validations/__tests__/`

## Bloqueadores
- Ninguno

## Notas
- Alias único requiere query a `profile_alias_history` (Fase 1)
- Ver plan-implementacion.md línea 2050 para schema exacto
```

**memoria/estado.md:**
```markdown
# Estado Actual del Proyecto

**Fecha última actualización**: 2025-09-30

## Fase Actual
**Fase**: 5 - API de Gestión de Perfiles
**Estado**: 🔄 EN PROGRESO (43% completado)
**Iniciada**: 2025-09-30

## Progreso de Fase 5
- [x] Subtarea 1: Schema Zod
- [x] Subtarea 2: GET /api/perfil
- [x] Subtarea 3: PATCH /api/perfil
- [ ] Subtarea 4: Validación alias
- [ ] Subtarea 5: Tests
- [ ] Subtarea 6: Rate limiting
- [ ] Subtarea 7: Documentación

## Progreso General
- Fases completadas: 0, 1, 2, 3, 4 (5/26 = 19%)
- Hito actual: 1 - Database y Autenticación
```

**memoria/proximos-pasos.md:**
```markdown
# Próximos Pasos

## Inmediato (próxima sesión)
1. Continuar Fase 5 - Subtarea 4: Validación de alias único
   - Consultar tabla `profile_alias_history`
   - Implementar en PATCH /api/perfil
   - Ver: docs/plan-implementacion.md línea 2055

2. Ejecutar tests de subtareas 1-3
   - npm test lib/validations

3. Implementar subtareas 5-7

4. Ejecutar /fase complete al terminar Fase 5

## Siguiente Fase
- Fase 6: Sistema de Archivos (Upload + Storage)
```

**Output esperado:**
```
✅ Sesión documentada

📄 Archivos actualizados:
- memoria/recaps/2025-09-30.md
- memoria/estado.md
- memoria/proximos-pasos.md

🔒 Seguro hacer /clear ahora
```

---

## 🔀 CASOS DE USO ESPECIALES

### **CASO 1: Fase Incompleta entre Sesiones**

**Sesión 1:**
```
/fase start         → Inicia Fase 5
[Implementa 3/7]    → Implementa subtareas 1-3
/commit             → Commit después de cada subtarea
/recap              → Documenta progreso
/clear
```

**Sesión 2:**
```
/arranca            → "Fase 5 en progreso (43%), continuar subtarea 4"
[Lee breakdown.md]  → Ve que faltan subtareas 4-7
[Implementa 4-7]    → Completa la fase
/commit             → Commit después de cada subtarea
/fase complete      → Marca fase como terminada
/commit             → Commit de fase completa
/recap
/clear
```

**⚠️ NO ejecutar `/fase start` de nuevo en Sesión 2**

---

### **CASO 2: Saltear a Fase Específica (debugging)**

**Uso:**
```
/fase 10
```

**Qué hace:**
- Actualiza `docs/estado-proyecto.json`: `fase_actual = 10`
- Resetea `fecha_inicio_fase = null`
- **NO** completa automáticamente fases anteriores

**Cuándo usarlo:**
- Debugging de fase específica
- Trabajo en paralelo (múltiples desarrolladores)
- Testing de fases individuales

**Después:**
```
/fase start  → Inicia Fase 10 normalmente
```

---

### **CASO 3: Ver todas las Fases**

**Uso:**
```
/fase list
```

**Output:**
```
📋 Plan de Implementación - 26 Fases

Hito 1: Database y Autenticación
├─ [✅] Fase 0: Setup Infraestructura
├─ [✅] Fase 1: Database Schema Base
├─ [✅] Fase 2: RLS Policies
├─ [✅] Fase 3: Auth - Email/Password
├─ [✅] Fase 4: Auth - Google OAuth
└─ [🔄] Fase 5: API de Gestión de Perfiles (43%)

Hito 2: Sistema de Archivos
├─ [⏸️] Fase 6: Storage y Upload
├─ [⏸️] Fase 7: Validaciones de Archivos
└─ ...
```

---

### **CASO 4: Revisar Estado sin Comando**

**Archivos a leer manualmente:**

```bash
# Estado técnico
cat docs/estado-proyecto.json

# Estado narrativo
cat memoria/estado.md

# Qué hacer ahora
cat memoria/proximos-pasos.md

# Breakdown de fase actual
cat docs/fases/FASE_5_breakdown.md
```

---

### **CASO 5: Checkpoint de Hito**

**Al completar todas las fases de un hito:**

1. Verificar que todas las fases del hito están completadas:
   ```
   /fase list
   ```

2. Ejecutar tests del hito:
   ```bash
   npm run test:hito-1
   ```

3. Deploy a preview:
   ```bash
   vercel --prod
   ```

4. Smoke tests manuales

5. Actualizar estado:
   ```json
   // docs/estado-proyecto.json
   {
     "hitos_completados": [1]
   }
   ```

6. Commit y tag:
   ```bash
   /commit "feat: completar Hito 1 - Database y Autenticación"
   git tag v0.1-hito-1
   git push --tags
   ```

---

## ⚠️ REGLAS IMPORTANTES

### ✅ HACER

1. **SIEMPRE** ejecutar `/arranca` al inicio de sesión
2. **SIEMPRE** ejecutar `/recap` antes de `/clear`
3. **SIEMPRE** actualizar checkboxes en `breakdown.md` al completar subtareas
4. **SIEMPRE** usar `/commit` después de completar cada subtarea o grupo lógico
5. **SIEMPRE** leer `memoria/proximos-pasos.md` para saber qué hacer

### ❌ NO HACER

1. **NO** ejecutar `/fase start` si la fase ya está iniciada
2. **NO** ejecutar `/fase complete` si hay checkboxes sin marcar
3. **NO** modificar `docs/plan-implementacion.md` sin consenso
4. **NO** saltear fases sin completarlas (usar `/fase N` solo para debugging)
5. **NO** hacer `/clear` sin antes hacer `/recap`

---

## 🔧 TROUBLESHOOTING

### "No sé en qué fase estoy"
```
/arranca
```

### "Olvidé qué hacer"
```
cat memoria/proximos-pasos.md
```

### "¿Qué subtareas faltan?"
```
cat docs/fases/FASE_N_breakdown.md
```

### "Perdí el contexto de la sesión anterior"
```
cat memoria/recaps/[ultima-fecha].md
```

### "Los archivos de memoria no existen"
```
/recap  → El agente docs-creator los creará
```

### "El comando /fase no funciona"
Verificar que existe `.claude/commands/fase.md`

---

## 📊 MÉTRICAS DE PROGRESO

### Ver progreso general:
```json
// docs/estado-proyecto.json
{
  "fases_completadas": [0,1,2,3,4],  // 5/26 = 19%
  "hitos_completados": [1]            // 1/6 = 17%
}
```

### Ver progreso de fase actual:
```markdown
// docs/fases/FASE_5_breakdown.md
## Progreso: 3/7 (43%)
```

---

## 🎓 EJEMPLO COMPLETO - Día Típico

**Mañana:**
```
/arranca
> "Fase 5 en progreso (43%), continuar subtarea 4"

[Leo docs/fases/FASE_5_breakdown.md]
[Implemento subtarea 4]
[Actualizo checkbox: - [x] 4. Validación...]
/commit
> "feat(fase-5): validar alias único"

[Implemento subtarea 5]
[Actualizo checkbox: - [x] 5. Tests...]
/commit
> "test(fase-5): tests de API perfil"
```

**Tarde:**
```
[Implemento subtareas 6-7]
[Todos los checkboxes marcados]

/fase complete
> "✅ Fase 5 completada. Siguiente: Fase 6"

/commit
> "feat: completar Fase 5 - API Gestión"

/fase start
> "✅ Fase 6 iniciada"

[Implemento 2 subtareas de Fase 6]

/recap
> "✅ Sesión documentada"

/clear
```

**Siguiente día:**
```
/arranca
> "Fase 6 en progreso (29%), continuar subtarea 3"

[Continúo desde donde dejé]
```

---

## 📖 REFERENCIAS

- **Plan Maestro**: `docs/plan-implementacion.md`
- **Estado Técnico**: `docs/estado-proyecto.json`
- **Estado Narrativo**: `memoria/estado.md`
- **Próximos Pasos**: `memoria/proximos-pasos.md`
- **Reglas del Proyecto**: `.claude/CLAUDE.md`
- **Comandos**: `.claude/commands/`

---

**Última actualización**: 2025-09-30
**Mantenido por**: Claude Code + Usuario
