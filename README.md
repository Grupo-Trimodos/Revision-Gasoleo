# Revisión del precio del transporte por gasóleo

Calculadora gratuita de la revisión obligatoria del precio del transporte de mercancías por carretera según la variación del precio del gasóleo (Ley 15/2009, art. 38, y Orden FOM/1882/2012, condición 3.4, en la redacción de los RDL 9/2026 y 18/2026).

- Calcula G (variación del gasóleo), el coeficiente C según el tipo de vehículo y el importe a revisar (ΔP = G × P × C / 100).
- Cada precio puede tomarse a mano, por día, por semana, por mes o por mes natural.
- Los precios salen de la Excel semanal del Ministerio de Transportes y se actualizan solos.

Es una herramienta pública para clientes, sin contraseña ni control de acceso. `localStorage` solo guarda las preferencias del formulario (`gasoleo-calc`) y, en la página independiente, el idioma (`gasoleo-lang`).

## Dónde está

| | Dirección |
|---|---|
| En la web (iframe en Odoo) | `https://www.grupotrimodos.com/herramientas/calculadora-gasoleo` (inglés: `/en/herramientas/calculadora-gasoleo`) |
| Centro de herramientas | `https://www.grupotrimodos.com/herramientas` |
| Calculadora independiente | <https://grupo-trimodos.github.io/Revision-Gasoleo/> (inglés: `?lang=en`) |
| Versión de pruebas | <https://grupo-trimodos.github.io/Revision-Gasoleo/pruebas/> |
| Versión ACTM | <https://grupo-trimodos.github.io/Revision-Gasoleo/actm/> |

La imagen de «Compartir resultado» y el texto que la acompaña llevan la dirección de la web (`grupotrimodos.com/herramientas/calculadora-gasoleo`). Si la página de Odoo acaba con otra dirección, cámbiala en `PUBLIC_URL` y en `pubHost` de `index.html`.

## Diseño

La página copia el sistema visual de grupotrimodos.com, medido en la web en vivo: el mismo logotipo (`img/logo-trimodos.png`) y cabecera de 56 px, el mismo contenedor de Bootstrap (540, 720, 960, 1140 y 1320 px con 15 px de margen), Mulish 500 en los títulos y Roboto en el texto, y secciones a todo el ancho: blanco, crema `#FFEED4` para los datos y amarillo `#FCDA00` para el resultado. La referencia va en verde Stinsa y la realización en naranja Transargi.

Al final de la página, siempre a la vista y en letra pequeña, están la normativa aplicable (Ley 15/2009, art. 38; Orden FOM/1882/2012, condición 3.4; Real Decreto-ley 9/2026 y Real Decreto-ley 18/2026), el aviso de resultado orientativo y la fuente de los precios.

En el móvil (menos de 576 px de ancho, también dentro del iframe de Odoo) se muestra una versión simplificada con el mismo aspecto y solo lo esencial: título corto («Revisión del precio por gasóleo»), sin entradilla ni textos de ayuda, el detalle de las semanas usadas en una línea (los avisos de precio que falta se ven enteros), el resultado con el importe y el nuevo precio en dos columnas y el veredicto sin explicación (salvo «Faltan datos»), los desplegables más compactos, la normativa sin las fechas de entrada en vigor, el aviso legal en un solo párrafo (resultado orientativo y fuente de los precios) y el pie sin la presentación de la empresa.

La normativa es una lista (`#normsL`) que se adapta al ancho: cuando no cabe en una línea, cada norma salta entera a la siguiente, nunca se parte por la mitad, y el punto separador que quedaría al principio de una línea se oculta. En pantallas anchas «Normativa:» va a la izquierda y las normas se alinean a su derecha; en el móvil va encima. Se controla con las clases `d-only` (solo en pantallas grandes) y `m-only` (solo en el móvil); los textos en inglés de `STATIC_EN` llevan las mismas clases. En el móvil los campos y botones miden 44 px (tamaño táctil) con texto de 16 px, para que el iPhone no amplíe al tocarlos. En iPhone y iPad las fechas y los meses se dibujan sin el aspecto nativo de Safari, que les daba un ancho mínimo propio y las hacía sobresalir de la caja. El selector Manual/Día/Semana/Mes/Mes natural pone cada botón en una línea si cabe; si no (móvil muy estrecho, inglés o tableta con dos columnas), `fitSeg()` le añade la clase `tight` y el texto se parte en dos líneas. Los efectos *hover* solo se aplican con ratón.

