# Types Map - Preskit.AR

**Proyecto**: Preskit.AR
**Lenguaje**: TypeScript
**Stack**: Next.js 14 + Supabase
**Última actualización**: 2025-09-30

---

## 📋 ESTADO

📝 **Documentación post-implementación**: Este archivo se actualiza DESPUÉS de crear/modificar tipos.

**Propósito**:
- Inventario de todos los tipos/interfaces implementados
- Schema de datos del proyecto organizado por dominio
- Referencia rápida para evitar duplicación y entender relaciones

**Cuándo actualizar**:
- ✅ Después de crear nuevos tipos/interfaces
- ✅ Después de modificar tipos existentes
- ✅ Después de regenerar tipos de Supabase
- ✅ Después de crear tipos derivados (Omit, Pick, etc.)

**NO es para planificación** → Usar arquitectura.md (schema de DB) para eso

---

## 🗂️ ESTRUCTURA PLANEADA

### Ubicación de tipos
```
types/
├── database.ts       # Tipos generados de Supabase
├── perfil.ts         # Tipos del perfil del artista
├── archivos.ts       # Tipos de archivos multimedia
├── componentes.ts    # Tipos de componentes del perfil
└── auth.ts           # Tipos de autenticación
```

---

## 📝 DOMINIOS DE TIPOS

### 1. Database (Supabase)

**Archivo**: `types/database.ts`

**Estado**: ⏭️ Se generará automáticamente desde Supabase

**Comando de generación**:
```bash
npx supabase gen types typescript --project-id <project-id> > types/database.ts
```

**Contendrá**:
- Tipos de todas las tablas
- Tipos de las columnas
- Tipos de las relaciones
- Tipos de las vistas (si hay)

---

### 2. Perfil del Artista

**Archivo**: `types/perfil.ts`

**Estado**: ⏭️ Planificado

**Tipos esperados**:
```typescript
// Profile del artista
interface Profile {
  id: string
  user_id: string
  artist_name: string
  full_name: string
  birth_date: Date | null
  bio: string | null
  tagline: string | null
  profile_image_url: string | null
  cover_image_url: string | null
  genres: string[]
  is_published: boolean
  is_available_for_booking: boolean
  theme_settings: ThemeSettings
  contact_whatsapp: string | null
  contact_email: string | null
  contact_button_text: string
  created_at: Date
  updated_at: Date
}

// Configuración visual del perfil
interface ThemeSettings {
  backgroundColor: string
  textColor: string
  accentColor: string
  buttonStyle: ButtonStyle
  fontFamily: string
  isDarkMode: boolean
}

interface ButtonStyle {
  backgroundColor: string
  textColor: string
  borderColor: string
  borderWidth: number
  hasShadow: boolean
  shadowColor: string
}
```

**Referencia**: Ver `docs/arquitectura.md` sección 4 (tabla `profiles`)

---

### 3. Archivos Multimedia

**Archivo**: `types/archivos.ts`

**Estado**: ⏭️ Planificado

**Tipos esperados**:
```typescript
// Tipos de archivo
type FileType = 'profile_image' | 'cover_image' | 'audio' | 'pdf'

// Archivo multimedia
interface File {
  id: string
  user_id: string
  profile_id: string | null
  type: FileType
  bucket: string
  path: string
  original_filename: string
  size_bytes: number
  mime_type: string
  duration_seconds: number | null // Solo para audio
  is_private: boolean
  sha256_hash: string | null
  blur_data_url: string | null    // Solo para imágenes
  created_at: Date
  updated_at: Date
}

// Límites y constantes
interface FileLimits {
  MAX_AUDIO_BYTES: number
  MAX_PDF_BYTES: number
  MAX_IMAGE_BYTES: number
  MAX_AUDIO_DURACION_SEGUNDOS: number
  FREE_MAX_AUDIO_COUNT: number
  FREE_MAX_STORAGE_MB: number
}
```

**Referencia**: Ver `docs/arquitectura.md` sección 4 (tabla `files`)

---

### 4. Componentes del Perfil

**Archivo**: `types/componentes.ts`

**Estado**: ⏭️ Planificado

**Tipos esperados**:
```typescript
// Tipos de componente
type ComponentType =
  | 'button'
  | 'text_block'
  | 'audio_player'
  | 'video_embed'
  | 'pdf_download'

// Componente base
interface ProfileComponent {
  id: string
  profile_id: string
  type: ComponentType
  position: number
  is_visible: boolean
  file_id: string | null
  data: ComponentData
  created_at: Date
  updated_at: Date
}

// Datos específicos por tipo de componente
type ComponentData =
  | ButtonData
  | TextBlockData
  | AudioPlayerData
  | VideoEmbedData
  | PdfDownloadData

interface ButtonData {
  text: string
  url: string
  icon?: string
}

interface TextBlockData {
  content: string    // HTML sanitizado
  format: 'html'
}

interface AudioPlayerData {
  title: string
}

interface VideoEmbedData {
  url: string
  provider: 'youtube' | 'vimeo'
}

interface PdfDownloadData {
  filename: string
}
```

