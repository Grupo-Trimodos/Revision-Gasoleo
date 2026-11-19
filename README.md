# Revisión del precio del transporte por gasóleo

Calculadora gratuita de la revisión obligatoria del precio del transporte de mercancías por carretera según la variación del precio del gasóleo (Ley 15/2009, art. 38, y Orden FOM/1882/2012, condición 3.4, en la redacción de los RDL 9/2026 y 18/2026).

- Calcula G (variación del gasóleo), el coeficiente C según el tipo de vehículo y el importe a revisar (ΔP = G × P × C / 100).
- Cada precio puede tomarse a mano, por día, por semana, por mes o por mes natural.
- Los precios salen de la Excel semanal del Ministerio de Transportes y se actualizan solos.

Es una herramienta pública para clientes, sin contraseña ni control de acceso. `localStorage` solo guarda las preferencias del formulario (`gasoleo-calc`) y el idioma (`gasoleo-lang`).

## Dirección

- Calculadora: <https://grupo-trimodos.github.io/Revision-Gasoleo/>
- En inglés: <https://grupo-trimodos.github.io/Revision-Gasoleo/?lang=en>
- Versión de pruebas: <https://grupo-trimodos.github.io/Revision-Gasoleo/pruebas/>
- Versión ACTM: <https://grupo-trimodos.github.io/Revision-Gasoleo/actm/>

La web corporativa (www.grupotrimodos.com, en Odoo) enlaza a la calculadora con un botón. Desde la versión en inglés de la web conviene enlazar la dirección con `?lang=en`.

## Diseño

La página copia el sistema visual de grupotrimodos.com, medido en la web en vivo, para que al pulsar el botón no se note el cambio de sitio:

- La misma cabecera: blanca, de 56 px, con el logotipo de la web (`img/logo-trimodos.png`) a 40 px y en la misma posición.
- El mismo contenedor de Bootstrap (540, 720, 960, 1140 y 1320 px, con 15 px de margen).
- Las mismas tipografías: Mulish 500 en los títulos (H1 de 48 px que escala en el móvil), Roboto 16 px en el texto y Roboto 300 en la entradilla.
- Secciones a todo el ancho como las de la web: blanco, crema `#FFEED4` para los datos y amarillo `#FCDA00` para el resultado. El pie es gris `#F3F2F2`, con los mismos textos de contacto que la web.
- Botones en píldora y la referencia en verde Stinsa y la realización en naranja Transargi, como los titulares de la web.

La página no enlaza a ninguna otra web: no hay botones de «volver», ni de contacto, ni enlaces externos. El correo comercial del pie se puede copiar, pero no abre nada. Tampoco hay pantalla de inicio animada, ni «Instalar app», ni modo oscuro (la web no lo tiene).

La información técnica está plegada para no distraer: «Opciones avanzadas» (precio para G, umbral, redondeo y C a mano), «Evolución del precio del gasóleo» (gráfica) y «Cómo se calcula» (el cálculo con sus cifras, el método, las semanas usadas, las tablas de C y la normativa).

Al compartir el enlace (WhatsApp, LinkedIn, correo) se muestra la vista previa `img/og-gasoleo.png`.

## Publicación

`.github/workflows/publicar.yml` publica la web en GitHub Pages en cada cambio de `main` y cuando termina la actualización de precios. Este repositorio es un *fork* de `Elomarmu/Revision-Gasoleo`, y GitHub desactiva las Actions en los forks. Para que funcione hay que hacer esto una vez:

1. **Actions** → «I understand my workflows, go ahead and enable them».
2. **Settings → Pages → Build and deployment → Source: GitHub Actions**. El repositorio es público, así que la web también lo es.
3. **Actions** → «Publicar la calculadora» → **Run workflow**.

## Cómo se actualizan los datos

`.github/workflows/actualizar.yml` ejecuta `scripts/actualizar.py` cada 2 horas de miércoles a viernes. El script busca en la página del Ministerio el enlace a la última Excel, la descarga, lee las hojas «PRECIOS_DEL_GASOLEO» y «PRECIOS DE REFERENCIA» y, si hay cambios, guarda `data.json`.

Para forzar una actualización: pestaña **Actions** → «Actualizar precios del gasóleo» → **Run workflow**.

## Formato de data.json

Cada fila de `rows` es `[fecha (serial Excel), PVP, PVP sin IVA, impuesto hidrocarburos, PAI, devolución gasóleo profesional, Pmed MMA ≥ 7,5 t, Pmed MMA < 7,5 t]`, en €/litro.

## Versión de pruebas

`pruebas/index.html` es una copia de `index.html` con la banda «VERSIÓN DE PRUEBAS», las rutas a `../`, `noindex` y su propia clave de `localStorage` (`gasoleo-calc-pruebas`).

## Aviso

Herramienta orientativa. Para un litigio o una reclamación, contrasta el resultado con la calculadora oficial del Ministerio de Transportes.
