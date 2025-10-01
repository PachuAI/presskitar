# Revisi�n T�cnica del Plan 1.1

## Resumen
- El plan refleja varias correcciones declaradas, pero persisten inconsistencias funcionales en uploads de im�genes/audio y en la API de archivos.
- Se introdujeron nuevos artefactos (Fases 8bis, 9bis, 18bis), pero algunos fragmentos no se conectan con el estado actual de los componentes existentes.
- El requerimiento de social share preview del PRD sigue sin cobertura expl�cita.

## Hallazgos Detallados

### 1. Campos de blur en `profiles` no planificados
- **Detalle**: `PerfilPublicoView` espera `profile.cover_blur_data_url` y `profile.profile_blur_data_url` (`docs/plan-implementacion.md:1690`, `docs/plan-implementacion.md:1705`), pero las fases de schema (Fase 1) no a�aden dichos campos ni existe flujo para persistirlos desde `ImageUploader`.
- **Impacto**: Al seguir el plan, el c�digo acceder� a propiedades inexistentes ? TypeScript fallar� o el runtime mostrar� `undefined`. Tambi�n se pierde la ventaja del blur placeholder prometido.
- **Recomendaci�n**: Ampliar la migraci�n de `profiles` para incluir columnas `profile_blur_data_url`/`cover_blur_data_url` (o cambiar la UI para usar directamente `files.blur_data_url`) y actualizar `PATCH /api/perfil` para persistirlas.

### 2. `PanelCustomizacion` no recibe `profile`
- **Detalle**: La integraci�n de `ImageUploader` asume un `profile` accesible (`docs/plan-implementacion.md:1645`), pero `PanelCustomizacion` sigue sin par�metros ni acceso al perfil (`docs/plan-implementacion.md:2752`�`2810`), y el store s�lo guarda `theme` y `components` (`docs/plan-implementacion.md:2528`�`2585`).
- **Impacto**: Las llamadas a `profile.profile_image_url` producir�n `ReferenceError` o quedar�n sin ejecutar, dejando inoperante la subida de im�genes.
- **Recomendaci�n**: Propagar el perfil al store o pasar `profile` como prop desde `EditorContainer`, y actualizar `loadProfile`/estado para exponer campos necesarios a `ImageUploader`.

### 3. `confirmar-subida` no asigna `profile_id`
- **Detalle**: El insert en `files` omite `profile_id` (`docs/plan-implementacion.md:1162`�`1176`). `Streaming` y checks posteriores dependen de ese enlace (`docs/plan-implementacion.md:1341`�`1400`).
- **Impacto**: Los archivos quedan sin relaci�n con el perfil, por lo que `/api/media/:id` no podr� validar publicaci�n y retornar� 403/404; tambi�n complica auditor�as y RLS futuras.
- **Recomendaci�n**: En `confirmar-subida`, resolver el `profile_id` del usuario (`profiles.user_id = session.user.id`) e incluirlo en el insert/update del registro.

### 4. Bug en `GET /api/archivos/:id`
- **Detalle**: La verificaci�n de privacidad compara `profiles.id` contra `files.user_id` (`docs/plan-implementacion.md:1268`�`1284`), lo que nunca coincide.
- **Impacto**: La ruta devolver� 403 aun cuando el perfil est� publicado, impidiendo al reproductor cargar metadata.
- **Recomendaci�n**: Consultar usando `profile_id` (o `user_id` pero comparando con la columna correcta) y evitar la segunda query anidada.

### 5. Uso directo de `body.isPrivate`
- **Detalle**: Tras validar con Zod, el handler vuelve a leer `body.isPrivate` (`docs/plan-implementacion.md:1160`) en vez de `result.data.isPrivate`.
- **Impacto**: Se ignoran transformaciones/valores por defecto del esquema y reintroduce riesgo de payload malicioso. Adem�s rompe el contrato de validaci�n.
- **Recomendaci�n**: Extraer `isPrivate` desde `result.data` y eliminar el acceso al body original.

### 6. Sin UI para marcar audio como privado
- **Detalle**: `AudioUploader` env�a `isPrivate: false` hardcodeado (`docs/plan-implementacion.md:3216`) y `FormularioComponente` no ofrece ning�n toggle.
- **Impacto**: El gap 3 del PRD (demos privadas) sigue sin resolverse; toda pista queda p�blica por defecto.
- **Recomendaci�n**: Agregar controles en el formulario de audio para seleccionar visibilidad y propagarla hasta `/api/archivos/iniciar-subida`/`confirmar-subida`.

### 7. Social share preview pendiente
- **Detalle**: El PRD exige preview de tarjeta social y bot�n "Copiar link" (`docs/PRD.md:584`�`609`, `docs/PRD.md:736`�`748`). Ninguna fase nueva los aborda.
- **Impacto**: Se incumple un deliverable del hito Alpha.
- **Recomendaci�n**: A�adir fase dedicada (o ampliar Fase 21/20) con componentes de preview y acci�n de copiado.

## Conclusi�n
Aunque la versi�n 1.1 incorpora varias mejoras, los puntos anteriores impiden cumplir el PRD y provocar�an fallos en runtime. Sugiero ajustar las fases indicadas antes de considerar el plan �listo para implementar�.
