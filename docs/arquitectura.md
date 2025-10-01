# Preskit.AR - Arquitectura del Sistema

**Fecha:** 2025-03-30
**Proyecto:** Preskit.AR - SaaS para artistas de música electrónica
**Stack:** Next.js 14 + Supabase + Vercel

---

## 1. VISIÓN GENERAL

### Descripción del Sistema

Preskit.AR es un SaaS para artistas de música electrónica (DJs y productores) que centraliza su presencia digital. Permite crear perfiles públicos personalizables con contenido multimedia: audio MP3, videos embebidos, links, biografía y press kits PDF descargables.

**Características principales:**
- Editor visual drag & drop con preview en tiempo real
- URLs públicas personalizadas: `preskit.ar/nombreartista`
- Sistema de archivos privados para demos unreleased
- Customización visual completa (colores, tipografías, estilos)
- Autenticación robusta con email/password y OAuth Google

### Arquitectura de Alto Nivel

```
┌─────────────────────────────────────────────────────────────┐
│                        FRONTEND                              │
│  Next.js 14 App Router + React + shadcn/ui + Tailwind      │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │   Landing    │  │   Dashboard  │  │   Perfil     │     │
│  │   /          │  │   /usuario/  │  │   Público    │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
└─────────────────────────────────────────────────────────────┘
                            ↕
┌─────────────────────────────────────────────────────────────┐
│                    NEXT.JS API ROUTES                        │
│     Server-side logic + JWT + Rate Limiting                 │
└─────────────────────────────────────────────────────────────┘
                            ↕
┌─────────────────────────────────────────────────────────────┐
│                      SUPABASE BaaS                           │
│  ┌───────────────┐  ┌──────────────┐  ┌─────────────┐     │
│  │ Auth          │  │ PostgreSQL   │  │  Storage    │     │
│  │ - Email/Pass  │  │ - Profiles   │  │  (Private)  │     │
│  │ - OAuth Google│  │ - Files      │  │  + RLS      │     │
│  │ - Sessions    │  │ - Components │  │  + JWT      │     │
│  └───────────────┘  └──────────────┘  └─────────────┘     │
└─────────────────────────────────────────────────────────────┘
                            ↕
┌─────────────────────────────────────────────────────────────┐
│                      VERCEL HOSTING                          │
│    Edge Functions + CDN + CI/CD + Analytics                 │
└─────────────────────────────────────────────────────────────┘
```

### Principios Arquitectónicos

1. **Serverless-first**: Infraestructura sin gestión de servidores (Vercel + Supabase)
2. **Security-first**: RLS estricta, validación en ambos lados, tokens con expiración
3. **Transaccional**: Operaciones críticas con rollback automático
4. **Mobile-first**: Diseño optimizado para mobile, desktop adaptativo
5. **Performance**: Caching agresivo, lazy loading, streaming de audio
6. **Escalable**: Preparado para crecer de 100 a 10,000+ usuarios

---

## 2. STACK TECNOLÓGICO

### Frontend

| Componente | Tecnología | Versión | Propósito |
|------------|-----------|---------|-----------|
| **Framework** | Next.js | 14 | SSR, routing, API routes |
| **UI Library** | React | 18 | Componentes interactivos |
| **Componentes UI** | shadcn/ui | Latest | Sistema de diseño accesible |
| **Estilos** | Tailwind CSS | Latest | Utility-first styling |
| **Formularios** | React Hook Form + Zod | Latest | Validación y gestión de forms |
| **Drag & Drop** | dnd-kit | Latest | Editor visual |
| **Estado Global** | Zustand + Immer | Latest | State management predecible |
| **Upload de Archivos** | React Dropzone | Latest | Validación client-side |
| **Animaciones** | Framer Motion | Latest | Transiciones suaves |

### Backend

| Componente | Tecnología | Propósito |
|------------|-----------|-----------|
| **API** | Next.js API Routes | Endpoints server-side |
| **Autenticación** | Supabase Auth | Sessions + OAuth |
| **Base de Datos** | PostgreSQL (Supabase) | Datos relacionales |
| **Storage** | Supabase Storage | Archivos multimedia |
| **Email** | React Email | Templates transaccionales |
| **Metadata de Audio** | music-metadata | Validación de duración MP3 |
| **Procesamiento de Imágenes** | sharp | Blur placeholders y optimización |
| **Tokens** | jose | JWT para acceso privado |
| **Sanitización** | DOMPurify (isomorphic) | Limpieza de HTML |

### Infraestructura

| Componente | Proveedor | Propósito |
|------------|-----------|-----------|
| **Hosting** | Vercel | Deploy, CDN, edge functions |
| **BaaS** | Supabase | Database + Auth + Storage |
| **Rate Limiting** | In-memory (MVP) | Control de requests |
| **Dominio** | A definir | preskit.ar |
| **Monitoring** | Vercel Analytics | Performance y errores |

---

## 3. ESTRUCTURA DEL PROYECTO

```
preskit.ar/
├── app/
│   ├── (autenticacion)/
│   │   ├── ingresar/              # Login
│   │   ├── registrarse/           # Registro
│   │   ├── verificar-email/       # Post-registro
│   │   └── recuperar-clave/       # Password reset
│   ├── (dashboard)/
│   │   └── usuario/
│   │       └── [username]/        # Editor del perfil
│   ├── (publico)/
│   │   └── [username]/            # Perfil público: preskit.ar/artistname
│   ├── api/
│   │   ├── auth/
│   │   │   ├── registrarse/
│   │   │   ├── ingresar/
│   │   │   ├── salir/
│   │   │   └── callback/          # OAuth callback
│   │   ├── perfil/
│   │   │   ├── route.ts           # GET, PATCH del perfil
│   │   │   ├── publicar/          # Toggle publicado/borrador
│   │   │   └── alias-disponible/  # Verificar alias
│   │   ├── archivos/
│   │   │   ├── iniciar-subida/    # Pre-check + signed URL
│   │   │   └── confirmar-subida/  # Validación + DB
│   │   ├── media/
│   │   │   └── [id]/              # Streaming con token
│   │   ├── componentes/
│   │   │   ├── route.ts           # CRUD de componentes
│   │   │   └── reordenar/
│   │   └── tokens/
│   │       └── acceso-privado/    # Generación de JWT
│   ├── layout.tsx
│   └── page.tsx                   # Landing
├── components/
│   ├── ui/                        # shadcn/ui components
│   ├── editor/
│   │   ├── Canvas.tsx
│   │   ├── PaletaComponentes.tsx
│   │   ├── PanelCustomizacion.tsx
│   │   └── Preview.tsx
│   ├── perfil/
│   │   ├── EncabezadoPerfil.tsx
│   │   ├── ReproductorAudio.tsx
│   │   ├── VideoEmbebido.tsx
│   │   ├── BotonLink.tsx
│   │   └── BloqueTexto.tsx
│   └── compartidos/
├── lib/
│   ├── supabase/
│   │   ├── client.ts
│   │   ├── server.ts
│   │   └── middleware.ts
│   ├── validaciones/
│   │   ├── auth.ts
│   │   ├── perfil.ts
│   │   └── archivos.ts           # Schemas + constantes de límites
│   ├── utils/
│   │   ├── audio.ts               # music-metadata wrapper
│   │   ├── imagenes.ts            # sharp wrapper (blur placeholders)
│   │   ├── tokens.ts              # JWT helpers
│   │   └── sanitizar.ts           # DOMPurify wrapper
│   ├── rate-limit.ts
│   └── hooks/
├── types/
│   ├── database.ts                # Tipos generados de Supabase
│   ├── perfil.ts
│   └── archivos.ts
├── store/
│   └── editor-store.ts            # Zustand store
├── supabase/
│   ├── migrations/
│   │   ├── 001_schema_inicial.sql
│   │   ├── 002_tabla_archivos.sql
│   │   ├── 003_historial_alias.sql
│   │   └── 004_politicas_rls.sql
│   └── seed.sql
├── .env.local
├── next.config.js
├── tailwind.config.ts
├── tsconfig.json
└── package.json
```

