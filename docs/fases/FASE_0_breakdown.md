# Fase 0: Setup de Infraestructura - Breakdown

**Objetivo**: Tener el proyecto corriendo en local + Vercel preview + Supabase configurado

**Prerequisitos**:
- Cuenta Vercel (nueva o existente)
- Cuenta Supabase (nueva o existente)
- Cuenta Google Cloud (para OAuth)
- Dominio preskit.ar apuntando a Vercel

**Hito**: 0 - Infraestructura Base

---

## Subtareas

- [x] 1. Buscar y seleccionar boilerplate Next.js 14 + Supabase (TypeScript, shadcn/ui compatible, OAuth)
- [x] 2. Crear proyecto en Vercel y conectar con GitHub repo
- [x] 3. Crear proyecto Supabase (preskit-ar-production) y obtener credenciales
- [ ] 4. Configurar OAuth Google (Cloud Console + credenciales)
- [x] 5. Configurar variables de entorno (`.env.local` + Vercel dashboard + `.env.example`)
- [ ] 6. Setup Git (branches main/develop)
- [x] 7. Validar que todo funciona (npm install, npm run dev, preview deploy)

## Progreso: 5/7 (71%)

---

## Criterios de Aceptación

- [ ] `npm install` completa sin errores
- [ ] `npm run dev` inicia servidor en `http://localhost:3000`
- [ ] Preview deploy exitoso en Vercel
- [ ] Supabase client conecta correctamente
- [ ] OAuth Google redirige correctamente
- [ ] Variables de entorno configuradas en local y Vercel

---

## Tests/Validación

Crear script `scripts/verify-setup.sh`:

```bash
#!/bin/bash

# Verificar variables de entorno críticas
test -n "$NEXT_PUBLIC_SUPABASE_URL" || exit 1
test -n "$SUPABASE_SERVICE_ROLE_KEY" || exit 1
test -n "$GOOGLE_CLIENT_ID" || exit 1

# Verificar build exitoso
npm run build || exit 1

echo "✅ Setup validado correctamente"
```

---

## Archivos a Crear/Modificar

- `.env.local` (nuevo - con credenciales reales)
- `.env.example` (nuevo - template sin valores)
- `scripts/verify-setup.sh` (nuevo)
- `README.md` (actualizar con instrucciones de setup)

---

## Credenciales Necesarias

### Supabase
- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY`

### Google OAuth
- `GOOGLE_CLIENT_ID`
- `GOOGLE_CLIENT_SECRET`

### URLs de Redirect OAuth
- `http://localhost:3000/api/auth/callback`
- `https://preskit-ar-preview.vercel.app/api/auth/callback`

---

## Notas

- Git ya está inicializado (commit inicial ya hecho)
- Dominio preskit.ar se configurará en fase posterior
- Boilerplate debe tener shadcn/ui pre-configurado
- Verificar que Supabase esté en región South America o US East

---

**Dependencias**: Ninguna (fase inicial)

**Salida esperada**:
- Proyecto base funcionando
- Credenciales documentadas
- README actualizado