No hay enlaces a otras webs, ni pantalla de inicio animada, ni app instalable, ni modo oscuro. La información técnica está plegada: «Opciones avanzadas» (precio para calcular G, umbral y redondeo), «Evolución del precio del gasóleo» y «Cómo se calcula» (el cálculo con sus cifras, el método, las semanas usadas y las tablas de C).

En la caja «Vehículo y contrato» está la casilla «Fijar el coeficiente C a mano»: al marcarla aparece a su lado el valor de C, que empieza con el C automático que se estaba usando. No hay opción de contrato continuado: el veredicto depende solo de G y del umbral (la regla de los contratos continuados sigue explicada en «Cómo se calcula»). Si el navegador tenía guardada esa opción de una versión anterior, se descarta.

## Modo incrustado

Se activa solo cuando la calculadora está dentro de un iframe, o con `?embed=1`. Un script en línea en `<head>` añade la clase `embedded` a `<html>` antes de pintar nada, así que no hay parpadeo. En ese modo:

- **Sin duplicados.** Se ocultan la cabecera (logotipo y selector ES/EN), el enlace «Saltar al contenido» y el pie (contactos, copyright, copiar correo): los pone Odoo.
- **Encaja en la página.** El fondo de `html` y `body` es transparente y el encabezado empieza arriba con 28 px de respiro. El contenedor es el mismo que el de Odoo, así que las columnas quedan alineadas; las bandas crema y amarilla llegan a los bordes del iframe.
- **Idioma.** Se usa `?lang=es|en`; si no viene, el mensaje `trimodos:lang` de la web, y si no, el idioma del navegador. Dentro del iframe no se lee ni se guarda `gasoleo-lang`: manda la web. El idioma también puede cambiarse en caliente con un mensaje.
- **Altura automática.** Un `ResizeObserver` sobre `<html>` envía la altura a la web cada vez que cambia (al cargar los precios, al abrir o cerrar un desplegable, al cambiar de idioma…), sin repetir valores.
- **Seguridad.** La calculadora solo habla con `https://www.grupotrimodos.com` y `https://grupotrimodos.com` (constante `PADRES` en `index.html`), siempre con ese origen como destino y nunca con `"*"`, y comprueba el origen de cada mensaje que recibe. Cuando la propia calculadora se sirve desde `localhost` o `127.0.0.1`, admite también una página padre en localhost, solo para probar en local.

Abierta directamente en github.io, la página es exactamente la de siempre, con su cabecera, su pie y su selector ES/EN.

### Mensajes

| Mensaje | Sentido | Para qué |
|---|---|---|
| `{type:"trimodos:height", height:<px>}` | calculadora → web | Ajustar la altura del iframe. |
| `{type:"trimodos:scrollTo", top:<px>}` | calculadora → web | Desplazar la página hasta un punto de la calculadora (`top` se mide desde el principio del iframe). La web suma la posición del iframe y resta su cabecera fija. Ninguna acción de la calculadora desplaza la página hoy; cualquier desplazamiento que se añada debe usar la función `irA()`. |
| `{type:"trimodos:lang", lang:"es"\|"en"}` | web → calculadora | Fijar o cambiar el idioma. El fragmento de Odoo lo envía al cargar, y así la calculadora conoce el origen de la web aunque el navegador no envíe *referrer*. |

## Código para Odoo (carpeta `odoo/`)

| Fichero | Para qué |
|---|---|
| `odoo/bloque-calculadora.html` | Bloque «Código incrustado» de la página de la calculadora: iframe, idioma y altura automática. |
| `odoo/bloque-herramientas.html` | Bloque «Código incrustado» de la página «Herramientas»: cabecera y rejilla de tarjetas. |
| `odoo/prueba-local.html` | Página de prueba que imita Odoo y monta los dos bloques (ver «Comprobación»). |

### Fragmento de la calculadora

