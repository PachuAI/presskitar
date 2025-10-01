---
description: Gestión de fases del plan de implementación (start, complete, status)
---

# 🎯 Gestión de Fases

Sistema de gestión del Plan de Implementación v1.2 (26 fases totales).

---

## 📊 Estado Actual del Proyecto

@docs/estado-proyecto.json

---

## 📖 Plan de Implementación

@docs/plan-implementacion.md

---

## 🎬 Comando Ejecutado

**Argumentos recibidos**: {{ARGS}}

---

## 🤖 Instrucciones

Analizar el comando ejecutado y realizar la acción correspondiente:

### 1. `/fase` (sin argumentos) - MOSTRAR ESTADO
- Leer `docs/estado-proyecto.json` → fase_actual
- Extraer esa fase del `plan-implementacion.md`
- Mostrar resumen:
  ```
  📍 Fase Actual: N - [Nombre de la Fase]
  🎯 Hito: X
  📅 Fecha inicio fase: YYYY-MM-DD (o "No iniciada")
  ✅ Fases completadas: [lista]

  Próximo paso: Ejecuta `/fase start` para iniciar
  ```

### 2. `/fase start` - INICIAR FASE ACTUAL
- Leer `docs/estado-proyecto.json` → fase_actual
- Extraer fase completa del plan (tareas, criterios, tests)
- Crear `docs/fases/FASE_N_breakdown.md` con formato:
  ```markdown
  # Fase N: [Nombre] - Breakdown

  **Objetivo**: [Extraído del plan]
  **Prerequisitos**: [Listado]
  **Hito**: X

  ## Subtareas
  - [ ] 1. [Descripción subtarea] (archivo.ts)
  - [ ] 2. [Descripción subtarea]
  - [ ] 3. [Descripción subtarea]

  ## Progreso: 0/X (0%)

  ## Criterios de Aceptación
  - [ ] Criterio 1
  - [ ] Criterio 2

  ## Tests a Implementar
  - [ ] test/archivo.test.ts - Descripción

  ## Archivos a Crear/Modificar
  - `path/to/file.ts` (nuevo)
  - `path/to/otro.ts` (modificar)
  ```
- Actualizar `docs/estado-proyecto.json`:
  - `fecha_inicio_fase`: fecha actual (YYYY-MM-DD)
- Actualizar `memoria/estado.md`:
  - Fase actual, estado EN PROGRESO, 0% completado
- Actualizar `memoria/proximos-pasos.md`:
  - Primera subtarea de la fase
- Usar **TodoWrite** para crear checklist inmediata de todas las subtareas
- Output:
  ```
  ✅ Fase N iniciada
  📄 Breakdown: docs/fases/FASE_N_breakdown.md
  📝 Checklist creada en TodoList

  🚀 Arranca con la primera subtarea:
     [Descripción primera subtarea]
  ```

### 3. `/fase complete` - COMPLETAR FASE ACTUAL
- Leer `docs/estado-proyecto.json` → fase_actual
- Leer `docs/fases/FASE_N_breakdown.md`
- **Verificaciones obligatorias:**
  - ✅ Todos los checkboxes de subtareas marcados `[x]`
  - ✅ Progreso dice 100%
  - ⚠️ Si faltan checkboxes, RECHAZAR con mensaje:
    ```
    ❌ No se puede completar Fase N
    Faltan subtareas sin marcar:
    - [ ] 4. [Descripción]
    - [ ] 5. [Descripción]

    Completa las subtareas primero y ejecuta /fase complete de nuevo
    ```
- **Si todas las verificaciones pasan:**
  - Actualizar `docs/estado-proyecto.json`:
    - Agregar fase_actual a `fases_completadas`
    - Incrementar `fase_actual++`
    - Resetear `fecha_inicio_fase: null`
  - Actualizar `memoria/estado.md`:
    - Última fase completada: N
    - Próxima fase: N+1
  - Actualizar `memoria/proximos-pasos.md`:
    - "Ejecutar /fase start para iniciar Fase N+1"
  - Limpiar TodoWrite (marcar todos completados)
- Output:
  ```
  ✅ Fase N completada
  🎉 Progreso: X/26 fases (Y%)

  Sugerencia de commit:
  "feat: completar Fase N - [Nombre]"

  ➡️ Siguiente: Fase N+1 - [Nombre]
  Ejecuta /fase start cuando estés listo
  ```

### 4. `/fase N` (número específico) - SALTAR A FASE
- Actualizar `docs/estado-proyecto.json`:
  - `fase_actual: N`
  - `fecha_inicio_fase: null`
- Output:
  ```
  ⚠️ Saltaste a Fase N
  Ejecuta `/fase start` para iniciarla
  ```

### 5. `/fase list` - LISTAR TODAS LAS FASES
- Extraer todas las fases del plan
- Mostrar tabla:
  ```
  | # | Fase | Estado |
  |---|------|--------|
  | 0 | Setup | ✅ |
  | 1 | Database | 🔄 |
  | 2 | Auth | ⏸️ |
  ```

---

## 🔄 DURANTE LA IMPLEMENTACIÓN

### Actualizar Checkboxes de Subtareas

**Cuando Claude completa una subtarea:**

1. Leer `docs/fases/FASE_N_breakdown.md`
2. Identificar la subtarea completada
3. Cambiar `- [ ]` a `- [x]` en esa línea
4. Recalcular progreso: `X/Y (Z%)`
5. Actualizar línea de progreso en el breakdown
6. Guardar archivo

**Ejemplo:**

Antes:
```markdown
## Subtareas
- [x] 1. Crear schema Zod
- [x] 2. Implementar GET
- [ ] 3. Implementar PATCH
- [ ] 4. Tests

## Progreso: 2/4 (50%)
```

Después de implementar subtarea 3:
```markdown
## Subtareas
- [x] 1. Crear schema Zod
- [x] 2. Implementar GET
- [x] 3. Implementar PATCH
- [ ] 4. Tests

## Progreso: 3/4 (75%)
```

**Esto permite:**
- Tracking granular del progreso
- Continuidad entre sesiones (ver qué falta)
- Validación automática en `/fase complete`

---

## ⚙️ Reglas de Ejecución

1. **Siempre** actualizar `docs/estado-proyecto.json` en operaciones de estado
2. **Siempre** actualizar `memoria/estado.md` y `memoria/proximos-pasos.md` en cambios de fase
3. **Siempre** usar TodoWrite para tracking inmediato en `/fase start`
4. **Siempre** actualizar checkboxes en `docs/fases/FASE_N_breakdown.md` al completar subtareas
5. **Validar** que fase_actual esté en rango [0, 25]
6. **Ser conciso** en outputs (max 15 líneas)
7. **Incluir emojis** para claridad visual

---

## 📚 Documentación Relacionada

**Manual completo de uso:**
@docs/manual-uso-agentes-comandos.md

**Plan de implementación:**
@docs/plan-implementacion.md

**Reglas del proyecto:**
@.claude/CLAUDE.md

---

## 📝 Notas

- Este comando gestiona metadata, genera breakdowns y actualiza estado
- La implementación real la hace Claude durante la sesión
- Los tests se definen pero no se ejecutan automáticamente
- Claude debe actualizar checkboxes del breakdown.md al completar cada subtarea
- `/fase complete` verifica que todos los checkboxes estén marcados antes de avanzar
