# Revisión del precio del transporte por gasóleo

Calculadora gratuita de la revisión obligatoria del precio del transporte de mercancías por carretera según la variación del precio del gasóleo (Ley 15/2009, art. 38, y Orden FOM/1882/2012, condición 3.4, en la redacción de los RDL 9/2026 y 18/2026).

- Calcula G (variación del gasóleo), el coeficiente C según el tipo de vehículo y el importe a revisar (ΔP = G × P × C / 100).
- Cada precio puede tomarse a mano, por día, por semana, por mes o por mes natural.
- Permite elegir a cuántos decimales se redondea G antes de multiplicarlo por C (en «Opciones avanzadas»).
- Los precios salen de la Excel semanal del Ministerio de Transportes y se actualizan solos.

Es pública: la usan clientes desde la web corporativa, no solo el personal interno. No tiene contraseña ni ningún control de acceso, y `localStorage` solo guarda las preferencias del formulario (`gasoleo-calc`) y el idioma (`gasoleo-lang`).

## Dónde está publicada

- Calculadora: <https://grupo-trimodos.github.io/Revision-Gasoleo/>
- Versión de pruebas: <https://grupo-trimodos.github.io/Revision-Gasoleo/pruebas/>
- Versión ACTM: <https://grupo-trimodos.github.io/Revision-Gasoleo/actm/>
- Página prevista en la web: <https://www.grupotrimodos.com/calculadora-gasoleo> (inglés: `/en/calculadora-gasoleo`). Esta dirección aparece en la imagen de «Compartir resultado», en el texto que se comparte y en la etiqueta `canonical`. Si la página de Odoo acaba con otra dirección, cámbiala en `PUBLIC_URL` (y `pubHost`) de `index.html`.

## Modo incrustado (para la web de Odoo)

La calculadora sigue alojada en GitHub Pages (así se actualiza sola) y se incrusta en una página de Odoo con un `<iframe>`. Se activa con `?embed=1` o automáticamente cuando la página está dentro de un iframe. En ese modo:

- No muestra su cabecera ni su pie (los pone Odoo), ni la pantalla de inicio del logotipo. El loader solo aparece si los precios tardan en cargar.
- Fondo transparente, sin márgenes laterales y siempre en tema claro.
- No registra el *service worker* ni ofrece «Instalar app».
- Envía su altura a la página padre con `postMessage` para que el iframe crezca o encoja sin barras de desplazamiento.
- Los enlaces a grupotrimodos.com («Hablamos») se abren en la página completa; los externos (Ministerio), en una pestaña nueva.

Parámetros de la URL:

| Parámetro | Efecto |
|---|---|
| `embed=1` | Fuerza el modo incrustado. |
| `lang=es` / `lang=en` | Idioma. En modo incrustado, si viene en la URL se oculta el selector ES/EN propio (la web ya tiene el suyo). |
| `titulo=0` | Oculta el antetítulo, el título y la entradilla, para que la página de Odoo ponga los suyos como texto propio (mejor para el SEO). La fecha de los datos se sigue viendo. |

### Fragmento para pegar en Odoo

1. En la web, entra en la página (o créala: **Sitio web → + Nuevo → Página**, por ejemplo `calculadora-gasoleo`).
2. **Editar** → pestaña **Bloques** → arrastra el bloque **«Código incrustado»** (*Embed Code*; en algunas versiones está en «Contenido» o se llama «HTML»).
3. Pulsa **Editar código** en el bloque, borra el ejemplo y pega esto tal cual:

```html
<!-- Calculadora de revisión del precio del transporte por gasóleo (Grupo Trimodos) -->
<iframe id="calculadora-gasoleo"
  src="https://grupo-trimodos.github.io/Revision-Gasoleo/?embed=1&lang=es"
  title="Calculadora de revisión del precio del transporte por gasóleo"
  style="display:block;width:100%;height:2400px;border:0"
  allow="clipboard-write; web-share"></iframe>
<script>
(function () {
  var ORIGEN = "https://grupo-trimodos.github.io";
  var f = document.getElementById("calculadora-gasoleo");
  if (!f) return;
  // El idioma de la página de Odoo (es-ES, en-GB…) decide el de la calculadora
  var lang = /^en/i.test(document.documentElement.lang || "") ? "en" : "es";
  var url = new URL(f.getAttribute("src"));
  if (url.searchParams.get("lang") !== lang) { url.searchParams.set("lang", lang); f.src = url.href; }
  // La calculadora envía su altura; solo se acepta desde su origen y desde ese iframe
  window.addEventListener("message", function (e) {
    if (e.origin !== ORIGEN || e.source !== f.contentWindow) return;
    var d = e.data;
    if (!d || d.source !== "trimodos-gasoleo" || d.type !== "height") return;
    var h = Math.ceil(Number(d.height));
    if (h > 300 && h < 20000) f.style.height = h + "px";
  });
  f.addEventListener("load", function () {
    f.contentWindow.postMessage({ source: "trimodos-gasoleo", type: "hello" }, ORIGEN);
  });
})();
</script>
```

