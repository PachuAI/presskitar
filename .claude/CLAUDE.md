# 🔧 Reglas del Proyecto - presskit.ar

**Versión**: 1.0
**Fecha**: 2025-09-30

---

## 📖 Documentación Obligatoria

**Antes de empezar, leer:**
@docs/manual-uso-agentes-comandos.md

Este manual contiene el workflow oficial con todos los comandos y casos de uso.

---

## 🌐 Idioma del Proyecto

**Idioma principal**: Español

### Reglas de Idioma

**✅ Usar ESPAÑOL en:**
- Código de aplicación (variables, funciones, comentarios)
- Nombres de variables de negocio (`artistName`, `nombreArtista`, `perfilPublicado`)
- Comentarios en código
- Mensajes de error al usuario
- Documentación de features y funcionalidades
- Commits (español o inglés, preferentemente español)

**✅ Usar INGLÉS en:**
- Términos técnicos IT estándar (`useState`, `useEffect`, `async`, `await`)
- Nombres de componentes React (`ProfileEditor`, `AudioUploader`)
- Nombres de tipos TypeScript (`ProfileData`, `ThemeSettings`)
- Nombres de archivos de código (`profile-editor.tsx`, `use-auth.ts`)
- Librerías y frameworks (Next.js, Supabase, Zod, etc.)
- Documentación técnica cuando sea más claro (endpoints: `GET /api/perfil`)

**Ejemplo de código:**
```typescript
// ✅ CORRECTO: Mezcla natural de español e inglés técnico
async function obtenerPerfilDeArtista(artistName: string): Promise<ProfileData> {
  const { data: perfil, error } = await supabase
    .from('profiles')
    .select('*')
    .eq('artist_name', artistName)
    .single()

  if (error) {
    throw new Error('No se pudo obtener el perfil del artista')
  }

  return perfil
}

// ❌ INCORRECTO: Todo en inglés sin razón técnica
async function getArtistProfile(artistName: string): Promise<ProfileData> {
  const { data: profile, error } = await supabase
    .from('profiles')
    .select('*')
    .eq('artist_name', artistName)
    .single()

  if (error) {
    throw new Error('Could not fetch artist profile')
  }

  return profile
}
```

---

## 🛠️ Stack Técnico

### Framework y Librerías
- **Next.js 14** (App Router)
- **React 18** (Server Components + Client Components)
- **TypeScript** (strict mode)
- **Supabase**:
  - PostgreSQL (database)
  - Auth (email + Google OAuth)
  - Storage (archivos de usuarios)

### Estado y Validación
- **Zod** - Validación de schemas (OBLIGATORIO para todas las APIs)
- **Zustand + Immer** - Estado global del editor
- **React Hook Form** - Formularios

### UI y Estilos
- **shadcn/ui** - Componentes base
- **Tailwind CSS** - Estilos
- **Lucide React** - Iconos

### Testing
- **Vitest** - Unit tests
- **Playwright** - E2E tests
- **Testing Library** - React component tests

### Infraestructura
- **Vercel** - Hosting y preview deploys
- **Vercel KV** - Rate limiting
- **Sharp** - Procesamiento de imágenes (blur placeholders)

---

## 📝 Convenciones de Código

### Nombres de Variables
- **Variables de negocio**: Español (`artistName`, `nombreArtista`, `perfilPublicado`)
- **Componentes React**: Inglés (`ProfileEditor`, `AudioUploader`)
- **Funciones de utilidad**: Inglés (`sanitizeHtml`, `generateBlurHash`)
- **Endpoints API**: Español (`/api/perfil`, `/api/archivos`)

### Estructura de Archivos
```
app/
├── (auth)/           # Rutas de autenticación
├── (public)/         # Rutas públicas (perfiles)
├── (dashboard)/      # Rutas protegidas (editor)
└── api/              # API routes

components/
├── ui/               # shadcn/ui base components
├── editor/           # Componentes del editor
└── public/           # Componentes públicos (perfil view)

lib/
├── validations/      # Schemas Zod
├── supabase/         # Clients y helpers
└── utils/            # Utilidades

types/                # TypeScript types globales
```

