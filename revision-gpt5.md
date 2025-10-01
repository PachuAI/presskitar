# REVISIÓN TÉCNICA - Preskit.AR

  ## FORTALEZAS DEL PLAN

  - Modelo de datos y RLS muy sólidos, alineados con la arquitectura (migraciones y políticas en docs/plan-
  implementacion.md:312–420 y 421–540).
  - Pipeline de uploads bien desglosado (constantes, iniciar, confirmar, tokens, streaming en docs/plan-
  implementacion.md:861–1360), cubriendo cuotas y rollback.
  - API backend planificada con validaciones Zod y tests unitarios por endpoint (docs/plan-implementacion.md:1475–1820).
  - Uso de Zustand + dnd-kit para el editor con estado centralizado y drag & drop (docs/plan-implementacion.md:2141–
  2289).
  - Consideración temprana de autenticación OAuth y middleware de protección (docs/plan-implementacion.md:541–700 y
  701–860).
  - Distribución en hitos y scripts de verificación que fomentan calidad incremental (docs/plan-implementacion.md:3032–
  3072 y secciones de hitos).
  - Documentación exhaustiva de tareas y criterios de aceptación por fase, lo que facilita seguimiento.

  ## GAPS CRÍTICOS DETECTADOS

  ### Gap 1: Editor sin flujo de creación/edición de componentes

  Severidad: Blocker
  Descripción: El plan define Canvas, store y renderer, pero no existe una fase que implemente la librería de
  componentes (botones, texto, video, PDF) con formularios de alta/edición, toolbars o inline editing. No hay UI ni
  llamadas a /api/componentes para crear/actualizar/eliminar, pese a que es un requisito clave del PRD (docs/PRD.md:233–
  470) y del checklist de hito 1 (docs/PRD.md:736–748).
  Impacto: El usuario no podrá agregar contenido real a su press kit, bloqueando la propuesta de valor del MVP.
  Recomendación: Añadir fases específicas para el sidebar de componentes, formularios inline y sincronización con el
  store/API (incluyendo eliminación y validaciones visuales).
  Fase sugerida para agregar: Nueva fase entre las fases 16 y 20 (por ejemplo “Fase 18bis: Biblioteca y edición de
  componentes”).

  ### Gap 2: Persistencia de tema y datos clave fallará

  Severidad: Blocker
  Descripción: updatePerfilSchema no contempla theme_settings, profile_image_url, cover_image_url ni checklist de
  publicación; cualquier intento de guardar el tema desde el editor fallará (docs/plan-implementacion.md:1475–1488 y
  2671–2689).
  Impacto: El botón “Guardar” del editor devolverá 400, impidiendo persistir personalización o contacto.
  Recomendación: Extender esquema y tests para aceptar theme_settings, URLs de imágenes, estado de checklist y campos de
  contacto descritos en arquitectura (docs/arquitectura.md:207–252).
  Fase sugerida para agregar: Ajuste en Fase 11 con tests adicionales.

  ### Gap 3: Privacidad de audio y share link sin implementación real

  Severidad: Alta
  Descripción: La ruta de confirmación fuerza is_private: false (docs/plan-implementacion.md:1097), no existe UI para
  marcar privados ni endpoint GET /api/archivos/:id, pero Fase 22 depende de él (docs/plan-implementacion.md:2932). El
  PRD exige toggles público/privado y generación de enlace seguro (docs/PRD.md:273–279).
  Impacto: El diferenciador de demos privadas desaparece; el reproductor fallará al llamar a una API inexistente.
  Recomendación: Incorporar flag is_private configurable, endpoint de metadata protegido y UI para generar/copiar
  enlaces con expiración.
  Fase sugerida para agregar: Extender Fases 8–10 y 22.

  ### Gap 4: Uploads de foto/cover y binding con el perfil ausentes

  Severidad: Alta
  Descripción: El PRD demanda subir foto de perfil desde el editor (docs/PRD.md:233–250, 444–451), la arquitectura
  reserva campos en profiles (docs/arquitectura.md:207–252), pero el plan no incluye pasos para disparar uploads de
  imágenes, guardarlas en profile_image_url/cover_image_url, ni mostrar placeholders en el editor o la vista pública.
  Impacto: Perfiles quedarían sin identidad visual, deteriorando la experiencia y contradiciendo los requisitos básicos
  del MVP.
  Recomendación: Añadir fase que integre el flujo “subir imagen → confirmar → actualizar profile” y componentes UI
  correspondientes (editor + PerfilPublicoView).
  Fase sugerida para agregar: Justo después de Fase 8 (por ejemplo “Fase 9bis: Gestión de imágenes de perfil”).

  ### Gap 5: Onboarding y checklist del PRD omitidos

  Severidad: Alta
  Descripción: El PRD exige plantilla rápida vs. editor vacío, checklist que habilita “Publicar” y mensaje de
  checklist visible (docs/PRD.md:359–415). El plan solo menciona una página de bienvenida genérica (docs/plan-
  implementacion.md:3038–3072) sin estado gating ni plantillas.
  Impacto: Se incumple el onboarding “lento pero seguro”, los usuarios no tendrán guía ni bloqueo suave para preparar
  el perfil.
  Recomendación: Modelar checklist en DB, exponerlo en el editor y crear plantilla inicial cargable al registrar.
  Fase sugerida para agregar: Extender Fase 23 y Fase 20 con subtareas específicas.

  ### Gap 6: Social share preview y “copiar link” no planificados

  Severidad: Media
  Descripción: El PRD demanda preview de tarjeta social y botón “Copiar link” (docs/PRD.md:584–609, 736–748). Fase 21
  solo añade metadatos sin UI de vista previa ni utilidades de sharing (docs/plan-implementacion.md:2781–2940).
  Impacto: Se pierde una parte del valor de difusión y del checklist de hito 1.
  Recomendación: Añadir componentes en el editor (sección share) con preview generada y acción de copiar enlace.
  Fase sugerida para agregar: Complementar Fase 21 o 20.

  ## 🔧 AJUSTES RECOMENDADOS

  ### Ajuste 1: Incorporar fases de UI de componentes

  Fase afectada: 16–20
  Cambio propuesto: Insertar fases para sidebar de componentes, formularios inline, tooltips y wiring con /api/
  componentes (crear/actualizar/eliminar).
  Razón: Sin estas capas, el editor no cumple los requisitos de gestión de contenido del PRD.

  ### Ajuste 2: Extender Fase 11 para soportar theme, checklist y assets

  Fase afectada: Fase 11
  Cambio propuesto: Ampliar schemas y tests a theme_settings, imágenes, checklist y disponibilidad de booking;
  actualizar API para devolver esos datos.
  Razón: Permite que el editor persista la personalización exigida.

  ### Ajuste 3: Reordenar dependencias de storage

  Fase afectada: Fase 5
  Cambio propuesto: Declarar explícitamente dependencia de la Fase 1 (migraciones) y añadir integración que vincule
  archivos con profiles en confirmación.
  Razón: Evita buckets sin consumo real y asegura integridad referencial.

  ### Ajuste 4: Añadir fase de integración end-to-end uploads → componentes

  Fase afectada: Nueva fase tras Fase 22
  Cambio propuesto: Pruebas manuales/automáticas que recorran subir archivo, crear componente, verlo en preview y perfil
  público.
  Razón: Garantiza que los subsistemas funcionen juntos antes del hito 4.

  ## 🚨 RIESGOS TÉCNICOS NO CONTEMPLADOS

  ### Riesgo 1: Rate limiting in-memory en serverless

  Probabilidad: Alta
  Impacto: Medio
  Descripción: El limitador usa Map global y setInterval (docs/plan-implementacion.md:1188–1220), lo que no persiste
  entre lambdas y puede saturar recursos.
  Mitigación sugerida: Usar servicio externo (Upstash/Redis, KV) o Supabase Edge Functions con almacenamiento
  consistente.
  Fase para implementar mitigación: Fase 9.

  ### Riesgo 2: Streaming descarga el archivo completo en memoria

  Probabilidad: Media
  Impacto: Alto
  Descripción: /api/media/:id usa supabase.storage.download y luego blob.slice (docs/plan-implementacion.md:1341–1430),
  cargando 15 MB en RAM por request, con latencias altas y timeouts en Vercel.
  Mitigación sugerida: Generar URLs firmadas temporales o usar supabase.storage.createSignedUrl y delegar streaming al
  CDN/Edge.
  Fase para implementar mitigación: Fase 10.

  ### Riesgo 3: Sanitización de contenido HTML inexistente

  Probabilidad: Media
  Impacto: Alto
  Descripción: text_block permite data.content en HTML (docs/plan-implementacion.md:1701–1760) pero no se planifica
  sanitización antes de render (PerfilPublicoView en docs/plan-implementacion.md:2461–2620).
  Mitigación sugerida: Incorporar sanitizador (DOMPurify/rehype-sanitize) en API o renderizado.
  Fase para implementar mitigación: Fase 14/21.

  ## 📊 ANÁLISIS DE TESTING

  Coverage estimado según plan actual: ~55% (enfocado en unit tests de API)
  Tipos de tests cubiertos: Unitarios backend, algunos unitarios de store/UI declarativos, scripts por hito.
  Tipos de tests faltantes: Integración end-to-end (upload → componente → publish), pruebas UI interactivas (Playwright/
  Cypress), smoke tests post-deploy reales, pruebas de rendimiento (streaming, cuota).
  Recomendaciones:

  - Añadir tests de integración para flujos críticos (registro, onboarding, creación de perfil completo).
  - Incorporar pruebas UI automatizadas para drag & drop y checklist de publicación.
  - Definir pruebas de carga ligeras para streaming y límites de storage.

  ## 🎯 RECOMENDACIONES FINALES

  ### Corto Plazo (antes de empezar)

  - Completar las fases faltantes para componentes, checklist y share preview.
  - Ajustar schemas/API para theme, imágenes y privacidad.
  - Decidir estrategia de rate limiting con almacenamiento consistente.
  - Documentar el flujo end-to-end esperado del editor (incluyendo plantillas iniciales).
  - Preparar definición clara de los tests de integración obligatorios por hito.

  ### Mediano Plazo (durante desarrollo)

  - Implementar sanitización de contenido y controles de XSS.
  - Validar rendimiento de music-metadata en Vercel; mover a worker si es pesado.
  - Coordinar la creación de /api/archivos/:id y UI de enlaces privados.
  - Mantener checklist de publicación sincronizado con la UI y la API.
  - Reforzar documentación DX (scripts, seeds, mocks) para acelerar QA.

  ### Largo Plazo (post-MVP)

  - Planificar migration a rate limiting externo (Upstash/Cloudflare) y CDN para audio.
  - Diseñar analíticas básicas y monitoreo de uso.
  - Evaluar automatización de moderación/reportes y sistemas de plantillas premium.
  - Preparar roadmap para features premium (calendario, más archivos, dominios custom).
  - Revisar arquitectura para escalar tokens JWT y streaming si crece el catálogo.

  ## VEREDICTO GENERAL

  El plan se apoya en bases sólidas para backend, seguridad básica y despliegue modular, pero omite piezas críticas
  del editor, onboarding y privacidad que el PRD define como esenciales. También hay inconsistencias entre APIs y
  UI planeadas que impedirían persistir personalización o consumir audio privado. Sin cubrir estos huecos, el MVP no
  entregaría el valor prometido a los DJs.

  ¿El plan está listo para empezar?: NO
  Razón: Faltan fases imprescindibles para creación/edición de componentes, gestión de privacidad, onboarding y
  persistencia de tema; además hay riesgos técnicos (rate limiting, sanitización, streaming) que requieren rediseño
  previo al desarrollo.
