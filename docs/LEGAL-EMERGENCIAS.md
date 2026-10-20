# Texto legal para los datos de emergencia (0059)

Para pasarle a la sesión del portal, que tiene los cambios legales sin publicar. **Hay que publicarlo antes de aplicar 0059 en producción o, como mínimo, antes de que un profe cargue la primera emergencia.** Son datos de salud de menores (dato sensible, Ley 25.326, art. 7): consentimiento expreso e informado, finalidad clara y quién accede.

Al publicar: subir `VERSION_LEGAL` en `src/data/legal.js` (obliga a todos a aceptar de nuevo), poner la misma fecha en `privacidad.html` y `terminos.html` (lo compara `tests/contratoLegal.test.js`). Esta rama **no toca** `public/legal/` ni `legal.js`.

## privacidad.html

**Sección 2, "De los jugadores"** — sumar una viñeta:
> Datos de emergencia, si la familia los dio en la inscripción: teléfono del adulto, contacto de urgencia (nombre y teléfono), alertas médicas de una lista cerrada (desmayos, taquicardia, convulsiones, epilepsia, meningitis, diabetes, pérdida de conocimiento traumática y asma), alergias, medicación y una indicación médica especial, en texto corto, y la obra social.

**Sección 2, "Lo que la app no guarda"** — reemplazar la frase por:
> **Lo que la app no guarda:** DNI, número de afiliado, grupo sanguíneo, domicilio, fotos, ubicación ni historias clínicas o diagnósticos. De salud sólo guarda los datos de emergencia descriptos arriba. Los videos que se usan para medir el salto se procesan en el teléfono y no se suben a ningún lado.

**Sección 2, "Formularios de las familias"** — corregir "los datos de salud … no se guardan en la app ni en su base de datos" por:
> … la inscripción, la documentación (por ejemplo el DNI y las fotos), la ficha médica completa y los comprobantes de pago se completan en formularios de Google … y se guardan en el Google Drive del club. De todo eso, la app guarda únicamente lo que se indica en esta sección (nombre, fecha de nacimiento, categoría, mail del responsable y los datos de emergencia).

**Sección 3, "Para qué se usan"** — sumar:
> Los datos de emergencia se usan **sólo** para que el profe pueda actuar rápido y avisar a la familia si un chico se golpea o se siente mal durante un entrenamiento o un partido. No se usan para decidir si un chico juega, se selecciona o se evalúa, ni para ninguna otra cosa.

**Sección 4, "Quién puede verlos"** — sumar a Entrenadores y Coordinación, y aclarar las otras:
> - **Entrenadores:** … y los datos de emergencia de esos jugadores.
> - **Coordinación del club:** … y los datos de emergencia de los jugadores del club.
> - **Cada jugador con cuenta y cada familia con cuenta:** nunca ven los datos de emergencia de nadie, ni siquiera los propios (están en el formulario que completó la familia).
>
> Un entrenador deja de verlos cuando deja de tener asignada la categoría del chico.

**Sección 7, "Cookies y almacenamiento en tu teléfono"** — sumar:
> Los datos de emergencia **no se guardan en tu teléfono**: se piden a la base cada vez que abrís esa pantalla y se muestran mientras la tenés abierta.

**Sección 8, "Cuánto tiempo se guardan"** — sumar:
> Los datos de emergencia se borran automáticamente cuando el chico deja de pertenecer a una categoría del club (porque se va, o porque el club lo da de baja al cambiar de temporada). Cada vez que el club los vuelve a cargar, reemplazan a los anteriores.

**Sección 9, "Tus derechos"** — sin cambios de fondo; agregar que para corregir o borrar los datos de emergencia se pide al profe de la categoría o a la coordinación.

## Consentimiento en el formulario de inscripción (lo arma la sesión de inscripción)

Junto a la casilla "Consentimiento de Datos" del formulario, con este texto o uno equivalente:
> Autorizo al club a cargar en la aplicación Semillero el teléfono del adulto responsable, el contacto de urgencia, las afecciones médicas marcadas, las alergias, la medicación, la indicación médica especial y la obra social de mi hijo/a. Los verán sólo el profe de su categoría y la coordinación del club, y se usarán únicamente para actuar ante una emergencia. Puedo pedir que se corrijan o se borren en cualquier momento. Más información: política de privacidad de Semillero.

Sin esa casilla tildada, la fila **no** pasa a la planilla "Emergencias para Semillero" (ver `google/PRECARGA-2026.md`).

## Para tener en cuenta
- Para el plantel 2026 que ya completó el formulario, la casilla "Consentimiento de Datos" del formulario viejo no nombra a Semillero. Antes de cargar sus emergencias hay que decidir con Tomás si alcanza o si se les pide la confirmación de nuevo (por ejemplo, con el link de la reinscripción).
- La lista de profes con acceso cambia sola con las asignaciones: quien se va del club no sigue viendo nada.