Es el contenido de `odoo/bloque-calculadora.html`; se pega tal cual:

```html
<!--
  Calculadora de revisión del precio del transporte por gasóleo · Grupo Trimodos
  Pegar tal cual en un bloque «Código incrustado» de Odoo, en la página /herramientas/calculadora-gasoleo.

  - El idioma sale de la propia página de Odoo (/en/… → inglés): no hay que traducir nada.
  - La calculadora envía su altura y el iframe se ajusta: sin barra de desplazamiento propia.
  - Solo se aceptan mensajes del origen de la calculadora (el de data-base) y del propio iframe.
  - El estilo de abajo deja el bloque a ancho completo y sin márgenes de sección, para que las bandas
    crema y amarilla de la calculadora lleguen a los bordes de la página.
  - Si Odoo carga el bloque dos veces, cada iframe se inicia una sola vez y el oyente de mensajes es único.
-->
<style>
  .s_embed_code:has(.tt-calc){padding-top:0!important;padding-bottom:0!important}
  .s_embed_code:has(.tt-calc) .s_embed_code_embedded,
  .s_embed_code:has(.tt-calc) > .container,
  .s_embed_code:has(.tt-calc) > .container-fluid,
  .s_embed_code:has(.tt-calc) > .o_container_small{max-width:none;width:100%;padding-left:0;padding-right:0}
  .tt-calc{margin:0;padding:0}
  .tt-calc-frame{display:block;width:100%;height:1600px;min-height:600px;border:0;overflow:hidden;background:transparent}
</style>
<div class="tt-calc">
  <iframe class="tt-calc-frame" data-trimodos-calc
    data-base="https://grupo-trimodos.github.io/Revision-Gasoleo/"
    src="https://grupo-trimodos.github.io/Revision-Gasoleo/?embed=1&amp;lang=es"
    title="Calculadora de revisión del precio del transporte por gasóleo"
    loading="lazy" scrolling="no" allow="clipboard-write; web-share"></iframe>
</div>
<script>
(function () {
  "use strict";
  // en_US, en-US, en-GB… → inglés; el resto → español
  function idioma() { return /^en/i.test(document.documentElement.lang || "") ? "en" : "es"; }
  function origenDe(f) { return new URL(f.getAttribute("data-base")).origin; }
  function marcos() { return Array.prototype.slice.call(document.querySelectorAll("iframe[data-trimodos-calc]")); }
  // Altura de la cabecera fija de Odoo (header#top), para que el desplazamiento no quede debajo de ella
  function cabecera() {
    var h = document.querySelector("header#top") || document.querySelector("header");
    if (!h) return 0;
    var pos = getComputedStyle(h).position;
    var fija = pos === "fixed" || pos === "sticky" || h.classList.contains("o_top_fixed_element") || h.classList.contains("o_header_affixed");
    return fija ? h.getBoundingClientRect().height : 0;
  }
  function iniciar(f) {
    if (f.getAttribute("data-tt-listo")) return;
    f.setAttribute("data-tt-listo", "1");
    var lang = idioma(), url = new URL(f.getAttribute("data-base"));
    url.searchParams.set("embed", "1");
    url.searchParams.set("lang", lang);
    if (f.src !== url.href) f.src = url.href;   // en español ya viene puesto; solo cambia para inglés
    f.addEventListener("load", function () {
      // Saludo con el idioma: así la calculadora conoce el origen de la página aunque el navegador no envíe referrer
      try { f.contentWindow.postMessage({ type: "trimodos:lang", lang: lang }, origenDe(f)); } catch (e) {}
      // Si en 8 s no ha llegado ninguna altura (script bloqueado, red lenta…), se deja una altura que lo muestre todo
      setTimeout(function () { if (!f.getAttribute("data-tt-alto")) f.style.height = "6500px"; }, 8000);
    });
  }
  marcos().forEach(iniciar);
  if (window.__trimodosCalc) return;
  window.__trimodosCalc = true;
  window.addEventListener("message", function (e) {
    var f = marcos().filter(function (x) { return x.contentWindow === e.source; })[0];
    if (!f || e.origin !== origenDe(f)) return;
    var d = e.data || {};
    if (d.type === "trimodos:height") {
      var h = Math.ceil(Number(d.height));
      if (h > 200 && h < 30000) { f.style.height = h + "px"; f.setAttribute("data-tt-alto", "1"); }
    } else if (d.type === "trimodos:scrollTo") {
      var top = Number(d.top);
      if (!isFinite(top)) return;
      var destino = f.getBoundingClientRect().top + window.pageYOffset + top - cabecera() - 12;
      var suave = !(window.matchMedia && matchMedia("(prefers-reduced-motion: reduce)").matches);
      window.scrollTo({ top: Math.max(0, destino), behavior: suave ? "smooth" : "auto" });
    }
  });
})();
</script>
```

