# Plan de Implementación - Preskit.AR

**Proyecto**: Preskit.AR - SaaS para artistas de música electrónica
**Stack**: Next.js 14 + Supabase + Vercel
**Metodología**: Desarrollo por fases granulares con validación continua
**Fecha**: 2025-09-30

---

## 📋 ÍNDICE

1. [Metodología de Trabajo](#metodología-de-trabajo)
2. [Roles y Responsabilidades](#roles-y-responsabilidades)
3. [Workflow por Fase](#workflow-por-fase)
4. [Fases de Implementación (0-23)](#fases-de-implementación)
5. [Hitos Funcionales](#hitos-funcionales)
6. [Sistema de Verificación](#sistema-de-verificación)
7. [Criterios de Avance](#criterios-de-avance)

---

## 🔧 CORRECCIONES APLICADAS (Basadas en revisiones GPT5)

**Fecha de actualización**: 2025-09-30
**Versión**: 1.2 (Corregida - 2da revisión)

Este plan fue auditado por GPT5 en **dos rondas**:
- **Ronda 1**: 5 gaps críticos + 3 riesgos técnicos ✅ Corregidos
- **Ronda 2**: 7 gaps adicionales ✅ Corregidos

**Total**: **12 gaps + 3 riesgos** → Todos resueltos

### ✅ Gaps Bloqueadores Resueltos

1. **Gap 1 (BLOCKER) - Editor sin UI de componentes**:
   - **Solución**: Añadida **Fase 18bis** - Biblioteca de componentes con formularios completos
   - Sidebar con botones para agregar todos los tipos de componentes
   - Formularios específicos por tipo (button, text, audio, video, PDF)
   - Botones editar/eliminar en cada componente
   - Sanitización HTML con DOMPurify

2. **Gap 2 (BLOCKER) - Schemas API incompletos**:
   - **Solución**: Actualizada **Fase 11** con `updatePerfilSchema` corregido
   - Ahora incluye: `theme_settings`, `profile_image_url`, `cover_image_url`
   - El botón "Guardar" del editor puede persistir la personalización

### ✅ Gaps de Alta Severidad Resueltos

3. **Gap 3 - Sistema de privacidad de audio ausente**:
   - **Solución**: Añadida **Fase 8bis** con endpoint `GET /api/archivos/:id`
   - Extendida Fase 8 para hacer `is_private` configurable
   - AudioPlayerPublico puede verificar privacidad correctamente

4. **Gap 4 - Uploads de imágenes de perfil ausentes**:
   - **Solución**: Añadida **Fase 9bis** - Sistema completo de imágenes
   - Componente `ImageUploader` reutilizable con drag & drop
   - Foto de perfil + cover image implementados
   - Blur placeholders integrados

5. **Gap 5 - Onboarding y checklist faltantes**:
   - **Solución**: Extendida **Fase 23** con sistema completo de onboarding
   - Checklist de publicación con items obligatorios/opcionales
   - Botón "Publicar" se deshabilita si faltan obligatorios
   - Plantilla inicial con componentes placeholder
   - Página de bienvenida post-registro

### ✅ Riesgos Técnicos Corregidos

6. **Riesgo 1 - Rate limiting in-memory (no funciona en serverless)**:
   - **Solución**: Actualizada **Fase 9** para usar Vercel KV (Redis)
   - Sistema persistente compatible con lambdas de Vercel
   - Fallback "fail open" en caso de error

7. **Riesgo 2 - Streaming descarga archivos en memoria**:
   - **Solución**: Actualizada **Fase 10** para usar signed URLs + redirect
   - Delega streaming al CDN de Supabase
   - Ahorra memoria y mejora performance

8. **Riesgo 3 - Sin sanitización HTML**:
   - **Solución**: Integrado en **Fase 18bis**
   - `isomorphic-dompurify` sanitiza contenido antes de guardar
   - Protección contra XSS en bloques de texto

### 🔴 Gaps Ronda 2 Resueltos (Revisión adicional)

9. **Gap 1 Ronda 2 - Campos blur ausentes en DB**:
   - **Solución**: Agregados `profile_blur_data_url` y `cover_blur_data_url` en tabla `profiles` (Fase 1)
   - `updatePerfilSchema` extendido para incluir estos campos
   - `ImageUploader` actualizado para pasar y persistir blur placeholders

10. **Gap 2 Ronda 2 - Profile no accesible en PanelCustomizacion**:
    - **Solución**: Store de Zustand extendido para incluir `profile` completo
    - `PanelCustomizacion` ahora usa `profile` del store
    - `loadProfile` actualizado para cargar el perfil en el estado

11. **Gap 3 Ronda 2 - profile_id faltante en archivos**:
    - **Solución**: `confirmar-subida` ahora resuelve y asigna `profile_id`
    - Query a `profiles` para obtener el ID antes del INSERT
    - Archivos correctamente vinculados al perfil del usuario

12. **Gap 4 Ronda 2 - Bug en query de metadata**:
    - **Solución**: `GET /api/archivos/:id` usa JOIN directo con `profiles`
    - Eliminada query anidada incorrecta
    - Validación de `is_published` funciona correctamente

13. **Gap 5 Ronda 2 - Validación Zod ignorada**:
    - **Solución**: `confirmar-subida` usa `result.data` post-validación
    - `isPrivate` extraído de `result.data` en vez de `body`
    - Contrato de validación respetado

14. **Gap 6 Ronda 2 - Sin UI para marcar audio privado**:
    - **Solución**: Switch agregado en `FormularioComponente` para audio
    - `AudioUploader` acepta prop `isPrivate`
    - Flag de privacidad se propaga correctamente a los endpoints
    - **Diferenciador clave del producto ahora funcional**

15. **Gap 7 Ronda 2 - Social share preview ausente**:
    - **Solución**: Componente `SocialSharePreview` agregado en Fase 20
    - Simula tarjeta Open Graph (cover + título + bio)
    - Botón "Copiar link" con feedback visual
    - Indicador de estado publicado/no publicado

### 📊 Nuevas Fases Añadidas

- **Fase 8bis**: Endpoint de metadata de archivos (`GET /api/archivos/:id`)
- **Fase 9bis**: Sistema de uploads de imágenes de perfil
- **Fase 18bis**: Biblioteca de componentes y formularios de edición

### 🔄 Fases Actualizadas

**Ronda 1**:
- **Fase 8**: `is_private` ahora es configurable
- **Fase 9**: Rate limiting con Vercel KV
- **Fase 10**: Streaming con signed URLs (no descarga en memoria)
- **Fase 11**: `updatePerfilSchema` incluye theme e imágenes
- **Fase 23**: Checklist + plantilla inicial + onboarding completo

**Ronda 2**:
- **Fase 1**: Schema de `profiles` incluye campos blur
- **Fase 8**: `confirmar-subida` asigna `profile_id` y usa `result.data`
- **Fase 8bis**: Query de metadata corregida con JOIN
- **Fase 16**: Store incluye `profile` completo
- **Fase 18**: `PanelCustomizacion` usa profile del store
- **Fase 18bis**: `AudioUploader` acepta `isPrivate` + Switch en formulario
- **Fase 20**: Integrado `SocialSharePreview`

---

## 🎯 METODOLOGÍA DE TRABAJO

### Principios Fundamentales

1. **Lento pero seguro**: Cada fase debe estar 100% completa antes de avanzar
2. **Todo listo desde momento cero**: Credenciales, URLs, configuraciones definidas antes de codear
3. **Modular y testeable**: Cada fase tiene tests específicos que deben pasar
4. **Memoria continua**: DocsCreator documenta todo para continuidad post-clear
5. **Validación multi-capa**: Code → Tests → Quality → Release

### Estructura de Progreso

```
FASE N
  ↓
DIVIDIR EN SPRINTS/TAREAS
  ↓
IMPLEMENTAR (Claude principal)
  ↓
VALIDAR (Agentes especializados)
  ↓
DOCUMENTAR (DocsCreator)
  ↓
COMMIT (ReleaseConductor)
  ↓
FASE N+1
```

---

## 👥 ROLES Y RESPONSABILIDADES

### Claude (Instancia Principal)
**Responsabilidad**: Implementación de código

- Escribe TODO el código frontend y backend
- Toma decisiones técnicas finales
- Aplica correcciones basadas en reportes de agentes
- Mantiene contexto maestro del proyecto

### TEST-MAESTRO (Agente)
**Responsabilidad**: Auditoría de testing

- Ejecuta suite de tests
- Analiza coverage por área
- Detecta tests faltantes
- Reporta qué tests crear con boilerplate
- **NO crea tests**, solo audita y reporta

**Cuándo se invoca**:
- Durante planificación de cada fase (define tests necesarios)
- Después de implementar código (valida coverage)
- Antes de avanzar a siguiente fase (gate de calidad)

### CODE-GUARDIAN (Agente)
**Responsabilidad**: Auditoría de calidad de código

- Ejecuta ESLint, TypeScript, build
- Detecta anti-patterns de Next.js 14
- Valida seguridad y performance
- Reporta problemas con código de corrección
- **NO modifica código**, solo audita y reporta

**Cuándo se invoca**:
- Después de escribir código de cada fase
- Antes de commit
- Como validación pre-deploy

### RELEASE-CONDUCTOR (Agente)
**Responsabilidad**: Orquestación de releases

- Valida formato de commits (conventional commits)
- Valida CHANGELOG.md actualizado
- Valida variables de entorno
- Ejecuta deploy cuando todo pasa
- Ejecuta smoke tests post-deploy
- **NO modifica archivos**, solo valida y despliega

**Cuándo se invoca**:
- Al finalizar grupo de fases (hito)
- Antes de deploy a preview/producción
- Para validar preparación de release

### DOCS-CREATOR (Agente)
**Responsabilidad**: Memoria entre sesiones

- Mantiene `memoria/estado.md` actualizado
- Mantiene `memoria/proximos-pasos.md`
- Crea recaps de sesión en `memoria/recaps/`
- Garantiza continuidad post-clear

**Cuándo se invoca**:
- Al final de cada sesión de trabajo
- Antes de hacer `/clear`
- Cuando se completa una fase importante

---

## 🔄 WORKFLOW POR FASE

### 1. Planificación de Fase
```yaml
Entrada: Especificación de fase del plan

Acciones:
  - Leer especificación de fase
  - Invocar TEST-MAESTRO: "Define tests para esta fase"
  - TEST-MAESTRO retorna: Lista de tests + boilerplate
  - Dividir fase en tareas específicas
  - Crear documento FASE_N.md con:
    * Objetivo
    * Tareas específicas
    * Tests a implementar (del reporte de TEST-MAESTRO)
    * Criterios de aceptación
    * Script de verificación

Salida: Documento FASE_N.md completo y listo
```

### 2. Implementación de Fase
```yaml
Entrada: Documento FASE_N.md

Acciones:
  - Claude principal lee FASE_N.md
  - Implementa código tarea por tarea
  - Escribe tests (usando boilerplate de TEST-MAESTRO)
  - Ejecuta tests localmente mientras desarrolla

Salida: Código implementado + tests escritos
```

### 3. Validación de Fase
```yaml
Entrada: Código implementado

Acciones (en paralelo):
  - Invocar CODE-GUARDIAN: "Audita código de fase N"
  - Invocar TEST-MAESTRO: "Valida tests de fase N"

Reportes:
  - CODE-GUARDIAN retorna: Errores/warnings con código de corrección
  - TEST-MAESTRO retorna: Coverage, tests faltantes

Si hay errores bloqueantes:
  - Claude principal aplica correcciones
  - Re-invocar agentes hasta que status = "pass"

Si todo OK:
  - Continuar a Documentación
```

### 4. Documentación de Fase
```yaml
Entrada: Fase validada y funcional

Acciones:
  - Invocar DOCS-CREATOR: "Documenta fase N completada"
  - DOCS-CREATOR actualiza:
    * memoria/estado.md (tachar fase N)
    * memoria/proximos-pasos.md (agregar fase N+1)
    * memoria/recaps/YYYY-MM-DD.md (detalles técnicos)

Salida: Documentación persistida
```

### 5. Commit y Release (si aplica)
```yaml
Entrada: Fase documentada

Acciones:
  - Preparar commit con conventional commits
  - Invocar RELEASE-CONDUCTOR: "Valida preparación para commit"
  - Si es hito completo:
    * RELEASE-CONDUCTOR valida CHANGELOG
    * RELEASE-CONDUCTOR ejecuta deploy preview
    * RELEASE-CONDUCTOR ejecuta smoke tests
  - Si todo OK:
    * Commit se realiza
    * Deploy se completa

Salida: Fase commiteada y deployada (si aplica)
```

### 6. Avance a Siguiente Fase
```yaml
Entrada: Fase N completada

Acciones:
  - Marcar fase N como DONE
  - Comenzar fase N+1 desde paso 1

Salida: Loop continúa
```

---

## 📦 FASES DE IMPLEMENTACIÓN

### HITO 0: INFRAESTRUCTURA BASE
**Objetivo del hito**: Sistema corriendo en local + preview + Supabase conectado

---

### Fase 0: Setup Infraestructura ⚙️
**Objetivo**: Tener el proyecto corriendo en local + Vercel preview + Supabase configurado

**Prerequisitos**:
- Cuenta Vercel (nueva o existente)
- Cuenta Supabase (nueva o existente)
- Cuenta Google Cloud (para OAuth)
- Dominio preskit.ar apuntando a Vercel

**Tareas**:
1. Buscar y seleccionar boilerplate Next.js 14 + Supabase
   - Debe incluir: TypeScript, shadcn/ui compatible, OAuth pre-configurado
   - Repositorio: TBD (definir durante fase)
2. Crear proyecto en Vercel
   - URL preview: `preskit-ar-preview.vercel.app` (se genera automático)
   - Conectar con GitHub repo
3. Crear proyecto Supabase
   - Nombre: `preskit-ar-production`
   - Región: South America (São Paulo) o US East
   - Plan: Free tier inicialmente
   - Obtener credenciales:
     * `NEXT_PUBLIC_SUPABASE_URL`
     * `NEXT_PUBLIC_SUPABASE_ANON_KEY`
     * `SUPABASE_SERVICE_ROLE_KEY`
4. Configurar OAuth Google
   - Crear proyecto en Google Cloud Console
   - Configurar OAuth consent screen
   - Crear credenciales OAuth 2.0
   - Obtener:
     * `GOOGLE_CLIENT_ID`
     * `GOOGLE_CLIENT_SECRET`
   - Configurar redirect URLs:
     * `http://localhost:3000/api/auth/callback`
     * `https://preskit-ar-preview.vercel.app/api/auth/callback`
5. Configurar variables de entorno
   - Crear `.env.local` con todas las vars
   - Configurar vars en Vercel dashboard
   - Crear `.env.example` sin valores reales
6. Setup Git
   - Inicializar repo: `git init`
   - Crear branches: `main`, `develop`
   - Primer commit: `chore: initial setup from boilerplate`
7. Configurar dominio (futuro)
   - DNS de preskit.ar → Vercel (se hará en fase posterior)

**Criterios de Aceptación**:
- ✅ `npm install` completa sin errores
- ✅ `npm run dev` inicia servidor en `http://localhost:3000`
- ✅ Preview deploy exitoso en Vercel
- ✅ Supabase client conecta correctamente
- ✅ OAuth Google redirige correctamente (aunque aún no funcione el flow completo)
- ✅ Variables de entorno configuradas en local y Vercel

**Tests/Validación**:
```bash
# Script: scripts/verify-setup.sh
#!/bin/bash

# Verificar variables de entorno críticas
test -n "$NEXT_PUBLIC_SUPABASE_URL" || exit 1
test -n "$SUPABASE_SERVICE_ROLE_KEY" || exit 1
test -n "$GOOGLE_CLIENT_ID" || exit 1

# Verificar build exitoso
npm run build || exit 1

# Verificar Supabase conecta
# (crear script simple que hace ping a Supabase)

echo "✅ Setup validado correctamente"
```

**Dependencias**: Ninguna (fase inicial)

**Salida**:
- Proyecto base funcionando
- Credenciales documentadas en `.env.example`
- README actualizado con instrucciones de setup

---

### HITO 1: DATABASE Y AUTENTICACIÓN COMPLETA
**Objetivo del hito**: Sistema con DB completa, auth funcional (email + Google), y RLS protegiendo datos

---

### Fase 1: Database Schema Base 🗄️
**Objetivo**: Tener todas las tablas, triggers y funciones SQL funcionando

**Prerequisitos**: Fase 0 completada

**Tareas**:
1. Crear estructura de carpeta de migraciones
   ```
   supabase/
   ├── migrations/
   │   ├── 001_schema_inicial.sql
   │   ├── 002_tabla_archivos.sql
   │   ├── 003_historial_alias.sql
   │   ├── 004_storage_usage.sql
   │   └── 005_componentes.sql
   └── seed.sql
   ```

2. Crear migración `001_schema_inicial.sql`
   - Tabla `profiles` con todos los campos del diseño
   - **CORRECCIÓN GAP 1**: Agregar campos `profile_blur_data_url TEXT` y `cover_blur_data_url TEXT` para blur placeholders
   - Índices necesarios
   - Trigger `update_updated_at_column`
   - Función `update_updated_at_column()`

   ```sql
   CREATE TABLE profiles (
     id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
     user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,

     artist_name VARCHAR(50) NOT NULL UNIQUE,
     full_name VARCHAR(100) NOT NULL,
     birth_date DATE,
     bio TEXT,
     tagline VARCHAR(100),

     -- Imágenes
     profile_image_url TEXT,
     cover_image_url TEXT,
     profile_blur_data_url TEXT,  -- GAP 1: Blur placeholder para foto de perfil
     cover_blur_data_url TEXT,    -- GAP 1: Blur placeholder para cover

     genres TEXT[] DEFAULT '{}',
     is_published BOOLEAN DEFAULT FALSE,
     is_available_for_booking BOOLEAN DEFAULT TRUE,
     theme_settings JSONB DEFAULT '{ ... }',
     contact_whatsapp VARCHAR(20),
     contact_email VARCHAR(100),
     contact_button_text VARCHAR(50) DEFAULT 'Contactar',
     alias_changed_at TIMESTAMPTZ,
     created_at TIMESTAMPTZ DEFAULT NOW(),
     updated_at TIMESTAMPTZ DEFAULT NOW(),
     last_published_at TIMESTAMPTZ,

     CONSTRAINT valid_artist_name CHECK (artist_name ~ '^[a-z0-9-_]+$')
   );
   ```

3. Crear migración `002_tabla_archivos.sql`
   - ENUM `file_type`
   - Tabla `files` con metadata completa
   - Constraints de validación (tamaño, duración)
   - Índices

4. Crear migración `003_historial_alias.sql`
   - Tabla `profile_alias_history`
   - Función `validar_cambio_alias()`
   - Trigger `validate_alias_change`

5. Crear migración `004_storage_usage.sql`
   - Tabla `storage_usage`
   - Función `increment_storage_version()`
   - Trigger `storage_version_increment`
   - Función `update_storage_after_file_insert()`
   - Función `update_storage_after_file_delete()`
   - Triggers para actualizar storage automáticamente

6. Crear migración `005_componentes.sql`
   - ENUM `component_type`
   - Tabla `profile_components`
   - Índices

7. Función RPC `verificar_y_reservar_espacio()`
   - Lock pesimista con `FOR UPDATE`
   - Validación de cuotas
   - Return boolean

8. Ejecutar todas las migraciones
   - Ejecutar en Supabase SQL Editor en orden
   - Validar cada migración individualmente

**Criterios de Aceptación**:
- ✅ Todas las tablas existen: `SELECT * FROM information_schema.tables`
- ✅ Todos los triggers funcionan (verificar con INSERT de prueba)
- ✅ RPC `verificar_y_reservar_espacio` ejecuta: `SELECT verificar_y_reservar_espacio(...)`
- ✅ Constraints funcionan (intentar insertar dato inválido debe fallar)

**Tests/Validación**:
```sql
-- Script: supabase/tests/test-schema.sql

-- 1. Verificar tablas existen
SELECT COUNT(*) FROM information_schema.tables
WHERE table_schema = 'public'
AND table_name IN ('profiles', 'files', 'profile_alias_history', 'storage_usage', 'profile_components');
-- Debe retornar 5

-- 2. Test trigger updated_at
INSERT INTO profiles (user_id, artist_name, full_name)
VALUES ('00000000-0000-0000-0000-000000000001', 'test-user', 'Test User');
-- Esperar 1 segundo
UPDATE profiles SET bio = 'Test bio' WHERE artist_name = 'test-user';
-- Verificar que updated_at cambió
SELECT created_at, updated_at FROM profiles WHERE artist_name = 'test-user';
-- updated_at debe ser > created_at

-- 3. Test RPC verificar_y_reservar_espacio
SELECT verificar_y_reservar_espacio(
  '00000000-0000-0000-0000-000000000001'::uuid,
  5242880,
  'audio'
);
-- Debe retornar true o false (sin error)

-- 4. Cleanup
DELETE FROM profiles WHERE artist_name = 'test-user';
```

**Dependencias**: Fase 0

**Salida**:
- Schema SQL completo en Supabase
- Migraciones documentadas
- Tests SQL validados

---

### Fase 2: Row Level Security (RLS) 🔒
**Objetivo**: Todas las políticas RLS funcionando para proteger datos por usuario

**Prerequisitos**: Fase 1 completada

**Tareas**:
1. Crear migración `006_rls_policies.sql`

2. Habilitar RLS en todas las tablas
   ```sql
   ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
   ALTER TABLE files ENABLE ROW LEVEL SECURITY;
   ALTER TABLE profile_components ENABLE ROW LEVEL SECURITY;
   ALTER TABLE storage_usage ENABLE ROW LEVEL SECURITY;
   ALTER TABLE profile_alias_history ENABLE ROW LEVEL SECURITY;
   ```

3. Crear policies para `profiles`
   - `users_view_own_profile`: SELECT WHERE user_id = auth.uid()
   - `users_create_own_profile`: INSERT WITH CHECK user_id = auth.uid()
   - `users_update_own_profile`: UPDATE WHERE user_id = auth.uid()
   - `published_profiles_public`: SELECT WHERE is_published = TRUE

4. Crear policies para `files`
   - `users_view_own_files`: SELECT WHERE user_id = auth.uid()
   - `users_insert_own_files`: INSERT WITH CHECK user_id = auth.uid()
   - `users_delete_own_files`: DELETE WHERE user_id = auth.uid()
   - `public_files_visible`: SELECT WHERE is_private = FALSE AND profile publicado

5. Crear policies para `profile_components`
   - `users_manage_own_components`: ALL usando EXISTS (SELECT FROM profiles)
   - `public_components_visible`: SELECT WHERE profile publicado

6. Crear policies para `storage_usage`
   - `users_view_own_storage`: SELECT WHERE user_id = auth.uid()
   - `users_update_own_storage`: UPDATE WHERE user_id = auth.uid()

7. Crear policies para `profile_alias_history`
   - `users_view_own_alias_history`: SELECT usando EXISTS

8. Ejecutar migración en Supabase

**Criterios de Aceptación**:
- ✅ RLS habilitado en todas las tablas
- ✅ Usuario A no puede ver datos de usuario B (validar con query)
- ✅ Usuario autenticado puede crear su propio perfil
- ✅ Perfiles publicados son visibles sin auth
- ✅ Archivos privados NO son accesibles sin condiciones correctas

**Tests/Validación**:
```sql
-- Script: supabase/tests/test-rls.sql

-- Setup: Crear 2 usuarios de prueba
INSERT INTO auth.users (id, email) VALUES
  ('11111111-1111-1111-1111-111111111111', 'user1@test.com'),
  ('22222222-2222-2222-2222-222222222222', 'user2@test.com');

INSERT INTO profiles (user_id, artist_name, full_name, is_published) VALUES
  ('11111111-1111-1111-1111-111111111111', 'user1', 'User One', true),
  ('22222222-2222-2222-2222-222222222222', 'user2', 'User Two', false);

-- Test 1: Usuario A NO puede ver perfil privado de usuario B
SET LOCAL "request.jwt.claims" = '{"sub": "11111111-1111-1111-1111-111111111111"}';
SELECT * FROM profiles WHERE user_id = '22222222-2222-2222-2222-222222222222';
-- Debe retornar 0 filas

-- Test 2: Usuario A puede ver su propio perfil
SELECT * FROM profiles WHERE user_id = '11111111-1111-1111-1111-111111111111';
-- Debe retornar 1 fila

-- Test 3: Perfil publicado es visible sin auth
RESET "request.jwt.claims";
SELECT * FROM profiles WHERE artist_name = 'user1' AND is_published = true;
-- Debe retornar 1 fila

-- Cleanup
DELETE FROM profiles WHERE user_id IN ('11111111-1111-1111-1111-111111111111', '22222222-2222-2222-2222-222222222222');
DELETE FROM auth.users WHERE id IN ('11111111-1111-1111-1111-111111111111', '22222222-2222-2222-2222-222222222222');
```

**Dependencias**: Fase 1

**Salida**:
- Políticas RLS activas
- Datos protegidos por usuario
- Tests RLS validados

---

### Fase 3: Autenticación Base (Email/Password) 🔐
**Objetivo**: Registro + login + verificación de email funcionando

**Prerequisitos**: Fase 2 completada

**Tareas**:
1. Crear estructura de validaciones
   ```
   lib/
   └── validaciones/
       └── auth.ts  # Schemas Zod para auth
   ```

2. Crear schema Zod `registroSchema`
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

3. Crear helpers de Supabase
   ```
   lib/
   └── supabase/
       ├── client.ts      # Cliente para client components
       ├── server.ts      # Cliente para server components/API
       └── middleware.ts  # Cliente para middleware
   ```

4. Crear API route `/api/auth/registrarse/route.ts` (POST)
   - Validar body con Zod
   - Llamar `supabase.auth.signUp()`
   - Enviar email de verificación
   - Retornar success/error

5. Crear trigger en Supabase que auto-crea `profiles` y `storage_usage`
   ```sql
   -- En migración 007_auth_triggers.sql
   CREATE FUNCTION handle_new_user() RETURNS TRIGGER AS $$
   BEGIN
     INSERT INTO profiles (user_id, artist_name, full_name, birth_date, genres)
     VALUES (NEW.id, NEW.raw_user_meta_data->>'artist_name', ...);

     INSERT INTO storage_usage (user_id, plan, total_limit_mb, audio_files_limit)
     VALUES (NEW.id, 'free', 50, 1);

     RETURN NEW;
   END;
   $$ LANGUAGE plpgsql SECURITY DEFINER;

   CREATE TRIGGER on_auth_user_created
     AFTER INSERT ON auth.users
     FOR EACH ROW EXECUTE FUNCTION handle_new_user();
   ```

6. Crear API route `/api/auth/ingresar/route.ts` (POST)
   - Validar credentials
   - Llamar `supabase.auth.signInWithPassword()`
   - Retornar session

7. Crear API route `/api/auth/salir/route.ts` (POST)
   - Llamar `supabase.auth.signOut()`

8. Crear página `/registrarse/page.tsx`
   - Formulario con React Hook Form + Zod
   - Validación client-side
   - Submit a `/api/auth/registrarse`

9. Crear página `/ingresar/page.tsx`
   - Formulario de login
   - Submit a `/api/auth/ingresar`

10. Crear página `/verificar-email/page.tsx`
    - Mensaje de "verifica tu email"
    - Instrucciones

11. Configurar email templates
    - Instalar `react-email`
    - Crear template de verificación
    - Configurar en Supabase dashboard

12. Crear `middleware.ts`
    - Proteger rutas `/usuario/*`
    - Refresh token automático

**Criterios de Aceptación**:
- ✅ Usuario puede registrarse con email/password
- ✅ Recibe email de verificación (revisar inbox)
- ✅ Puede verificar email y acceder al sistema
- ✅ Puede hacer login exitosamente
- ✅ Puede hacer logout
- ✅ Middleware protege `/usuario/*` (redirige a login si no auth)
- ✅ `profiles` y `storage_usage` se crean automáticamente post-registro

**Tests** (TEST-MAESTRO los define):
```typescript
// app/api/auth/registrarse/route.test.ts
describe('POST /api/auth/registrarse', () => {
  it('retorna 200 con body válido')
  it('retorna 400 con email inválido')
  it('retorna 400 con password débil')
  it('retorna 409 si email ya existe')
  it('crea profile y storage_usage automáticamente')
  it('envía email de verificación')
})

// app/api/auth/ingresar/route.test.ts
describe('POST /api/auth/ingresar', () => {
  it('retorna 200 con credentials válidos')
  it('retorna 401 con password incorrecto')
  it('retorna 401 con email no verificado')
  it('retorna session JWT válido')
})

// middleware.test.ts
describe('Middleware de autenticación', () => {
  it('permite acceso a rutas públicas sin auth')
  it('redirige a /ingresar si intenta acceder a /usuario/* sin auth')
  it('permite acceso a /usuario/* con auth válido')
  it('refresca token automáticamente')
})
```

**Dependencias**: Fase 2

**Salida**:
- Auth funcional con email/password
- Middleware protegiendo rutas privadas
- Trigger auto-creando datos de usuario

---

### Fase 4: Autenticación OAuth (Google) 🔑
**Objetivo**: Login con Google funcionando completamente

**Prerequisitos**: Fase 3 completada

**Tareas**:
1. Configurar Google OAuth en Supabase
   - Dashboard → Authentication → Providers → Google
   - Agregar `GOOGLE_CLIENT_ID` y `GOOGLE_CLIENT_SECRET`
   - Configurar redirect URL: `https://[PROJECT_REF].supabase.co/auth/v1/callback`

2. Crear API route `/api/auth/callback/route.ts` (GET)
   - Manejar callback de OAuth
   - Intercambiar code por session
   - Verificar si usuario ya tiene perfil completo
   - Si no: redirigir a `/completar-perfil`
   - Si sí: redirigir a `/usuario/:username`

3. Agregar botón "Continuar con Google"
   - En `/registrarse/page.tsx`
   - En `/ingresar/page.tsx`
   - Llamar a `supabase.auth.signInWithOAuth({ provider: 'google' })`

4. Crear página `/completar-perfil/page.tsx`
   - Formulario para usuarios OAuth nuevos
   - Campos: artist_name, genres, birth_date
   - Submit guarda en `profiles` (ya existe user_id de auth.users)

5. Actualizar trigger `handle_new_user()`
   - Detectar si viene de OAuth (metadata de Google)
   - Crear `profiles` con data parcial
   - Crear `storage_usage` igual

6. Crear API route `/api/perfil/completar/route.ts` (POST)
   - Recibe artist_name, genres, birth_date
   - Actualiza profile existente
   - Valida que artist_name esté disponible

**Criterios de Aceptación**:
- ✅ Botón "Continuar con Google" redirige a consent screen de Google
- ✅ Usuario puede autenticarse con cuenta Google
- ✅ Post-auth, si usuario nuevo, se redirige a `/completar-perfil`
- ✅ Post-completar perfil, puede acceder al dashboard
- ✅ Usuario OAuth existente va directo a dashboard

**Tests**:
```typescript
// app/api/auth/callback/route.test.ts
describe('GET /api/auth/callback', () => {
  it('maneja callback de Google exitosamente')
  it('redirige a /completar-perfil si usuario nuevo')
  it('redirige a /usuario/:username si perfil completo')
  it('maneja errores de OAuth correctamente')
})

// app/api/perfil/completar/route.test.ts
describe('POST /api/perfil/completar', () => {
  it('completa perfil de usuario OAuth')
  it('valida que artist_name esté disponible')
  it('retorna 409 si artist_name ya existe')
  it('valida genres y birth_date')
})
```

**Dependencias**: Fase 3

**Salida**:
- OAuth Google funcional
- Flow de completar perfil implementado
- Usuarios pueden registrarse con Google

---

### HITO 2: SISTEMA DE ARCHIVOS COMPLETO
**Objetivo del hito**: Upload, storage, validación y streaming de archivos funcionando

---

### Fase 5: Buckets de Storage (Configuración) 🪣
**Objetivo**: Buckets privados configurados con políticas RLS correctas

**Prerequisitos**: Fase 2 completada

**Tareas**:
1. Crear bucket `profile-images` en Supabase
   - Storage → Create bucket
   - Name: `profile-images`
   - Public: `false`
   - File size limit: 5 MB
   - Allowed MIME types: `image/jpeg, image/png, image/webp`

2. Crear bucket `audio-files`
   - Name: `audio-files`
   - Public: `false`
   - File size limit: 15 MB
   - Allowed MIME types: `audio/mpeg, audio/mp3`

3. Crear bucket `pdf-files`
   - Name: `pdf-files`
   - Public: `false`
   - File size limit: 10 MB
   - Allowed MIME types: `application/pdf`

4. Crear políticas RLS para `profile-images`
   ```sql
   -- Solo el dueño puede subir a su carpeta
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

5. Crear políticas RLS para `audio-files` (iguales a profile-images)

6. Crear políticas RLS para `pdf-files` (iguales a profile-images)

**Criterios de Aceptación**:
- ✅ Buckets creados en Supabase dashboard
- ✅ Configuración de límites correcta
- ✅ Políticas RLS activas
- ✅ Usuario A no puede subir archivo a carpeta de usuario B
- ✅ Usuario A no puede leer archivo de carpeta de usuario B

**Tests/Validación**:
```bash
# Script: scripts/test-storage-buckets.sh

# Verificar buckets existen (usando Supabase REST API)
curl -X GET "https://[PROJECT_REF].supabase.co/storage/v1/bucket" \
  -H "Authorization: Bearer $SUPABASE_SERVICE_ROLE_KEY"
# Debe incluir profile-images, audio-files, pdf-files

# Test upload a carpeta propia (debe pasar)
# Test upload a carpeta ajena (debe fallar con 403)
```

**Dependencias**: Fase 2

**Salida**:
- Buckets configurados y protegidos
- Políticas RLS validadas

---

### Fase 6: Sistema de Uploads - Constantes y Validaciones 📏
**Objetivo**: Archivo de constantes y schemas Zod para validación de uploads

**Prerequisitos**: Fase 5 completada

**Tareas**:
1. Crear archivo `lib/validaciones/archivos.ts`
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

   export const iniciarSubidaSchema = z.object({
     type: z.enum(['profile_image', 'cover_image', 'audio', 'pdf']),
     size: z.number().positive(),
     mimeType: z.string(),
     filename: z.string(),
     isPrivate: z.boolean().optional(), // CORRECCIÓN: Consistencia con AudioUploader
   })

   export const confirmarSubidaSchema = z.object({
     fileId: z.string().uuid(),
     bucket: z.string(),
     path: z.string(),
     type: z.enum(['profile_image', 'cover_image', 'audio', 'pdf']),
     originalFilename: z.string(),
     isPrivate: z.boolean().optional(), // CORRECCIÓN GAP 3
   })
   ```

2. Crear helpers para archivos
   ```typescript
   export function obtenerLimitePorTipo(type: string): number {
     switch (type) {
       case 'audio': return LIMITES_ARCHIVOS.MAX_AUDIO_BYTES
       case 'pdf': return LIMITES_ARCHIVOS.MAX_PDF_BYTES
       case 'profile_image':
       case 'cover_image': return LIMITES_ARCHIVOS.MAX_IMAGE_BYTES
       default: throw new Error(`Tipo desconocido: ${type}`)
     }
   }

   export function obtenerBucketPorTipo(type: string): string {
     if (type.includes('image')) return 'profile-images'
     if (type === 'audio') return 'audio-files'
     if (type === 'pdf') return 'pdf-files'
     throw new Error('Tipo inválido')
   }
   ```

**Criterios de Aceptación**:
- ✅ Archivo creado con todas las constantes
- ✅ Schemas Zod exportados correctamente
- ✅ Helpers funcionan correctamente (unit tests)

**Tests**:
```typescript
// lib/validaciones/archivos.test.ts
describe('Validaciones de archivos', () => {
  describe('obtenerLimitePorTipo', () => {
    it('retorna 15MB para audio')
    it('retorna 10MB para pdf')
    it('retorna 5MB para imágenes')
    it('lanza error para tipo inválido')
  })

  describe('obtenerBucketPorTipo', () => {
    it('retorna profile-images para profile_image')
    it('retorna audio-files para audio')
    it('retorna pdf-files para pdf')
    it('lanza error para tipo inválido')
  })

  describe('iniciarSubidaSchema', () => {
    it('valida objeto correcto')
    it('rechaza type inválido')
    it('rechaza size negativo')
  })
})
```

**Dependencias**: Fase 5

**Salida**:
- Constantes centralizadas
- Schemas Zod para validación
- Helpers testeados

---

### Fase 7: Sistema de Uploads - Iniciar Subida 📤
**Objetivo**: Endpoint `/api/archivos/iniciar-subida` funcionando

**Prerequisitos**: Fase 6 completada

**Tareas**:
1. Crear API route `/api/archivos/iniciar-subida/route.ts` (POST)

2. Implementar lógica:
   ```typescript
   export async function POST(request: Request) {
     // 1. Verificar auth
     const { data: { session } } = await supabase.auth.getSession()
     if (!session) return NextResponse.json({ error: 'No autorizado' }, { status: 401 })

     // 2. Validar input con Zod
     const body = await request.json()
     const result = iniciarSubidaSchema.safeParse(body)
     if (!result.success) return NextResponse.json({ error: result.error }, { status: 400 })

     const { type, size, mimeType, filename } = result.data

     // 3. Validar límites por tipo
     const limite = obtenerLimitePorTipo(type)
     if (size > limite) {
       return NextResponse.json({
         error: `Archivo muy grande. Máximo: ${limite / 1024 / 1024} MB`
       }, { status: 400 })
     }

     // 4. Verificar cuota con RPC (lock pesimista)
     const { data: cuotaOk, error: cuotaError } = await supabase.rpc(
       'verificar_y_reservar_espacio',
       { p_user_id: session.user.id, p_size_bytes: size, p_file_type: type }
     )
     if (cuotaError || !cuotaOk) {
       return NextResponse.json({ error: 'Sin espacio disponible' }, { status: 403 })
     }

     // 5. Generar file_id y path
     const fileId = crypto.randomUUID()
     const extension = filename.split('.').pop()
     const bucket = obtenerBucketPorTipo(type)
     const path = `${session.user.id}/${fileId}.${extension}`

     // 6. Crear signed URL (15 min)
     const { data: signedUrl, error } = await supabase.storage
       .from(bucket)
       .createSignedUploadUrl(path, { upsert: false })

     if (error) return NextResponse.json({ error: 'Error generando URL' }, { status: 500 })

     return NextResponse.json({
       uploadUrl: signedUrl.signedUrl,
       fileId,
       bucket,
       path,
       token: signedUrl.token,
     })
   }
   ```

**Criterios de Aceptación**:
- ✅ Endpoint retorna signed URL válida
- ✅ Valida autenticación (401 si no auth)
- ✅ Valida límites de tamaño correctamente
- ✅ Rechaza si usuario alcanzó límite de plan (1 MP3 en free)
- ✅ Rechaza si usuario sin espacio disponible

**Tests**:
```typescript
// app/api/archivos/iniciar-subida/route.test.ts
describe('POST /api/archivos/iniciar-subida', () => {
  it('retorna signed URL con body válido')
  it('rechaza sin autenticación (401)')
  it('rechaza archivo muy grande (> 15MB para audio)')
  it('rechaza si usuario ya tiene 1 MP3 (plan free)')
  it('rechaza si usuario sin espacio disponible')
  it('valida mime type correcto')
  it('genera fileId UUID válido')
  it('path incluye user_id y fileId')
})
```

**Dependencias**: Fase 6

**Salida**:
- Endpoint `/api/archivos/iniciar-subida` funcional
- Validación de cuotas funcionando
- Signed URLs generándose correctamente

---

### Fase 8: Sistema de Uploads - Confirmar Subida ✅
**Objetivo**: Endpoint `/api/archivos/confirmar-subida` con validación de metadata

**Prerequisitos**: Fase 7 completada

**Tareas**:
1. Instalar dependencias
   ```bash
   npm install music-metadata sharp
   ```

2. Crear helper `lib/utils/audio.ts`
   ```typescript
   import { parseBlob } from 'music-metadata'

   export async function obtenerDuracionAudio(blob: Blob): Promise<number> {
     const metadata = await parseBlob(blob)
     return Math.ceil(metadata.format.duration || 0)
   }
   ```

3. Crear helper `lib/utils/imagenes.ts`
   ```typescript
   import sharp from 'sharp'

   export async function generarBlurPlaceholder(blob: Blob): Promise<string> {
     const buffer = Buffer.from(await blob.arrayBuffer())
     const blurBuffer = await sharp(buffer)
       .resize(10, 10, { fit: 'inside' })
       .blur(5)
       .toBuffer()
     const base64 = blurBuffer.toString('base64')
     return `data:image/jpeg;base64,${base64}`
   }
   ```

4. Crear API route `/api/archivos/confirmar-subida/route.ts` (POST)

5. Implementar lógica:
   ```typescript
   export async function POST(request: Request) {
     // 1. Verificar auth
     const { data: { session } } = await supabase.auth.getSession()
     if (!session) return NextResponse.json({ error: 'No autorizado' }, { status: 401 })

     // 2. Validar input
     const body = await request.json()
     const result = confirmarSubidaSchema.safeParse(body)
     if (!result.success) return NextResponse.json({ error: result.error }, { status: 400 })

     // CORRECCIÓN GAP 5: Extraer TODAS las variables de result.data (post-validación)
     const { fileId, bucket, path, type, originalFilename, isPrivate = false } = result.data

     try {
       // 3. Verificar archivo existe en Storage
       const { data: fileInfo, error: headError } = await supabase.storage
         .from(bucket)
         .list(session.user.id, { search: fileId })

       if (headError || !fileInfo || fileInfo.length === 0) {
         throw new Error('Archivo no encontrado')
       }

       const uploadedFile = fileInfo[0]
       const realSize = uploadedFile.metadata?.size || 0

       // 4. SI ES AUDIO: validar duración
       let duracionSegundos: number | null = null
       if (type === 'audio') {
         const { data: audioBlob } = await supabase.storage.from(bucket).download(path)
         if (!audioBlob) throw new Error('Error descargando audio')

         duracionSegundos = await obtenerDuracionAudio(audioBlob)

         if (duracionSegundos > 420) {
           await supabase.storage.from(bucket).remove([path])
           return NextResponse.json({
             error: `Audio muy largo. Máximo: 7 min. Tu archivo: ${Math.ceil(duracionSegundos / 60)} min`
           }, { status: 400 })
         }
       }

       // 5. SI ES IMAGEN: generar blur placeholder
       let blurDataUrl: string | null = null
       if (type === 'profile_image' || type === 'cover_image') {
         const { data: imageBlob } = await supabase.storage.from(bucket).download(path)
         if (imageBlob) {
           blurDataUrl = await generarBlurPlaceholder(imageBlob)
         }
       }

       // 6. INSERT en DB (trigger actualiza storage_usage)
       // CORRECCIÓN GAP 3: Obtener profile_id del usuario
       const { data: profile } = await supabase
         .from('profiles')
         .select('id')
         .eq('user_id', session.user.id)
         .single()

       if (!profile) {
         await supabase.storage.from(bucket).remove([path])
         return NextResponse.json({ error: 'Perfil no encontrado' }, { status: 404 })
       }

       const { data: file, error: insertError } = await supabase
         .from('files')
         .insert({
           id: fileId,
           user_id: session.user.id,
           profile_id: profile.id, // GAP 3: Vincular archivo con perfil
           type,
           bucket,
           path,
           original_filename: originalFilename,
           size_bytes: realSize,
           mime_type: uploadedFile.metadata?.mimetype || 'application/octet-stream',
           duration_seconds: duracionSegundos,
           blur_data_url: blurDataUrl,
           is_private: isPrivate,
         })
         .select()
         .single()

       if (insertError) {
         // Rollback: eliminar de Storage
         await supabase.storage.from(bucket).remove([path])
         throw insertError
       }

       // 7. Generar signed URL para acceso (1 año)
       const { data: publicUrl } = await supabase.storage
         .from(bucket)
         .createSignedUrl(path, 31536000) // 1 año

       return NextResponse.json({
         success: true,
         fileId,
         file,
         publicUrl: publicUrl?.signedUrl,
         blurDataUrl,
       })

     } catch (error: any) {
       console.error('Error confirmando subida:', error)
       // Cleanup
       try {
         await supabase.storage.from(bucket).remove([path])
       } catch (cleanupError) {
         console.error('Error en cleanup:', cleanupError)
       }
       return NextResponse.json({ error: error.message }, { status: 500 })
     }
   }
   ```

**Criterios de Aceptación**:
- ✅ Archivo se persiste correctamente en tabla `files`
- ✅ Duración de audio se valida (rechaza si > 7 min)
- ✅ `storage_usage` se actualiza automáticamente vía trigger
- ✅ Rollback funciona si falla DB insert (archivo se borra de Storage)
- ✅ Blur placeholder se genera para imágenes

**Tests**:
```typescript
// app/api/archivos/confirmar-subida/route.test.ts
describe('POST /api/archivos/confirmar-subida', () => {
  it('confirma subida exitosa con metadata correcta')
  it('rechaza audio > 7 minutos')
  it('actualiza storage_usage automáticamente')
  it('hace rollback si falla DB insert')
  it('genera blur placeholder para imágenes')
  it('rechaza si archivo no existe en Storage')
  it('valida autenticación requerida')
})
```

**Dependencias**: Fase 7

**Salida**:
- Endpoint `/api/archivos/confirmar-subida` funcional
- Validación de duración de audio
- Blur placeholders para imágenes
- Transaccionalidad con rollback

---

### Fase 8bis: Endpoint de Metadata de Archivos 📋
**Objetivo**: Endpoint GET para obtener metadata de archivos (necesario para componentes)

**Prerequisitos**: Fase 8 completada

**CORRECCIÓN GAP 3**: AudioPlayerPublico necesita este endpoint para verificar si archivo es privado.

**Tareas**:
1. Crear API route `/api/archivos/[id]/route.ts` (GET)
   ```typescript
   export async function GET(
     request: Request,
     { params }: { params: { id: string } }
   ) {
     const supabase = createClient()
     const fileId = params.id

     // CORRECCIÓN GAP 4: Obtener archivo con perfil en un solo query (JOIN)
     const { data: file, error } = await supabase
       .from('files')
       .select(`
         id,
         type,
         is_private,
         original_filename,
         size_bytes,
         mime_type,
         duration_seconds,
         blur_data_url,
         profiles!inner(is_published)
       `)
       .eq('id', fileId)
       .single()

     if (error || !file) {
       return NextResponse.json({ error: 'Archivo no encontrado' }, { status: 404 })
     }

     // GAP 4: Verificar si privado y perfil no publicado
     if (file.is_private && !file.profiles.is_published) {
       return NextResponse.json({ error: 'No disponible' }, { status: 403 })
     }

     // Remover profiles del response (solo se usó para validación)
     const { profiles, ...fileData } = file

     return NextResponse.json(fileData)
   }
   ```

**Criterios de Aceptación**:
- ✅ Retorna metadata de archivo público sin auth
- ✅ Retorna metadata de archivo privado si perfil publicado
- ✅ NO retorna contenido del archivo (solo metadata)
- ✅ Retorna 404 si archivo no existe
- ✅ Retorna 403 si archivo privado y perfil no publicado

**Tests**:
```typescript
// app/api/archivos/[id]/route.test.ts
describe('GET /api/archivos/:id', () => {
  it('retorna metadata de archivo público')
  it('retorna metadata de archivo privado si perfil publicado')
  it('retorna 404 si archivo no existe')
  it('retorna 403 si privado y perfil no publicado')
  it('no incluye path ni bucket (seguridad)')
})
```

**Dependencias**: Fase 8

**Salida**:
- Endpoint `/api/archivos/:id` funcional
- Metadata accesible para componentes del frontend

---

### Fase 9: Tokens JWT para Archivos Privados 🔐
**Objetivo**: Sistema de tokens JWT para acceso a archivos privados

**Prerequisitos**: Fase 8 completada

**Tareas**:
1. Instalar dependencias
   ```bash
   npm install jose
   ```

2. Crear `lib/utils/tokens.ts`
   ```typescript
   import { SignJWT, jwtVerify } from 'jose'

   const JWT_SECRET = new TextEncoder().encode(
     process.env.JWT_SECRET || 'your-secret-key-min-32-chars'
   )

   export async function generarTokenAcceso(fileId: string, userId: string): Promise<string> {
     const token = await new SignJWT({ fileId, userId })
       .setProtectedHeader({ alg: 'HS256' })
       .setIssuedAt()
       .setExpirationTime('1h')
       .setJti(crypto.randomUUID())
       .sign(JWT_SECRET)
     return token
   }

   export async function verificarTokenAcceso(token: string) {
     const { payload } = await jwtVerify(token, JWT_SECRET)
     return payload
   }
   ```

3. Instalar Vercel KV para rate limiting persistente
   ```bash
   npm install @vercel/kv
   ```

4. Crear `lib/rate-limit.ts`
   ```typescript
   // CORRECCIÓN RIESGO 1: Usar Vercel KV para persistencia en serverless
   import { kv } from '@vercel/kv'

   export async function rateLimit(
     identifier: string,
     limit: number = 10,
     windowSeconds: number = 60
   ): Promise<{ success: boolean; remaining: number }> {
     const key = `ratelimit:${identifier}`

     try {
       const count = await kv.incr(key)

       // Primera request, setear TTL
       if (count === 1) {
         await kv.expire(key, windowSeconds)
       }

       const remaining = Math.max(0, limit - count)
       const success = count <= limit

       return { success, remaining }
     } catch (error) {
       console.error('Rate limit error:', error)
       // En caso de error, permitir (fail open)
       return { success: true, remaining: limit }
     }
   }
   ```

5. Configurar variables de entorno
   - Vercel KV se configura automáticamente en Vercel dashboard
   - Para desarrollo local: Crear cuenta gratuita en Upstash Redis
   - Agregar a `.env.local`:
     ```
     KV_REST_API_URL=https://your-url.upstash.io
     KV_REST_API_TOKEN=your-token
     ```

6. Crear API route `/api/tokens/acceso-privado/route.ts` (POST)
   ```typescript
   export async function POST(request: Request) {
     const supabase = createClient()
     const ip = request.headers.get('x-forwarded-for') || 'unknown'

     // Rate limiting: 10 tokens/min
     const { success } = rateLimit(ip, 10, 60000)
     if (!success) {
       return NextResponse.json({ error: 'Demasiados requests' }, { status: 429 })
     }

     const { fileId } = await request.json()

     // Verificar archivo existe y es privado
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

     // Verificar perfil publicado
     const { data: profile } = await supabase
       .from('profiles')
       .select('is_published')
       .eq('id', file.profile_id)
       .single()

     if (!profile?.is_published) {
       return NextResponse.json({ error: 'Perfil no publicado' }, { status: 403 })
     }

     // Generar JWT (1 hora)
     const token = await generarTokenAcceso(file.id, file.user_id)

     return NextResponse.json({
       token,
       expiresIn: 3600,
       streamUrl: `/api/media/${fileId}?token=${token}`,
     })
   }
   ```

7. Agregar variable de entorno
   - Generar secret: `openssl rand -base64 32`
   - Agregar `JWT_SECRET=...` a `.env.local` y Vercel

**Criterios de Aceptación**:
- ✅ Token se genera correctamente para archivos privados
- ✅ Token tiene expiración de 1 hora
- ✅ Rate limiting protege endpoint (10 tokens/min)
- ✅ Valida que archivo sea privado
- ✅ Valida que perfil esté publicado

**Tests**:
```typescript
// app/api/tokens/acceso-privado/route.test.ts
describe('POST /api/tokens/acceso-privado', () => {
  it('genera token JWT válido para archivo privado')
  it('rechaza si archivo no existe')
  it('rechaza si archivo es público')
  it('rechaza si perfil no está publicado')
  it('respeta rate limiting (10 tokens/min)')
  it('token expira después de 1 hora')
})
```

**Dependencias**: Fase 8

**Salida**:
- Sistema de tokens JWT funcional
- Rate limiting implementado
- Endpoint `/api/tokens/acceso-privado` listo

---

### Fase 9bis: Uploads y Gestión de Imágenes de Perfil 📸
**Objetivo**: Sistema completo para subir y gestionar foto de perfil y cover image

**Prerequisitos**: Fase 9 completada

**CORRECCIÓN GAP 4**: Esta fase es crítica para el MVP - sin imágenes el perfil no tiene identidad visual.

**Tareas**:
1. Crear componente reutilizable de upload de imágenes
   ```typescript
   // components/editor/ImageUploader.tsx
   'use client'

   import { useState } from 'react'
   import { useDropzone } from 'react-dropzone'
   import Image from 'next/image'
   import { Button } from '@/components/ui/button'

   interface ImageUploaderProps {
     currentImageUrl?: string
     onUploadComplete: (fileId: string, publicUrl: string, blurDataUrl: string) => void // GAP 1: Incluir blur
     type: 'profile_image' | 'cover_image'
     label: string
   }

   export function ImageUploader({ currentImageUrl, onUploadComplete, type, label }: ImageUploaderProps) {
     const [uploading, setUploading] = useState(false)
     const [preview, setPreview] = useState<string | undefined>(currentImageUrl)

     const onDrop = async (acceptedFiles: File[]) => {
       const file = acceptedFiles[0]
       if (!file) return

       setUploading(true)

       try {
         // 1. Iniciar upload
         const initRes = await fetch('/api/archivos/iniciar-subida', {
           method: 'POST',
           headers: { 'Content-Type': 'application/json' },
           body: JSON.stringify({
             type,
             size: file.size,
             mimeType: file.type,
             filename: file.name,
           }),
         })

         const { uploadUrl, fileId, bucket, path } = await initRes.json()

         // 2. Upload directo a Supabase
         await fetch(uploadUrl, {
           method: 'PUT',
           body: file,
           headers: { 'Content-Type': file.type },
         })

         // 3. Confirmar upload
         const confirmRes = await fetch('/api/archivos/confirmar-subida', {
           method: 'POST',
           headers: { 'Content-Type': 'application/json' },
           body: JSON.stringify({
             fileId,
             bucket,
             path,
             type,
             originalFilename: file.name,
           }),
         })

         const { publicUrl, blurDataUrl } = await confirmRes.json()

         // 4. Preview local
         setPreview(publicUrl)

         // 5. Callback para actualizar perfil (GAP 1: pasar blurDataUrl)
         onUploadComplete(fileId, publicUrl, blurDataUrl)

       } catch (error) {
         console.error('Error subiendo imagen:', error)
         alert('Error al subir imagen')
       } finally {
         setUploading(false)
       }
     }

     const { getRootProps, getInputProps, isDragActive } = useDropzone({
       onDrop,
       accept: { 'image/*': ['.jpeg', '.jpg', '.png', '.webp'] },
       maxSize: 5 * 1024 * 1024, // 5 MB
       multiple: false,
     })

     return (
       <div className="space-y-2">
         <label className="text-sm font-medium">{label}</label>

         {preview ? (
           <div className="relative">
             <Image
               src={preview}
               alt="Preview"
               width={type === 'profile_image' ? 200 : 400}
               height={type === 'profile_image' ? 200 : 200}
               className="rounded-lg object-cover"
             />
             <Button
               variant="outline"
               size="sm"
               onClick={() => setPreview(undefined)}
               className="mt-2"
             >
               Cambiar imagen
             </Button>
           </div>
         ) : (
           <div
             {...getRootProps()}
             className={`
               border-2 border-dashed rounded-lg p-8 text-center cursor-pointer
               transition-colors
               ${isDragActive ? 'border-blue-500 bg-blue-50' : 'border-gray-300 hover:border-gray-400'}
             `}
           >
             <input {...getInputProps()} />
             {uploading ? (
               <p>Subiendo...</p>
             ) : isDragActive ? (
               <p>Soltá la imagen acá</p>
             ) : (
               <div>
                 <p>Arrastrá una imagen o hacé click para seleccionar</p>
                 <p className="text-sm text-gray-500 mt-2">
                   JPG, PNG o WEBP • Máximo 5 MB
                 </p>
               </div>
             )}
           </div>
         )}
       </div>
     )
   }
   ```

2. Crear endpoint para actualizar URLs de imágenes en perfil
   ```typescript
   // Ya existe en /api/perfil PATCH, pero asegurar que acepta:
   // - profile_image_url
   // - cover_image_url
   // Esto ya fue corregido en Gap 2
   ```

3. Integrar ImageUploader en el editor
   ```typescript
   // components/editor/PanelCustomizacion.tsx
   // Agregar sección de imágenes:

   <div className="space-y-4 mb-6">
     <h3 className="font-semibold">Imágenes</h3>

     <ImageUploader
       currentImageUrl={profile.profile_image_url}
       onUploadComplete={async (fileId, publicUrl, blurDataUrl) => {
         // GAP 1: Actualizar perfil con URL + blur placeholder
         await fetch('/api/perfil', {
           method: 'PATCH',
           headers: { 'Content-Type': 'application/json' },
           body: JSON.stringify({
             profile_image_url: publicUrl,
             profile_blur_data_url: blurDataUrl
           }),
         })
       }}
       type="profile_image"
       label="Foto de perfil"
     />

     <ImageUploader
       currentImageUrl={profile.cover_image_url}
       onUploadComplete={async (fileId, publicUrl, blurDataUrl) => {
         // GAP 1: Actualizar perfil con URL + blur placeholder
         await fetch('/api/perfil', {
           method: 'PATCH',
           headers: { 'Content-Type': 'application/json' },
           body: JSON.stringify({
             cover_image_url: publicUrl,
             cover_blur_data_url: blurDataUrl
           }),
         })
       }}
       type="cover_image"
       label="Imagen de portada"
     />
   </div>
   ```

4. Mostrar imágenes en PerfilPublicoView
   ```typescript
   // components/perfil/PerfilPublicoView.tsx
   import Image from 'next/image'

   export function PerfilPublicoView({ theme, components, profile }: Props) {
     return (
       <div className="p-6 space-y-4">
         {/* Cover image como fondo opcional */}
         {profile.cover_image_url && (
           <div className="relative w-full h-48 -mx-6 -mt-6 mb-6">
             <Image
               src={profile.cover_image_url}
               alt="Cover"
               fill
               className="object-cover"
               placeholder="blur"
               blurDataURL={profile.cover_blur_data_url}
             />
           </div>
         )}

         {/* Foto de perfil */}
         {profile.profile_image_url && (
           <div className="flex justify-center mb-4">
             <Image
               src={profile.profile_image_url}
               alt={profile.artist_name}
               width={120}
               height={120}
               className="rounded-full object-cover"
               placeholder="blur"
               blurDataURL={profile.profile_blur_data_url}
             />
           </div>
         )}

         {/* Nombre artístico */}
         <h1 className="text-3xl font-bold text-center">{profile.artist_name}</h1>

         {/* Resto de componentes */}
         {components.map(component => (
           <ComponentRenderer key={component.id} component={component} theme={theme} />
         ))}
       </div>
     )
   }
   ```

5. Instalar dependencia react-dropzone
   ```bash
   npm install react-dropzone
   ```

**Criterios de Aceptación**:
- ✅ Usuario puede subir foto de perfil desde editor
- ✅ Usuario puede subir cover image desde editor
- ✅ Preview se muestra inmediatamente después de upload
- ✅ URLs se guardan en `profiles.profile_image_url` y `cover_image_url`
- ✅ Imágenes se muestran en perfil público con blur placeholder
- ✅ Validación de tamaño (5 MB) y formato funciona

**Tests**:
```typescript
// components/editor/ImageUploader.test.tsx
describe('ImageUploader', () => {
  it('muestra zona de drop si no hay imagen')
  it('muestra preview si hay imagen actual')
  it('inicia upload al soltar archivo')
  it('valida tamaño máximo 5MB')
  it('valida formato (jpg, png, webp)')
  it('llama a callback onUploadComplete con URLs')
  it('muestra loading durante upload')
})

// components/perfil/PerfilPublicoView.test.tsx
describe('PerfilPublicoView con imágenes', () => {
  it('renderiza foto de perfil si existe')
  it('renderiza cover image si existe')
  it('usa blur placeholder')
  it('no crashea si faltan imágenes')
})
```

**Dependencias**: Fase 9

**Salida**:
- Sistema de upload de imágenes funcional
- Imágenes visibles en editor y perfil público
- Blur placeholders implementados

---

### Fase 10: Streaming de Archivos con Validación JWT 🎵
**Objetivo**: Endpoint de streaming con soporte para Range requests y validación JWT

**Prerequisitos**: Fase 9bis completada

**Tareas**:
1. Crear API route `/api/media/[id]/route.ts` (GET)

2. Implementar lógica:
   ```typescript
   export async function GET(
     request: Request,
     { params }: { params: { id: string } }
   ) {
     const supabase = createClient()
     const { searchParams } = new URL(request.url)
     const token = searchParams.get('token')
     const fileId = params.id

     // 1. Obtener info del archivo
     const { data: file, error: fileError } = await supabase
       .from('files')
       .select('*')
       .eq('id', fileId)
       .single()

     if (fileError || !file) {
       return NextResponse.json({ error: 'Archivo no encontrado' }, { status: 404 })
     }

     // 2. Si es privado, validar token
     if (file.is_private) {
       if (!token) {
         return NextResponse.json({ error: 'Token requerido' }, { status: 401 })
       }

       try {
         const payload = await verificarTokenAcceso(token)
         if (payload.fileId !== fileId) {
           return NextResponse.json({ error: 'Token inválido' }, { status: 403 })
         }
       } catch (error) {
         return NextResponse.json({ error: 'Token expirado o inválido' }, { status: 401 })
       }
     }

     // 3. Si es público, verificar perfil publicado
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

     // 4. CORRECCIÓN RIESGO 2: Usar signed URL y delegar streaming al CDN
     // En vez de descargar en memoria, generar signed URL y redirigir
     const expiresIn = file.is_private ? 3600 : 86400 // 1 hora privado, 24hs público

     const { data: signedUrlData, error: signedError } = await supabase.storage
       .from(file.bucket)
       .createSignedUrl(file.path, expiresIn)

     if (signedError || !signedUrlData) {
       return NextResponse.json({ error: 'Error generando URL' }, { status: 500 })
     }

     // Redirigir a signed URL - Supabase CDN maneja Range requests automáticamente
     return NextResponse.redirect(signedUrlData.signedUrl, 302)
   }
   ```

**Criterios de Aceptación**:
- ✅ Redirige a signed URL con token válido (archivos privados)
- ✅ Redirige a signed URL sin token (archivos públicos)
- ✅ Falla con token inválido/expirado
- ✅ Supabase CDN maneja Range requests automáticamente
- ✅ Expiración correcta: 1h privados, 24h públicos

**Tests**:
```typescript
// app/api/media/[id]/route.test.ts
describe('GET /api/media/:id', () => {
  it('redirige a signed URL con token válido')
  it('rechaza con token expirado')
  it('rechaza sin token si archivo es privado')
  it('redirige sin token si archivo es público')
  it('signed URL tiene expiración correcta')
  it('rechaza si perfil no está publicado')
})
```

**Dependencias**: Fase 9

**Salida**:
- Endpoint `/api/media/:id` funcional
- Streaming con Range requests
- Validación JWT implementada

---

### HITO 3: API DE PERFILES Y COMPONENTES
**Objetivo del hito**: CRUD completo de perfiles y componentes, validación de alias, sistema de publicación

---

### Fase 11: API de Perfiles - CRUD Base 👤
**Objetivo**: Endpoints para crear, leer, actualizar perfiles

**Prerequisitos**: Fase 4 completada

**Tareas**:
1. Crear `lib/validaciones/perfil.ts`
   ```typescript
   const themeSettingsSchema = z.object({
     backgroundColor: z.string(),
     textColor: z.string(),
     accentColor: z.string(),
     buttonStyle: z.object({
       backgroundColor: z.string(),
       textColor: z.string(),
       borderColor: z.string(),
       borderWidth: z.number(),
       hasShadow: z.boolean(),
       shadowColor: z.string(),
     }),
     fontFamily: z.string(),
     isDarkMode: z.boolean(),
   })

   export const perfilSchema = z.object({
     artist_name: z.string().min(3).max(50).regex(/^[a-z0-9-_]+$/),
     full_name: z.string().min(2).max(100),
     bio: z.string().max(500).optional(),
     tagline: z.string().max(100).optional(),
     genres: z.array(z.string()).min(1).max(4),
     contact_whatsapp: z.string().optional(),
     contact_email: z.string().email().optional(),
     contact_button_text: z.string().max(50).optional(),
     is_available_for_booking: z.boolean().optional(),
   })

   // CORRECCIÓN GAP 2: Incluir theme_settings e imágenes
   export const updatePerfilSchema = perfilSchema.partial().omit({ artist_name: true }).extend({
     theme_settings: themeSettingsSchema.optional(),
     profile_image_url: z.string().url().optional(),
     cover_image_url: z.string().url().optional(),
     profile_blur_data_url: z.string().optional(), // GAP 1: Blur placeholder
     cover_blur_data_url: z.string().optional(),   // GAP 1: Blur placeholder
   })
   ```

2. Crear API route `/api/perfil/route.ts`

3. Implementar GET
   ```typescript
   export async function GET(request: Request) {
     const supabase = createClient()
     const { data: { session } } = await supabase.auth.getSession()
     if (!session) return NextResponse.json({ error: 'No autorizado' }, { status: 401 })

     const { data: profile, error } = await supabase
       .from('profiles')
       .select('*')
       .eq('user_id', session.user.id)
       .single()

     if (error) return NextResponse.json({ error: error.message }, { status: 500 })

     return NextResponse.json(profile)
   }
   ```

4. Implementar PATCH
   ```typescript
   export async function PATCH(request: Request) {
     const supabase = createClient()
     const { data: { session } } = await supabase.auth.getSession()
     if (!session) return NextResponse.json({ error: 'No autorizado' }, { status: 401 })

     const body = await request.json()
     const result = updatePerfilSchema.safeParse(body)
     if (!result.success) {
       return NextResponse.json({ error: result.error }, { status: 400 })
     }

     const { data: profile, error } = await supabase
       .from('profiles')
       .update(result.data)
       .eq('user_id', session.user.id)
       .select()
       .single()

     if (error) return NextResponse.json({ error: error.message }, { status: 500 })

     return NextResponse.json(profile)
   }
   ```

**Criterios de Aceptación**:
- ✅ Usuario puede obtener su perfil (GET)
- ✅ Usuario puede actualizar su perfil (PATCH)
- ✅ Validación Zod funciona correctamente
- ✅ RLS impide modificar perfil ajeno
- ✅ Retorna 401 si no autenticado

**Tests**:
```typescript
// app/api/perfil/route.test.ts
describe('GET /api/perfil', () => {
  it('retorna perfil del usuario autenticado')
  it('retorna 401 si no autenticado')
})

describe('PATCH /api/perfil', () => {
  it('actualiza perfil exitosamente')
  it('valida datos con Zod')
  it('retorna 400 con datos inválidos')
  it('retorna 401 si no autenticado')
  it('no permite cambiar artist_name (omitido del schema)')
})
```

**Dependencias**: Fase 4

**Salida**:
- Endpoint `/api/perfil` funcional (GET, PATCH)
- Validación de datos con Zod

---

### Fase 12: API de Perfiles - Publicar/Despublicar 📢
**Objetivo**: Toggle para publicar/despublicar perfil con invalidación de cache

**Prerequisitos**: Fase 11 completada

**Tareas**:
1. Crear API route `/api/perfil/publicar/route.ts` (POST)

2. Implementar lógica:
   ```typescript
   import { revalidatePath } from 'next/cache'

   export async function POST(request: Request) {
     const supabase = createClient()
     const { data: { session } } = await supabase.auth.getSession()
     if (!session) return NextResponse.json({ error: 'No autorizado' }, { status: 401 })

     const { isPublished } = await request.json()

     // Obtener artist_name para invalidar cache
     const { data: profile } = await supabase
       .from('profiles')
       .select('artist_name')
       .eq('user_id', session.user.id)
       .single()

     if (!profile) {
       return NextResponse.json({ error: 'Perfil no encontrado' }, { status: 404 })
     }

     // Actualizar estado publicado
     const updateData: any = { is_published: isPublished }
     if (isPublished) {
       updateData.last_published_at = new Date().toISOString()
     }

     const { data: updated, error } = await supabase
       .from('profiles')
       .update(updateData)
       .eq('user_id', session.user.id)
       .select()
       .single()

     if (error) return NextResponse.json({ error: error.message }, { status: 500 })

     // Invalidar cache del perfil público
     revalidatePath(`/${profile.artist_name}`)

     return NextResponse.json({ success: true, profile: updated })
   }
   ```

**Criterios de Aceptación**:
- ✅ Usuario puede publicar su perfil
- ✅ Usuario puede despublicar su perfil
- ✅ `last_published_at` se actualiza al publicar
- ✅ Cache de ISR se invalida con `revalidatePath()`

**Tests**:
```typescript
// app/api/perfil/publicar/route.test.ts
describe('POST /api/perfil/publicar', () => {
  it('publica perfil exitosamente')
  it('despublica perfil exitosamente')
  it('actualiza last_published_at al publicar')
  it('no actualiza last_published_at al despublicar')
  it('invalida cache de ISR')
  it('retorna 401 si no autenticado')
})
```

**Dependencias**: Fase 11

**Salida**:
- Endpoint `/api/perfil/publicar` funcional
- Cache invalidation funcionando

---

### Fase 13: API de Perfiles - Validación de Alias 🔍
**Objetivo**: Endpoint para validar disponibilidad de alias en tiempo real

**Prerequisitos**: Fase 11 completada

**Tareas**:
1. Crear API route `/api/perfil/alias-disponible/route.ts` (GET)

2. Implementar rate limiting específico
   ```typescript
   export async function GET(request: Request) {
     const ip = request.headers.get('x-forwarded-for') || 'unknown'
     const { success } = rateLimit(ip, 20, 60000) // 20 requests/min
     if (!success) {
       return NextResponse.json({ error: 'Demasiados requests' }, { status: 429 })
     }

     const { searchParams } = new URL(request.url)
     const alias = searchParams.get('alias')

     if (!alias) {
       return NextResponse.json({ error: 'Alias requerido' }, { status: 400 })
     }

     // Validar formato
     if (!/^[a-z0-9-_]+$/.test(alias)) {
       return NextResponse.json({
         disponible: false,
         error: 'Formato inválido. Solo minúsculas, números, guiones y guiones bajos.'
       })
     }

     // Verificar disponibilidad (case-insensitive)
     const supabase = createClient()
     const { data, error } = await supabase
       .from('profiles')
       .select('artist_name')
       .ilike('artist_name', alias)
       .maybeSingle()

     if (error) {
       return NextResponse.json({ error: error.message }, { status: 500 })
     }

     const disponible = !data

     // Si no disponible, generar sugerencias
     const sugerencias = disponible ? undefined : [
       `${alias}-dj`,
       `${alias}-ar`,
       `${alias}-music`,
       `dj-${alias}`,
     ]

     return NextResponse.json({ disponible, sugerencias })
   }
   ```

**Criterios de Aceptación**:
- ✅ Retorna `{ disponible: true }` si alias libre
- ✅ Retorna `{ disponible: false, sugerencias: [...] }` si tomado
- ✅ Validación case-insensitive
- ✅ Rate limiting (20 requests/min)
- ✅ Valida formato del alias

**Tests**:
```typescript
// app/api/perfil/alias-disponible/route.test.ts
describe('GET /api/perfil/alias-disponible', () => {
  it('retorna disponible: true si alias libre')
  it('retorna disponible: false si alias tomado')
  it('es case-insensitive (TEST = test)')
  it('respeta rate limiting (20/min)')
  it('valida formato correcto')
  it('retorna sugerencias si no disponible')
  it('retorna 400 si falta parámetro alias')
})
```

**Dependencias**: Fase 11

**Salida**:
- Endpoint `/api/perfil/alias-disponible` funcional
- Validación en tiempo real lista

---

### Fase 14: API de Componentes - CRUD 🧩
**Objetivo**: Endpoints para gestionar componentes del perfil

**Prerequisitos**: Fase 11 completada

**Tareas**:
1. Crear schemas en `lib/validaciones/perfil.ts`
   ```typescript
   const componenteBaseSchema = z.object({
     type: z.enum(['button', 'text_block', 'audio_player', 'video_embed', 'pdf_download']),
     position: z.number().int().min(0),
     is_visible: z.boolean().optional(),
   })

   const buttonDataSchema = z.object({
     text: z.string().min(1).max(50),
     url: z.string().url(),
     icon: z.string().optional(),
   })

   const textBlockDataSchema = z.object({
     content: z.string().max(2000),
     format: z.enum(['html', 'markdown']).optional(),
   })

   const audioPlayerDataSchema = z.object({
     title: z.string().max(100),
     file_id: z.string().uuid(),
   })

   const videoEmbedDataSchema = z.object({
     url: z.string().url(),
     provider: z.enum(['youtube', 'vimeo']),
   })

   const pdfDownloadDataSchema = z.object({
     filename: z.string().max(100),
     file_id: z.string().uuid(),
   })

   export const componenteSchema = componenteBaseSchema.extend({
     data: z.union([
       buttonDataSchema,
       textBlockDataSchema,
       audioPlayerDataSchema,
       videoEmbedDataSchema,
       pdfDownloadDataSchema,
     ]),
   })
   ```

2. Crear API route `/api/componentes/route.ts`

3. Implementar GET (listar componentes)
   ```typescript
   export async function GET(request: Request) {
     const supabase = createClient()
     const { data: { session } } = await supabase.auth.getSession()
     if (!session) return NextResponse.json({ error: 'No autorizado' }, { status: 401 })

     // Obtener profile_id del usuario
     const { data: profile } = await supabase
       .from('profiles')
       .select('id')
       .eq('user_id', session.user.id)
       .single()

     if (!profile) return NextResponse.json({ error: 'Perfil no encontrado' }, { status: 404 })

     // Obtener componentes ordenados
     const { data: components, error } = await supabase
       .from('profile_components')
       .select('*')
       .eq('profile_id', profile.id)
       .order('position', { ascending: true })

     if (error) return NextResponse.json({ error: error.message }, { status: 500 })

     return NextResponse.json(components)
   }
   ```

4. Implementar POST (crear componente)
   ```typescript
   export async function POST(request: Request) {
     const supabase = createClient()
     const { data: { session } } = await supabase.auth.getSession()
     if (!session) return NextResponse.json({ error: 'No autorizado' }, { status: 401 })

     const body = await request.json()
     const result = componenteSchema.safeParse(body)
     if (!result.success) {
       return NextResponse.json({ error: result.error }, { status: 400 })
     }

     // Obtener profile_id
     const { data: profile } = await supabase
       .from('profiles')
       .select('id')
       .eq('user_id', session.user.id)
       .single()

     if (!profile) return NextResponse.json({ error: 'Perfil no encontrado' }, { status: 404 })

     // Insertar componente
     const { data: component, error } = await supabase
       .from('profile_components')
       .insert({
         profile_id: profile.id,
         type: result.data.type,
         position: result.data.position,
         is_visible: result.data.is_visible ?? true,
         data: result.data.data,
       })
       .select()
       .single()

     if (error) return NextResponse.json({ error: error.message }, { status: 500 })

     return NextResponse.json(component)
   }
   ```

5. Implementar PATCH (actualizar componente)
   ```typescript
   export async function PATCH(request: Request) {
     const supabase = createClient()
     const { data: { session } } = await supabase.auth.getSession()
     if (!session) return NextResponse.json({ error: 'No autorizado' }, { status: 401 })

     const { id, ...updates } = await request.json()

     // Validar que el componente pertenece al usuario (RLS)
     const { data: component, error } = await supabase
       .from('profile_components')
       .update(updates)
       .eq('id', id)
       .select()
       .single()

     if (error) return NextResponse.json({ error: error.message }, { status: 500 })

     return NextResponse.json(component)
   }
   ```

6. Implementar DELETE (eliminar componente)
   ```typescript
   export async function DELETE(request: Request) {
     const supabase = createClient()
     const { data: { session } } = await supabase.auth.getSession()
     if (!session) return NextResponse.json({ error: 'No autorizado' }, { status: 401 })

     const { searchParams } = new URL(request.url)
     const id = searchParams.get('id')

     if (!id) return NextResponse.json({ error: 'ID requerido' }, { status: 400 })

     // RLS valida que pertenece al usuario
     const { error } = await supabase
       .from('profile_components')
       .delete()
       .eq('id', id)

     if (error) return NextResponse.json({ error: error.message }, { status: 500 })

     return NextResponse.json({ success: true })
   }
   ```

**Criterios de Aceptación**:
- ✅ Usuario puede listar sus componentes
- ✅ Usuario puede crear nuevo componente
- ✅ Usuario puede actualizar componente existente
- ✅ Usuario puede eliminar componente
- ✅ Validación Zod por tipo de componente
- ✅ RLS impide manipular componentes ajenos

**Tests**:
```typescript
// app/api/componentes/route.test.ts
describe('API /api/componentes', () => {
  describe('GET', () => {
    it('retorna lista de componentes del perfil')
    it('retorna componentes ordenados por position')
    it('retorna 401 si no autenticado')
  })

  describe('POST', () => {
    it('crea nuevo componente de tipo button')
    it('crea nuevo componente de tipo text_block')
    it('crea nuevo componente de tipo audio_player')
    it('valida datos con Zod según type')
    it('retorna 400 con datos inválidos')
  })

  describe('PATCH', () => {
    it('actualiza componente exitosamente')
    it('no permite actualizar componente ajeno (RLS)')
  })

  describe('DELETE', () => {
    it('elimina componente exitosamente')
    it('no permite eliminar componente ajeno (RLS)')
  })
})
```

**Dependencias**: Fase 11

**Salida**:
- Endpoint `/api/componentes` funcional (GET, POST, PATCH, DELETE)
- Validación por tipo de componente

---

### Fase 15: API de Componentes - Reordenar 🔄
**Objetivo**: Endpoint para reordenar componentes (drag & drop)

**Prerequisitos**: Fase 14 completada

**Tareas**:
1. Crear API route `/api/componentes/reordenar/route.ts` (POST)

2. Implementar lógica:
   ```typescript
   export async function POST(request: Request) {
     const supabase = createClient()
     const { data: { session } } = await supabase.auth.getSession()
     if (!session) return NextResponse.json({ error: 'No autorizado' }, { status: 401 })

     const { components } = await request.json()
     // components = [{ id: 'uuid', position: 0 }, { id: 'uuid', position: 1 }, ...]

     if (!Array.isArray(components)) {
       return NextResponse.json({ error: 'Array requerido' }, { status: 400 })
     }

     // Validar que todos los componentes pertenecen al usuario
     const componentIds = components.map(c => c.id)
     const { data: profile } = await supabase
       .from('profiles')
       .select('id')
       .eq('user_id', session.user.id)
       .single()

     if (!profile) return NextResponse.json({ error: 'Perfil no encontrado' }, { status: 404 })

     const { data: owned, error: ownedError } = await supabase
       .from('profile_components')
       .select('id')
       .eq('profile_id', profile.id)
       .in('id', componentIds)

     if (ownedError || owned.length !== componentIds.length) {
       return NextResponse.json({ error: 'Componentes inválidos' }, { status: 403 })
     }

     // Actualizar positions en batch
     const updates = components.map(c =>
       supabase
         .from('profile_components')
         .update({ position: c.position })
         .eq('id', c.id)
     )

     const results = await Promise.all(updates)
     const hasError = results.some(r => r.error)

     if (hasError) {
       return NextResponse.json({ error: 'Error actualizando positions' }, { status: 500 })
     }

     return NextResponse.json({ success: true })
   }
   ```

**Criterios de Aceptación**:
- ✅ Actualiza positions en batch correctamente
- ✅ Valida que todos los componentes pertenecen al usuario
- ✅ Transacción atómica (todo o nada)

**Tests**:
```typescript
// app/api/componentes/reordenar/route.test.ts
describe('POST /api/componentes/reordenar', () => {
  it('actualiza positions en batch correctamente')
  it('valida que todos los componentes pertenecen al usuario')
  it('retorna 403 si algún componente no pertenece al usuario')
  it('retorna 400 si no es array')
  it('retorna 401 si no autenticado')
})
```

**Dependencias**: Fase 14

**Salida**:
- Endpoint `/api/componentes/reordenar` funcional
- Reordenamiento en batch implementado

---

### HITO 4: FRONTEND - EDITOR Y PERFIL PÚBLICO
**Objetivo del hito**: Editor visual funcionando + perfiles públicos renderizando

---

### Fase 16: Store Zustand + Estado del Editor 🎨
**Objetivo**: State management centralizado para el editor

**Prerequisitos**: Fase 15 completada

**Tareas**:
1. Instalar dependencias
   ```bash
   npm install zustand immer
   ```

2. Crear `store/editor-store.ts`
   ```typescript
   import { create } from 'zustand'
   import { immer } from 'zustand/middleware/immer'

   interface ThemeSettings {
     backgroundColor: string
     textColor: string
     accentColor: string
     buttonStyle: {
       backgroundColor: string
       textColor: string
       borderColor: string
       borderWidth: number
       hasShadow: boolean
       shadowColor: string
     }
     fontFamily: string
     isDarkMode: boolean
   }

   interface ProfileComponent {
     id: string
     type: 'button' | 'text_block' | 'audio_player' | 'video_embed' | 'pdf_download'
     position: number
     is_visible: boolean
     data: any
   }

   interface EditorState {
     profile: any // GAP 2: Agregar perfil completo al store
     theme: ThemeSettings
     components: ProfileComponent[]
     isPreviewMode: boolean
     isSaving: boolean

     setProfile: (profile: any) => void // GAP 2
     setTheme: (theme: ThemeSettings) => void
     addComponent: (component: ProfileComponent) => void
     updateComponent: (id: string, data: any) => void
     deleteComponent: (id: string) => void
     reorderComponents: (newOrder: ProfileComponent[]) => void
     togglePreviewMode: () => void
     setSaving: (saving: boolean) => void
     loadProfile: (profile: any, components: ProfileComponent[]) => void
   }

   export const useEditorStore = create<EditorState>()(
     immer((set) => ({
       profile: null, // GAP 2: Estado inicial del perfil
       theme: {
         backgroundColor: '#ffffff',
         textColor: '#000000',
         accentColor: '#000000',
         buttonStyle: {
           backgroundColor: '#000000',
           textColor: '#ffffff',
           borderColor: '#000000',
           borderWidth: 1,
           hasShadow: false,
           shadowColor: '#000000',
         },
         fontFamily: 'Inter',
         isDarkMode: false,
       },
       components: [],
       isPreviewMode: false,
       isSaving: false,

       setProfile: (profile) => set((state) => { // GAP 2
         state.profile = profile
       }),

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

       loadProfile: (profile, components) => set((state) => {
         state.profile = profile // GAP 2: Cargar perfil completo
         state.theme = profile.theme_settings
         state.components = components
       }),
     }))
   )
   ```

**Criterios de Aceptación**:
- ✅ Store creado con Zustand + Immer
- ✅ Acciones definidas para todas las operaciones
- ✅ Theme settings incluidos
- ✅ Components array manejado correctamente

**Tests**:
```typescript
// store/editor-store.test.ts
describe('Editor Store', () => {
  it('inicializa con estado default')
  it('setTheme actualiza theme correctamente')
  it('addComponent agrega componente')
  it('updateComponent actualiza data de componente')
  it('deleteComponent elimina componente')
  it('reorderComponents reordena array')
  it('loadProfile carga theme y components')
})
```

**Dependencias**: Fase 15

**Salida**:
- Store Zustand configurado
- State management centralizado

---

### Fase 17: Editor - Canvas Drag & Drop 🎯
**Objetivo**: Canvas de editor con soporte drag & drop para reordenar componentes

**Prerequisitos**: Fase 16 completada

**Tareas**:
1. Instalar dependencias
   ```bash
   npm install @dnd-kit/core @dnd-kit/sortable @dnd-kit/utilities
   ```

2. Crear `components/editor/Canvas.tsx`
   ```typescript
   'use client'

   import { DndContext, closestCenter, DragEndEvent } from '@dnd-kit/core'
   import { SortableContext, verticalListSortingStrategy } from '@dnd-kit/sortable'
   import { useEditorStore } from '@/store/editor-store'
   import { ComponentRenderer } from './ComponentRenderer'

   export function Canvas() {
     const { components, reorderComponents } = useEditorStore()

     function handleDragEnd(event: DragEndEvent) {
       const { active, over } = event
       if (!over || active.id === over.id) return

       const oldIndex = components.findIndex(c => c.id === active.id)
       const newIndex = components.findIndex(c => c.id === over.id)

       const newOrder = [...components]
       const [moved] = newOrder.splice(oldIndex, 1)
       newOrder.splice(newIndex, 0, moved)

       // Actualizar positions
       const updated = newOrder.map((c, idx) => ({ ...c, position: idx }))
       reorderComponents(updated)
     }

     return (
       <DndContext collisionDetection={closestCenter} onDragEnd={handleDragEnd}>
         <SortableContext items={components.map(c => c.id)} strategy={verticalListSortingStrategy}>
           <div className="space-y-2 p-4">
             {components.length === 0 ? (
               <div className="text-center text-gray-500 py-12">
                 Comenzá agregando el primer elemento
               </div>
             ) : (
               components.map(component => (
                 <ComponentRenderer key={component.id} component={component} />
               ))
             )}
           </div>
         </SortableContext>
       </DndContext>
     )
   }
   ```

3. Crear `components/editor/ComponentRenderer.tsx`
   ```typescript
   'use client'

   import { useSortable } from '@dnd-kit/sortable'
   import { CSS } from '@dnd-kit/utilities'
   import { ButtonComponent } from './components/ButtonComponent'
   import { TextBlockComponent } from './components/TextBlockComponent'
   // ... otros componentes

   export function ComponentRenderer({ component }: { component: any }) {
     const { attributes, listeners, setNodeRef, transform, transition } = useSortable({
       id: component.id,
     })

     const style = {
       transform: CSS.Transform.toString(transform),
       transition,
     }

     return (
       <div
         ref={setNodeRef}
         style={style}
         {...attributes}
         {...listeners}
         className="bg-white border rounded-lg p-4 cursor-move hover:border-blue-500"
       >
         {component.type === 'button' && <ButtonComponent data={component.data} />}
         {component.type === 'text_block' && <TextBlockComponent data={component.data} />}
         {/* ... otros tipos */}
       </div>
     )
   }
   ```

**Criterios de Aceptación**:
- ✅ Canvas renderiza lista de componentes
- ✅ Drag & drop funciona correctamente
- ✅ Positions se actualizan al reordenar
- ✅ Mensaje "Comenzá agregando..." si lista vacía

**Tests**:
```typescript
// components/editor/Canvas.test.tsx
describe('Canvas', () => {
  it('renderiza lista de componentes')
  it('muestra mensaje si no hay componentes')
  it('permite drag & drop')
  it('actualiza positions al reordenar')
})
```

**Dependencias**: Fase 16

**Salida**:
- Canvas con drag & drop funcional
- ComponentRenderer implementado

---

### Fase 18: Editor - Panel de Customización 🎨
**Objetivo**: Panel lateral para customizar theme (colores, fuentes, estilos)

**Prerequisitos**: Fase 17 completada

**Tareas**:
1. Crear `components/editor/PanelCustomizacion.tsx`
   ```typescript
   'use client'

   import { useEditorStore } from '@/store/editor-store'
   import { ColorPicker } from '@/components/ui/color-picker'
   // GAP 2: Importar ImageUploader para integrar subida de imágenes
   import { ImageUploader } from './ImageUploader'
   import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select'
   import { Switch } from '@/components/ui/switch'
   import { Label } from '@/components/ui/label'

   export function PanelCustomizacion() {
     const { profile, setProfile, theme, setTheme } = useEditorStore() // GAP 2: Acceder a profile

     const updateTheme = (updates: Partial<typeof theme>) => {
       setTheme({ ...theme, ...updates })
     }

     return (
       <div className="w-80 bg-gray-50 p-6 overflow-y-auto h-full">
         <h2 className="text-xl font-bold mb-6">Customización</h2>

         {/* GAP 2: Sección de imágenes de perfil */}
         {profile && (
           <div className="mb-6 space-y-4">
             <h3 className="font-semibold">Imágenes</h3>

             <ImageUploader
               currentImageUrl={profile.profile_image_url}
               onUploadComplete={async (fileId, publicUrl, blurDataUrl) => {
                 // Actualizar perfil en el servidor
                 await fetch('/api/perfil', {
                   method: 'PATCH',
                   headers: { 'Content-Type': 'application/json' },
                   body: JSON.stringify({
                     profile_image_url: publicUrl,
                     profile_blur_data_url: blurDataUrl
                   }),
                 })
                 // Actualizar perfil en el store
                 setProfile({
                   ...profile,
                   profile_image_url: publicUrl,
                   profile_blur_data_url: blurDataUrl
                 })
               }}
               type="profile_image"
               label="Foto de perfil"
             />

             <ImageUploader
               currentImageUrl={profile.cover_image_url}
               onUploadComplete={async (fileId, publicUrl, blurDataUrl) => {
                 await fetch('/api/perfil', {
                   method: 'PATCH',
                   headers: { 'Content-Type': 'application/json' },
                   body: JSON.stringify({
                     cover_image_url: publicUrl,
                     cover_blur_data_url: blurDataUrl
                   }),
                 })
                 setProfile({
                   ...profile,
                   cover_image_url: publicUrl,
                   cover_blur_data_url: blurDataUrl
                 })
               }}
               type="cover_image"
               label="Imagen de portada"
             />
           </div>
         )}

         {/* Colores */}
         <div className="space-y-4 mb-6">
           <h3 className="font-semibold">Colores</h3>

           <div>
             <Label>Color de fondo</Label>
             <ColorPicker
               value={theme.backgroundColor}
               onChange={(color) => updateTheme({ backgroundColor: color })}
             />
           </div>

           <div>
             <Label>Color de texto</Label>
             <ColorPicker
               value={theme.textColor}
               onChange={(color) => updateTheme({ textColor: color })}
             />
           </div>

           <div>
             <Label>Color de acento</Label>
             <ColorPicker
               value={theme.accentColor}
               onChange={(color) => updateTheme({ accentColor: color })}
             />
           </div>
         </div>

         {/* Fuente */}
         <div className="mb-6">
           <Label>Fuente</Label>
           <Select
             value={theme.fontFamily}
             onValueChange={(font) => updateTheme({ fontFamily: font })}
           >
             <SelectTrigger>
               <SelectValue />
             </SelectTrigger>
             <SelectContent>
               <SelectItem value="Inter">Inter</SelectItem>
               <SelectItem value="Poppins">Poppins</SelectItem>
               <SelectItem value="Roboto">Roboto</SelectItem>
               <SelectItem value="Montserrat">Montserrat</SelectItem>
             </SelectContent>
           </Select>
         </div>

         {/* Estilo de botones */}
         <div className="space-y-4 mb-6">
           <h3 className="font-semibold">Estilo de Botones</h3>

           <div>
             <Label>Color de fondo</Label>
             <ColorPicker
               value={theme.buttonStyle.backgroundColor}
               onChange={(color) => updateTheme({
                 buttonStyle: { ...theme.buttonStyle, backgroundColor: color }
               })}
             />
           </div>

           {/* ... más controles de buttonStyle ... */}
         </div>

         {/* Modo oscuro */}
         <div className="flex items-center justify-between">
           <Label>Modo oscuro</Label>
           <Switch
             checked={theme.isDarkMode}
             onCheckedChange={(checked) => updateTheme({ isDarkMode: checked })}
           />
         </div>
       </div>
     )
   }
   ```

2. Crear componente ColorPicker custom con shadcn/ui
   ```typescript
   // components/ui/color-picker.tsx
   'use client'

   import { Input } from './input'

   export function ColorPicker({ value, onChange }: { value: string; onChange: (color: string) => void }) {
     return (
       <div className="flex gap-2">
         <input
           type="color"
           value={value}
           onChange={(e) => onChange(e.target.value)}
           className="w-12 h-12 rounded cursor-pointer"
         />
         <Input
           value={value}
           onChange={(e) => onChange(e.target.value)}
           placeholder="#000000"
         />
       </div>
     )
   }
   ```

**Criterios de Aceptación**:
- ✅ Panel renderiza controles de theme
- ✅ Cambios se reflejan en tiempo real en store
- ✅ ColorPicker funcional
- ✅ Select de fuentes funcional
- ✅ Toggle de modo oscuro funcional

**Tests**:
```typescript
// components/editor/PanelCustomizacion.test.tsx
describe('PanelCustomizacion', () => {
  it('renderiza controles de customización')
  it('actualiza backgroundColor en store')
  it('actualiza fontFamily en store')
  it('toggle isDarkMode funciona')
})
```

**Dependencias**: Fase 17

**Salida**:
- Panel de customización funcional
- Controles de theme implementados

---

### Fase 18bis: Editor - Biblioteca de Componentes y Formularios 🧩
**Objetivo**: UI para agregar, editar y eliminar componentes del perfil

**Prerequisitos**: Fase 18 completada

**CORRECCIÓN GAP 1 (BLOCKER)**: Sin esta fase, el usuario no puede agregar contenido al perfil.

**Tareas**:
1. Instalar dependencia para sanitización HTML
   ```bash
   npm install isomorphic-dompurify
   ```

2. Crear sidebar de biblioteca de componentes
   ```typescript
   // components/editor/BibliotecaComponentes.tsx
   'use client'

   import { Button } from '@/components/ui/button'
   import { Plus } from 'lucide-react'
   import { useEditorStore } from '@/store/editor-store'
   import { useState } from 'react'

   const TIPOS_COMPONENTES = [
     { type: 'button', label: 'Botón de enlace', icon: '🔗' },
     { type: 'text_block', label: 'Bloque de texto', icon: '📝' },
     { type: 'audio_player', label: 'Reproductor de audio', icon: '🎵' },
     { type: 'video_embed', label: 'Video (YouTube/Vimeo)', icon: '📹' },
     { type: 'pdf_download', label: 'Descarga de PDF', icon: '📄' },
   ]

   export function BibliotecaComponentes() {
     const [modalOpen, setModalOpen] = useState(false)
     const [selectedType, setSelectedType] = useState<string | null>(null)

     const handleAddComponent = (type: string) => {
       setSelectedType(type)
       setModalOpen(true)
     }

     return (
       <>
         <div className="p-4 border-b">
           <h3 className="font-semibold mb-3">Agregar elementos</h3>
           <div className="space-y-2">
             {TIPOS_COMPONENTES.map((item) => (
               <Button
                 key={item.type}
                 variant="outline"
                 className="w-full justify-start"
                 onClick={() => handleAddComponent(item.type)}
               >
                 <span className="mr-2">{item.icon}</span>
                 {item.label}
               </Button>
             ))}
           </div>
         </div>

         {modalOpen && selectedType && (
           <FormularioComponente
             type={selectedType}
             onClose={() => {
               setModalOpen(false)
               setSelectedType(null)
             }}
           />
         )}
       </>
     )
   }
   ```

3. Crear formularios específicos por tipo de componente
   ```typescript
   // components/editor/FormularioComponente.tsx
   'use client'

   import { Dialog, DialogContent, DialogHeader, DialogTitle } from '@/components/ui/dialog'
   import { Button } from '@/components/ui/button'
   import { Input } from '@/components/ui/input'
   import { Textarea } from '@/components/ui/textarea'
   import { Label } from '@/components/ui/label'
   import { useEditorStore } from '@/store/editor-store'
   import { useState } from 'react'
   import DOMPurify from 'isomorphic-dompurify'

   interface FormularioComponenteProps {
     type: string
     onClose: () => void
     componentId?: string // Para edición
     initialData?: any
   }

   export function FormularioComponente({ type, onClose, componentId, initialData }: FormularioComponenteProps) {
     const { addComponent, updateComponent, components } = useEditorStore()
     const [formData, setFormData] = useState(initialData || {})
     const [loading, setLoading] = useState(false)

     const handleSubmit = async (e: React.FormEvent) => {
       e.preventDefault()
       setLoading(true)

       try {
         // Sanitizar contenido HTML
         if (type === 'text_block' && formData.content) {
           formData.content = DOMPurify.sanitize(formData.content)
         }

         if (componentId) {
           // Editar existente
           await fetch('/api/componentes', {
             method: 'PATCH',
             headers: { 'Content-Type': 'application/json' },
             body: JSON.stringify({ id: componentId, data: formData }),
           })
           updateComponent(componentId, formData)
         } else {
           // Crear nuevo
           const res = await fetch('/api/componentes', {
             method: 'POST',
             headers: { 'Content-Type': 'application/json' },
             body: JSON.stringify({
               type,
               position: components.length,
               is_visible: true,
               data: formData,
             }),
           })
           const newComponent = await res.json()
           addComponent(newComponent)
         }

         onClose()
       } catch (error) {
         console.error('Error guardando componente:', error)
         alert('Error al guardar')
       } finally {
         setLoading(false)
       }
     }

     return (
       <Dialog open onOpenChange={onClose}>
         <DialogContent>
           <DialogHeader>
             <DialogTitle>
               {componentId ? 'Editar' : 'Agregar'} {getTipoLabel(type)}
             </DialogTitle>
           </DialogHeader>

           <form onSubmit={handleSubmit} className="space-y-4">
             {type === 'button' && (
               <>
                 <div>
                   <Label>Texto del botón</Label>
                   <Input
                     required
                     maxLength={50}
                     value={formData.text || ''}
                     onChange={(e) => setFormData({ ...formData, text: e.target.value })}
                     placeholder="Escuchá mi set"
                   />
                 </div>
                 <div>
                   <Label>URL</Label>
                   <Input
                     required
                     type="url"
                     value={formData.url || ''}
                     onChange={(e) => setFormData({ ...formData, url: e.target.value })}
                     placeholder="https://soundcloud.com/..."
                   />
                 </div>
                 <div>
                   <Label>Ícono (opcional)</Label>
                   <Input
                     value={formData.icon || ''}
                     onChange={(e) => setFormData({ ...formData, icon: e.target.value })}
                     placeholder="🎧"
                   />
                 </div>
               </>
             )}

             {type === 'text_block' && (
               <>
                 <div>
                   <Label>Contenido</Label>
                   <Textarea
                     required
                     maxLength={2000}
                     rows={6}
                     value={formData.content || ''}
                     onChange={(e) => setFormData({ ...formData, content: e.target.value })}
                     placeholder="Escribí tu biografía, descripción, etc."
                   />
                   <p className="text-xs text-gray-500 mt-1">
                     Podés usar HTML básico (será sanitizado por seguridad)
                   </p>
                 </div>
               </>
             )}

             {type === 'audio_player' && (
               <>
                 <div>
                   <Label>Título</Label>
                   <Input
                     required
                     maxLength={100}
                     value={formData.title || ''}
                     onChange={(e) => setFormData({ ...formData, title: e.target.value })}
                     placeholder="Mi último set - Julio 2025"
                   />
                 </div>

                 {/* CORRECCIÓN GAP 6: UI para marcar audio como privado */}
                 <div className="flex items-center gap-3 p-3 border rounded bg-gray-50">
                   <Switch
                     id="is-private"
                     checked={formData.isPrivate || false}
                     onCheckedChange={(checked) => setFormData({ ...formData, isPrivate: checked })}
                   />
                   <div className="flex-1">
                     <Label htmlFor="is-private" className="cursor-pointer">
                       Hacer privado
                     </Label>
                     <p className="text-xs text-gray-500">
                       Solo se podrá escuchar con un link especial (ideal para demos unreleased)
                     </p>
                   </div>
                 </div>

                 <div>
                   <Label>Archivo de audio</Label>
                   <AudioUploader
                     isPrivate={formData.isPrivate || false} // GAP 6: Pasar flag de privacidad
                     onUploadComplete={(fileId) => setFormData({ ...formData, file_id: fileId })}
                   />
                 </div>
               </>
             )}

             {type === 'video_embed' && (
               <>
                 <div>
                   <Label>URL del video</Label>
                   <Input
                     required
                     type="url"
                     value={formData.url || ''}
                     onChange={(e) => {
                       const url = e.target.value
                       const provider = url.includes('youtube') || url.includes('youtu.be')
                         ? 'youtube'
                         : url.includes('vimeo')
                         ? 'vimeo'
                         : null
                       setFormData({ ...formData, url, provider })
                     }}
                     placeholder="https://youtube.com/watch?v=..."
                   />
                 </div>
               </>
             )}

             {type === 'pdf_download' && (
               <>
                 <div>
                   <Label>Nombre del archivo</Label>
                   <Input
                     required
                     maxLength={100}
                     value={formData.filename || ''}
                     onChange={(e) => setFormData({ ...formData, filename: e.target.value })}
                     placeholder="Rider técnico"
                   />
                 </div>
                 <div>
                   <Label>Archivo PDF</Label>
                   <PDFUploader
                     onUploadComplete={(fileId) => setFormData({ ...formData, file_id: fileId })}
                   />
                 </div>
               </>
             )}

             <div className="flex gap-2">
               <Button type="submit" disabled={loading}>
                 {loading ? 'Guardando...' : 'Guardar'}
               </Button>
               <Button type="button" variant="outline" onClick={onClose}>
                 Cancelar
               </Button>
             </div>
           </form>
         </DialogContent>
       </Dialog>
     )
   }

   function getTipoLabel(type: string): string {
     const labels = {
       button: 'Botón',
       text_block: 'Texto',
       audio_player: 'Audio',
       video_embed: 'Video',
       pdf_download: 'PDF',
     }
     return labels[type] || type
   }
   ```

4. Crear uploaders específicos para audio y PDF
   ```typescript
   // components/editor/AudioUploader.tsx
   'use client'

   import { useState } from 'react'
   import { Button } from '@/components/ui/button'

   // CORRECCIÓN GAP 6: Agregar isPrivate como prop
   export function AudioUploader({
     isPrivate = false,
     onUploadComplete
   }: {
     isPrivate?: boolean
     onUploadComplete: (fileId: string) => void
   }) {
     const [uploading, setUploading] = useState(false)
     const [fileName, setFileName] = useState<string | null>(null)

     const handleUpload = async (e: React.ChangeEvent<HTMLInputElement>) => {
       const file = e.target.files?.[0]
       if (!file) return

       setUploading(true)

       try {
         // Flow completo de upload (igual que ImageUploader)
         const initRes = await fetch('/api/archivos/iniciar-subida', {
           method: 'POST',
           headers: { 'Content-Type': 'application/json' },
           body: JSON.stringify({
             type: 'audio',
             size: file.size,
             mimeType: file.type,
             filename: file.name,
             isPrivate, // GAP 6: Usar prop isPrivate
           }),
         })

         const { uploadUrl, fileId, bucket, path } = await initRes.json()

         await fetch(uploadUrl, {
           method: 'PUT',
           body: file,
           headers: { 'Content-Type': file.type },
         })

         await fetch('/api/archivos/confirmar-subida', {
           method: 'POST',
           headers: { 'Content-Type': 'application/json' },
           body: JSON.stringify({
             fileId,
             bucket,
             path,
             type: 'audio',
             originalFilename: file.name,
             isPrivate, // GAP 6: Incluir flag de privacidad
           }),
         })

         setFileName(file.name)
         onUploadComplete(fileId)
       } catch (error) {
         console.error('Error:', error)
         alert('Error al subir audio')
       } finally {
         setUploading(false)
       }
     }

     return (
       <div>
         <input
           type="file"
           accept="audio/mp3,audio/mpeg"
           onChange={handleUpload}
           disabled={uploading}
           className="block w-full text-sm"
         />
         {uploading && <p className="text-sm text-gray-500 mt-1">Subiendo...</p>}
         {fileName && <p className="text-sm text-green-600 mt-1">✓ {fileName}</p>}
       </div>
     )
   }

   // components/editor/PDFUploader.tsx - Idéntico pero para PDF
   ```

5. Agregar botón de eliminar a ComponentRenderer
   ```typescript
   // components/editor/ComponentRenderer.tsx - ACTUALIZAR
   import { Trash2, Edit } from 'lucide-react'
   import { useEditorStore } from '@/store/editor-store'

   export function ComponentRenderer({ component }: { component: any }) {
     const { deleteComponent } = useEditorStore()
     const [editing, setEditing] = useState(false)

     const handleDelete = async () => {
       if (!confirm('¿Eliminar este elemento?')) return

       await fetch(`/api/componentes?id=${component.id}`, { method: 'DELETE' })
       deleteComponent(component.id)
     }

     return (
       <div className="group relative">
         {/* Controles de edición */}
         <div className="absolute -right-2 -top-2 opacity-0 group-hover:opacity-100 transition-opacity flex gap-1">
           <Button
             size="icon"
             variant="outline"
             className="h-8 w-8 bg-white"
             onClick={() => setEditing(true)}
           >
             <Edit className="h-4 w-4" />
           </Button>
           <Button
             size="icon"
             variant="outline"
             className="h-8 w-8 bg-white text-red-600"
             onClick={handleDelete}
           >
             <Trash2 className="h-4 w-4" />
           </Button>
         </div>

         {/* Contenido del componente */}
         <div ref={setNodeRef} style={style} {...attributes} {...listeners}>
           {/* ... renderizado del componente ... */}
         </div>

         {/* Modal de edición */}
         {editing && (
           <FormularioComponente
             type={component.type}
             componentId={component.id}
             initialData={component.data}
             onClose={() => setEditing(false)}
           />
         )}
       </div>
     )
   }
   ```

6. Integrar BibliotecaComponentes en EditorContainer
   ```typescript
   // components/editor/EditorContainer.tsx - ACTUALIZAR
   import { BibliotecaComponentes } from './BibliotecaComponentes'

   export function EditorContainer({ profile, components }: Props) {
     return (
       <div className="h-screen flex flex-col">
         <EditorToolbar profile={profile} />

         <div className="flex-1 flex">
           {/* Panel izquierdo: Customización + Biblioteca */}
           <div className="w-80 bg-gray-50 flex flex-col overflow-hidden">
             <div className="flex-1 overflow-y-auto">
               <PanelCustomizacion />
             </div>
             <BibliotecaComponentes />
           </div>

           {/* Centro: Canvas */}
           <div className="flex-1 overflow-y-auto">
             <Canvas />
           </div>

           {/* Derecha: Preview */}
           <Preview />
         </div>
       </div>
     )
   }
   ```

**Criterios de Aceptación**:
- ✅ Usuario puede agregar nuevos componentes desde biblioteca
- ✅ Formularios específicos por tipo de componente funcionan
- ✅ Usuario puede editar componentes existentes (botón editar)
- ✅ Usuario puede eliminar componentes (botón papelera)
- ✅ HTML se sanitiza antes de guardar (DOMPurify)
- ✅ Uploads de audio y PDF funcionan desde formularios
- ✅ Componentes se persisten en DB via API

**Tests**:
```typescript
// components/editor/BibliotecaComponentes.test.tsx
describe('BibliotecaComponentes', () => {
  it('muestra todos los tipos de componentes disponibles')
  it('abre modal al hacer click en tipo')
  it('cierra modal después de guardar')
})

// components/editor/FormularioComponente.test.tsx
describe('FormularioComponente', () => {
  it('muestra campos correctos para tipo button')
  it('muestra campos correctos para tipo text_block')
  it('sanitiza HTML en text_block')
  it('valida URLs en botones')
  it('llama a API POST al crear nuevo')
  it('llama a API PATCH al editar existente')
})

// components/editor/ComponentRenderer.test.tsx
describe('ComponentRenderer con controles', () => {
  it('muestra botones editar y eliminar al hover')
  it('abre modal de edición al hacer click en editar')
  it('elimina componente al confirmar')
  it('no elimina si cancela confirmación')
})
```

**Dependencias**: Fase 18

**Salida**:
- Usuario puede agregar, editar y eliminar componentes
- Formularios específicos implementados
- Sistema de uploads integrado
- Sanitización HTML activa

---

### Fase 19: Editor - Preview en Tiempo Real 📱
**Objetivo**: Vista preview que refleja cambios del editor en tiempo real

**Prerequisitos**: Fase 18bis completada

**Tareas**:
1. Crear `components/editor/Preview.tsx`
   ```typescript
   'use client'

   import { useEditorStore } from '@/store/editor-store'
   import { PerfilPublicoView } from '@/components/perfil/PerfilPublicoView'

   export function Preview() {
     const { theme, components, isPreviewMode } = useEditorStore()

     return (
       <div className="flex-1 bg-gray-100 flex items-center justify-center p-8">
         {/* Toggle mobile/desktop */}
         <div className="flex gap-2 absolute top-4 right-4">
           <button className="px-4 py-2 bg-white rounded">Mobile</button>
           <button className="px-4 py-2 bg-gray-200 rounded">Desktop</button>
         </div>

         {/* Frame mobile */}
         <div
           className="w-[375px] h-[667px] bg-white rounded-xl shadow-2xl overflow-y-auto"
           style={{
             backgroundColor: theme.backgroundColor,
             color: theme.textColor,
             fontFamily: theme.fontFamily,
           }}
         >
           <PerfilPublicoView theme={theme} components={components} isPreview={true} />
         </div>
       </div>
     )
   }
   ```

2. Crear `components/perfil/PerfilPublicoView.tsx`
   ```typescript
   'use client'

   import { ButtonPublico } from './ButtonPublico'
   import { TextBlockPublico } from './TextBlockPublico'
   import { AudioPlayerPublico } from './AudioPlayerPublico'
   // ... otros componentes

   export function PerfilPublicoView({
     theme,
     components,
     isPreview = false
   }: {
     theme: any
     components: any[]
     isPreview?: boolean
   }) {
     return (
       <div className="p-6 space-y-4">
         {components
           .filter(c => c.is_visible)
           .sort((a, b) => a.position - b.position)
           .map(component => (
             <div key={component.id}>
               {component.type === 'button' && (
                 <ButtonPublico data={component.data} theme={theme} />
               )}
               {component.type === 'text_block' && (
                 <TextBlockPublico data={component.data} theme={theme} />
               )}
               {component.type === 'audio_player' && (
                 <AudioPlayerPublico data={component.data} theme={theme} isPreview={isPreview} />
               )}
               {/* ... otros tipos */}
             </div>
           ))}
       </div>
     )
   }
   ```

**Criterios de Aceptación**:
- ✅ Preview se actualiza en tiempo real al modificar theme
- ✅ Preview se actualiza al agregar/eliminar/reordenar componentes
- ✅ Frame mobile simula dispositivo móvil
- ✅ Toggle mobile/desktop funcional

**Tests**:
```typescript
// components/editor/Preview.test.tsx
describe('Preview', () => {
  it('renderiza frame mobile')
  it('aplica theme del store')
  it('renderiza componentes en orden correcto')
  it('filtra componentes no visibles')
  it('toggle mobile/desktop funciona')
})
```

**Dependencias**: Fase 18

**Salida**:
- Preview en tiempo real funcional
- Vista mobile simulada

---

### Fase 20: Página del Editor Completa 🖥️
**Objetivo**: Página `/usuario/:username` integrando Canvas + Panel + Preview

**Prerequisitos**: Fase 19 completada

**Tareas**:
1. Crear `app/usuario/[username]/page.tsx`
   ```typescript
   import { createClient } from '@/lib/supabase/server'
   import { redirect } from 'next/navigation'
   import { EditorContainer } from '@/components/editor/EditorContainer'

   export default async function EditorPage({ params }: { params: { username: string } }) {
     const supabase = createClient()
     const { data: { session } } = await supabase.auth.getSession()

     if (!session) {
       redirect('/ingresar')
     }

     // Verificar que el usuario es dueño del perfil
     const { data: profile } = await supabase
       .from('profiles')
       .select('*, user_id')
       .eq('artist_name', params.username)
       .single()

     if (!profile || profile.user_id !== session.user.id) {
       redirect('/ingresar')
     }

     // Obtener componentes
     const { data: components } = await supabase
       .from('profile_components')
       .select('*')
       .eq('profile_id', profile.id)
       .order('position', { ascending: true })

     return <EditorContainer profile={profile} components={components || []} />
   }
   ```

2. Crear `components/editor/EditorContainer.tsx`
   ```typescript
   'use client'

   import { useEffect } from 'react'
   import { useEditorStore } from '@/store/editor-store'
   import { PanelCustomizacion } from './PanelCustomizacion'
   import { Canvas } from './Canvas'
   import { Preview } from './Preview'
   import { EditorToolbar } from './EditorToolbar'

   export function EditorContainer({ profile, components }: { profile: any; components: any[] }) {
     const { loadProfile, isSaving } = useEditorStore()

     useEffect(() => {
       loadProfile(profile, components)
     }, [])

     return (
       <div className="h-screen flex flex-col">
         {/* Toolbar superior */}
         <EditorToolbar profile={profile} />

         {/* Layout principal */}
         <div className="flex-1 flex">
           {/* Panel izquierdo: Customización */}
           <PanelCustomizacion />

           {/* Centro: Canvas */}
           <div className="flex-1 overflow-y-auto">
             <Canvas />
           </div>

           {/* Derecha: Preview */}
           <Preview />
         </div>

         {/* Loading overlay */}
         {isSaving && (
           <div className="absolute inset-0 bg-black/50 flex items-center justify-center">
             <div className="bg-white p-6 rounded-lg">
               Guardando...
             </div>
           </div>
         )}
       </div>
     )
   }
   ```

3. Crear `components/editor/EditorToolbar.tsx`
   ```typescript
   'use client'

   import { Button } from '@/components/ui/button'
   import { useEditorStore } from '@/store/editor-store'
   import { useState } from 'react'

   export function EditorToolbar({ profile }: { profile: any }) {
     const { components, theme, setSaving } = useEditorStore()
     const [isPublished, setIsPublished] = useState(profile.is_published)

     const handleSave = async () => {
       setSaving(true)

       // 1. Guardar theme
       await fetch('/api/perfil', {
         method: 'PATCH',
         headers: { 'Content-Type': 'application/json' },
         body: JSON.stringify({ theme_settings: theme }),
       })

       // 2. Guardar componentes (reordenar)
       await fetch('/api/componentes/reordenar', {
         method: 'POST',
         headers: { 'Content-Type': 'application/json' },
         body: JSON.stringify({
           components: components.map(c => ({ id: c.id, position: c.position }))
         }),
       })

       setSaving(false)
     }

     const handlePublish = async () => {
       const newState = !isPublished
       await fetch('/api/perfil/publicar', {
         method: 'POST',
         headers: { 'Content-Type': 'application/json' },
         body: JSON.stringify({ isPublished: newState }),
       })
       setIsPublished(newState)
     }

     return (
       <div className="bg-white border-b px-6 py-3 flex items-center justify-between">
         <div>
           <h1 className="text-xl font-bold">{profile.artist_name}</h1>
           <p className="text-sm text-gray-500">preskit.ar/{profile.artist_name}</p>
         </div>

         <div className="flex gap-3">
           <Button variant="outline" onClick={handleSave}>
             Guardar
           </Button>
           <Button onClick={handlePublish}>
             {isPublished ? 'Despublicar' : 'Publicar'}
           </Button>
         </div>
       </div>
     )
   }
   ```

4. **CORRECCIÓN GAP 7**: Crear componente de Social Share Preview
   ```typescript
   // components/editor/SocialSharePreview.tsx
   'use client'

   import { Button } from '@/components/ui/button'
   import { Copy, Check } from 'lucide-react'
   import { useState } from 'react'
   import Image from 'next/image'

   export function SocialSharePreview({ profile }: { profile: any }) {
     const [copied, setCopied] = useState(false)
     const shareUrl = `https://preskit.ar/${profile.artist_name}`

     const handleCopyLink = async () => {
       try {
         await navigator.clipboard.writeText(shareUrl)
         setCopied(true)
         setTimeout(() => setCopied(false), 2000)
       } catch (error) {
         console.error('Error copiando al portapapeles:', error)
       }
     }

     return (
       <div className="border rounded-lg p-4 bg-white">
         <h3 className="font-semibold mb-3">Vista previa al compartir</h3>
         <p className="text-sm text-gray-600 mb-3">
           Así se verá tu perfil cuando lo compartas en redes sociales
         </p>

         {/* Simulación de tarjeta social (Open Graph) */}
         <div className="border rounded-lg overflow-hidden bg-gray-50 mb-4">
           {profile.cover_image_url && (
             <div className="relative w-full h-32">
               <Image
                 src={profile.cover_image_url}
                 alt="Cover"
                 fill
                 className="object-cover"
               />
             </div>
           )}
           <div className="p-3">
             <p className="font-semibold text-sm line-clamp-1">{profile.artist_name}</p>
             <p className="text-xs text-gray-600 line-clamp-2">
               {profile.tagline || profile.bio || 'Press Kit Digital'}
             </p>
             <p className="text-xs text-gray-400 mt-1">preskit.ar</p>
           </div>
         </div>

         {/* URL y botón copiar */}
         <div className="flex items-center gap-2">
           <input
             type="text"
             value={shareUrl}
             readOnly
             className="flex-1 px-3 py-2 text-sm border rounded bg-gray-50"
           />
           <Button onClick={handleCopyLink} variant="outline" size="sm">
             {copied ? (
               <>
                 <Check className="h-4 w-4 mr-1" />
                 Copiado
               </>
             ) : (
               <>
                 <Copy className="h-4 w-4 mr-1" />
                 Copiar
               </>
             )}
           </Button>
         </div>

         {profile.is_published && (
           <p className="text-xs text-green-600 mt-2">
             ✓ Tu perfil está publicado y listo para compartir
           </p>
         )}
         {!profile.is_published && (
           <p className="text-xs text-orange-600 mt-2">
             ⚠ Publicá tu perfil para que sea visible públicamente
           </p>
         )}
       </div>
     )
   }
   ```

5. Integrar SocialSharePreview en PanelCustomizacion
   ```typescript
   // components/editor/PanelCustomizacion.tsx - ACTUALIZAR
   // NOTA: Este componente se definió originalmente en Fase 18 (líneas 2858-3050)
   // Aquí se muestra la versión actualizada con SocialSharePreview integrado

   import { SocialSharePreview } from './SocialSharePreview'
   import { ImageUploader } from './ImageUploader'
   import { useEditorStore } from '@/store/editor-store'
   import { ColorPicker } from '@/components/ui/color-picker'
   import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select'
   import { Switch } from '@/components/ui/switch'
   import { Label } from '@/components/ui/label'

   export function PanelCustomizacion() {
     const { profile, setProfile, theme, setTheme } = useEditorStore()

     const updateTheme = (updates: Partial<typeof theme>) => {
       setTheme({ ...theme, ...updates })
     }

     return (
       <div className="w-80 bg-gray-50 p-6 overflow-y-auto h-full space-y-6">
         <h2 className="text-xl font-bold">Customización</h2>

         {/* GAP 7: Social Share Preview al inicio */}
         {profile && <SocialSharePreview profile={profile} />}

         {/* GAP 2: Sección de imágenes (implementación completa de Fase 18, líneas 2888-2927) */}
         {profile && (
           <div className="space-y-4">
             <h3 className="font-semibold">Imágenes</h3>

             <ImageUploader
               currentImageUrl={profile.profile_image_url}
               onUploadComplete={async (fileId, publicUrl, blurDataUrl) => {
                 await fetch('/api/perfil', {
                   method: 'PATCH',
                   headers: { 'Content-Type': 'application/json' },
                   body: JSON.stringify({
                     profile_image_url: publicUrl,
                     profile_blur_data_url: blurDataUrl
                   }),
                 })
                 setProfile({
                   ...profile,
                   profile_image_url: publicUrl,
                   profile_blur_data_url: blurDataUrl
                 })
               }}
               type="profile_image"
               label="Foto de perfil"
             />

             <ImageUploader
               currentImageUrl={profile.cover_image_url}
               onUploadComplete={async (fileId, publicUrl, blurDataUrl) => {
                 await fetch('/api/perfil', {
                   method: 'PATCH',
                   headers: { 'Content-Type': 'application/json' },
                   body: JSON.stringify({
                     cover_image_url: publicUrl,
                     cover_blur_data_url: blurDataUrl
                   }),
                 })
                 setProfile({
                   ...profile,
                   cover_image_url: publicUrl,
                   cover_blur_data_url: blurDataUrl
                 })
               }}
               type="cover_image"
               label="Imagen de portada"
             />
           </div>
         )}

         {/* Colores - CÓDIGO COMPLETO de Fase 18, líneas 2933-3005 */}
         <div className="space-y-4 mb-6">
           <h3 className="font-semibold">Colores</h3>

           <div>
             <Label>Color de fondo</Label>
             <ColorPicker
               value={theme.backgroundColor}
               onChange={(color) => updateTheme({ backgroundColor: color })}
             />
           </div>

           <div>
             <Label>Color de texto</Label>
             <ColorPicker
               value={theme.textColor}
               onChange={(color) => updateTheme({ textColor: color })}
             />
           </div>

           <div>
             <Label>Color de acento</Label>
             <ColorPicker
               value={theme.accentColor}
               onChange={(color) => updateTheme({ accentColor: color })}
             />
           </div>
         </div>

         {/* Fuente */}
         <div className="mb-6">
           <Label>Fuente</Label>
           <Select
             value={theme.fontFamily}
             onValueChange={(font) => updateTheme({ fontFamily: font })}
           >
             <SelectTrigger>
               <SelectValue />
             </SelectTrigger>
             <SelectContent>
               <SelectItem value="Inter">Inter</SelectItem>
               <SelectItem value="Poppins">Poppins</SelectItem>
               <SelectItem value="Roboto">Roboto</SelectItem>
               <SelectItem value="Montserrat">Montserrat</SelectItem>
             </SelectContent>
           </Select>
         </div>

         {/* Estilo de botones */}
         <div className="space-y-4 mb-6">
           <h3 className="font-semibold">Estilo de Botones</h3>

           <div>
             <Label>Color de fondo</Label>
             <ColorPicker
               value={theme.buttonStyle.backgroundColor}
               onChange={(color) => updateTheme({
                 buttonStyle: { ...theme.buttonStyle, backgroundColor: color }
               })}
             />
           </div>

           <div>
             <Label>Color de texto</Label>
             <ColorPicker
               value={theme.buttonStyle.textColor}
               onChange={(color) => updateTheme({
                 buttonStyle: { ...theme.buttonStyle, textColor: color }
               })}
             />
           </div>

           <div>
             <Label>Color de borde</Label>
             <ColorPicker
               value={theme.buttonStyle.borderColor}
               onChange={(color) => updateTheme({
                 buttonStyle: { ...theme.buttonStyle, borderColor: color }
               })}
             />
           </div>

           <div>
             <Label>Ancho de borde (px)</Label>
             <Input
               type="number"
               value={theme.buttonStyle.borderWidth}
               onChange={(e) => updateTheme({
                 buttonStyle: { ...theme.buttonStyle, borderWidth: parseInt(e.target.value) }
               })}
             />
           </div>

           <div className="flex items-center justify-between">
             <Label>Sombra</Label>
             <Switch
               checked={theme.buttonStyle.hasShadow}
               onCheckedChange={(checked) => updateTheme({
                 buttonStyle: { ...theme.buttonStyle, hasShadow: checked }
               })}
             />
           </div>

           {theme.buttonStyle.hasShadow && (
             <div>
               <Label>Color de sombra</Label>
               <ColorPicker
                 value={theme.buttonStyle.shadowColor}
                 onChange={(color) => updateTheme({
                   buttonStyle: { ...theme.buttonStyle, shadowColor: color }
                 })}
               />
             </div>
           )}
         </div>

         {/* Modo oscuro */}
         <div className="flex items-center justify-between">
           <Label>Modo oscuro</Label>
           <Switch
             checked={theme.isDarkMode}
             onCheckedChange={(checked) => updateTheme({ isDarkMode: checked })}
           />
         </div>
       </div>
     )
   }
   ```

**Criterios de Aceptación**:
- ✅ Página carga perfil y componentes del usuario
- ✅ Layout split-screen funciona (Panel | Canvas | Preview)
- ✅ Toolbar con botones Guardar y Publicar funcional
- ✅ Guardar persiste theme y reordena componentes
- ✅ Publicar toggle funciona
- ✅ **GAP 7**: Social Share Preview muestra tarjeta simulada
- ✅ **GAP 7**: Botón "Copiar link" funciona
- ✅ **GAP 7**: Indica si perfil está publicado o no

**Tests**:
```typescript
// app/usuario/[username]/page.test.tsx
describe('Página del Editor', () => {
  it('carga perfil del usuario autenticado')
  it('redirige a /ingresar si no autenticado')
  it('redirige si usuario intenta editar perfil ajeno')
  it('renderiza layout completo')
  it('botón Guardar persiste cambios')
  it('botón Publicar actualiza estado')
})

// components/editor/SocialSharePreview.test.tsx (GAP 7)
describe('SocialSharePreview', () => {
  it('renderiza tarjeta social con cover image')
  it('renderiza tarjeta social sin cover image')
  it('muestra URL correcta del perfil')
  it('botón copiar copia URL al portapapeles')
  it('muestra feedback visual después de copiar')
  it('muestra estado publicado correctamente')
  it('muestra advertencia si perfil no publicado')
  it('usa tagline o bio para descripción')
})
```

**Dependencias**: Fase 19

**Salida**:
- Editor completo funcional
- Split-screen implementado
- Guardar y publicar funcionando
- **GAP 7**: Social Share Preview con botón copiar link

---

### Fase 21: Perfiles Públicos - Página SSG/ISR 🌐
**Objetivo**: Página pública `/:username` con SSG + ISR

**Prerequisitos**: Fase 20 completada

**Tareas**:
1. Crear `app/(publico)/[username]/page.tsx`
   ```typescript
   import { createClient } from '@/lib/supabase/server'
   import { notFound } from 'next/navigation'
   import { PerfilPublicoView } from '@/components/perfil/PerfilPublicoView'
   import { Metadata } from 'next'

   // ISR: revalidar cada 60 segundos
   export const revalidate = 60

   export async function generateMetadata({ params }: { params: { username: string } }): Promise<Metadata> {
     const supabase = createClient()
     const { data: profile } = await supabase
       .from('profiles')
       .select('artist_name, tagline, profile_image_url')
       .eq('artist_name', params.username)
       .eq('is_published', true)
       .single()

     if (!profile) {
       return { title: 'Perfil no encontrado' }
     }

     return {
       title: `${profile.artist_name} - Preskit.AR`,
       description: profile.tagline || `Perfil de ${profile.artist_name}`,
       openGraph: {
         title: profile.artist_name,
         description: profile.tagline || '',
         images: profile.profile_image_url ? [profile.profile_image_url] : [],
         url: `https://preskit.ar/${profile.artist_name}`,
       },
       twitter: {
         card: 'summary_large_image',
         title: profile.artist_name,
         description: profile.tagline || '',
         images: profile.profile_image_url ? [profile.profile_image_url] : [],
       },
     }
   }

   export default async function PerfilPublico({ params }: { params: { username: string } }) {
     const supabase = createClient()

     // Obtener perfil
     const { data: profile, error } = await supabase
       .from('profiles')
       .select('*')
       .eq('artist_name', params.username)
       .eq('is_published', true)
       .single()

     if (error || !profile) {
       notFound()
     }

     // Obtener componentes
     const { data: components } = await supabase
       .from('profile_components')
       .select('*')
       .eq('profile_id', profile.id)
       .eq('is_visible', true)
       .order('position', { ascending: true })

     return (
       <div className="min-h-screen">
         <PerfilPublicoView
           theme={profile.theme_settings}
           components={components || []}
           profile={profile}
         />
       </div>
     )
   }
   ```

2. Crear `app/(publico)/[username]/not-found.tsx`
   ```typescript
   export default function PerfilNotFound() {
     return (
       <div className="min-h-screen flex items-center justify-center">
         <div className="text-center">
           <div className="text-6xl mb-4">🎧</div>
           <h1 className="text-2xl font-bold mb-2">Este perfil no existe</h1>
           <p className="text-gray-600 mb-6">
             ¿Querés crear el tuyo?
           </p>
           <a
             href="/registrarse"
             className="px-6 py-3 bg-black text-white rounded-lg hover:bg-gray-800"
           >
             Registrate acá
           </a>
         </div>
       </div>
     )
   }
   ```

3. Crear página "En construcción" para perfiles borradores
   - Detectar en middleware si perfil existe pero no está publicado
   - Mostrar página especial

**Criterios de Aceptación**:
- ✅ Página renderiza perfil publicado correctamente
- ✅ ISR revalida cada 60 segundos
- ✅ Meta tags Open Graph funcionan
- ✅ Página 404 custom si perfil no existe
- ✅ Página "En construcción" si perfil en borrador

**Tests**:
```typescript
// app/(publico)/[username]/page.test.tsx
describe('Perfil Público', () => {
  it('renderiza perfil publicado')
  it('retorna 404 si perfil no existe')
  it('retorna 404 si perfil no está publicado')
  it('aplica theme correctamente')
  it('renderiza componentes en orden')
  it('meta tags Open Graph están presentes')
})
```

**Dependencias**: Fase 20

**Salida**:
- Perfiles públicos funcionando
- SSG + ISR configurado
- Meta tags implementados

---

### Fase 22: Componentes Públicos - Reproductor de Audio 🎵
**Objetivo**: Componente de audio player con soporte para archivos privados

**Prerequisitos**: Fase 21 completada

**Tareas**:
1. Crear `components/perfil/AudioPlayerPublico.tsx`
   ```typescript
   'use client'

   import { useState, useEffect, useRef } from 'react'
   import { Button } from '@/components/ui/button'

   interface AudioPlayerPublicoProps {
     data: {
       title: string
       file_id: string
     }
     theme: any
     isPreview?: boolean
   }

   export function AudioPlayerPublico({ data, theme, isPreview = false }: AudioPlayerPublicoProps) {
     const [audioUrl, setAudioUrl] = useState<string | null>(null)
     const [loading, setLoading] = useState(true)
     const [isPlaying, setIsPlaying] = useState(false)
     const audioRef = useRef<HTMLAudioElement>(null)

     useEffect(() => {
       async function fetchAudioUrl() {
         if (isPreview) {
           // En preview, no cargar audio real
           setLoading(false)
           return
         }

         try {
           // Obtener info del archivo
           const fileRes = await fetch(`/api/archivos/${data.file_id}`)
           const fileData = await fileRes.json()

           if (fileData.is_private) {
             // Obtener token JWT
             const tokenRes = await fetch('/api/tokens/acceso-privado', {
               method: 'POST',
               headers: { 'Content-Type': 'application/json' },
               body: JSON.stringify({ fileId: data.file_id }),
             })
             const { streamUrl } = await tokenRes.json()
             setAudioUrl(streamUrl)
           } else {
             // Archivo público
             setAudioUrl(`/api/media/${data.file_id}`)
           }

           setLoading(false)
         } catch (error) {
           console.error('Error cargando audio:', error)
           setLoading(false)
         }
       }

       fetchAudioUrl()
     }, [data.file_id, isPreview])

     const togglePlay = () => {
       if (!audioRef.current) return

       if (isPlaying) {
         audioRef.current.pause()
       } else {
         audioRef.current.play()
       }
       setIsPlaying(!isPlaying)
     }

     if (loading) {
       return <div className="py-4">Cargando audio...</div>
     }

     if (isPreview) {
       return (
         <div className="border rounded-lg p-4" style={{ borderColor: theme.accentColor }}>
           <h3 className="font-semibold mb-2">{data.title}</h3>
           <div className="text-sm text-gray-500">
             🎵 Preview de reproductor de audio
           </div>
         </div>
       )
     }

     return (
       <div className="border rounded-lg p-4" style={{ borderColor: theme.accentColor }}>
         <h3 className="font-semibold mb-2">{data.title}</h3>
         <audio
           ref={audioRef}
           src={audioUrl || undefined}
           controls
           controlsList="nodownload"
           preload="metadata"
           className="w-full"
           onPlay={() => setIsPlaying(true)}
           onPause={() => setIsPlaying(false)}
         >
           Tu navegador no soporta audio HTML5.
         </audio>
       </div>
     )
   }
   ```

**Criterios de Aceptación**:
- ✅ Reproduce archivos públicos sin token
- ✅ Obtiene token JWT para archivos privados
- ✅ Controles HTML5 nativos funcionan
- ✅ Preview mode muestra placeholder
- ✅ `controlsList="nodownload"` impide descarga

**Tests**:
```typescript
// components/perfil/AudioPlayerPublico.test.tsx
describe('AudioPlayerPublico', () => {
  it('carga y reproduce archivo público')
  it('obtiene token para archivo privado')
  it('muestra placeholder en preview mode')
  it('controles HTML5 funcionan')
  it('no permite descarga (controlsList)')
})
```

**Dependencias**: Fase 21

**Salida**:
- Reproductor de audio funcional
- Soporte para archivos privados

---

### Fase 23: Landing Page + Onboarding Completo 🚀
**Objetivo**: Landing page profesional + checklist de publicación + plantilla inicial

**Prerequisitos**: Fase 22 completada

**CORRECCIÓN GAP 5**: Checklist y plantilla inicial son críticos para onboarding exitoso.

**Tareas**:
1. Crear `app/page.tsx` (landing page)
   ```typescript
   import { Hero } from '@/components/landing/Hero'
   import { Features } from '@/components/landing/Features'
   import { Ejemplos } from '@/components/landing/Ejemplos'
   import { CTA } from '@/components/landing/CTA'

   export default function LandingPage() {
     return (
       <main>
         <Hero />
         <Features />
         <Ejemplos />
         <CTA />
       </main>
     )
   }
   ```

2. Crear componentes de landing:
   - `Hero.tsx`: Presentación con CTA principal
   - `Features.tsx`: Grid de features principales
   - `Ejemplos.tsx`: 3 perfiles ejemplo destacados
   - `CTA.tsx`: Call to action final

3. Mejorar flow de registro:
   - Agregar validación en tiempo real de alias
   - Preview de URL mientras escribe: `preskit.ar/{alias}`
   - Selección de géneros con autocomplete

4. Implementar checklist de publicación
   ```typescript
   // lib/checklist.ts
   export interface ChecklistItem {
     id: string
     label: string
     completed: boolean
     required: boolean
   }

   export function calcularChecklist(profile: any, components: any[]): ChecklistItem[] {
     return [
       {
         id: 'foto_perfil',
         label: 'Agregar foto de perfil',
         completed: !!profile.profile_image_url,
         required: true,
       },
       {
         id: 'bio',
         label: 'Completar biografía',
         completed: !!profile.bio && profile.bio.length > 20,
         required: true,
       },
       {
         id: 'componente',
         label: 'Agregar al menos un elemento (botón, audio, etc.)',
         completed: components.length > 0,
         required: true,
       },
       {
         id: 'audio',
         label: 'Subir un track o set (recomendado)',
         completed: components.some(c => c.type === 'audio_player'),
         required: false,
       },
       {
         id: 'contacto',
         label: 'Agregar información de contacto',
         completed: !!profile.contact_email || !!profile.contact_whatsapp,
         required: false,
       },
       {
         id: 'colores',
         label: 'Personalizar colores y fuente',
         completed: profile.theme_settings?.backgroundColor !== '#ffffff',
         required: false,
       },
     ]
   }

   export function puedePublicar(checklist: ChecklistItem[]): boolean {
     return checklist.filter(item => item.required).every(item => item.completed)
   }
   ```

5. Integrar checklist en EditorToolbar
   ```typescript
   // components/editor/EditorToolbar.tsx - ACTUALIZAR
   import { calcularChecklist, puedePublicar } from '@/lib/checklist'
   import { Popover, PopoverContent, PopoverTrigger } from '@/components/ui/popover'
   import { CheckCircle2, Circle } from 'lucide-react'

   export function EditorToolbar({ profile }: { profile: any }) {
     const { components } = useEditorStore()
     const checklist = calcularChecklist(profile, components)
     const canPublish = puedePublicar(checklist)
     const completados = checklist.filter(i => i.completed).length

     return (
       <div className="bg-white border-b px-6 py-3 flex items-center justify-between">
         <div>
           <h1 className="text-xl font-bold">{profile.artist_name}</h1>
           <p className="text-sm text-gray-500">preskit.ar/{profile.artist_name}</p>
         </div>

         <div className="flex gap-3 items-center">
           {/* Progreso de checklist */}
           <Popover>
             <PopoverTrigger asChild>
               <Button variant="ghost" size="sm">
                 {completados}/{checklist.length} completado
               </Button>
             </PopoverTrigger>
             <PopoverContent className="w-80">
               <h3 className="font-semibold mb-2">Checklist de publicación</h3>
               <div className="space-y-2">
                 {checklist.map(item => (
                   <div key={item.id} className="flex items-center gap-2">
                     {item.completed ? (
                       <CheckCircle2 className="h-5 w-5 text-green-600" />
                     ) : (
                       <Circle className="h-5 w-5 text-gray-300" />
                     )}
                     <span className={item.completed ? 'line-through text-gray-500' : ''}>
                       {item.label}
                       {item.required && <span className="text-red-500 ml-1">*</span>}
                     </span>
                   </div>
                 ))}
               </div>
               {!canPublish && (
                 <p className="text-xs text-gray-500 mt-3">
                   * Completá los items obligatorios para publicar
                 </p>
               )}
             </PopoverContent>
           </Popover>

           <Button variant="outline" onClick={handleSave}>
             Guardar
           </Button>
           <Button onClick={handlePublish} disabled={!canPublish}>
             {isPublished ? 'Despublicar' : 'Publicar'}
           </Button>
         </div>
       </div>
     )
   }
   ```

6. Crear plantilla inicial post-registro
   ```typescript
   // lib/plantilla-inicial.ts
   export async function crearPlantillaInicial(userId: string, profileId: string) {
     const supabase = createClient()

     // Componente 1: Texto de bienvenida
     await supabase.from('profile_components').insert({
       profile_id: profileId,
       type: 'text_block',
       position: 0,
       is_visible: true,
       data: {
         content: '<p>¡Hola! Soy [Tu Nombre Artístico] 👋</p><p>Bienvenido a mi press kit digital.</p>',
         format: 'html',
       },
     })

     // Componente 2: Botón placeholder
     await supabase.from('profile_components').insert({
       profile_id: profileId,
       type: 'button',
       position: 1,
       is_visible: true,
       data: {
         text: 'Escuchá mis sets en SoundCloud',
         url: 'https://soundcloud.com/tu-usuario',
         icon: '🎧',
       },
     })

     // Theme inicial (colores neutros)
     await supabase.from('profiles').update({
       theme_settings: {
         backgroundColor: '#ffffff',
         textColor: '#1a1a1a',
         accentColor: '#000000',
         buttonStyle: {
           backgroundColor: '#000000',
           textColor: '#ffffff',
           borderColor: '#000000',
           borderWidth: 2,
           hasShadow: true,
           shadowColor: 'rgba(0,0,0,0.1)',
         },
         fontFamily: 'Inter',
         isDarkMode: false,
       },
     }).eq('id', profileId)
   }
   ```

7. Invocar plantilla en trigger de registro
   ```sql
   -- En migración 007_auth_triggers.sql - ACTUALIZAR
   CREATE FUNCTION handle_new_user() RETURNS TRIGGER AS $$
   DECLARE
     new_profile_id UUID;
   BEGIN
     -- Crear profile
     INSERT INTO profiles (user_id, artist_name, full_name, birth_date, genres)
     VALUES (NEW.id, NEW.raw_user_meta_data->>'artist_name', ...)
     RETURNING id INTO new_profile_id;

     -- Crear storage_usage
     INSERT INTO storage_usage (user_id, plan, total_limit_mb, audio_files_limit)
     VALUES (NEW.id, 'free', 50, 1);

     -- Llamar a función de plantilla inicial (desde API)
     PERFORM pg_notify('new_user_registered', json_build_object(
       'user_id', NEW.id,
       'profile_id', new_profile_id
     )::text);

     RETURN NEW;
   END;
   $$ LANGUAGE plpgsql SECURITY DEFINER;
   ```

   Alternativa: Llamar directamente desde API de registro:
   ```typescript
   // app/api/auth/registrarse/route.ts - ACTUALIZAR
   const { data: { user } } = await supabase.auth.signUp(...)

   if (user) {
     // Esperar a que trigger cree profile
     await new Promise(resolve => setTimeout(resolve, 500))

     // Obtener profile_id
     const { data: profile } = await supabase
       .from('profiles')
       .select('id')
       .eq('user_id', user.id)
       .single()

     if (profile) {
       await crearPlantillaInicial(user.id, profile.id)
     }
   }
   ```

8. Crear página de bienvenida `/bienvenida/page.tsx`
   ```typescript
   'use client'

   import { useRouter } from 'next/navigation'
   import { Button } from '@/components/ui/button'

   export default function BienvenidaPage() {
     const router = useRouter()

     return (
       <div className="min-h-screen flex items-center justify-center p-6">
         <div className="max-w-2xl text-center">
           <h1 className="text-4xl font-bold mb-4">¡Listo! Tu perfil fue creado 🎉</h1>
           <p className="text-lg text-gray-600 mb-8">
             Ahora podés personalizar tu press kit y compartirlo con promoters, medios y fans.
           </p>

           <div className="bg-blue-50 border border-blue-200 rounded-lg p-6 mb-8">
             <h2 className="font-semibold mb-3">Próximos pasos:</h2>
             <ul className="text-left space-y-2 text-sm">
               <li>✅ Agregá tu foto de perfil</li>
               <li>✅ Escribí una bio que te represente</li>
               <li>✅ Subí tu mejor set o track</li>
               <li>✅ Personalizá colores y fuentes</li>
               <li>✅ Publicá tu perfil cuando esté listo</li>
             </ul>
           </div>

           <Button size="lg" onClick={() => router.push('/usuario/[username]')}>
             Empezar a customizar
           </Button>
         </div>
       </div>
     )
   }
   ```

**Criterios de Aceptación**:
- ✅ Landing page renderiza correctamente
- ✅ Ejemplos destacados visibles
- ✅ CTAs funcionan
- ✅ Flow de registro completo y pulido
- ✅ Checklist de publicación funciona en editor
- ✅ Botón "Publicar" se deshabilita si faltan items obligatorios
- ✅ Plantilla inicial se crea automáticamente post-registro
- ✅ Página de bienvenida guía al usuario
- ✅ Usuario nuevo ve perfil pre-poblado (no vacío)

**Tests**:
```typescript
// app/page.test.tsx
describe('Landing Page', () => {
  it('renderiza hero')
  it('renderiza features')
  it('renderiza ejemplos')
  it('CTAs redirigen correctamente')
})

// lib/checklist.test.ts
describe('Checklist de publicación', () => {
  it('calcula items completados correctamente')
  it('puedePublicar retorna false si faltan obligatorios')
  it('puedePublicar retorna true con todos los obligatorios')
  it('items opcionales no bloquean publicación')
})

// lib/plantilla-inicial.test.ts
describe('Plantilla inicial', () => {
  it('crea 2 componentes placeholder')
  it('crea theme por defecto')
  it('componentes son visibles')
  it('componentes tienen position correcta')
})
```

**Dependencias**: Fase 22

**Salida**:
- Landing page completa
- Flow de registro pulido
- Checklist de publicación activo
- Plantilla inicial funcionando
- Onboarding completo implementado

---

## 🎯 HITOS FUNCIONALES

### Hito 0: Infraestructura Base ✅
**Fases incluidas**: Fase 0
**Objetivo**: Sistema corriendo en local + preview + Supabase conectado

**Tests del hito**:
```bash
# scripts/test-hito-0.sh
npm run build
npm run dev &
sleep 5
curl -f http://localhost:3000 || exit 1
echo "✅ Hito 0 completado"
```

---

### Hito 1: Database y Autenticación Completa ✅
**Fases incluidas**: Fases 1-4
**Objetivo**: DB completa, auth funcional (email + Google), RLS protegiendo datos

**Tests del hito**:
```bash
# scripts/test-hito-1.sh
# Ejecutar todos los tests de auth
npm test -- auth
npm test -- rls
echo "✅ Hito 1 completado"
```

---

### Hito 2: Sistema de Archivos Completo ✅
**Fases incluidas**: Fases 5-10
**Objetivo**: Upload, storage, validación y streaming de archivos funcionando

**Tests del hito**:
```bash
# scripts/test-hito-2.sh
npm test -- archivos
npm test -- tokens
npm test -- streaming
echo "✅ Hito 2 completado"
```

---

### Hito 3: API de Perfiles y Componentes ✅
**Fases incluidas**: Fases 11-15
**Objetivo**: CRUD completo de perfiles y componentes

**Tests del hito**:
```bash
# scripts/test-hito-3.sh
npm test -- perfil
npm test -- componentes
echo "✅ Hito 3 completado"
```

---

### Hito 4: Editor y Perfil Público ✅
**Fases incluidas**: Fases 16-23
**Objetivo**: Editor visual + perfiles públicos funcionando

**Tests del hito**:
```bash
# scripts/test-hito-4.sh
npm test -- editor
npm test -- perfil-publico
npm run build
echo "✅ Hito 4 completado - MVP FUNCIONAL"
```

---

## ✅ SISTEMA DE VERIFICACIÓN

### Verificación por Fase

Cada fase debe pasar:

1. **Implementación completa**: Todas las tareas marcadas como ✅
2. **Tests unitarios**: Escritos y pasando
3. **CODE-GUARDIAN**: Status "pass" (0 errores bloqueantes)
4. **TEST-MAESTRO**: Coverage adecuado (>80% para utils, >70% para components)
5. **Documentación**: DocsCreator documentó la fase en memoria/

### Verificación por Hito

Cada hito debe pasar:

1. **Todas las fases del hito completadas**
2. **Tests de integración del hito**: Script `test-hito-N.sh` exitoso
3. **Build exitoso**: `npm run build` sin errores
4. **RELEASE-CONDUCTOR**: Validación pre-deploy OK (si aplica)
5. **Deploy preview funcional**: Smoke tests pasando

---

## 📈 CRITERIOS DE AVANCE

### NO avanzar a siguiente fase si:
- ❌ Hay tests fallando
- ❌ CODE-GUARDIAN reporta errores bloqueantes
- ❌ Funcionalidad no está completa
- ❌ Documentación falta

### Sí avanzar a siguiente fase si:
- ✅ Todos los tests pasan
- ✅ CODE-GUARDIAN status "pass" o "warning" (warnings son aceptables)
- ✅ TEST-MAESTRO coverage adecuado
- ✅ Funcionalidad validada manualmente
- ✅ DocsCreator documentó todo

### Checkpoint de Hito:
- Ejecutar `test-hito-N.sh`
- Invocar RELEASE-CONDUCTOR para validación completa
- Deploy a preview
- Smoke tests post-deploy
- Si todo OK: Continuar siguiente hito
- Si falla: Identificar fase problemática y corregir

---

## 📝 NOTAS FINALES

### Este plan es:
- ✅ Granular y detallado
- ✅ Modular y testeable
- ✅ Con dependencias claras
- ✅ Con criterios de aceptación específicos
- ✅ Con tests definidos por TEST-MAESTRO

### Workflow de trabajo:
1. Elegir fase N del plan
2. Dividir fase en sprints/tareas específicas (crear `docs/fases/FASE_N.md`)
3. Invocar TEST-MAESTRO para definir tests
4. Implementar código (Claude principal)
5. Ejecutar tests
6. Invocar CODE-GUARDIAN
7. Corregir errores si los hay
8. Invocar DOCS-CREATOR
9. Commit con RELEASE-CONDUCTOR (si aplica)
10. Avanzar a fase N+1

### Próximos pasos después de crear este plan:
1. Revisar plan completo con el usuario
2. Confirmar metodología y fases
3. Comenzar Fase 0: Setup Infraestructura
4. Crear documento `docs/fases/FASE_0.md` detallado
5. Implementar Fase 0 paso a paso

---

**Plan creado**: 2025-09-30
**Última actualización**: 2025-09-30
**Versión**: 1.2 (Corregida - 2da revisión GPT5)
**Estado**: ✅ LISTO PARA IMPLEMENTACIÓN - Todos los gaps resueltos

**Revisiones completadas**: 2 rondas de auditoría GPT5
**Total de correcciones**: 15 gaps + 3 riesgos técnicos = **18 correcciones aplicadas**
**Total de fases**: 26 (23 originales + 3 nuevas: 8bis, 9bis, 18bis)