**Referencia**: Ver `docs/arquitectura.md` sección 4 (tabla `profile_components`)

---

### 5. Autenticación

**Archivo**: `types/auth.ts`

**Estado**: ⏭️ Planificado

**Tipos esperados**:
```typescript
// Datos de registro
interface RegistroData {
  email: string
  password: string
  artistName: string
  fullName: string
  birthDate: Date
  genres: string[]
}

// Sesión de usuario
interface Session {
  user: User
  access_token: string
  refresh_token: string
  expires_at: number
}

interface User {
  id: string
  email: string
  email_verified: boolean
  created_at: Date
}
```

---

### 6. Storage y Cuotas

**Archivo**: `types/storage.ts`

**Estado**: ⏭️ Planificado

**Tipos esperados**:
```typescript
// Uso de almacenamiento
interface StorageUsage {
  user_id: string
  plan: 'free' | 'premium'
  total_limit_mb: number
  audio_files_limit: number
  total_bytes: number
  audio_bytes: number
  image_bytes: number
  pdf_bytes: number
  audio_files_count: number
  video_embeds_count: number
  version: number
  updated_at: Date
}
```

**Referencia**: Ver `docs/arquitectura.md` sección 4 (tabla `storage_usage`)

---

### 7. API Responses

**Archivo**: `types/api.ts`

**Estado**: ⏭️ Planificado

**Tipos esperados**:
```typescript
// Respuesta exitosa genérica
interface ApiResponse<T> {
  success: true
  data: T
}

// Respuesta de error
interface ApiError {
  success: false
  error: string
  details?: any
}

// Union type de respuestas
type ApiResult<T> = ApiResponse<T> | ApiError
```

---

### 8. Editor Store

**Archivo**: `store/editor-store.ts` (incluye tipos inline)

**Estado**: ⏭️ Planificado

**Tipos esperados**:
```typescript
interface EditorState {
  theme: ThemeSettings
  components: ProfileComponent[]
  isPreviewMode: boolean
  isSaving: boolean

  // Actions
  setTheme: (theme: ThemeSettings) => void
  addComponent: (component: ProfileComponent) => void
  updateComponent: (id: string, data: any) => void
  deleteComponent: (id: string) => void
  reorderComponents: (newOrder: ProfileComponent[]) => void
  togglePreviewMode: () => void
  setSaving: (saving: boolean) => void
}
```

**Referencia**: Ver `docs/arquitectura.md` sección 9 (Componentes > Store de Zustand)

---

## 📦 UTILIDADES DE TIPOS

### Type Guards

```typescript
// Verificar tipo de componente
function isButtonComponent(component: ProfileComponent): component is ProfileComponent & { data: ButtonData } {
  return component.type === 'button'
}

function isAudioComponent(component: ProfileComponent): component is ProfileComponent & { data: AudioPlayerData } {
  return component.type === 'audio_player'
}
```

### Tipos derivados

```typescript
// Tipo de perfil público (sin datos sensibles)
type PublicProfile = Omit<Profile, 'contact_email' | 'contact_whatsapp'>

// Tipo de componente sin relaciones
type ComponentWithoutRelations = Omit<ProfileComponent, 'profile_id' | 'file_id'>
```

---

## 🔄 GENERACIÓN AUTOMÁTICA

### Tipos de Supabase

**Comando**:
```bash
npx supabase gen types typescript --project-id <project-id> > types/database.ts
```

**Frecuencia**: Ejecutar cada vez que cambie el schema de base de datos

---

### Tipos de Zod (validación)

**Patrón usado**:
```typescript
import { z } from 'zod'

// Schema de validación
const perfilSchema = z.object({
  artist_name: z.string().min(3).max(50).regex(/^[a-z0-9-_]+$/),
  full_name: z.string().min(2).max(100),
  bio: z.string().max(500).optional(),
  // ...
})

// Inferir tipo desde schema
type PerfilInput = z.infer<typeof perfilSchema>
```

**Ubicación**: `lib/validaciones/*.ts`

---

## 📚 REFERENCIAS

**Arquitectura completa**: Ver `docs/arquitectura.md`

**Schema de base de datos**: Ver `docs/arquitectura.md` sección 4

**Validaciones con Zod**: Ver `docs/arquitectura.md` sección 7 (ejemplo de validaciones)

---

## 🚀 CONVENCIONES

### Nomenclatura

- **Interfaces**: PascalCase (ej: `Profile`, `ThemeSettings`)
- **Types**: PascalCase (ej: `FileType`, `ComponentType`)
- **Enums**: PascalCase (ej: `UserRole`)
- **Type guards**: `is<TypeName>` (ej: `isButtonComponent`)

### Organización

- Un archivo por dominio
- Exportar tipos e interfaces nombrados (no default)
- Importar tipos con `import type` cuando sea posible

```typescript
// ✅ Bueno
import type { Profile, ThemeSettings } from '@/types/perfil'

// ❌ Evitar
import { Profile } from '@/types/perfil'  // si solo se usa como tipo
```

---

**Nota**: Este archivo se irá actualizando a medida que se implementen los tipos.

**Última actualización**: 2025-09-30 (inicial, vacío)