Qué hace:

- Toma el idioma de `<html lang>` de la página de Odoo (`en_US`, `en-US`… → inglés; el resto → español). La versión inglesa de la web (`/en/…`) muestra la calculadora en inglés sin traducir nada.
- Solo acepta mensajes del origen de la calculadora (`https://grupo-trimodos.github.io`, sacado de `data-base`) y del propio iframe.
- `trimodos:height` ajusta la altura (empieza en 1600 px, con `min-height` de 600 px para que no salte). Si en 8 s no llega ninguna altura, deja 6500 px para que se vea todo.
- `trimodos:scrollTo` desplaza la ventana sumando la posición del iframe y restando la altura de la cabecera fija de Odoo (`header#top`).
- El iframe no tiene barra de desplazamiento propia (`scrolling="no"`), se carga en diferido (`loading="lazy"`) y lleva `allow="clipboard-write; web-share"` para que «Compartir resultado» pueda copiar la imagen o abrir el menú de compartir del móvil.
- Su estilo quita el contenedor y el relleno del bloque «Código incrustado» que lo contiene (con `:has(.tt-calc)`), para que la calculadora vaya a ancho completo aunque se olvide ajustarlo en el editor.
- Si Odoo carga el bloque dos veces, cada iframe se inicia una sola vez y el oyente de mensajes es único.

### Centro de herramientas

`odoo/bloque-herramientas.html` lleva una cabecera («HERRAMIENTAS», «Herramientas para el transporte» y una entradilla, con el semicírculo amarillo de la web) y una rejilla de tarjetas: 3 columnas en escritorio, 2 en tableta y 1 en móvil. Cada tarjeta es un único enlace con icono, categoría, título, descripción y el botón «Abrir herramienta →». Al pasar el ratón sube 3 px y toma el color de la herramienta.

- Todas las clases empiezan por `tt-` para no chocar con Odoo ni con Bootstrap.
- El color de cada tarjeta va en cuatro variables (`--tt-color`, `--tt-soft`, `--tt-deep`, `--tt-on`): amarillo Trimodos, verde Stinsa, naranja Transargi (la calculadora de gasóleo, por ser carretera) o azul. Todas cumplen el contraste AA.
- Las dos tarjetas «Próximamente» son ejemplos: están desactivadas (opacidad 0,6, sin enlace, `aria-disabled="true"`). El comentario del principio del fichero explica cómo añadir una herramienta, activar una de ejemplo o borrarla.
- Los textos están en español. Para la versión inglesa de la web se traducen con el traductor de Odoo.

**Alternativa sin código.** Si preferís añadir tarjetas desde el editor sin tocar HTML, el bloque nativo «Tarjetas» (o «Características») de Odoo da un resultado parecido. Ponedlo en 3 columnas, con fondo blanco en cada tarjeta, radio de borde de 16–20 px, borde de 1 px gris claro (`#E9ECEF`) y sombra pequeña. Usad un icono por tarjeta con un círculo de color suave detrás, el título en el estilo «Título 5» y un botón en píldora con el estilo secundario del tema. Lo que no se consigue sin código es el efecto al pasar el ratón ni que toda la tarjeta sea un único enlace. Por eso se recomienda el bloque HTML.

## Pasos en Odoo