---

## 4. BASE DE DATOS

### Schema de PostgreSQL (Supabase)

#### Tabla: `profiles`

Datos del perfil del artista.

```sql
CREATE TABLE profiles (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,

  -- Datos básicos
  artist_name VARCHAR(50) NOT NULL UNIQUE,
  full_name VARCHAR(100) NOT NULL,
  birth_date DATE,
  bio TEXT,
  tagline VARCHAR(100), -- Para meta tags

  -- Imágenes
  profile_image_url TEXT,
  cover_image_url TEXT,

  -- Géneros musicales
  genres TEXT[] DEFAULT '{}',

  -- Estado
  is_published BOOLEAN DEFAULT FALSE,
  is_available_for_booking BOOLEAN DEFAULT TRUE,

  -- Customización visual (JSON)
  theme_settings JSONB DEFAULT '{
    "backgroundColor": "#ffffff",
    "textColor": "#000000",
    "accentColor": "#000000",
    "buttonStyle": {
      "backgroundColor": "#000000",
      "textColor": "#ffffff",
      "borderColor": "#000000",
      "borderWidth": 1,
      "hasShadow": false,
      "shadowColor": "#000000"
    },
    "fontFamily": "Inter",
    "isDarkMode": false
  }',

  -- Contacto
  contact_whatsapp VARCHAR(20),
  contact_email VARCHAR(100),
  contact_button_text VARCHAR(50) DEFAULT 'Contactar',

  -- Control de cambio de alias
  alias_changed_at TIMESTAMPTZ,

  -- Timestamps
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  last_published_at TIMESTAMPTZ,

  CONSTRAINT valid_artist_name CHECK (artist_name ~ '^[a-z0-9-_]+$')
);

-- Índices
CREATE INDEX idx_profiles_user_id ON profiles(user_id);
CREATE UNIQUE INDEX idx_profiles_artist_name_lower ON profiles(LOWER(artist_name));
CREATE INDEX idx_profiles_published ON profiles(is_published) WHERE is_published = TRUE;

-- Trigger para updated_at
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_profiles_updated_at
  BEFORE UPDATE ON profiles
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();
```

#### Tabla: `profile_alias_history`

Auditoría de cambios de alias.

```sql
CREATE TABLE profile_alias_history (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  profile_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  old_alias VARCHAR(50) NOT NULL,
  new_alias VARCHAR(50) NOT NULL,
  changed_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_alias_history_profile ON profile_alias_history(profile_id, changed_at DESC);
```

#### Función: Validar cambio de alias (máx 1 por mes)

```sql
CREATE OR REPLACE FUNCTION validar_cambio_alias()
RETURNS TRIGGER AS $$
BEGIN
  -- Si no cambió el alias, permitir
  IF NEW.artist_name = OLD.artist_name THEN
    RETURN NEW;
  END IF;

  -- Verificar si cambió hace menos de 30 días
  IF OLD.alias_changed_at IS NOT NULL
     AND OLD.alias_changed_at > NOW() - INTERVAL '30 days' THEN
    RAISE EXCEPTION 'Solo podés cambiar tu alias una vez por mes. Próximo cambio disponible: %',
      TO_CHAR(OLD.alias_changed_at + INTERVAL '30 days', 'DD/MM/YYYY');
  END IF;

  -- Registrar en historial
  INSERT INTO profile_alias_history (profile_id, old_alias, new_alias)
  VALUES (NEW.id, OLD.artist_name, NEW.artist_name);

  -- Actualizar timestamp
  NEW.alias_changed_at := NOW();

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER validate_alias_change
  BEFORE UPDATE OF artist_name ON profiles
  FOR EACH ROW
  EXECUTE FUNCTION validar_cambio_alias();
```

#### Tabla: `files`

Archivos multimedia (imágenes, audio, PDFs).

```sql
CREATE TYPE file_type AS ENUM ('profile_image', 'cover_image', 'audio', 'pdf');

CREATE TABLE files (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  profile_id UUID REFERENCES profiles(id) ON DELETE CASCADE,

  -- Tipo y ubicación
  type file_type NOT NULL,
  bucket VARCHAR(50) NOT NULL,
  path TEXT NOT NULL,
  original_filename VARCHAR(255),

  -- Metadata
  size_bytes BIGINT NOT NULL,
  mime_type VARCHAR(100) NOT NULL,
  duration_seconds INTEGER, -- Solo para audio

  -- Privacidad
  is_private BOOLEAN DEFAULT FALSE,

  -- Hash opcional para validación
  sha256_hash VARCHAR(64),

  -- Blur placeholder para imágenes (Next.js Image)
  blur_data_url TEXT,

  -- Timestamps
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),

  -- Constraints
  CONSTRAINT valid_audio_duration CHECK (
    (type = 'audio' AND duration_seconds IS NOT NULL AND duration_seconds <= 420)
    OR type != 'audio'
  ),
  CONSTRAINT valid_file_size CHECK (
    (type = 'audio' AND size_bytes <= 15728640) -- 15 MB
    OR (type = 'pdf' AND size_bytes <= 10485760) -- 10 MB
    OR (type IN ('profile_image', 'cover_image') AND size_bytes <= 5242880) -- 5 MB
  )
);

-- Índices
CREATE INDEX idx_files_user_id ON files(user_id);
CREATE INDEX idx_files_profile_id ON files(profile_id);
CREATE INDEX idx_files_type ON files(type);
CREATE INDEX idx_files_created ON files(created_at DESC);
CREATE INDEX idx_files_private ON files(id) WHERE is_private = TRUE;

CREATE TRIGGER update_files_updated_at
  BEFORE UPDATE ON files
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();
```

#### Tabla: `profile_components`

Componentes del perfil (botones, texto, audio, video, PDF).

```sql
CREATE TYPE component_type AS ENUM (
  'button',
  'text_block',
  'audio_player',
  'video_embed',
  'pdf_download'
);

CREATE TABLE profile_components (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  profile_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,

  -- Tipo y orden
  type component_type NOT NULL,
  position INTEGER NOT NULL,
  is_visible BOOLEAN DEFAULT TRUE,

  -- Referencia a archivo si aplica
  file_id UUID REFERENCES files(id) ON DELETE SET NULL,

  -- Datos específicos del componente (JSON flexible)
  data JSONB NOT NULL,
  /*
    button: { "text": "Instagram", "url": "https://...", "icon": "instagram" }
    text_block: { "content": "<p>Bio...</p>", "format": "html" }
    audio_player: { "title": "Track Name" }
    video_embed: { "url": "https://youtube.com/...", "provider": "youtube" }
    pdf_download: { "filename": "presskit.pdf" }
  */

  -- Timestamps
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),

  CONSTRAINT valid_position CHECK (position >= 0)
);

-- Índices
CREATE INDEX idx_components_profile ON profile_components(profile_id, position);
CREATE INDEX idx_components_type ON profile_components(type);
CREATE INDEX idx_components_file ON profile_components(file_id) WHERE file_id IS NOT NULL;

CREATE TRIGGER update_components_updated_at
  BEFORE UPDATE ON profile_components
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();
```