4. **Guardar** y comprobar la página fuera del editor (en el editor de Odoo los scripts no siempre se ejecutan, así que ahí el iframe puede verse con altura fija).
5. Para la versión en inglés no hace falta otro fragmento: el script lee el idioma de la página y pide `lang=en`.

Notas:

- `allow="clipboard-write; web-share"` hace falta para que «Compartir resultado» pueda copiar la imagen o abrir el menú de compartir del móvil desde dentro del iframe.
- La altura de 2400 px es solo el valor inicial: si el script no llegara a ejecutarse, la calculadora se vería entera con su propia barra de desplazamiento.
- Seguridad del `postMessage`, en los dos sentidos: la página de Odoo solo acepta mensajes de `https://grupo-trimodos.github.io` que vengan de ese iframe, y la calculadora solo envía su altura a `https://www.grupotrimodos.com`, `https://grupotrimodos.com` y, para pruebas, `http://localhost` y `http://127.0.0.1` (constante `PARENT_OK` en `index.html`). Si la web se publica en otro dominio (por ejemplo `*.odoo.com`) o la calculadora pasa a un dominio propio, hay que actualizar `PARENT_OK` y `ORIGEN`.
- El contenido del iframe no cuenta para el SEO de la página de Odoo. Pon en Odoo el título de la página, la meta descripción (**Promocionar → Optimizar SEO**) y, si quieres, el título y un texto introductorio propios con `&titulo=0` en el `src`.

## Diseño

Sigue el sistema visual de grupotrimodos.com: Mulish (títulos, peso 500–600), Roboto 16 px (texto), IBM Plex Mono solo en cifras, fondo blanco con secciones en `#F3F2F2`, texto `#212529`/`#6C757D`, resultado en `#2D3142` con rótulos amarillos, botón principal sólido con radio 0,4 rem y botones secundarios en píldora. La referencia va en verde Stinsa y la realización en naranja Transargi (para el texto de las etiquetas se usan tonos oscurecidos que cumplen el contraste AA). En la página independiente hay modo oscuro; en el iframe, no.

### Decisión pendiente: el color morado de los botones

Los botones de la web («Contacto», «Contáctanos») usan `#714B67`, que es el color por defecto de los temas de Odoo y no aparece en ningún logotipo del grupo. Para que la calculadora coincida con la web, el botón «Hablamos» y los enlaces lo usan, pero está en una sola variable:

```css
--accent:#714b67; --accent-hover:#5f3f57; --link:#714b67; --focus:#714b67;
```

Si se decide que no es intencionado y se cambia en el tema de Odoo, basta con cambiar esas variables en `:root` (`index.html` y `pruebas/index.html`). Cualquier color oscuro con contraste de 4,5:1 o más con el blanco sirve (por ejemplo `#2D3142`).

## Cómo se actualizan los datos

`.github/workflows/actualizar.yml` ejecuta `scripts/actualizar.py` cada 2 horas de miércoles a viernes. El script busca en la página del Ministerio el enlace a la última Excel, la descarga, lee las hojas «PRECIOS_DEL_GASOLEO» y «PRECIOS DE REFERENCIA» y, si hay cambios, guarda `data.json`. `.github/workflows/publicar.yml` publica la web en GitHub Pages en cada cambio de `main` y cuando termina la actualización de precios.

Requisitos en este repositorio (es un *fork* de `Elomarmu/Revision-Gasoleo`, y GitHub desactiva las Actions en los forks):

1. **Actions** → «I understand my workflows, go ahead and enable them».
2. **Settings → Pages → Build and deployment → Source: GitHub Actions**. El repositorio es público, así que la web también lo es para cualquiera.

Para forzar una actualización: pestaña **Actions** → «Actualizar precios del gasóleo» → **Run workflow**.

Los logotipos de `img/` son los de la app DECA Trimodos.

## Formato de data.json

Cada fila de `rows` es `[fecha (serial Excel), PVP, PVP sin IVA, impuesto hidrocarburos, PAI, devolución gasóleo profesional, Pmed MMA ≥ 7,5 t, Pmed MMA < 7,5 t]`, en €/litro.

## Aviso

Herramienta orientativa. Para un litigio o una reclamación, contrasta el resultado con la calculadora oficial del Ministerio de Transportes.
