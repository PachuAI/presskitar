# Mapa de Hooks - Preskit.AR

**Proyecto**: Preskit.AR
**Framework**: React 18 + Next.js 14
**Última actualización**: 2025-09-30

---

## 📋 ESTADO

📝 **Documentación post-implementación**: Este archivo se actualiza DESPUÉS de crear/modificar hooks.

**Propósito**:
- Catálogo de todos los custom hooks implementados
- Parámetros, retorno, lógica y ejemplos de uso
- Evitar duplicación de lógica reutilizable

**Cuándo actualizar**:
- ✅ Después de crear un nuevo custom hook
- ✅ Después de modificar la API de un hook existente
- ✅ Después de refactorizar lógica en hooks

**NO es para planificación** → Usar PRD.md y memoria/estado.md para eso

---

## 🗂️ ORGANIZACIÓN

Los hooks están organizados por propósito funcional.

---

## 🪝 HOOKS POR CATEGORÍA

### Gestión de Estado

**Ubicación**: `lib/hooks/`

**Hooks planeados**:
- useEditorStore (acceso a Zustand store del editor)
- useProfile (gestión del perfil del usuario)

**Estado**: ⏭️ Por implementar

---

### File Upload

**Ubicación**: `lib/hooks/`

**Hooks planeados**:
- useFileUpload (upload transaccional a Supabase Storage)
- useAudioUpload (upload específico de archivos de audio con validación)
- useImageUpload (upload de imágenes con optimización)

**Estado**: ⏭️ Por implementar

---

### Autenticación

**Ubicación**: `lib/hooks/`

**Hooks planeados**:
- useAuth (wrapper de Supabase Auth)
- useSession (acceso a sesión del usuario)

**Estado**: ⏭️ Por implementar (puede venir del boilerplate)

---

### Validaciones

**Ubicación**: `lib/hooks/`

**Hooks planeados**:
- useAliasValidation (verificar disponibilidad de alias en tiempo real)

**Estado**: ⏭️ Por implementar

---

### Media

**Ubicación**: `lib/hooks/`

**Hooks planeados**:
- useAudioPlayer (control de reproductor de audio)
- useVideoEmbed (gestión de embeds de video)

**Estado**: ⏭️ Por implementar

---

## 📝 TEMPLATE PARA DOCUMENTAR HOOKS

**Cuando implementes un hook, usar este formato:**

### useNombreHook

**Ubicación**: `lib/hooks/useNombreHook.ts`

**Descripción**: Breve descripción de qué hace el hook (1-2 líneas)

**Parámetros**:
```typescript
interface UseNombreHookParams {
  param1: string             // Descripción de param1
  param2?: number            // Parámetro opcional
  onSuccess?: (data: T) => void
  onError?: (error: Error) => void
}
```

**Retorna**:
```typescript
interface UseNombreHookReturn {
  data: T | null             // Datos retornados
  isLoading: boolean         // Estado de carga
  error: Error | null        // Error si ocurre
  action: () => void         // Acción principal
  reset: () => void          // Reset del estado
}
```

**Lógica interna**:
- useEffect para...
- useState para...
- Llamada a API/servicio para...

**Dependencias**:
- useOtroHook (ver mapa-de-hooks.md)
- Función de lib/utils/ (ver mapa-de-utils.md)

**Usado en**:
- components/dominio/Componente.tsx
- app/ruta/page.tsx

**Ejemplo de uso**:
```tsx
const { data, isLoading, error, action } = useNombreHook({
  param1: 'valor',
  param2: 123,
  onSuccess: (data) => console.log('Success!', data),
  onError: (error) => console.error('Error:', error),
})

if (isLoading) return <Loading />
if (error) return <Error message={error.message} />

return <div>{data}</div>
```

**Consideraciones**:
- Performance: ...
- Edge cases: ...
- Limitaciones: ...

---

## 🔍 HOOKS DE LIBRERÍAS EXTERNAS

**Esta sección documenta wrappers de librerías de terceros**

### (Vacío por ahora)

---

## 🎯 HOOKS CRÍTICOS

**Esta sección documenta hooks especialmente importantes o complejos**

### (Vacío por ahora)

---

## 🔗 REFERENCIAS

**Arquitectura completa**: Ver `docs/arquitectura.md`

**Componentes que usan hooks**: Ver `docs/mapas/mapa-de-components.md`

---

**Nota**: Este archivo se irá actualizando a medida que se implementen hooks.

**Última actualización**: 2025-09-30 (inicial, vacío)