#### Tabla: `storage_usage`

Control de cuotas de almacenamiento por usuario.

```sql
CREATE TABLE storage_usage (
  user_id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,

  -- Límites por plan
  plan VARCHAR(20) DEFAULT 'free',
  total_limit_mb INTEGER DEFAULT 50,
  audio_files_limit INTEGER DEFAULT 1, -- Plan free: 1 audio

  -- Uso actual (en bytes)
  total_bytes BIGINT DEFAULT 0,
  audio_bytes BIGINT DEFAULT 0,
  image_bytes BIGINT DEFAULT 0,
  pdf_bytes BIGINT DEFAULT 0,

  -- Contadores
  audio_files_count INTEGER DEFAULT 0,
  video_embeds_count INTEGER DEFAULT 0,

  -- Versión para optimistic locking
  version INTEGER DEFAULT 0,

  -- Timestamp
  updated_at TIMESTAMPTZ DEFAULT NOW(),

  -- Constraints
  CONSTRAINT valid_audio_count CHECK (
    (plan = 'free' AND audio_files_count <= audio_files_limit)
    OR plan != 'free'
  ),
  CONSTRAINT valid_total_size CHECK (
    total_bytes <= (total_limit_mb * 1048576)
  )
);

CREATE TRIGGER update_storage_updated_at
  BEFORE UPDATE ON storage_usage
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();

-- Trigger para incrementar version (optimistic locking)
CREATE OR REPLACE FUNCTION increment_storage_version()
RETURNS TRIGGER AS $$
BEGIN
  NEW.version := OLD.version + 1;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER storage_version_increment
  BEFORE UPDATE ON storage_usage
  FOR EACH ROW
  EXECUTE FUNCTION increment_storage_version();
```

#### Triggers: Actualizar storage_usage automáticamente

```sql
-- Al insertar un archivo, actualizar contadores
CREATE OR REPLACE FUNCTION update_storage_after_file_insert()
RETURNS TRIGGER AS $$
BEGIN
  -- Lock pesimista para evitar race conditions
  PERFORM 1 FROM storage_usage WHERE user_id = NEW.user_id FOR UPDATE;

  UPDATE storage_usage
  SET
    total_bytes = total_bytes + NEW.size_bytes,
    audio_bytes = CASE WHEN NEW.type = 'audio' THEN audio_bytes + NEW.size_bytes ELSE audio_bytes END,
    image_bytes = CASE WHEN NEW.type IN ('profile_image', 'cover_image') THEN image_bytes + NEW.size_bytes ELSE image_bytes END,
    pdf_bytes = CASE WHEN NEW.type = 'pdf' THEN pdf_bytes + NEW.size_bytes ELSE pdf_bytes END,
    audio_files_count = CASE WHEN NEW.type = 'audio' THEN audio_files_count + 1 ELSE audio_files_count END
  WHERE user_id = NEW.user_id;

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_storage_after_insert
  AFTER INSERT ON files
  FOR EACH ROW
  EXECUTE FUNCTION update_storage_after_file_insert();

-- Al eliminar un archivo, decrementar contadores
CREATE OR REPLACE FUNCTION update_storage_after_file_delete()
RETURNS TRIGGER AS $$
BEGIN
  UPDATE storage_usage
  SET
    total_bytes = GREATEST(0, total_bytes - OLD.size_bytes),
    audio_bytes = CASE WHEN OLD.type = 'audio' THEN GREATEST(0, audio_bytes - OLD.size_bytes) ELSE audio_bytes END,
    image_bytes = CASE WHEN OLD.type IN ('profile_image', 'cover_image') THEN GREATEST(0, image_bytes - OLD.size_bytes) ELSE image_bytes END,
    pdf_bytes = CASE WHEN OLD.type = 'pdf' THEN GREATEST(0, pdf_bytes - OLD.size_bytes) ELSE pdf_bytes END,
    audio_files_count = CASE WHEN OLD.type = 'audio' THEN GREATEST(0, audio_files_count - 1) ELSE audio_files_count END
  WHERE user_id = OLD.user_id;

  RETURN OLD;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_storage_after_delete
  AFTER DELETE ON files
  FOR EACH ROW
  EXECUTE FUNCTION update_storage_after_file_delete();
```

### Row Level Security (RLS)

#### Policies para `profiles`

```sql
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- Ver propio perfil
CREATE POLICY "users_view_own_profile"
  ON profiles FOR SELECT
  USING (auth.uid() = user_id);

-- Crear propio perfil
CREATE POLICY "users_create_own_profile"
  ON profiles FOR INSERT
  WITH CHECK (auth.uid() = user_id);

-- Actualizar propio perfil
CREATE POLICY "users_update_own_profile"
  ON profiles FOR UPDATE
  USING (auth.uid() = user_id);

-- Perfiles publicados son públicos
CREATE POLICY "published_profiles_public"
  ON profiles FOR SELECT
  USING (is_published = TRUE);
```

#### Policies para `files`

```sql
ALTER TABLE files ENABLE ROW LEVEL SECURITY;

-- Ver propios archivos
CREATE POLICY "users_view_own_files"
  ON files FOR SELECT
  USING (auth.uid() = user_id);

-- Insertar propios archivos
CREATE POLICY "users_insert_own_files"
  ON files FOR INSERT
  WITH CHECK (auth.uid() = user_id);

-- Eliminar propios archivos
CREATE POLICY "users_delete_own_files"
  ON files FOR DELETE
  USING (auth.uid() = user_id);

-- Archivos públicos de perfiles publicados son visibles
CREATE POLICY "public_files_visible"
  ON files FOR SELECT
  USING (
    is_private = FALSE
    AND EXISTS (
      SELECT 1 FROM profiles
      WHERE profiles.id = files.profile_id
      AND profiles.is_published = TRUE
    )
  );
```

#### Policies para `profile_components`

```sql
ALTER TABLE profile_components ENABLE ROW LEVEL SECURITY;

-- Gestionar componentes propios
CREATE POLICY "users_manage_own_components"
  ON profile_components
  USING (
    EXISTS (
      SELECT 1 FROM profiles
      WHERE profiles.id = profile_components.profile_id
      AND profiles.user_id = auth.uid()
    )
  );

-- Componentes de perfiles publicados son públicos
CREATE POLICY "public_components_visible"
  ON profile_components FOR SELECT
  USING (
    EXISTS (
      SELECT 1 FROM profiles
      WHERE profiles.id = profile_components.profile_id
      AND profiles.is_published = TRUE
    )
  );
```

#### Policies para `storage_usage`

```sql
ALTER TABLE storage_usage ENABLE ROW LEVEL SECURITY;

CREATE POLICY "users_view_own_storage"
  ON storage_usage FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "users_update_own_storage"
  ON storage_usage FOR UPDATE
  USING (auth.uid() = user_id);
```

#### Policies para `profile_alias_history`

```sql
ALTER TABLE profile_alias_history ENABLE ROW LEVEL SECURITY;

CREATE POLICY "users_view_own_alias_history"
  ON profile_alias_history FOR SELECT
  USING (
    EXISTS (
      SELECT 1 FROM profiles
      WHERE profiles.id = profile_alias_history.profile_id
      AND profiles.user_id = auth.uid()
    )
  );
```

---

## 5. SUPABASE STORAGE

### Configuración General

**Todos los buckets son PRIVADOS por defecto.**

El acceso público se controla mediante:
- **Signed URLs** para archivos públicos (generadas on-demand)
- **JWT tokens** para archivos privados con expiración

