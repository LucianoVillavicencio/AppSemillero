# Portal multipágina con Noticias — plan

> Ejecución: en la sesión, sin subagentes (CLAUDE.md). Una task = un commit local en `feat/portal-multipagina`. Sin push y sin `db push`.

**Goal:** Pasar el portal de una página a cinco (Inicio, Noticias, Categorías, Calendario, Ayuda) con la identidad del sitio oficial de Newell's, las acciones principales siempre a la vista, y Noticias con fotos que publica el coordinador.
**Spec:** `docs/superpowers/specs/2026-10-21-portal-multipagina-design.md` (leerlo antes de cada task).
**Stack:** JS vanilla + módulos ES, sin build. Supabase (RLS, Storage). Vercel estático. `node --test`.

## Restricciones globales

- No se inventa contenido: sólo `src/data/portal.js`, la base o Google Calendar. Lo que falta, oculto. Nunca "Próximamente".
- No cambian: `formularios_publicos`, los destinos 0061–0063, las URLs `/`, `/app`, `/consultas`, `/privacidad`, `/terminos`, ni `portalEntrada.js` primero en `/`.
- Las cinco páginas llevan el import map idéntico al de `index.html` (hash de la CSP) y ningún script inline.
- `DESIGN.md`: sin loops, sin cuenta regresiva, sin fuentes nuevas, colores sólo en `tokens.css`, transiciones sólo `transform`/`opacity`.
- HTML con `html\`\`` de `ui/html.js`. La UI pide datos sólo por `repositorio.js`.
- El WhatsApp del coordinador aparece una sola vez en todo el sitio (en `/ayuda`).
- Si una task cambia un test existente, el commit dice cuál y por qué.
- Antes de cada commit: `npm run test:q` en verde.

## Foco de revisión

1. Pantalla chica (320×568) o letra agrandada: las dos acciones siguen en el primer pantallazo. Verificación manual en las tasks 3 y 7.
2. Links viejos `/#ayuda`, `/#categorias`, `/#calendario`: redirigen y no rompen la vuelta de login (`?code=`, `#access_token`). Test en la task 3.
3. Noticia con fotos y sin la autorización de imagen: la rechazan la base (check) y la interfaz. Tests en las tasks 9 y 10.
4. Texto raro en una noticia (`<script>`, palabras de 300 letras, saltos de línea): sale escapado, en párrafos y sin desbordar. Tests en las tasks 10 y 12.
5. Foto HEIC, de 20 MB o que no es imagen; `?n=` que no es un uuid: mensaje claro, sin consulta a la base. Tests en las tasks 10 y 12.

---

### Task 0: Línea de base
- `npm ci`, después `npm run test:q`. Anotar en el commit las fallas que ya existían (tests que necesitan `.xlsx` reales se saltean).
- **Acepta:** la suite corre. Sin commit si no hay nada que anotar.

### Task 1 (refactor): `accionesPrincipales`
- **Archivos:** `src/data/formulariosPortal.js`, `src/ui/portal.js` (`htmlHeroe` la usa), `tests/formulariosPortal.test.js`.
- **Produce:** `accionesPrincipales({ inscripcion, consultas, comprobante }) → { primera: { tipo: 'inscribir'|'jugar', url: string|null }, comprobante: { url: string|null } }` (URL ya validada con `enlaceFormulario`).
- **Tests:** `accionesPrincipales: inscripción activa → inscribir`, `… cerrada con consultas → jugar`, `… sin formularios → urls null`, `… enlace que no es Google Forms → null`.
- **Acepta:** `tests/portalPagina.test.js` pasa sin tocarlo.

### Task 2: Estilos del portal y DESIGN.md
- **Archivos:** crear `public/css/portal.css` (sólo lo enlazan las páginas del portal), tokens nuevos si hacen falta en `tokens.css`, `DESIGN.md` (sección "Portal": alterna negro, papel y rojo; tarjeta de noticia; barra de acciones). Mover las reglas `.portal-*` de `publico.css` a `portal.css`.
- **Tests:** `estilosTokens` y `estilosTextoLargo` pasan con el archivo nuevo (si sólo escanean una lista fija, sumarlo).
- **Acepta:** `/` se ve igual que antes (sin cambio visual todavía).

