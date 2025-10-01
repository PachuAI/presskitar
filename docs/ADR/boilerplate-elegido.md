# Boilerplate Elegido para Preskit.AR

**Fecha de decisión:** 2025-03-30
**Proyecto:** Preskit.AR - SaaS para DJs y productores de música electrónica

---

## 🎯 DECISIÓN FINAL

**Boilerplate seleccionado:** `launch-mvp-stripe-nextjs-supabase`

- **Repositorio:** https://github.com/ShenSeanChen/launch-mvp-stripe-nextjs-supabase
- **Stars:** 864
- **Licencia:** Open source
- **Última actualización:** 2024-2025 (commits recientes)

---

## 📋 CONTEXTO DEL PROYECTO

**Preskit.AR** es un SaaS que centraliza la presencia digital de artistas de música electrónica. Similar a Linktree pero permite mostrar ARTE (archivos de audio MP3 propios, videos embebidos, PDF de press kit descargable).

### Requisitos técnicos críticos del MVP:

1. **Autenticación robusta:**
   - Email/password + OAuth Google
   - Email verification obligatoria
   - Password reset flow
   - Session management seguro

2. **Base de datos (Supabase):**
   - PostgreSQL
   - Row Level Security (RLS) policies
   - Schema para users/profiles
   - Migraciones configuradas

3. **File upload a Supabase Storage:**
   - Archivos MP3 (hasta 7 min, 320kbps)
   - PDFs de press kit
   - Imágenes de perfil
   - Validaciones de tipo y tamaño
   - URLs públicas/privadas

4. **Stack técnico definido:**
   - Next.js 14 (estable)
   - Supabase (backend-as-a-service)
   - shadcn/ui (componentes UI)
   - TypeScript
   - Deployable en Vercel

5. **Features del MVP:**
   - Editor drag & drop con preview en tiempo real
   - URLs públicas personalizadas: `preskit.ar/nombreartista`
   - Sistema publicado/borrador
   - Customización visual (colores, fuentes, botones)

---

## 🏆 POR QUÉ ESTE BOILERPLATE

### Criterio de evaluación aplicado:

**Prioridad absoluta:** Funcionalidad técnicamente compleja > Features visuales

El criterio se basó en **ahorro de tiempo en features complejas y riesgosas**:
- Autenticación robusta (OAuth, email verification, password reset)
- Integración con Supabase completa
- Estabilidad del stack (Next.js 14 vs 15)
- Comunidad activa para soporte

**Contexto del desarrollador:**
- 1 desarrollador usando agentes IA como guía
- Prioridad: Estabilidad > Flexibilidad
- Mejor "aburrido pero sólido" que "flashy pero incompleto"

---

## ✅ FEATURES QUE INCLUYE EL BOILERPLATE

### Autenticación (Tier 1 - Crítico):
- ✅ Email/password implementado
- ✅ OAuth Google configurado y funcionando
- ✅ Magic Links incluidos
- ✅ Password reset flow completo
- ✅ Email verification routes presentes (`/verify-email/`)
- ✅ Session management con cookies seguras
- ⚠️ Refresh tokens no mencionados explícitamente

### Base de Datos (Tier 1 - Crítico):
- ✅ Supabase PostgreSQL configurado
- ✅ Schema estructurado para users y profiles
- ✅ User trials table incluida
- ✅ Trigger automático para crear profiles
- ✅ Row Level Security (RLS) mencionado
- ⚠️ No hay ejemplos de queries complejas (no crítico)

### Infraestructura (Tier 2):
- ✅ Next.js 14 App Router (estable)
- ✅ TypeScript completo
- ✅ Desplegable en Vercel
- ✅ Variables de entorno documentadas
- ✅ Setup paso a paso para Supabase, Stripe, Google Cloud
- ✅ Commits recientes (2024-2025)

### Features adicionales (Bonus):
- ✅ Stripe integration completa (útil para plan Premium futuro)
- ✅ React Email con templates bonitos
- ✅ PostHog analytics pre-configurado
- ✅ Framer Motion para animaciones
- ✅ MCP integration (feature 2025)
- ✅ Dark mode support

---

## ❌ LO QUE FALTA (Y ES ACEPTABLE)

### 1. File Upload a Supabase Storage
**Estado:** No implementado
**Esfuerzo estimado:** 2-4 días
**Complejidad:** Baja-Media

**Por qué es aceptable:**
- Supabase Storage SDK es simple y bien documentado
- React Dropzone para drag & drop (librería madura)
- RLS policies tienen ejemplos claros en docs de Supabase
- Con ayuda de IA: 2-4 días realistas

**Plan de implementación:**
- Día 1-2: Upload básico con progress bar
- Día 3: RLS policies para archivos
- Día 4: Validaciones (tipo, tamaño) y error handling

### 2. shadcn/ui
**Estado:** No incluido (usa Tailwind puro)
**Esfuerzo estimado:** 1-2 horas
**Complejidad:** Baja

**Por qué es aceptable:**
- shadcn/ui se instala fácilmente sobre Tailwind
- Documentación oficial excelente
- No requiere reescribir nada existente