### Bucket: `profile-images`

**Configuración:**
- Público: `FALSE`
- Límite: 5 MB por archivo
- MIME types: `image/jpeg`, `image/png`, `image/webp`

**Estructura:**
```
profile-images/
  └── {user_id}/
      ├── profile_{timestamp}.jpg
      └── cover_{timestamp}.jpg
```

**Políticas RLS:**
```sql
-- Solo el dueño puede subir
CREATE POLICY "users_upload_own_images"
  ON storage.objects FOR INSERT
  WITH CHECK (
    bucket_id = 'profile-images'
    AND auth.uid()::text = (storage.foldername(name))[1]
  );

-- Solo el dueño puede actualizar
CREATE POLICY "users_update_own_images"
  ON storage.objects FOR UPDATE
  USING (
    bucket_id = 'profile-images'
    AND auth.uid()::text = (storage.foldername(name))[1]
  );

-- Solo el dueño puede eliminar
CREATE POLICY "users_delete_own_images"
  ON storage.objects FOR DELETE
  USING (
    bucket_id = 'profile-images'
    AND auth.uid()::text = (storage.foldername(name))[1]
  );
```

**Acceso público:** Signed URLs con expiración de 1 año, generadas al publicar perfil.

### Bucket: `audio-files`

**Configuración:**
- Público: `FALSE`
- Límite: 15 MB por archivo
- MIME types: `audio/mpeg`, `audio/mp3`

**Estructura:**
```
audio-files/
  └── {user_id}/
      └── {file_id}.mp3
```

**Políticas RLS:**
```sql
-- Solo el dueño puede subir
CREATE POLICY "users_upload_own_audio"
  ON storage.objects FOR INSERT
  WITH CHECK (
    bucket_id = 'audio-files'
    AND auth.uid()::text = (storage.foldername(name))[1]
  );

-- Solo el dueño puede actualizar
CREATE POLICY "users_update_own_audio"
  ON storage.objects FOR UPDATE
  USING (
    bucket_id = 'audio-files'
    AND auth.uid()::text = (storage.foldername(name))[1]
  );

-- Solo el dueño puede eliminar
CREATE POLICY "users_delete_own_audio"
  ON storage.objects FOR DELETE
  USING (
    bucket_id = 'audio-files'
    AND auth.uid()::text = (storage.foldername(name))[1]
  );
```

**Acceso:**
- **Público:** Signed URL con expiración de 1 hora (regenerada en cada request)
- **Privado:** JWT token validado en `/api/media/:id?token=xxx`

### Bucket: `pdf-files`

**Configuración:**
- Público: `FALSE`
- Límite: 10 MB por archivo
- MIME types: `application/pdf`

**Estructura:**
```
pdf-files/
  └── {user_id}/
      └── presskit_{timestamp}.pdf
```

**Políticas RLS:**
```sql
-- Solo el dueño puede subir
CREATE POLICY "users_upload_own_pdfs"
  ON storage.objects FOR INSERT
  WITH CHECK (
    bucket_id = 'pdf-files'
    AND auth.uid()::text = (storage.foldername(name))[1]
  );

-- Solo el dueño puede actualizar
CREATE POLICY "users_update_own_pdfs"
  ON storage.objects FOR UPDATE
  USING (
    bucket_id = 'pdf-files'
    AND auth.uid()::text = (storage.foldername(name))[1]
  );

-- Solo el dueño puede eliminar
CREATE POLICY "users_delete_own_pdfs"
  ON storage.objects FOR DELETE
  USING (
    bucket_id = 'pdf-files'
    AND auth.uid()::text = (storage.foldername(name))[1]
  );
```

**Acceso público:** Signed URL con expiración de 1 día, regenerada en cada descarga.

---

## 6. AUTENTICACIÓN

### Flujos de Autenticación

#### Registro con Email/Password

1. Usuario completa formulario en `/registrarse`
2. API route valida datos (Zod schema)
3. Supabase Auth crea usuario
4. Trigger automático crea registros en `profiles` y `storage_usage`
5. Se envía email de verificación (template React Email)
6. Usuario verifica email en `/verificar-email`
7. Acceso al dashboard habilitado

**Endpoint:** `POST /api/auth/registrarse`

**Validaciones:**
```typescript
const registroSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8).regex(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)/),
  artistName: z.string().min(3).max(50).regex(/^[a-z0-9-_]+$/),
  fullName: z.string().min(2).max(100),
  birthDate: z.date().max(subYears(new Date(), 16)),
  genres: z.array(z.string()).min(1).max(4),
})
```

#### Registro con Google OAuth

1. Usuario hace click en "Continuar con Google" en `/registrarse`
2. Redirect a Google OAuth
3. Callback a `/api/auth/callback`
4. Supabase Auth crea usuario (ya verificado)
5. Redirect a formulario de completar perfil
6. Se guarda perfil en `profiles`
7. Acceso al dashboard habilitado

**Callback URL:** `https://preskit.ar/api/auth/callback`

#### Gestión de Sesiones

- **Cookies:** HttpOnly, Secure, SameSite=Lax
- **Refresh tokens:** Automático por Supabase Auth
- **Expiración:** 7 días de inactividad
- **Middleware:** Protección de rutas `/usuario/*`

```typescript
// middleware.ts
export async function middleware(req: NextRequest) {
  const { supabase, response } = createMiddlewareClient({ req, res })
  const { data: { session } } = await supabase.auth.getSession()

  if (!session && req.nextUrl.pathname.startsWith('/usuario')) {
    return NextResponse.redirect(new URL('/ingresar', req.url))
  }

  return response
}
```

---

## 7. SISTEMA DE UPLOADS TRANSACCIONAL

### Flow Completo

```
┌──────────────────────────────────────────────────────────┐
│  1. CLIENTE: POST /api/archivos/iniciar-subida          │
│     Body: { type, size, mimeType, filename }            │
└────────────────────┬─────────────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────────────────┐
│  2. API: /api/archivos/iniciar-subida                    │
│     ✓ Verificar autenticación                            │
│     ✓ Validar tipo y tamaño                              │
│     ✓ Verificar cuota (SELECT FOR UPDATE)               │
│     ✓ Verificar límite plan (free: 1 MP3)               │
│     ✓ Generar file_id UUID                               │
│     ✓ Crear signed URL (Supabase, 15 min)               │
│     → Retorna: { uploadUrl, fileId, bucket, path }      │
└────────────────────┬─────────────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────────────────┐
│  3. CLIENTE: Upload directo a Supabase Storage           │
│     Usando signed URL + fileId                           │
└────────────────────┬─────────────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────────────────┐
│  4. CLIENTE: POST /api/archivos/confirmar-subida         │
│     Body: { fileId, bucket, path, type, filename }      │
└────────────────────┬─────────────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────────────────┐
│  5. API: /api/archivos/confirmar-subida                  │
│     ✓ Verificar autenticación                            │
│     ✓ HEAD request a Storage (existe?)                  │
│     ✓ Obtener metadata real (size, mime)                │
│     ✓ SI ES AUDIO: Extraer duración (music-metadata)    │
│     ✓ Validar duración <= 7 min                          │
│                                                           │
│     TRANSACCIÓN:                                         │
│       BEGIN;                                             │
│         INSERT INTO files (...);                         │
│         -- Trigger actualiza storage_usage              │
│       COMMIT;                                            │
│                                                           │
│     SI FALLA:                                            │
│       ROLLBACK;                                          │
│       DELETE archivo de Storage;                         │
│       Retornar error;                                    │
│                                                           │
│     → Retorna: { success, fileId, fileUrl }             │
└──────────────────────────────────────────────────────────┘
```

