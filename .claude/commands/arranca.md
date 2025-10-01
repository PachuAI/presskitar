---
description: Onboarding completo - Lee reglas, memoria y contexto del proyecto para arrancar sesión
---

# 🚀 Arrancando Sesión

Lee los siguientes archivos y dame un resumen ejecutivo del estado del proyecto:

---

## 1. Reglas del Proyecto
@CLAUDE.md

---

## 2. Mapa de Documentación
@INDICE_MAESTRO.md

---

## 3. Últimos 10 Commits
!git log --oneline --graph --decorate -10 2>/dev/null || echo "No es un repositorio git"

---

## 4. Estado de la Fase Actual
@memoria/estado.md

---

## 5. Próximos Pasos
@memoria/proximos-pasos.md

---

## 6. Último Recap (Sesión Anterior)
!if [ -d "memoria/recaps" ]; then ULTIMO=$(ls -t memoria/recaps/*.md 2>/dev/null | head -1); if [ -n "$ULTIMO" ]; then echo "Leyendo: $(basename $ULTIMO)"; cat "$ULTIMO"; else echo "No hay recaps disponibles"; fi; else echo "Carpeta memoria/recaps no existe"; fi

---

## 7. Arquitectura y Mapas de Referencia

### Arquitectura Técnica
@docs/arquitectura.md

### Mapas Técnicos (si existen)
!if [ -f "docs/mapas/mapa-de-apis.md" ]; then echo "=== Mapa de APIs ==="; cat docs/mapas/mapa-de-apis.md; fi
!if [ -f "docs/mapas/mapa-de-types.md" ]; then echo "=== Mapa de Types ==="; cat docs/mapas/mapa-de-types.md; fi
!if [ -f "docs/mapas/mapa-de-components.md" ]; then echo "=== Mapa de Components ==="; cat docs/mapas/mapa-de-components.md; fi
!if [ -f "docs/mapas/mapa-de-hooks.md" ]; then echo "=== Mapa de Hooks ==="; cat docs/mapas/mapa-de-hooks.md; fi
!if [ -f "docs/mapas/mapa-de-utils.md" ]; then echo "=== Mapa de Utils ==="; cat docs/mapas/mapa-de-utils.md; fi

---

## 📊 Resumen Requerido

Después de leer todo, proporcioname:

1. **Fase actual**: Número y nombre de la fase
2. **Progreso**: X/Y subtareas completadas (porcentaje)
3. **Próximo paso inmediato**: Qué debo hacer ahora (con referencias a archivos específicos)
4. **Contexto crítico**: Cualquier decisión técnica o blocker importante
5. **Último cambio relevante**: Del git log o recap

**Formato**: Conciso, directo, accionable. Máximo 10 líneas de resumen.