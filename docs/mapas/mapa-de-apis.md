# API Map - Preskit.AR

**Proyecto**: Preskit.AR
**Stack**: Next.js 14 App Router + API Routes
**Última actualización**: 2025-09-30

---

## 📋 ESTADO

📝 **Documentación post-implementación**: Este archivo se actualiza DESPUÉS de implementar cada endpoint.

**Propósito**:
- Catálogo de todos los endpoints implementados en el proyecto
- Fuente de verdad para contratos de API
- Referencia rápida para Claude y desarrolladores

**Cuándo actualizar**:
- ✅ Después de implementar un nuevo endpoint
- ✅ Después de modificar request/response de endpoint existente
- ✅ Después de agregar validaciones o cambios importantes

**NO es para planificación** → Usar PRD.md y memoria/estado.md para eso

---

## 🗺️ ESTRUCTURA PLANEADA (según docs/arquitectura.md)

### Autenticación (`/api/auth/*`)

| Endpoint | Método | Descripción | Estado |
|----------|--------|-------------|--------|
| `/api/auth/registrarse` | POST | Registro con email/password | ⏭️ Planificado |
| `/api/auth/ingresar` | POST | Login | ⏭️ Planificado |
| `/api/auth/salir` | POST | Logout | ⏭️ Planificado |
| `/api/auth/callback` | GET | OAuth callback | ⏭️ Planificado |

### Perfil (`/api/perfil/*`)

| Endpoint | Método | Descripción | Estado |
|----------|--------|-------------|--------|
| `/api/perfil` | GET | Obtener perfil del usuario | ⏭️ Planificado |
| `/api/perfil` | PATCH | Actualizar perfil | ⏭️ Planificado |
| `/api/perfil/publicar` | POST | Toggle publicado/borrador | ⏭️ Planificado |
| `/api/perfil/alias-disponible` | GET | Verificar disponibilidad de alias | ⏭️ Planificado |

### Archivos (`/api/archivos/*`)

| Endpoint | Método | Descripción | Estado |
|----------|--------|-------------|--------|
| `/api/archivos/iniciar-subida` | POST | Pre-check + signed URL | ⏭️ Planificado |
| `/api/archivos/confirmar-subida` | POST | Validación + guardar en DB | ⏭️ Planificado |

### Media (`/api/media/*`)

| Endpoint | Método | Descripción | Estado |
|----------|--------|-------------|--------|
| `/api/media/:id` | GET | Streaming de audio con token | ⏭️ Planificado |

### Componentes (`/api/componentes/*`)

| Endpoint | Método | Descripción | Estado |
|----------|--------|-------------|--------|
| `/api/componentes` | GET | Obtener componentes del perfil | ⏭️ Planificado |
| `/api/componentes` | POST | Crear componente | ⏭️ Planificado |
| `/api/componentes` | PATCH | Actualizar componente | ⏭️ Planificado |
| `/api/componentes` | DELETE | Eliminar componente | ⏭️ Planificado |
| `/api/componentes/reordenar` | POST | Reordenar componentes | ⏭️ Planificado |

### Tokens (`/api/tokens/*`)

| Endpoint | Método | Descripción | Estado |
|----------|--------|-------------|--------|
| `/api/tokens/acceso-privado` | POST | Generar JWT para archivo privado | ⏭️ Planificado |

---

## 📝 TEMPLATE PARA DOCUMENTAR ENDPOINTS

**Cuando implementes un endpoint, usar este formato:**

### `POST /api/nombre-del-endpoint`

**Descripción**: Breve descripción de qué hace el endpoint

**Auth requerida**: ✅ Sí / ❌ No

**Headers**:
```
Authorization: Bearer <token>
Content-Type: application/json
```

**Body**:
```typescript
{
  campo1: string
  campo2: number
  // etc.
}
```

**Respuesta exitosa (200)**:
```typescript
{
  success: true
  data: {
    // estructura de respuesta
  }
}
```

**Errores posibles**:
- `400` - Bad Request: Validación falló
- `401` - Unauthorized: No autenticado
- `403` - Forbidden: Sin permisos
- `404` - Not Found: Recurso no existe
- `500` - Internal Server Error: Error del servidor

**Validaciones**:
- Campo 1: Requerido, string, min 3 caracteres
- Campo 2: Opcional, número positivo

**Ubicación del archivo**: `app/api/ruta/al/endpoint/route.ts`

**Rate limiting**: 10 requests por minuto (ejemplo)

**Ejemplos de uso**:
```typescript
// Cliente TypeScript
const response = await fetch('/api/endpoint', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({ campo1: 'valor', campo2: 123 }),
})
```

---

## 🔍 REFERENCIAS

**Arquitectura completa**: Ver `docs/arquitectura.md` sección 14 (Rutas de la Aplicación)

**Implementaciones**:
- Ver `docs/arquitectura.md` sección 7 para sistema de uploads transaccional
- Ver `docs/arquitectura.md` sección 8 para sistema de tokens JWT

---

**Nota**: Este archivo se irá actualizando a medida que se implementen los endpoints.

**Última actualización**: 2025-09-30 (inicial, vacío)