### Implementación: `/api/archivos/iniciar-subida`

```typescript
// app/api/archivos/iniciar-subida/route.ts
import { createClient } from '@/lib/supabase/server'
import { NextResponse } from 'next/server'
import { z } from 'zod'

const iniciarSubidaSchema = z.object({
  type: z.enum(['profile_image', 'cover_image', 'audio', 'pdf']),
  size: z.number().positive(),
  mimeType: z.string(),
  filename: z.string(),
})

export async function POST(request: Request) {
  const supabase = createClient()

  // 1. Verificar auth
  const { data: { session } } = await supabase.auth.getSession()
  if (!session) {
    return NextResponse.json({ error: 'No autorizado' }, { status: 401 })
  }

  // 2. Validar input
  const body = await request.json()
  const result = iniciarSubidaSchema.safeParse(body)
  if (!result.success) {
    return NextResponse.json({ error: result.error }, { status: 400 })
  }

  const { type, size, mimeType, filename } = result.data

  // 3. Validar límites por tipo
  const limites = {
    profile_image: 5 * 1024 * 1024,
    cover_image: 5 * 1024 * 1024,
    audio: 15 * 1024 * 1024,
    pdf: 10 * 1024 * 1024,
  }

  if (size > limites[type]) {
    return NextResponse.json({
      error: `Archivo muy grande. Máximo: ${limites[type] / 1024 / 1024} MB`
    }, { status: 400 })
  }

  // 4. Verificar cuota disponible (RPC con lock)
  const { data: cuotaOk, error: cuotaError } = await supabase
    .rpc('verificar_y_reservar_espacio', {
      p_user_id: session.user.id,
      p_size_bytes: size,
      p_file_type: type,
    })

  if (cuotaError || !cuotaOk) {
    return NextResponse.json({
      error: 'Sin espacio disponible o límite alcanzado'
    }, { status: 403 })
  }

  // 5. Generar file_id y path
  const fileId = crypto.randomUUID()
  const extension = filename.split('.').pop()
  const bucket = obtenerBucketPorTipo(type)
  const path = `${session.user.id}/${fileId}.${extension}`

  // 6. Crear signed URL (15 min)
  const { data: signedUrl, error: signedError } = await supabase.storage
    .from(bucket)
    .createSignedUploadUrl(path, { upsert: false })

  if (signedError || !signedUrl) {
    return NextResponse.json({
      error: 'Error al generar URL de subida'
    }, { status: 500 })
  }

  return NextResponse.json({
    uploadUrl: signedUrl.signedUrl,
    fileId,
    bucket,
    path,
    token: signedUrl.token,
  })
}

function obtenerBucketPorTipo(type: string): string {
  if (type.includes('image')) return 'profile-images'
  if (type === 'audio') return 'audio-files'
  if (type === 'pdf') return 'pdf-files'
  throw new Error('Tipo inválido')
}
```

### Constantes de Validación

```typescript
// lib/validaciones/archivos.ts
export const LIMITES_ARCHIVOS = {
  // Tamaños máximos en bytes
  MAX_AUDIO_BYTES: 15 * 1024 * 1024,      // 15 MB
  MAX_PDF_BYTES: 10 * 1024 * 1024,        // 10 MB
  MAX_IMAGE_BYTES: 5 * 1024 * 1024,       // 5 MB

  // Duración máxima de audio
  MAX_AUDIO_DURACION_SEGUNDOS: 420,       // 7 minutos

  // Límites por plan
  FREE_MAX_AUDIO_COUNT: 1,                // 1 archivo MP3 en plan free
  FREE_MAX_STORAGE_MB: 50,                // 50 MB total en plan free

  // MIME types permitidos
  MIME_TYPES_AUDIO: ['audio/mpeg', 'audio/mp3'],
  MIME_TYPES_PDF: ['application/pdf'],
  MIME_TYPES_IMAGE: ['image/jpeg', 'image/png', 'image/webp'],
} as const

// Helper para obtener límite por tipo
export function obtenerLimitePorTipo(type: string): number {
  switch (type) {
    case 'audio':
      return LIMITES_ARCHIVOS.MAX_AUDIO_BYTES
    case 'pdf':
      return LIMITES_ARCHIVOS.MAX_PDF_BYTES
    case 'profile_image':
    case 'cover_image':
      return LIMITES_ARCHIVOS.MAX_IMAGE_BYTES
    default:
      throw new Error(`Tipo de archivo desconocido: ${type}`)
  }
}
```

### Función RPC: `verificar_y_reservar_espacio`

```sql
CREATE OR REPLACE FUNCTION verificar_y_reservar_espacio(
  p_user_id UUID,
  p_size_bytes BIGINT,
  p_file_type VARCHAR
)
RETURNS BOOLEAN AS $$
DECLARE
  v_usage storage_usage;
BEGIN
  -- Lock pesimista
  SELECT * INTO v_usage
  FROM storage_usage
  WHERE user_id = p_user_id
  FOR UPDATE;

  -- Verificar límite total
  IF (v_usage.total_bytes + p_size_bytes) > (v_usage.total_limit_mb * 1048576) THEN
    RAISE EXCEPTION 'Límite de almacenamiento total excedido';
  END IF;

  -- Verificar límite de audios en plan free
  IF p_file_type = 'audio' AND v_usage.plan = 'free' THEN
    IF v_usage.audio_files_count >= v_usage.audio_files_limit THEN
      RAISE EXCEPTION 'Límite de archivos de audio alcanzado. Plan free: % archivo(s)', v_usage.audio_files_limit;
    END IF;
  END IF;

  RETURN TRUE;

EXCEPTION
  WHEN OTHERS THEN
    RAISE;
END;
$$ LANGUAGE plpgsql;
```

### Implementación: `/api/archivos/confirmar-subida`