### Server Actions y API Routes
- **Siempre** usar `"use server"` en server actions
- **Siempre** validar con Zod antes de procesar `request.json()`
- **Nunca** exponer `SUPABASE_SERVICE_ROLE_KEY` en cliente
- **Nunca** confiar en datos del cliente sin validación

```typescript
// ✅ CORRECTO
const body = await request.json()
const result = miSchema.safeParse(body)
if (!result.success) return NextResponse.json({ error: result.error }, { status: 400 })
const { campo1, campo2 } = result.data  // Usar result.data

// ❌ INCORRECTO
const body = await request.json()
const { campo1 } = body  // NO usar body directamente
```

---

## 🔐 Seguridad

### Variables de Entorno
- `NEXT_PUBLIC_*` → Expuestas al cliente (URLs públicas)
- `SUPABASE_SERVICE_ROLE_KEY` → **NUNCA** en cliente, solo en server actions/API routes
- `GOOGLE_CLIENT_SECRET` → Solo server-side

### Row Level Security (RLS)
- **SIEMPRE** usar RLS en Supabase
- **Nunca** confiar en checks del cliente
- Políticas definidas en `supabase/migrations/`

### Sanitización
- **DOMPurify** para HTML user-generated (bio, tagline)
- **Zod** para validar tipos y formatos
- **Sharp** para procesar imágenes (prevenir exploits)

---

## 🎯 Gestión de Fases - Workflow Oficial

### Comandos Principales

#### Inicio de Sesión
```
/arranca
```
→ Lee estado actual, memoria, últimos commits

#### Gestión de Fases
```
/fase          # Ver estado actual
/fase start    # Iniciar fase (genera breakdown + todos)
/fase complete # Completar fase (verifica checkboxes)
/fase list     # Ver todas las fases
```

#### Commits Durante Desarrollo
```
/commit              # Commit automático con mensaje generado
/commit "mensaje"    # Commit con mensaje custom
/commit --suggest    # Solo sugerir mensaje
```

#### Fin de Sesión
```
/recap         # Documentar sesión (SIEMPRE antes de /clear)
```

### Reglas del Workflow

#### ✅ HACER

1. **SIEMPRE** ejecutar `/arranca` al inicio de sesión
2. **SIEMPRE** ejecutar `/recap` antes de `/clear`
3. **SIEMPRE** leer `docs/fases/FASE_N_breakdown.md` para ver subtareas
4. **SIEMPRE** actualizar checkboxes al completar subtareas
5. **SIEMPRE** usar `/commit` después de completar cada subtarea o grupo lógico
6. **SIEMPRE** seguir el plan de implementación (`docs/plan-implementacion.md`)

#### ❌ NO HACER

1. **NO** ejecutar `/fase start` si la fase ya está iniciada
2. **NO** ejecutar `/fase complete` con checkboxes pendientes
3. **NO** modificar `docs/plan-implementacion.md` sin consenso
4. **NO** saltear fases sin completarlas (solo para debugging)
5. **NO** hacer `/clear` sin antes hacer `/recap`
6. **NO** pushear código sin tests (si la fase tiene 🧪)

---

## 🧪 Tests

### Cuándo Escribir Tests
- **Obligatorio**: Fases marcadas con 🧪 en el plan
- **Recomendado**: Funciones críticas (validaciones, autenticación, uploads)
- **Opcional**: Componentes simples de UI

### Tipos de Tests
```typescript
// Unit Tests (Vitest)
// lib/validations/__tests__/perfil.test.ts
describe('perfilSchema', () => {
  it('valida perfil correcto')
  it('rechaza artist_name inválido')
})

// Component Tests (Testing Library)
// components/editor/__tests__/PanelCustomizacion.test.tsx
describe('PanelCustomizacion', () => {
  it('actualiza backgroundColor en store')
})

// E2E Tests (Playwright)
// e2e/auth.spec.ts
test('usuario puede registrarse con email', async ({ page }) => {
  // ...
})
```

