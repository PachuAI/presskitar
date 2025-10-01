# Mapa de Utils - Preskit.AR

**Proyecto**: Preskit.AR
**Stack**: Next.js 14 + TypeScript
**Última actualización**: 2025-09-30

---

## 📋 ESTADO

📝 **Documentación post-implementación**: Este archivo se actualiza DESPUÉS de crear/modificar utilidades.

**Propósito**:
- Catálogo de todas las funciones utility implementadas
- Signature, descripción y ejemplos de uso
- Evitar duplicación de funciones helper

**Cuándo actualizar**:
- ✅ Después de crear una nueva función utility
- ✅ Después de modificar la signature de función existente
- ✅ Después de refactorizar utilidades

**NO es para planificación** → Usar arquitectura.md para definir estructura

---

## 🗂️ ORGANIZACIÓN

Las utilidades están organizadas por dominio funcional.

**Ubicación base**: `lib/utils/` y `lib/` (raíz)

---

## 🛠️ UTILS POR DOMINIO

### Audio

**Ubicación**: `lib/utils/audio.ts`

**Estado**: ⏭️ Por implementar

**Funciones planeadas** (según arquitectura.md):
- `obtenerDuracionAudio(blob: Blob): Promise<number>` - Extrae duración de MP3 usando music-metadata
- Validaciones de formato de audio

---

### Imágenes

**Ubicación**: `lib/utils/imagenes.ts`

**Estado**: ⏭️ Por implementar

**Funciones planeadas** (según arquitectura.md):
- `generarBlurPlaceholder(blob: Blob): Promise<string>` - Genera blur data URL con sharp
- Optimización de imágenes
- Validaciones de formato

---

### Tokens JWT

**Ubicación**: `lib/utils/tokens.ts`

**Estado**: ⏭️ Por implementar

**Funciones planeadas** (según arquitectura.md):
- Generación de JWT para archivos privados
- Verificación de tokens
- Helpers con `jose` library

---

### Sanitización

**Ubicación**: `lib/utils/sanitizar.ts`

**Estado**: ⏭️ Por implementar

**Funciones planeadas** (según arquitectura.md):
- `sanitizarHTML(html: string): string` - Wrapper de DOMPurify
- Limpieza de inputs de usuario

---

### Validaciones (Zod Schemas)

**Ubicación**: `lib/validaciones/`

**Estado**: ⏭️ Por implementar

**Archivos planeados** (según arquitectura.md):
- `auth.ts` - Schemas de autenticación
- `perfil.ts` - Schemas de perfil del artista
- `archivos.ts` - Schemas y constantes de límites de archivos

---

### Supabase Clients

**Ubicación**: `lib/supabase/`

**Estado**: ⏭️ Por implementar (probablemente viene del boilerplate)

**Archivos planeados** (según arquitectura.md):
- `client.ts` - Cliente de Supabase para cliente
- `server.ts` - Cliente de Supabase para servidor
- `middleware.ts` - Middleware de autenticación

---

### Rate Limiting

**Ubicación**: `lib/rate-limit.ts`

**Estado**: ⏭️ Por implementar

**Funciones planeadas** (según arquitectura.md):
- `rateLimit(identifier: string, limit: number, windowMs: number)` - Rate limiting in-memory

---

## 📝 TEMPLATE PARA DOCUMENTAR UTILS

**Cuando implementes una función utility, usar este formato:**

### nombreFuncion

**Ubicación**: `lib/utils/archivo.ts`

**Descripción**: Breve descripción de qué hace la función (1-2 líneas)

**Signature**:
```typescript
function nombreFuncion(
  param1: string,
  param2: number,
  options?: OpcionesOpcionales
): ReturnType
```

**Parámetros**:
- `param1`: Descripción del parámetro 1
- `param2`: Descripción del parámetro 2
- `options` (opcional): Objeto con opciones adicionales

**Retorna**:
- Tipo y descripción del valor retornado

**Throws**:
- Error si condición X
- Error si condición Y

**Dependencias externas**:
- Librería externa usada (ej: music-metadata, sharp)

**Usado en**:
- app/api/ruta/route.ts
- components/Componente.tsx
- lib/hooks/useHook.ts

**Ejemplo de uso**:
```typescript
import { nombreFuncion } from '@/lib/utils/archivo'

const resultado = await nombreFuncion('valor', 123, {
  opcion: true
})
```

**Notas**:
- Performance: ...
- Edge cases: ...
- Consideraciones especiales

---

## 🔍 CONSTANTES Y CONFIGURACIONES

**Esta sección documenta constantes importantes del proyecto**

### LIMITES_ARCHIVOS

**Ubicación**: `lib/validaciones/archivos.ts`

**Estado**: ⏭️ Por implementar

**Constantes planeadas** (según arquitectura.md):
```typescript
export const LIMITES_ARCHIVOS = {
  MAX_AUDIO_BYTES: 15 * 1024 * 1024,      // 15 MB
  MAX_PDF_BYTES: 10 * 1024 * 1024,        // 10 MB
  MAX_IMAGE_BYTES: 5 * 1024 * 1024,       // 5 MB
  MAX_AUDIO_DURACION_SEGUNDOS: 420,       // 7 minutos
  FREE_MAX_AUDIO_COUNT: 1,
  FREE_MAX_STORAGE_MB: 50,
  MIME_TYPES_AUDIO: ['audio/mpeg', 'audio/mp3'],
  MIME_TYPES_PDF: ['application/pdf'],
  MIME_TYPES_IMAGE: ['image/jpeg', 'image/png', 'image/webp'],
} as const
```

---

## 🎯 FUNCIONES CRÍTICAS

**Esta sección documenta funciones especialmente importantes o complejas**

### (Vacío por ahora)

---

## 🔗 REFERENCIAS

**Arquitectura completa**: Ver `docs/arquitectura.md` sección 3 (Estructura del proyecto)

**Constantes de validación**: Ver `docs/arquitectura.md` sección 7 (Constantes de Validación)

**Implementaciones específicas**: Ver `docs/arquitectura.md` para código ejemplo

---

**Nota**: Este archivo se irá actualizando a medida que se implementen utilidades.

**Última actualización**: 2025-09-30 (inicial, vacío)