```typescript
// app/api/archivos/confirmar-subida/route.ts
import { createClient } from '@/lib/supabase/server'
import { NextResponse } from 'next/server'
import { obtenerDuracionAudio } from '@/lib/utils/audio'
import { z } from 'zod'

const confirmarSubidaSchema = z.object({
  fileId: z.string().uuid(),
  bucket: z.string(),
  path: z.string(),
  type: z.enum(['profile_image', 'cover_image', 'audio', 'pdf']),
  originalFilename: z.string(),
})

export async function POST(request: Request) {
  const supabase = createClient()

  // 1. Verificar auth
  const { data: { session } } = await supabase.auth.getSession()
  if (!session) {
    return NextResponse.json({ error: 'No autorizado' }, { status: 401 })
  }

  // 2. Validar input
  const body = await request.json()
  const result = confirmarSubidaSchema.safeParse(body)
  if (!result.success) {
    return NextResponse.json({ error: result.error }, { status: 400 })
  }

  const { fileId, bucket, path, type, originalFilename } = result.data

  try {
    // 3. Verificar que existe en Storage
    const { data: fileInfo, error: headError } = await supabase.storage
      .from(bucket)
      .list(session.user.id, { search: fileId })

    if (headError || !fileInfo || fileInfo.length === 0) {
      throw new Error('Archivo no encontrado en Storage')
    }

    const uploadedFile = fileInfo[0]
    const realSize = uploadedFile.metadata?.size || 0

    // 4. Si es audio, validar duración
    let duracionSegundos: number | null = null
    if (type === 'audio') {
      const { data: audioBlob, error: downloadError } = await supabase.storage
        .from(bucket)
        .download(path)

      if (downloadError || !audioBlob) {
        throw new Error('Error al descargar audio para validación')
      }

      duracionSegundos = await obtenerDuracionAudio(audioBlob)

      // Validar máximo 7 minutos
      if (duracionSegundos > 420) {
        await supabase.storage.from(bucket).remove([path])
        return NextResponse.json({
          error: `Audio muy largo. Máximo: 7 minutos. Tu archivo: ${Math.ceil(duracionSegundos / 60)} minutos`
        }, { status: 400 })
      }
    }

    // 5. Si es imagen, generar blur placeholder
    let blurDataUrl: string | null = null
    if (type === 'profile_image' || type === 'cover_image') {
      const { data: imageBlob } = await supabase.storage
        .from(bucket)
        .download(path)

      if (imageBlob) {
        // Generar blur placeholder con plaiceholder o sharp
        blurDataUrl = await generarBlurPlaceholder(imageBlob)
      }
    }

    // 6. Insertar en DB (trigger actualiza storage_usage)
    const { data: file, error: insertError } = await supabase
      .from('files')
      .insert({
        id: fileId,
        user_id: session.user.id,
        type,
        bucket,
        path,
        original_filename: originalFilename,
        size_bytes: realSize,
        mime_type: uploadedFile.metadata?.mimetype || 'application/octet-stream',
        duration_seconds: duracionSegundos,
        blur_data_url: blurDataUrl,
        is_private: false,
      })
      .select()
      .single()

    if (insertError) {
      // Rollback: eliminar de Storage
      await supabase.storage.from(bucket).remove([path])
      throw insertError
    }

    // 7. Generar signed URL para acceso público (opcional)
    const { data: publicUrl } = await supabase.storage
      .from(bucket)
      .createSignedUrl(path, 31536000) // 1 año

    return NextResponse.json({
      success: true,
      fileId,
      file,
      publicUrl: publicUrl?.signedUrl,
      blurDataUrl, // Para Next.js Image placeholder
    })

  } catch (error: any) {
    console.error('Error confirmando subida:', error)

    // Limpiar Storage
    try {
      await supabase.storage.from(bucket).remove([path])
    } catch (cleanupError) {
      console.error('Error en cleanup:', cleanupError)
    }

    return NextResponse.json({
      error: error.message || 'Error al confirmar subida'
    }, { status: 500 })
  }
}
```

### Utilidad: Extraer duración de audio

```typescript
// lib/utils/audio.ts
import { parseBlob } from 'music-metadata'

export async function obtenerDuracionAudio(blob: Blob): Promise<number> {
  try {
    const metadata = await parseBlob(blob)
    return Math.ceil(metadata.format.duration || 0)
  } catch (error) {
    console.error('Error parseando metadata de audio:', error)
    throw new Error('No se pudo validar la duración del audio')
  }
}
```

### Utilidad: Generar blur placeholder

```typescript
// lib/utils/imagenes.ts
import sharp from 'sharp'

export async function generarBlurPlaceholder(blob: Blob): Promise<string> {
  try {
    const buffer = Buffer.from(await blob.arrayBuffer())

    // Generar versión pequeña y borrosa (10x10px)
    const blurBuffer = await sharp(buffer)
      .resize(10, 10, { fit: 'inside' })
      .blur(5)
      .toBuffer()

    // Convertir a base64 data URL
    const base64 = blurBuffer.toString('base64')
    return `data:image/jpeg;base64,${base64}`
  } catch (error) {
    console.error('Error generando blur placeholder:', error)
    return '' // Placeholder vacío si falla
  }
}
```

---

## 8. SISTEMA DE TOKENS JWT PARA ACCESO PRIVADO

### Generación de Tokens

**Endpoint:** `POST /api/tokens/acceso-privado`

```typescript
// app/api/tokens/acceso-privado/route.ts
import { SignJWT } from 'jose'
import { createClient } from '@/lib/supabase/server'
import { NextResponse } from 'next/server'
import { rateLimit } from '@/lib/rate-limit'

const JWT_SECRET = new TextEncoder().encode(
  process.env.JWT_SECRET || 'your-secret-key-min-32-chars'
)

export async function POST(request: Request) {
  const supabase = createClient()
  const ip = request.headers.get('x-forwarded-for') || 'unknown'

  // Rate limiting: 10 tokens por minuto por IP
  const { success } = rateLimit(ip, 10, 60000)
  if (!success) {
    return NextResponse.json(
      { error: 'Demasiados requests. Intentá de nuevo en un minuto.' },
      { status: 429 }
    )
  }

  const { fileId } = await request.json()

  // Verificar que el archivo existe y es privado
  const { data: file, error } = await supabase
    .from('files')
    .select('id, user_id, is_private, profile_id')
    .eq('id', fileId)
    .single()

  if (error || !file) {
    return NextResponse.json({ error: 'Archivo no encontrado' }, { status: 404 })
  }

  if (!file.is_private) {
    return NextResponse.json({ error: 'Archivo no es privado' }, { status: 400 })
  }

  // Verificar que el perfil está publicado
  const { data: profile } = await supabase
    .from('profiles')
    .select('is_published')
    .eq('id', file.profile_id)
    .single()

  if (!profile?.is_published) {
    return NextResponse.json({ error: 'Perfil no publicado' }, { status: 403 })
  }

  // Generar JWT con expiración de 1 hora
  const token = await new SignJWT({
    fileId: file.id,
    userId: file.user_id,
  })
    .setProtectedHeader({ alg: 'HS256' })
    .setIssuedAt()
    .setExpirationTime('1h')
    .setJti(crypto.randomUUID())
    .sign(JWT_SECRET)

  return NextResponse.json({
    token,
    expiresIn: 3600,
    streamUrl: `/api/media/${fileId}?token=${token}`,
  })
}
```

### Streaming con Validación de Token

**Endpoint:** `GET /api/media/:id`

```typescript
// app/api/media/[id]/route.ts
import { jwtVerify } from 'jose'
import { createClient } from '@/lib/supabase/server'
import { NextResponse } from 'next/server'

const JWT_SECRET = new TextEncoder().encode(
  process.env.JWT_SECRET || 'your-secret-key-min-32-chars'
)

export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  const supabase = createClient()
  const { searchParams } = new URL(request.url)
  const token = searchParams.get('token')
  const fileId = params.id

  // Obtener info del archivo
  const { data: file, error: fileError } = await supabase
    .from('files')
    .select('*')
    .eq('id', fileId)
    .single()

  if (fileError || !file) {
    return NextResponse.json({ error: 'Archivo no encontrado' }, { status: 404 })
  }

  // Si es privado, validar token
  if (file.is_private) {
    if (!token) {
      return NextResponse.json({ error: 'Token requerido' }, { status: 401 })
    }

    try {
      const { payload } = await jwtVerify(token, JWT_SECRET)

      if (payload.fileId !== fileId) {
        return NextResponse.json({ error: 'Token inválido' }, { status: 403 })
      }
    } catch (error) {
      return NextResponse.json({ error: 'Token expirado o inválido' }, { status: 401 })
    }
  }

  // Si es público, verificar que el perfil esté publicado
  if (!file.is_private) {
    const { data: profile } = await supabase
      .from('profiles')
      .select('is_published')
      .eq('id', file.profile_id)
      .single()

    if (!profile?.is_published) {
      return NextResponse.json({ error: 'Perfil no publicado' }, { status: 403 })
    }
  }

  // Descargar de Storage
  const { data: blob, error: downloadError } = await supabase.storage
    .from(file.bucket)
    .download(file.path)

  if (downloadError || !blob) {
    return NextResponse.json({ error: 'Error al descargar' }, { status: 500 })
  }

  // Soporte para Range requests (streaming)
  const range = request.headers.get('range')

  if (!range) {
    return new NextResponse(blob, {
      status: 200,
      headers: {
        'Content-Type': file.mime_type,
        'Content-Length': blob.size.toString(),
        'Accept-Ranges': 'bytes',
        'Cache-Control': file.is_private
          ? 'private, no-cache, no-store, must-revalidate'
          : 'public, max-age=3600',
      },
    })
  }

  // Parsear Range
  const parts = range.replace(/bytes=/, '').split('-')
  const start = parseInt(parts[0], 10)
  const end = parts[1] ? parseInt(parts[1], 10) : blob.size - 1
  const chunkSize = end - start + 1
  const chunk = blob.slice(start, end + 1)

  return new NextResponse(chunk, {
    status: 206,
    headers: {
      'Content-Type': file.mime_type,
      'Content-Length': chunkSize.toString(),
      'Content-Range': `bytes ${start}-${end}/${blob.size}`,
      'Accept-Ranges': 'bytes',
      'Cache-Control': file.is_private
        ? 'private, no-cache, no-store, must-revalidate'
        : 'public, max-age=3600',
    },
  })
}
```