1. **Página de la calculadora.** Abre la página actual de la calculadora y entra en **Sitio → Propiedades**:
   - URL: `/herramientas/calculadora-gasoleo` (si Odoo no admite la barra, `/calculadora-gasoleo`).
   - Título SEO: «Calculadora de revisión del precio por gasóleo | Grupo Trimodos».
   - Descripción: «Calcula cuánto debe cambiar el precio de un transporte por carretera cuando varía el gasóleo, con los precios oficiales del Ministerio de Transportes.»

   En el editor, haz clic en el bloque «Código incrustado» → **Editar código**, borra lo que haya y pega `odoo/bloque-calculadora.html`. En las opciones del bloque pon el ancho en **Completo** y el relleno superior e inferior a 0 (el código ya lo fuerza, pero así se ve bien también en el editor). Guarda y **publica** la página.
2. **Página «Herramientas».** **Sitio → Nuevo → Página**, con el nombre «Herramientas» y la URL `/herramientas`. Arrastra un bloque «Código incrustado», pulsa **Editar código** y pega `odoo/bloque-herramientas.html`. Guarda y **publica**.
3. **Menú.** **Sitio → Menús**: el elemento «Herramientas» debe apuntar a `/herramientas`. Opcional: un submenú con cada herramienta (por ejemplo, «Revisión por gasóleo» → `/herramientas/calculadora-gasoleo`).
4. **Redirecciones.** **Sitio → Configuración → Redirecciones** (o **Sitio → Redirecciones**, según la versión), tipo **301**, desde `/https/grupo-trimodos-github-io/revision-gasoleo` y desde `/calculadora-gasolina` hacia `/herramientas/calculadora-gasoleo`.
5. **Inglés.** Abre `/en/herramientas`, pulsa **Traducir**, traduce los textos de las tarjetas y guarda. Abre `/en/herramientas/calculadora-gasoleo` y comprueba que la calculadora sale en inglés.

## Comprobación

**Prueba local de la integración.** En la raíz del repositorio, `python3 -m http.server 8731`, y abre
`http://localhost:8731/odoo/prueba-local.html?calc=http://127.0.0.1:8731/`. La página padre (`localhost`) y la calculadora (`127.0.0.1`) son orígenes distintos, como en la web real. Parámetros: `pagina=herramientas|calculadora|ambas` y `lang=en` (pone `<html lang="en-US">`, como la versión inglesa de Odoo).

Resultado de la prueba (Chrome, 19/11/2026):

- Un solo logotipo, un solo selector de idioma y un solo pie: los de la web. Dentro de la calculadora, 0.
- El iframe ocupa todo el ancho (1280 de 1280 px) y la sección no deja relleno.
- La altura sigue al contenido sin barra interna. A 1280 px: 2024 px; 2200 al abrir «Opciones avanzadas»; 3347 al abrir también «Cómo se calcula»; 3171 al cerrar la primera; 2024 al cerrar la segunda.
- Con `<html lang="en-US">` la calculadora sale en inglés, y el mensaje `trimodos:lang` la cambia en caliente.
- `trimodos:scrollTo` lleva la página justo al punto pedido, debajo de la cabecera fija.
- Una página de otro origen no recibe ningún mensaje.
- Consola sin errores y ningún `postMessage` con `"*"`.
- Sin desplazamiento horizontal a 375, 768 y 1280 px.
- La página independiente es idéntica, píxel a píxel, a la versión anterior (1440 y 390 px).

**Mismos resultados que la versión original** (13 casos: vehículos A–D; transportes antes del 22/03/2026, entre el 22/03 y el 15/04, del 16/04 al 30/06 y desde el 01/07/2026; los cinco modos de precio; inglés). Se comparan G (también sin redondear, con 6 decimales), C, %, importe, nuevo precio, veredicto y los precios de referencia y realización:

