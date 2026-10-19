# Cambios legales que pide el portal del básquet (para revisar)

**Estado: aplicado el 18/10/2026.** Tomás decidió: el calendario va embebido en
la página y se actualiza la política ("no usa nadie la página aún, no hay problema
con cambiar esa política"). Se aplicó el texto de abajo en
`public/legal/privacidad.html`, y `VERSION_LEGAL` y las dos páginas legales pasaron
a `2026-10-18`: **todos los usuarios con cuenta aceptan de nuevo en su próximo
ingreso.** La sección "Alternativa" quedó descartada. Se agregó además, en la
sección 2, un párrafo sobre los formularios de las familias (el responsable es el
club y los datos viven en su Drive, no en Semillero).

**Cuentas de familia (0057), sumadas en la misma versión `2026-10-18`** para que
nadie acepte dos veces: la política y los términos describen la cuenta de
familia (madre, padre o tutor), el mail del responsable que carga el profe desde
la planilla de inscripciones, el vínculo automático por mail verificado, que la
familia ve sólo nombre, club y categoría (nunca estadísticas, mediciones ni
misiones), que los entrenadores ven esos mails, y que la coordinación ve los
vínculos con el mail oculto y puede darlos de baja (la baja cierra, no borra).
Cada afirmación sale de `0057_cuentas_familia.sql` y de su spec; si cambia el
código de esa rama, hay que revisar este texto.

## Por qué hace falta

La política vigente (`public/legal/privacidad.html`, versión `2026-09-26`)
dice, en el resumen y en la sección 7, que *"no hay analíticas ni cookies de
seguimiento"* y que la app no usa cookies de publicidad. El portal nuevo suma
dos cosas que lo cuestionan:

1. **Calendarios de Google Calendar embebidos** (iframe). Al cargarse, Google
   puede guardar cookies propias en el navegador de quien abre la página.
2. **Enlaces a Google Forms** para inscripción, comprobantes y trámites. No se
   embeben (se abren en otra pestaña), pero los datos de las familias y de los
   chicos, incluidos DNI y datos de salud, **se procesan en Google**, fuera de
   Semillero y de la base. El responsable es el club.

Además la política habla de "quienes tienen cuenta (entrenadores, coordinación
y jugadores)": las familias que usan el portal no tienen cuenta, pero sí dejan
datos en los formularios.

## Texto propuesto

**Sección 6 (terceros que intervienen)**, sumar:
- *Google Calendar*, en la página pública del básquet, para mostrar el
  calendario de partidos y eventos. Google puede usar cookies propias al
  mostrarlo.
- *Google Formularios y Drive*, para la inscripción, la documentación y los
  comprobantes que completan las familias. Esos datos los recibe y los
  administra el club, y se guardan en el Google Drive del club, no en Semillero.

**Sección 7 (cookies)**, reemplazar la primera oración por:
*"La app no usa cookies de publicidad, de seguimiento ni de analíticas. En la
página pública del básquet, el calendario lo muestra Google y Google puede
guardar sus propias cookies al hacerlo."*

**Resumen**, ajustar *"no hay analíticas ni cookies de seguimiento"* por
*"Semillero no usa analíticas ni cookies de seguimiento (el calendario de la
página pública lo muestra Google, con sus propias cookies)"*.

## Alternativa que evita cambiar el texto

Que el calendario **no se cargue solo**: el portal muestra un botón *"Ver el
calendario"* y recién al tocarlo se abre el embed de Google. Mientras nadie lo
toque, no se contacta a Google y la política actual sigue siendo cierta para el
calendario. Sigue haciendo falta decir lo de los formularios (punto 2), pero eso
no contradice ninguna frase actual. Costo: un toque más para la familia, y un
cambio chico en el portal (`src/ui/portal.js`). **Si se elige esta alternativa,
avisar y se hace.**

## Qué pasa si se cambia el texto

`VERSION_LEGAL` (`src/data/legal.js`) tiene que subir a la fecha del cambio y
las dos páginas llevar el mismo `data-version` (lo verifica
`tests/contratoLegal.test.js`). **Todos los usuarios con cuenta tienen que
aceptar de nuevo la próxima vez que entren.** Es la forma correcta de hacerlo,
pero conviene juntarlo con otros cambios legales y avisarle al club antes.

## Decisiones para Tomás y el club

1. ¿Texto nuevo (con la consecuencia de que todos vuelvan a aceptar), o la
   alternativa de cargar el calendario con un botón?
2. ¿El club confirma que es responsable del tratamiento de los datos que se
   completan en los formularios de Google? (`docs/legal/acuerdo-datos-club.md`)