### Rate Limiting (MVP)

```typescript
// lib/rate-limit.ts
interface RateLimitRecord {
  count: number
  resetAt: number
}

const store = new Map<string, RateLimitRecord>()

export function rateLimit(
  identifier: string,
  limit: number = 10,
  windowMs: number = 60000
): { success: boolean; remaining: number } {
  const now = Date.now()
  const record = store.get(identifier)

  if (!record || now > record.resetAt) {
    store.set(identifier, { count: 1, resetAt: now + windowMs })
    return { success: true, remaining: limit - 1 }
  }

  if (record.count >= limit) {
    return { success: false, remaining: 0 }
  }

  record.count++
  store.set(identifier, record)

  return { success: true, remaining: limit - record.count }
}

// Limpiar registros expirados cada 10 minutos
setInterval(() => {
  const now = Date.now()
  for (const [key, value] of store.entries()) {
    if (now > value.resetAt) {
      store.delete(key)
    }
  }
}, 10 * 60 * 1000)
```

---

## 9. COMPONENTES PRINCIPALES

### Editor de Perfil

#### Store de Zustand

```typescript
// store/editor-store.ts
import { create } from 'zustand'
import { immer } from 'zustand/middleware/immer'

interface EditorState {
  theme: ThemeSettings
  components: ProfileComponent[]
  isPreviewMode: boolean
  isSaving: boolean

  setTheme: (theme: ThemeSettings) => void
  addComponent: (component: ProfileComponent) => void
  updateComponent: (id: string, data: any) => void
  deleteComponent: (id: string) => void
  reorderComponents: (newOrder: ProfileComponent[]) => void
  togglePreviewMode: () => void
  setSaving: (saving: boolean) => void
}

export const useEditorStore = create<EditorState>()(
  immer((set) => ({
    theme: defaultTheme,
    components: [],
    isPreviewMode: false,
    isSaving: false,

    setTheme: (theme) => set((state) => {
      state.theme = theme
    }),

    addComponent: (component) => set((state) => {
      state.components.push(component)
    }),

    updateComponent: (id, data) => set((state) => {
      const index = state.components.findIndex(c => c.id === id)
      if (index !== -1) {
        state.components[index].data = { ...state.components[index].data, ...data }
      }
    }),

    deleteComponent: (id) => set((state) => {
      state.components = state.components.filter(c => c.id !== id)
    }),

    reorderComponents: (newOrder) => set((state) => {
      state.components = newOrder
    }),

    togglePreviewMode: () => set((state) => {
      state.isPreviewMode = !state.isPreviewMode
    }),

    setSaving: (saving) => set((state) => {
      state.isSaving = saving
    }),
  }))
)
```

#### Componente: Canvas Drag & Drop

```typescript
// components/editor/Canvas.tsx
import { DndContext, closestCenter, DragEndEvent } from '@dnd-kit/core'
import { SortableContext, verticalListSortingStrategy } from '@dnd-kit/sortable'
import { useEditorStore } from '@/store/editor-store'

export function Canvas() {
  const { components, reorderComponents } = useEditorStore()

  function handleDragEnd(event: DragEndEvent) {
    const { active, over } = event
    if (!over || active.id === over.id) return

    const oldIndex = components.findIndex(c => c.id === active.id)
    const newIndex = components.findIndex(c => c.id === over.id)

    const newOrder = arrayMove(components, oldIndex, newIndex)
    reorderComponents(newOrder)
  }

  return (
    <DndContext collisionDetection={closestCenter} onDragEnd={handleDragEnd}>
      <SortableContext items={components} strategy={verticalListSortingStrategy}>
        <div className="space-y-2">
          {components.map(component => (
            <ComponentRenderer key={component.id} component={component} />
          ))}
        </div>
      </SortableContext>
    </DndContext>
  )
}
```

### Perfil Público

#### Componente: Reproductor de Audio

```typescript
// components/perfil/ReproductorAudio.tsx
'use client'

import { useState, useEffect, useRef } from 'react'

interface ReproductorAudioProps {
  fileId: string
  title: string
  duration: number
  isPrivate: boolean
}

export function ReproductorAudio({ fileId, title, duration, isPrivate }: ReproductorAudioProps) {
  const [audioUrl, setAudioUrl] = useState<string | null>(null)
  const [loading, setLoading] = useState(true)
  const audioRef = useRef<HTMLAudioElement>(null)

  useEffect(() => {
    async function fetchAudioUrl() {
      if (isPrivate) {
        // Obtener token JWT
        const response = await fetch('/api/tokens/acceso-privado', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ fileId }),
        })

        const { streamUrl } = await response.json()
        setAudioUrl(streamUrl)
      } else {
        // Archivo público
        setAudioUrl(`/api/media/${fileId}`)
      }

      setLoading(false)
    }

    fetchAudioUrl()
  }, [fileId, isPrivate])

  if (loading) return <div>Cargando...</div>

  return (
    <div className="audio-player rounded-lg border p-4">
      <h3 className="font-semibold mb-2">{title}</h3>
      <audio
        ref={audioRef}
        src={audioUrl || undefined}
        controls
        controlsList="nodownload"
        preload="metadata"
        className="w-full"
      >
        Tu navegador no soporta audio HTML5.
      </audio>
      {isPrivate && (
        <p className="text-sm text-gray-500 mt-2">
          🔒 Demo privado - Solo streaming
        </p>
      )}
    </div>
  )
}
```

---

## 10. SEGURIDAD

### Medidas Implementadas

#### 1. Validación Doble (Cliente + Servidor)

```typescript
// Cliente (React Hook Form + Zod)
const schema = z.object({
  artistName: z.string().min(3).max(50).regex(/^[a-z0-9-_]+$/),
})

// Servidor (API route)
export async function POST(request: Request) {
  const body = await request.json()
  const result = schema.safeParse(body)

  if (!result.success) {
    return NextResponse.json({ error: result.error }, { status: 400 })
  }

  // Procesar...
}
```

#### 2. Sanitización de HTML Server-Side

```typescript
// lib/utils/sanitizar.ts
import DOMPurify from 'isomorphic-dompurify'

export function sanitizarHTML(html: string): string {
  return DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ['p', 'br', 'strong', 'em', 'a', 'ul', 'ol', 'li'],
    ALLOWED_ATTR: ['href', 'target', 'rel'],
    ALLOWED_URI_REGEXP: /^https?:\/\//,
  })
}

// Usar en API al persistir:
const contenidoLimpio = sanitizarHTML(data.content)
```