| Caso | Escenario | G | C | % transporte | Importe | Nuevo precio | Revisión | Igual a la versión original (independiente / incrustada) |
|---|---|---|---|---|---|---|---|---|
| 01 | A · Día → Semana · antes del 22/03/2026 (PVP, C fijo) | +3,49 % | 0,30 | +1,05 % | +19,37 € | 1869,37 € | No obligatoria | Sí / Sí |
| 02 | B ≥7,5 t · Mes → Mes · 16/04–30/06/2026 (Pmed, C RDL 9 por PAI) | +36,57 % | 0,30 | +10,97 % | +100,99 € | 1021,49 € | Obligatoria | Sí / Sí |
| 03 | C <7,5 t · Mes natural → Mes natural · junio 2026 (RDL 9) | +23,43 % | 0,30 | +7,03 % | +44,99 € | 684,99 € | Obligatoria | Sí / Sí |
| 04 | D · Semana → Día · después del 01/07/2026 (RDL 18) | +32,28 % | 0,20 | +6,46 % | +20,01 € | 330,01 € | Obligatoria | Sí / Sí |
| 05 | A · Manual → Manual (transporte 10/09/2026, RDL 18 con precio manual) | +11,72 % | 0,40 | +4,69 % | +112,51 € | 2512,51 € | Obligatoria | Sí / Sí |
| 06 | B <7,5 t · Día → Mes natural octubre 2026 (RDL 18, Pmed < 7,5 t) | +36,59 % | 0,30 | +10,98 % | +109,77 € | 1109,77 € | Obligatoria | Sí / Sí |
| 07 | C · precio PVP forzado, sin redondeo, umbral 3 %, continuado · semana 01/04/2026 | +25,7918 % | 0,20 | +5,16 % | +40,12 € | 817,89 € | Obligatoria | Sí / Sí |
| 08 | D · redondeo 0, C a mano 0,35 · Mes → Mes julio 2026 | +14 % | 0,35 | +4,90 % | +22,05 € | 472,05 € | Obligatoria | Sí / Sí |
| 09 | A · Mes natural dic 2025 → Día 30/03/2026 (PAI, C fijo) | +63,27 % | 0,30 | +18,98 % | +284,72 € | 1784,72 € | Obligatoria | Sí / Sí |
| 10 | B · Semana → Semana 16/04/2026 (frontera RDL 9) · redondeo 4 | +54,2426 % | 0,30 | +16,27 % | +200,90 € | 1435,46 € | Obligatoria | Sí / Sí |
| 11 | A · bajada del gasóleo: Día 2026-04-02 → Mes natural nov 2026 (mes incompleto) | -6,58 % | 0,40 | -2,63 % | -26,32 € | 973,68 € | Obligatoria | Sí / Sí |
| 12 | Estado por defecto (sin preferencias guardadas) | +40,28 % | 0,40 | +16,11 % | +161,12 € | 1161,12 € | Obligatoria | Sí / Sí |
| 13 | Caso 04 en inglés (?lang=en) | +32.28% | 0.20 | +6.46% | +€20.01 | €330.01 | Obligatoria | Sí / Sí |

## Publicación

`.github/workflows/publicar.yml` publica la web en GitHub Pages en cada cambio de `main` y cuando termina la actualización de precios. Este repositorio es un *fork* de `Elomarmu/Revision-Gasoleo`, y GitHub desactiva las Actions en los forks. Para que los precios se actualicen solos hay que hacer esto una vez:

1. **Actions** → «I understand my workflows, go ahead and enable them».
2. **Settings → Pages → Build and deployment → Source: GitHub Actions**.
3. **Actions** → «Publicar la calculadora» → **Run workflow**, y después «Actualizar precios del gasóleo» → **Run workflow**.

## Cómo se actualizan los datos

`.github/workflows/actualizar.yml` ejecuta `scripts/actualizar.py` cada 2 horas de miércoles a viernes. El script busca en la página del Ministerio el enlace a la última Excel, la descarga, lee las hojas «PRECIOS_DEL_GASOLEO» y «PRECIOS DE REFERENCIA» y, si hay cambios, guarda `data.json`.

## Formato de data.json

Cada fila de `rows` es `[fecha (serial Excel), PVP, PVP sin IVA, impuesto hidrocarburos, PAI, devolución gasóleo profesional, Pmed MMA ≥ 7,5 t, Pmed MMA < 7,5 t]`, en €/litro.

## Versión de pruebas

`pruebas/index.html` es una copia de `index.html` con la banda «VERSIÓN DE PRUEBAS», las rutas a `../`, `noindex` y su propia clave de `localStorage` (`gasoleo-calc-pruebas`).

## Aviso

Herramienta orientativa. Para un litigio o una reclamación, contrasta el resultado con la calculadora oficial del Ministerio de Transportes.