---

## 📊 COMPARACIÓN CON ALTERNATIVAS

Se evaluaron **6 boilerplates** en total:

| Boilerplate | Stars | Next.js | Supabase | OAuth | Email Verif. | File Upload | shadcn/ui | Score |
|-------------|-------|---------|----------|-------|--------------|-------------|-----------|-------|
| **launch-mvp** ⭐ | 864 | ✅ 14 | ✅ | ✅ | ✅ | ❌ | ❌ | 76/100 |
| Razikus | 201 | ❌ 15 | ✅ | ✅ | ⚠️ | ✅ | ❌ | 82/100 |
| KolbySisk | 678 | ❌ 15 | ✅ | ❌ | ❌ | ❌ | ✅ | 71/100 |
| Supabase Starter | N/A | ❌ canary | ✅ | ❌ | ❌ | ❌ | ✅ | ~50/100 |
| SaaS Starter (oficial) | 14.5k | ❓ | ❌ Drizzle | ❌ | ❌ | ❌ | ✅ | 52/100 |
| Enterprise (Blazity) | 7.1k | ❌ 15 | ❌ | ❌ | ❌ | ❌ | ❌ | 35/100 |

### Por qué NO las otras opciones:

#### Razikus (82/100 pts):
- ✅ File upload listo (único que lo tiene)
- ✅ MFA implementado
- ❌ **Next.js 15** (menos estable que v14)
- ❌ React 19 (stable desde Dic 2024, solo 3 meses)
- ❌ Menor comunidad (201 stars vs 864)
- ❌ Menos tutoriales/Stack Overflow

**Decisión:** El ahorro de 2-4 días en file upload NO compensa el riesgo de Next.js 15 + React 19 para un proyecto que prioriza estabilidad.

#### KolbySisk (71/100 pts):
- ✅ shadcn/ui incluido
- ✅ Next.js 15
- ❌ **OAuth NO configurado** ("We haven't configured auth providers")
- ❌ Sin email verification
- ❌ Sin file upload
- ⚠️ Última actualización hace 1 año

**Decisión:** Construir OAuth desde cero es más complejo que agregar shadcn/ui.

#### SaaS Starter oficial (52/100 pts):
- ✅ 14,500 stars (comunidad masiva)
- ✅ Oficial de Next.js/Vercel
- ✅ shadcn/ui incluido
- ❌ **NO usa Supabase** (usa Drizzle + Postgres)
- ❌ Auth muy básica (JWT sin OAuth)
- ❌ Sin email verification

**Decisión:** Migrar de Drizzle a Supabase = reescribir toda la capa de datos (7-12 días). Deal-breaker.

#### Supabase Starter (Vercel):
- ✅ Supabase configurado
- ✅ shadcn/ui
- ❌ Example básico, no boilerplate completo
- ❌ Solo password auth (sin OAuth)
- ❌ Sin email verification
- ❌ Sin file upload

**Decisión:** Muy básico, faltarían todas las features de auth robusta.

#### Enterprise (Blazity):
- ✅ 7,100 stars
- ✅ Testing completo
- ✅ Infraestructura enterprise-grade
- ❌ **NO tiene auth implementada**
- ❌ **NO tiene database**
- ❌ Es un scaffold vacío

**Decisión:** Tendrías que construir TODO desde cero (4-6 semanas).

---

## 🎯 TRADE-OFFS ACEPTADOS

### ✅ Aceptamos construir:
1. **File upload** (2-4 días)
2. **shadcn/ui integration** (1-2 horas)

### ✅ Ganamos:
1. **Next.js 14 estable** (vs Next.js 15)
2. **OAuth Google funcionando** (ahorro: 2-3 días)
3. **Email verification listo** (ahorro: 1-2 días)
4. **Comunidad grande** (864 stars = más soporte)
5. **Stripe integrado** (útil para Premium futuro)

### Balance final:
- Tiempo de construcción adicional: 3-5 días
- Tiempo ahorrado en auth: 3-5 días
- **Resultado neto:** Similar o mejor + stack más estable

---

## 🔧 SETUP DEL BOILERPLATE

### Pre-requisitos:
- Node.js 18+
- npm/pnpm/yarn
- Cuenta de Supabase
- Cuenta de Google Cloud (para OAuth)

### Variables de entorno requeridas:
```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=

# Google OAuth
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

# Stripe (opcional en MVP)
STRIPE_SECRET_KEY=
STRIPE_WEBHOOK_SECRET=

# PostHog (opcional)
NEXT_PUBLIC_POSTHOG_KEY=
NEXT_PUBLIC_POSTHOG_HOST=

# Base URL
BASE_URL=http://localhost:3000
```

### Pasos de instalación:
1. Clonar repositorio
2. `npm install`
3. Configurar proyecto Supabase
4. Configurar Google Cloud Console para OAuth
5. Setup variables de entorno
6. Ejecutar migraciones de Supabase
7. `npm run dev`

**Complejidad estimada:** Moderada-Alta
**Tiempo de setup:** 1-2 horas

---