### Ejecución
```bash
npm test              # Unit + component tests
npm run test:e2e      # Playwright E2E
npm run test:hito-1   # Tests de hito completo
```

---

## 📦 Commits

### Comando `/commit`

**Usar `/commit` para todos los commits durante desarrollo:**

```bash
/commit              # Commit automático con mensaje generado
/commit "mensaje"    # Commit con mensaje custom
/commit --suggest    # Solo sugerir mensaje (no commitea)
```

**Qué hace `/commit`:**
- Analiza cambios con git status y git diff
- Lee estado actual de la fase
- Genera mensaje semántico siguiendo conventional commits
- Commitea automáticamente

**Ver detalles**: `.claude/commands/commit.md`

### Formato de Mensajes (Conventional Commits)

```bash
# Subtareas de fases
feat(fase-N): descripción breve de subtarea
fix(fase-N): corregir bug en componente X
test(fase-N): agregar tests de validación

# Completar fase
feat: completar Fase N - Nombre de la Fase

# Completar hito
feat: completar Hito N - Nombre del Hito

# Documentación
docs: actualizar manual con flujo de commits
```

### Frecuencia
- **Cada subtarea** o grupo lógico de subtareas → `/commit`
- **Al completar fase** (después de `/fase complete`) → `/commit`
- **Antes de `/recap`** (para que aparezca en git log)

---

## 🚀 Deploy y Checkpoints

### Preview Deploys
- Automático en cada push a Vercel
- URL: `presskit-ar-[branch].vercel.app`

### Checkpoint de Hito
Al completar todas las fases de un hito:

1. ✅ Ejecutar tests completos del hito
2. ✅ Deploy a preview
3. ✅ Smoke tests manuales
4. ✅ Actualizar `docs/estado-proyecto.json`:
   ```json
   { "hitos_completados": [1] }
   ```
5. ✅ Commit y tag:
   ```bash
   git commit -m "feat: completar Hito 1 - Database y Autenticación"
   git tag v0.1-hito-1
   git push --tags
   ```

---

## 🎨 UI/UX

### Componentes shadcn/ui
- Usar componentes de `components/ui/` (generados por shadcn)
- NO modificar componentes base, extenderlos si necesario
- Customización de tema en `tailwind.config.ts`

### Responsive
- Mobile-first approach
- Breakpoints: `sm:` (640px), `md:` (768px), `lg:` (1024px)
- Editor: Desktop only (min-width: 1024px)
- Perfiles públicos: Fully responsive

### Accesibilidad
- Usar componentes semánticos de shadcn/ui (ya tienen ARIA)
- Labels en todos los inputs
- Alt text en imágenes
- Contraste adecuado (WCAG AA)

---

## 📚 Referencias Rápidas

| Documento | Propósito |
|-----------|-----------|
| `docs/manual-uso-agentes-comandos.md` | Workflow oficial completo |
| `docs/plan-implementacion.md` | Plan de 26 fases |
| `docs/estado-proyecto.json` | Estado técnico actual |
| `memoria/estado.md` | Estado narrativo |
| `memoria/proximos-pasos.md` | Qué hacer ahora |
| `docs/PRD.md` | Product Requirements |
| `docs/arquitectura.md` | Decisiones técnicas |

---

## 🔄 Ciclo Típico de Trabajo

```
1. /arranca
   → "Estás en Fase 5, continuar subtarea 4"

2. [Leo docs/fases/FASE_5_breakdown.md]
   → Veo qué falta

3. [Implemento subtarea]
   → Escribo código + tests

4. [Actualizo checkbox en breakdown.md]
   → - [x] 4. Subtarea completada

5. /commit
   → "feat(fase-5): implementar subtarea 4"

6. [Repito 3-5 hasta terminar fase]

7. /fase complete
   → "✅ Fase 5 completada"

8. /commit
   → "feat: completar Fase 5 - API Gestión"

9. /recap
   → "✅ Sesión documentada"

10. /clear
```

---

**Última actualización**: 2025-09-30
**Mantenido por**: Claude Code + Usuario

**Si tenés dudas, leé primero el manual:**
@docs/manual-uso-agentes-comandos.md
