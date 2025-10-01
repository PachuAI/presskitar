# Mapa de Components - Preskit.AR

**Proyecto**: Preskit.AR
**Framework**: Next.js 14 + React 18
**Última actualización**: 2025-09-30

---

## 📋 ESTADO

📝 **Documentación post-implementación**: Este archivo se actualiza DESPUÉS de crear/modificar componentes.

**Propósito**:
- Catálogo de todos los componentes React implementados
- Props, descripción, ubicación y ejemplos de uso
- Evitar duplicación y facilitar reutilización

**Cuándo actualizar**:
- ✅ Después de crear un nuevo componente
- ✅ Después de modificar props o funcionalidad de componente existente
- ✅ Después de refactorizar componentes

**NO es para planificación** → Usar PRD.md y memoria/estado.md para eso

---

## 🗂️ ORGANIZACIÓN

Los componentes están organizados por dominio funcional.

---

## 🎨 COMPONENTES POR DOMINIO

### Editor (Componentes del editor de perfil)

**Estado**: ⏭️ Por implementar

**Ubicación**: `components/editor/`

**Componentes planeados** (según arquitectura.md):
- Canvas
- PaletaComponentes
- PanelCustomizacion
- Preview

---

### Perfil (Componentes del perfil público)

**Estado**: ⏭️ Por implementar

**Ubicación**: `components/perfil/`

**Componentes planeados** (según arquitectura.md):
- EncabezadoPerfil
- ReproductorAudio
- VideoEmbebido
- BotonLink
- BloqueTexto

---

### UI (Componentes shadcn/ui y compartidos)

**Estado**: ⏭️ Por implementar

**Ubicación**: `components/ui/`

**Nota**: shadcn/ui se instalará después del boilerplate

---

### Compartidos (Componentes reutilizables)

**Estado**: ⏭️ Por implementar

**Ubicación**: `components/compartidos/`

---

## 📝 TEMPLATE PARA DOCUMENTAR COMPONENTES

**Cuando implementes un componente, usar este formato:**

### NombreComponente

**Ubicación**: `components/dominio/NombreComponente.tsx`

**Descripción**: Breve descripción de qué hace el componente (1-2 líneas)

**Props**:
```typescript
interface NombreComponenteProps {
  prop1: string              // Descripción de prop1
  prop2: number              // Descripción de prop2
  prop3?: boolean            // Prop opcional
  onAction?: () => void      // Callback opcional
}
```

**Estado interno** (si tiene):
- Estado 1: Descripción
- Estado 2: Descripción

**Hooks usados**:
- useCustomHook (ver mapa-de-hooks.md)
- useState, useEffect, etc.

**Dependencias externas**:
- dnd-kit (drag & drop)
- framer-motion (animaciones)

**Usado en**:
- app/ruta/page.tsx
- components/otro/Componente.tsx

**Ejemplo de uso**:
```tsx
import { NombreComponente } from '@/components/dominio/NombreComponente'

<NombreComponente
  prop1="valor"
  prop2={123}
  prop3={true}
  onAction={() => console.log('Action!')}
/>
```

**Notas adicionales**:
- Consideraciones especiales
- Performance tips
- Known issues

---

## 🎯 COMPONENTES CRÍTICOS

**Esta sección documenta componentes especialmente importantes o complejos**

### (Vacío por ahora)

---

## 🔍 REFERENCIAS

**Arquitectura completa**: Ver `docs/arquitectura.md` sección 9 (Componentes Principales)

**Estructura del proyecto**: Ver `docs/arquitectura.md` sección 3

---

**Nota**: Este archivo se irá actualizando a medida que se implementen componentes.

**Última actualización**: 2025-09-30 (inicial, vacío)