## 📝 DOCUMENTACIÓN Y RECURSOS

### Del boilerplate:
- **README completo:** Setup paso a paso para Supabase, Stripe, Google Cloud
- **Estructura organizada:** Código modular y escalable
- **TypeScript:** Todo tipado

### Recursos externos necesarios:
- Supabase docs: https://supabase.com/docs
- Next.js 14 docs: https://nextjs.org/docs
- Google Cloud Console: https://console.cloud.google.com
- shadcn/ui docs: https://ui.shadcn.com

### Comunidad:
- 864 stars en GitHub
- Issues activas y respondidas
- Commits recientes (2024-2025)

---

## 🚀 PRÓXIMOS PASOS

### Fase 1: Setup inicial (Día 1)
- [ ] Clonar repositorio
- [ ] Configurar Supabase project
- [ ] Setup Google OAuth
- [ ] Ejecutar migraciones
- [ ] Verificar que auth funciona

### Fase 2: Implementar features faltantes (Días 2-5)
- [ ] Implementar file upload a Supabase Storage
- [ ] Configurar RLS policies para archivos
- [ ] Validaciones de tipo y tamaño de archivos
- [ ] Instalar y configurar shadcn/ui

### Fase 3: Customización para Preskit.AR (Semana 2-3)
- [ ] Customizar schema de database según PRD
- [ ] Construir editor drag & drop
- [ ] Implementar preview en tiempo real
- [ ] Sistema de URLs públicas (`preskit.ar/nombreartista`)
- [ ] Sistema publicado/borrador

### Fase 4: Features del MVP (Semana 3-4)
- [ ] Componentes de contenido (botones, texto, audio, video)
- [ ] Customización visual (colores, fuentes, botones)
- [ ] Landing page
- [ ] Onboarding flow

---

## 📐 CONSIDERACIONES PARA ARQUITECTURA

### Database schema a extender:
El boilerplate incluye:
- `users` (via Supabase Auth)
- `profiles` (trigger automático)
- `user_trials` (para Stripe)

**Necesitamos agregar:**
- `artist_profiles` (datos del artista, alias, géneros, etc.)
- `components` (botones, texto, audio, video por perfil)
- `profile_settings` (customización visual)
- `audio_files` (metadata de archivos MP3)
- `pdf_files` (press kits)

### Storage buckets a crear:
- `profile-images` (fotos de perfil)
- `audio-files` (archivos MP3)
- `pdf-files` (press kits)

### RLS policies necesarias:
- Users solo pueden ver/editar sus propios perfiles
- Archivos públicos vs privados (demos unreleased)
- URLs públicas accesibles sin auth

---

## 🎓 LECCIONES APRENDIDAS DEL ANÁLISIS

### 1. Stars no lo son todo:
- SaaS Starter tiene 14.5k stars pero no usa Supabase
- launch-mvp con 864 stars es más útil para nuestro caso

### 2. Features visuales < Features complejas:
- File upload listo (Razikus) no vale la pena si el stack es inestable
- OAuth funcionando > shadcn/ui incluido

### 3. Estabilidad > Últimas features:
- Next.js 14 (1.5 años) > Next.js 15 (4-5 meses stable)
- React 18 (maduro) > React 19 (3 meses stable)

### 4. Comunidad importa:
- 864 stars = suficiente soporte
- Más importante: commits recientes y issues respondidas

### 5. Boilerplates "completos" pueden ser distractores:
- Enterprise (Blazity) tiene todo menos lo que necesitamos
- A veces menos es más

---

## 💡 NOTAS ADICIONALES

### Stripe integration:
El boilerplate incluye Stripe completo. Para MVP no lo necesitamos, pero:
- ✅ Dejarlo configurado no molesta
- ✅ Será útil para plan Premium futuro (definido en PRD)
- ✅ No requiere configuración si no se usa

### PostHog analytics:
Incluido pero opcional:
- Para MVP podemos usar analytics básicos custom
- Activar PostHog cuando lleguemos a 100 usuarios

### MCP integration:
Feature reciente (2025) del boilerplate:
- No es crítico para MVP
- Investigar después si es útil

---

## ✅ VALIDACIÓN DE LA DECISIÓN

**Criterios cumplidos:**
- ✅ Next.js 14 estable
- ✅ Supabase completo
- ✅ OAuth + Email verification
- ✅ Comunidad activa (864 stars)
- ✅ Deployable en Vercel
- ✅ TypeScript
- ✅ Documentación completa

**Trade-offs aceptables:**
- ⚠️ File upload por construir (2-4 días)
- ⚠️ shadcn/ui por agregar (1-2 horas)

**Resultado esperado:**
- Tiempo total de setup + features faltantes: ~1 semana
- Auth robusta desde día 1
- Stack estable y battle-tested
- Base sólida para construir el MVP completo

---

**Decisión confirmada:** ✅ Proceder con `launch-mvp-stripe-nextjs-supabase`

---

*Documento creado: 2025-03-30*
*Proyecto: Preskit.AR MVP*
*Próximo paso: Crear `arquitectura.md` basado en este boilerplate y PRD*