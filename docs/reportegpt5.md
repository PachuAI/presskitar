# Revisión Técnica del Plan 1.1

## Resumen
- El plan refleja varias correcciones declaradas, pero persisten inconsistencias funcionales en uploads de imágenes/audio y en la API de archivos.
- Se introdujeron nuevos artefactos (Fases 8bis, 9bis, 18bis), pero algunos fragmentos no se conectan con el estado actual de los componentes existentes.
- El requerimiento de social share preview del PRD sigue sin cobertura explícita.

## Hallazgos Detallados

### 1. Campos de blur en `profiles` no planificados
- **Detalle**: `PerfilPublicoView` espera `profile.cover_blur_data_url` y `profile.profile_blur_data_url` (`docs/plan-implementacion.md:1690`, `docs/plan-implementacion.md:1705`), pero las fases de schema (Fase 1) no añaden dichos campos ni existe flujo para persistirlos desde `ImageUploader`.
- **Impacto**: Al seguir el plan, el código accederá a propiedades inexistentes ? TypeScript fallará o el runtime mostrará `undefined`. También se pierde la ventaja del blur placeholder prometido.
- **Recomendación**: Ampliar la migración de `profiles` para incluir columnas `profile_blur_data_url`/`cover_blur_data_url` (o cambiar la UI para usar directamente `files.blur_data_url`) y actualizar `PATCH /api/perfil` para persistirlas.

### 2. `PanelCustomizacion` no recibe `profile`
- **Detalle**: La integración de `ImageUploader` asume un `profile` accesible (`docs/plan-implementacion.md:1645`), pero `PanelCustomizacion` sigue sin parámetros ni acceso al perfil (`docs/plan-implementacion.md:2752`–`2810`), y el store sólo guarda `theme` y `components` (`docs/plan-implementacion.md:2528`–`2585`).
- **Impacto**: Las llamadas a `profile.profile_image_url` producirán `ReferenceError` o quedarán sin ejecutar, dejando inoperante la subida de imágenes.
- **Recomendación**: Propagar el perfil al store o pasar `profile` como prop desde `EditorContainer`, y actualizar `loadProfile`/estado para exponer campos necesarios a `ImageUploader`.

### 3. `confirmar-subida` no asigna `profile_id`
- **Detalle**: El insert en `files` omite `profile_id` (`docs/plan-implementacion.md:1162`–`1176`). `Streaming` y checks posteriores dependen de ese enlace (`docs/plan-implementacion.md:1341`–`1400`).
- **Impacto**: Los archivos quedan sin relación con el perfil, por lo que `/api/media/:id` no podrá validar publicación y retornará 403/404; también complica auditorías y RLS futuras.
- **Recomendación**: En `confirmar-subida`, resolver el `profile_id` del usuario (`profiles.user_id = session.user.id`) e incluirlo en el insert/update del registro.

### 4. Bug en `GET /api/archivos/:id`
- **Detalle**: La verificación de privacidad compara `profiles.id` contra `files.user_id` (`docs/plan-implementacion.md:1268`–`1284`), lo que nunca coincide.
- **Impacto**: La ruta devolverá 403 aun cuando el perfil esté publicado, impidiendo al reproductor cargar metadata.
- **Recomendación**: Consultar usando `profile_id` (o `user_id` pero comparando con la columna correcta) y evitar la segunda query anidada.

### 5. Uso directo de `body.isPrivate`
- **Detalle**: Tras validar con Zod, el handler vuelve a leer `body.isPrivate` (`docs/plan-implementacion.md:1160`) en vez de `result.data.isPrivate`.
- **Impacto**: Se ignoran transformaciones/valores por defecto del esquema y reintroduce riesgo de payload malicioso. Además rompe el contrato de validación.
- **Recomendación**: Extraer `isPrivate` desde `result.data` y eliminar el acceso al body original.

### 6. Sin UI para marcar audio como privado
- **Detalle**: `AudioUploader` envía `isPrivate: false` hardcodeado (`docs/plan-implementacion.md:3216`) y `FormularioComponente` no ofrece ningún toggle.
- **Impacto**: El gap 3 del PRD (demos privadas) sigue sin resolverse; toda pista queda pública por defecto.
- **Recomendación**: Agregar controles en el formulario de audio para seleccionar visibilidad y propagarla hasta `/api/archivos/iniciar-subida`/`confirmar-subida`.

### 7. Social share preview pendiente
- **Detalle**: El PRD exige preview de tarjeta social y botón "Copiar link" (`docs/PRD.md:584`–`609`, `docs/PRD.md:736`–`748`). Ninguna fase nueva los aborda.
- **Impacto**: Se incumple un deliverable del hito Alpha.
- **Recomendación**: Añadir fase dedicada (o ampliar Fase 21/20) con componentes de preview y acción de copiado.

## Conclusión
Aunque la versión 1.1 incorpora varias mejoras, los puntos anteriores impiden cumplir el PRD y provocarían fallos en runtime. Sugiero ajustar las fases indicadas antes de considerar el plan “listo para implementar”.