### Task 3: Marco común (encabezado, pie, barra, redirecciones)
- **Archivos:** `public/portal.html` (encabezado con nav y menú `<details>`, pie), crear `src/ui/portalMarco.js`, `src/data/portal.js`, crear `tests/portalMarco.test.js`.
- **Produce:**
  - `paginaDeAncla(hash) → '/ayuda'|'/categorias'|'/calendario'|null` (pura, en `portal.js`).
  - `htmlBarraAcciones(acciones)`, usando la salida de la task 1.
  - `iniciarMarco() → Promise<{ estado, formularios separados }>`: lee `formularios_publicos` una vez, pinta la barra y devuelve lo leído para la página. Si `destinoDeAuth` da algo, no hace nada.
- **Tests:** `paginaDeAncla` (las tres anclas, `#otra` → null, vacío → null), `barra: inscribir y comprobante / jugar / apagados sin links`, `con vuelta de login no redirige`.
- **Acepta:** a mano en 360×640 y 320×568, la barra entra en pantalla. En `/`, aparece al salir el hero.

### Task 4: Página Ayuda
- **Archivos:** crear `public/ayuda.html` y `src/ui/portalAyuda.js` (mover `htmlAyuda`, sumar `htmlContacto({ contacto, sede, alias })`), crear `src/ui/portalAyudaArranque.js`. Sacar el `<dialog>` y `iniciarAyuda` de `portal.html`/`portal.js`.
- **Tests:** mover los de ayuda de `portalPagina.test.js` a `tests/portalAyuda.test.js`. Reemplazar los tests del `<dialog>` por `"Ayuda" del encabezado lleva a /ayuda`. `coordinador: una sola vez en las cinco páginas`, que recorre los HTML y el HTML generado.
- **Acepta:** las seis preguntas con el mismo texto. Contacto con WhatsApp, sede + mapa y alias.

### Task 5: Página Categorías
- **Archivos:** crear `public/categorias.html`, `src/ui/portalCategorias.js` (+ arranque), `src/data/portal.js` (campo opcional `detalle: null` en cada categoría), crear `tests/portalCategorias.test.js`.
- **Produce:** `anclaCategoria({ nombre, rama }) → 'u13'|'primera-femenino'…` (pura), y que `categoriasDelPortal` devuelva también `ancla` y `detalle`.
- **Tests:** `ancla única por categoría`, `sin horario: "Horario a confirmar"`, `detalle null no se muestra`, `buscador igual que antes` (mover los tests del buscador).
- **Acepta:** se puede llegar a `/categorias#u13` y la sección existe.

### Task 6: Página Calendario
- **Archivos:** crear `public/calendario.html`, mover el código del calendario de `portal.js` a `src/ui/portalCalendario.js` (+ arranque). El inicio conserva sólo `htmlProximo` + "Ver calendario".
- **Tests:** mover los tests del calendario a `tests/portalCalendario.test.js` sin cambiar sus aserciones. Nuevo: `inicio: "Lo que viene" con link a /calendario`.
- **Acepta:** el calendario anda igual en `/calendario` (meses, día, agregar, suscribirse).

### Task 7: Inicio rediseñado
- **Archivos:** `public/portal.html`, `src/ui/portal.js`, `portal.css`.
- Orden: hero → atajos → noticias (slot `#noticias`, oculto) → lo que viene → recaudación → colaborar → otros formularios → foto → sponsors.
- **Tests:** actualizar `arriba de todo, la banda…` con el orden nuevo. `#noticias arranca oculta`.
- **Acepta:** a mano en 360×640 y 320×568, título y las dos tarjetas sin scroll. En desktop, el ritmo negro/papel/rojo del spec.

### Task 8: Ruteo, SEO y consistencia
- **Archivos:** `vercel.json` (rewrites + `no-store` para `/noticias`, `/categorias`, `/calendario`, `/ayuda` y sus `.html`), `public/sitemap.xml`, `<title>`/description/OG por página. Crear `tests/paginasPortal.test.js`.
- **Tests:** por cada página: `import map idéntico`, `sin script inline`, `encabezado y pie iguales a los de /`, `tiene rewrite y no-store`, `está en el sitemap`, `no dice Próximamente`.

### Task 9: Migración 0064 noticias (modelo Opus, skill `/nueva-migracion`)
- **Archivos:** crear `supabase/migrations/0064_noticias.sql`, `tests/rollback0064.sql`, `tests/verificarNoticias.sql` (queda escrito, no se corre: no hay Supabase local), `src/data/limites.js` (`tituloNoticia`, `resumenNoticia`, `cuerpoNoticia`, `altFoto`), `supabase/ESQUEMA.md`.
- Contenido según el spec: tabla `noticia`, checks (etiqueta en la lista, ≤6 fotos, alt obligatorio, fotos ⇒ `autorizacion_imagen`), sellado y `publicada_en` por trigger, RLS de coordinador (select/insert/update/delete), `noticias_publicas(p_club uuid, p_limite int, p_antes timestamptz)` y `noticia_publica(p_club uuid, p_id uuid)` `security definer` para anon. Bucket `noticias` público, 1 MB, webp/jpeg, y policies de `storage.objects` por la carpeta del club.
- **Tests:** `tests/contratoNoticia.test.js` (etiquetas, máximo de fotos, mimes y tamaño, iguales en SQL y JS), `contratoLimites` con los cuatro nuevos.