#### 3. Content Security Policy (CSP)

```typescript
// next.config.js
const securityHeaders = [
  {
    key: 'Content-Security-Policy',
    value: `
      default-src 'self';
      script-src 'self' 'unsafe-eval' 'unsafe-inline';
      style-src 'self' 'unsafe-inline';
      img-src 'self' data: https:;
      media-src 'self' blob:;
      frame-src https://www.youtube.com https://player.vimeo.com;
      connect-src 'self' https://*.supabase.co;
    `.replace(/\s{2,}/g, ' ').trim()
  },
  {
    key: 'X-Frame-Options',
    value: 'DENY',
  },
  {
    key: 'X-Content-Type-Options',
    value: 'nosniff',
  },
  {
    key: 'Referrer-Policy',
    value: 'origin-when-cross-origin',
  },
]

module.exports = {
  async headers() {
    return [
      {
        source: '/:path*',
        headers: securityHeaders,
      },
    ]
  },
}
```

#### 4. Protección Contra Race Conditions

- `SELECT FOR UPDATE` en operaciones críticas
- Optimistic locking con campo `version`
- Triggers atómicos para contadores

#### 5. Rate Limiting

- 10 tokens JWT por minuto por IP
- 10 requests de upload init por minuto por usuario
- In-memory para MVP, Redis para escala

---

## 11. PERFORMANCE

### Optimizaciones

#### 1. Next.js Image Optimization

```typescript
import Image from 'next/image'

<Image
  src={profile.profile_image_url}
  alt={profile.artist_name}
  width={200}
  height={200}
  quality={85}
  placeholder="blur"
/>
```

#### 2. Code Splitting

```typescript
import dynamic from 'next/dynamic'

const Editor = dynamic(() => import('@/components/editor/Canvas'), {
  loading: () => <Skeleton />,
  ssr: false,
})
```

#### 3. Caching de Perfiles Públicos

```typescript
// app/(publico)/[username]/page.tsx
export const revalidate = 60 // ISR: 60 segundos

export default async function PerfilPublico({ params }) {
  const perfil = await obtenerPerfilPublico(params.username)
  return <VistaPerfil perfil={perfil} />
}
```

**Invalidación de cache al publicar/despublicar:**

```typescript
// app/api/perfil/publicar/route.ts
import { revalidatePath } from 'next/cache'

export async function POST(request: Request) {
  const { isPublished, artistName } = await request.json()

  // Actualizar estado en DB...
  await supabase
    .from('profiles')
    .update({ is_published: isPublished })
    .eq('artist_name', artistName)

  // Invalidar cache del perfil público
  revalidatePath(`/${artistName}`)

  return NextResponse.json({ success: true })
}
```

#### 4. Streaming de Audio

- Range requests habilitados
- Cache diferenciado público/privado
- Chunked transfer encoding

### Targets de Performance

- **Initial load:** < 200 KB
- **Total JS:** < 500 KB
- **LCP:** < 2.5s
- **FID:** < 100ms
- **CLS:** < 0.1
- **Lighthouse:** > 90

---

## 12. DEPLOYMENT

### Variables de Entorno

```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=https://xxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJxxx...
SUPABASE_SERVICE_ROLE_KEY=eyJxxx...

# Google OAuth
GOOGLE_CLIENT_ID=xxx.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-xxx

# JWT Secret
JWT_SECRET=your-super-secret-key-minimum-32-characters

# Base URL
NEXT_PUBLIC_BASE_URL=https://preskit.ar

# Email
RESEND_API_KEY=re_xxx

# Stripe (opcional en MVP)
STRIPE_SECRET_KEY=sk_xxx
STRIPE_WEBHOOK_SECRET=whsec_xxx
```

### Pipeline CI/CD (Vercel)

1. Push a `main` → Deploy a producción
2. Push a `develop` → Deploy a preview
3. Pull Request → Deploy preview único

**Checks pre-deploy:**
```json
{
  "scripts": {
    "build": "next build",
    "type-check": "tsc --noEmit",
    "lint": "eslint . --ext .ts,.tsx"
  }
}
```

### Monitoreo

- **Vercel Analytics:** Web Vitals, errores
- **Supabase Dashboard:** Queries, storage, auth
- **Logs:** Centralizados en Vercel

---

## 13. ESCALABILIDAD

### Fase 1: MVP (0-100 usuarios)

**Infraestructura:**
- Vercel Hobby/Pro
- Supabase Free/Pro
- Rate limiting in-memory

**Límites:**
- 100 usuarios concurrentes
- 5 GB storage total
- 10k requests/mes

### Fase 2: Growth (100-1000 usuarios)

**Ajustes:**
- Upgrade Vercel Pro
- Upgrade Supabase Pro
- Rate limiting con Upstash Redis
- CDN para audio (Cloudflare R2)

**Límites:**
- 1000 usuarios concurrentes
- 50 GB storage
- 100k requests/mes

### Fase 3: Scale (1000+ usuarios)

**Refactor:**
- Storage separado (S3 + CloudFront)
- Database read replicas
- Queueing para uploads (BullMQ)
- Analytics propio

---

## 14. RUTAS DE LA APLICACIÓN

### Públicas

| Ruta | Descripción |
|------|-------------|
| `/` | Landing page |
| `/:username` | Perfil público del artista |
| `/ingresar` | Login |
| `/registrarse` | Registro |
| `/verificar-email` | Post-registro verificación |
| `/recuperar-clave` | Password reset |

### Protegidas (requieren auth)

| Ruta | Descripción |
|------|-------------|
| `/usuario/:username` | Dashboard / Editor del perfil |
| `/usuario/:username/configuracion` | Configuración de cuenta |

### API Routes

| Ruta | Método | Descripción |
|------|--------|-------------|
| `/api/auth/registrarse` | POST | Registro con email/password |
| `/api/auth/ingresar` | POST | Login |
| `/api/auth/salir` | POST | Logout |
| `/api/auth/callback` | GET | OAuth callback |
| `/api/perfil` | GET, PATCH | Gestión del perfil |
| `/api/perfil/publicar` | POST | Toggle publicado/borrador |
| `/api/perfil/alias-disponible` | GET | Verificar alias |
| `/api/archivos/iniciar-subida` | POST | Pre-check + signed URL |
| `/api/archivos/confirmar-subida` | POST | Validación + DB |
| `/api/media/:id` | GET | Streaming con token |
| `/api/componentes` | GET, POST | CRUD componentes |
| `/api/componentes/reordenar` | POST | Reordenar componentes |
| `/api/tokens/acceso-privado` | POST | Generar JWT |

---

## RESUMEN EJECUTIVO

Esta arquitectura está diseñada para lanzar un MVP robusto de Preskit.AR en semanas, no meses.

**Decisiones clave:**
- Next.js 14 (estable) + Supabase (BaaS completo)
- Uploads transaccionales con validación server-side
- Archivos privados con JWT y expiración
- RLS estricta en toda la base de datos
- Rate limiting para prevenir abuso
- Performance-first con caching agresivo

**Estado:** Listo para desarrollo.

**Próximos pasos:**
1. Setup inicial del boilerplate
2. Migraciones de base de datos
3. Implementación de uploads transaccionales
4. Sistema de tokens JWT
5. Componentes del editor
6. Landing page + perfiles públicos

---

**Documento generado el 2025-03-30**
**Preskit.AR - De DJs para DJs** 🎧