### Task 10: Lógica pura de noticias
- **Archivos:** crear `src/data/noticias.js`, `tests/noticias.test.js`.
- **Produce:** `ETIQUETAS_NOTICIA`, `MAX_FOTOS`, `MAX_BYTES_FOTO`, `LADO_MAX_FOTO = 1600`; `validarNoticia(datos) → { error, noticia }`; `validarArchivoFoto({ type, size }) → string|null`; `medidasAchicadas(ancho, alto) → { ancho, alto }`; `parrafos(texto) → string[]`; `rutaFoto(clubId, noticiaId, nombre)`; `esIdNoticia(texto)`; `noticiaDesdeFila`, `noticiaPublicaDesdeFila`.
- **Tests:** `con fotos sin autorización → error`, `alt vacío → error`, `7 fotos → error`, `HEIC / 20 MB / text/plain → mensaje`, `4000×3000 → 1600×1200`, `foto chica no se agranda`, `párrafos por línea en blanco`, `esIdNoticia('abc') → false`.

### Task 11: Repo de noticias
- **Archivos:** crear `src/data/repos/noticias.js`, una línea en `repositorio.js`.
- **Produce:** `noticiasPublicas(clubId, { limite, antesDe })`, `noticiaPublica(clubId, id)`, `listarNoticias(clubId)`, `crearNoticia`, `actualizarNoticia(id, cambios)`, `publicarNoticia(id, publicada)`, `borrarNoticia(id)` (borra también sus fotos), `subirFotoNoticia(clubId, noticiaId, blob)`, `urlPublicaFoto(ruta)`. Mapean con los mappers de la task 10.
- **Tests:** `arquitectura` pasa (fachada y capas).

### Task 12: Noticias en el portal
- **Archivos:** crear `public/noticias.html`, `src/ui/portalNoticias.js` (+ arranque) con `htmlTarjetaNoticia`, `htmlListaNoticias`, `htmlDetalleNoticia`, `htmlNoticiaNoEsta`. En el inicio, las últimas 3. `vercel.json`: `img-src` suma el host de Supabase. `tests/csp.test.js`.
- **Tests:** `tests/portalNoticias.test.js`: `tarjeta escapada con etiqueta, fecha y LEER`, `foto con su alt`, `sin noticias → '' (sección oculta)`, `si la función falla → oculta, el resto anda`, `?n= no uuid → "ya no está" sin consulta`, `palabra larga no desborda` (sumar el caso a `estilosTextoLargo`), `img-src permite sólo self, blob, ytimg y Supabase`.
- **Acepta:** a mano con el servidor local contra la base real (sin 0064): `/` y `/noticias` sin errores visibles y con la sección oculta.

### Task 13: Panel del coordinador
- **Archivos:** crear `src/ui/pantallas/coordNoticias.js`; `public/index.html` (sección `p-coord-noticias`); `registro.js` (`registrarPantalla('p-coord-noticias', …)`); botón "Noticias del portal" en `coordFamilias.js`, al lado de Formularios.
- Lista (borradores/publicadas), editor con `maxlength` de `limites.js`, fotos con su alt (achica con canvas usando `medidasAchicadas`), checkbox de autorización, vista previa con `htmlDetalleNoticia`, publicar/despublicar, borrar con `confirmar`. Errores con `mensajeAlGuardar`.
- **Tests:** `tests/coordNoticias.test.js`: `publicar apagado con fotos sin autorización`, `alt vacío marca la foto`, `borrar pide confirmación`.

### Task 14: Documentación y cierre
- `docs/COORDINACION.md` (cómo publicar una noticia), `docs/legal/cambios-portal.md` y `public/legal/privacidad.html` (las fotos de noticias), `docs/SEO.md`.
- `npm run test:q` en verde. Recorrido a mano de las cinco páginas en 320×568, 360×640 y desktop.
- Review final de la rama (único subagente permitido por CLAUDE.md, modelo Opus).
- Pendiente para el usuario, fuera de esta rama: `npm run backup` → `db push` de 0064 → correr `verificarNoticias.sql` → publicar una noticia de prueba.
