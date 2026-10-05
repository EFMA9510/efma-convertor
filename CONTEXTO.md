# EFMA Convertor — Contexto completo del proyecto

> Documento para retomar el proyecto (en Codex, Claude u otro asistente) sin repetir el contexto.
> Guárdalo en la raíz del repositorio. Si lo nombras `AGENTS.md`, Codex lo lee automáticamente.

---

## 1. Resumen

**EFMA Convertor** es una app web que convierte archivos (documentos, PDF, imágenes, hojas de cálculo y texto) **directamente en el navegador**. No usa servidor: los archivos nunca salen de la computadora del usuario. No tiene límite de uso.

- **Repositorio:** `EFMA9510/efma-convertor` (público)
- **Publicación prevista:** GitHub Pages, rama `main`, carpeta raíz → `https://efma9510.github.io/efma-convertor/` (dirección esperada, sin verificar)
- **Archivos del repo:** `index.html` (la app completa) y `README.md`
- **Idioma de la interfaz:** español (Costa Rica)
- **Dueño:** usuario en Windows; el objetivo principal es que **PDF → Word quede idéntico y totalmente editable**.

---

## 2. Requisitos del usuario (lo que pidió)

1. Convertir cualquier tipo de archivo a otro (lo más común: Word ↔ PDF), imágenes y archivos varios.
2. Interfaz muy intuitiva, pero con control total.
3. **Registro (historial)** de todo lo convertido.
4. **Opciones de personalización** al convertir (p. ej. Word → PDF conservando o ajustando formato).
5. **Subir** archivos desde la PC y **descargar** el resultado a la PC.
6. **Sin restricciones** de cantidad de usos.
7. Que funcione **solo en el navegador** (se descartó la versión local de escritorio).
8. Nombre de la app: **EFMA Convertor**.
9. **PDF → Word:** el resultado debe quedar **igual al original sin tener que corregir nada**, aunque tarde más: fuentes, tamaños, colores, imágenes, tablas con sus colores, portadas y formatos de texto. **Todo el texto debe ser manipulable**, las tablas deben ser tablas reales (no imágenes) y las imágenes deben poder moverse y ajustarse.
10. Subirla a GitHub y tener un enlace que abra en pestaña nueva.

---

## 3. Arquitectura

- **Un solo archivo `index.html`** con HTML + CSS + JS. No hay build, npm ni frameworks.
- Las librerías se **cargan bajo demanda desde jsDelivr** con la función `lib(nombre)` (cada una solo cuando se necesita). Versiones fijas:

| Clave en `LIB` | Librería | Uso |
|---|---|---|
| `pdfjs` | pdfjs-dist 3.11.174 (+ worker) | Leer, dibujar y extraer texto de PDF |
| `pdflib` | pdf-lib 1.17.1 | Crear y editar PDF, unir PDF |
| `docx` | docx 8.5.0 | Generar .docx (conversiones secundarias) |
| `docxPreview` | docx-preview 0.3.3 (+ jszip) | Dibujar un .docx en HTML (Word → PDF fiel) |
| `mammoth` | mammoth 1.8.0 | .docx → HTML / texto / Markdown |
| `pdfmake` | pdfmake 0.2.10 + vfs_fonts + html-to-pdfmake 2.5.35 | HTML → PDF con formato ajustable |
| `xlsx` | SheetJS 0.18.5 | Hojas de cálculo |
| `marked` | marked 12.0.2 | Markdown → HTML |
| `html2canvas` | html2canvas 1.4.1 | Captura de páginas Word para PDF fiel |
| `jszip` | jszip 3.10.1 | .zip y escritura manual de .docx |

> ⚠️ `docx` y `docx-preview` usan el **mismo global `window.docx`**. El cargador lo maneja, pero hay que tenerlo en cuenta.

- **Diseño:** tokens CSS en `:root` con modo claro y oscuro (`prefers-color-scheme` y `[data-theme]`). Fuentes de Google: Bricolage Grotesque (títulos), IBM Plex Sans (cuerpo), IBM Plex Mono (datos). Color de acento verde azulado `#0E6B66`, resaltado amarillo `#F2C14E`. Diseño adaptable a celular (probado a 400 px sin desplazamiento horizontal).

---

## 4. Interfaz

Tres pestañas:

1. **Convertir**
   - Zona para arrastrar archivos o botón "Elegir archivos"; botón "Probar con ejemplos" (genera una imagen, un CSV y un Markdown de muestra).
   - Lista de archivos: chip del formato de origen → selector del formato de destino, tamaño, páginas u hojas, estado, botón Descargar, subir/bajar/quitar.
   - Barra: **Convertir todo**, **Unir en un PDF**, **Descargar todo (.zip)**, **Vaciar lista**.
   - Panel **Opciones** (derecha): opciones del archivo seleccionado, "Convertir este archivo", "Aplicar a todos los .ext", "Restablecer".
2. **Historial:** estadísticas (conversiones, % exitosas, datos procesados, ruta más usada), búsqueda, filtros por tipo y estado, tabla con fecha, original, resultado, tamaños, tiempo y opciones usadas; volver a descargar (solo en la misma sesión), **Exportar CSV** y **Borrar historial** (con confirmación dentro de la página).
3. **Formatos:** tabla de qué se convierte a qué.

---

## 5. Conversiones soportadas

| Origen | Destinos | Opciones principales |
|---|---|---|
| **PDF** | DOCX, JPG, PNG, TXT, PDF (editar) | DOCX: modo, páginas, nitidez, ajustar ancho de líneas · Imágenes: páginas, ppp, calidad, B/N (varias páginas → .zip) · TXT: unir líneas, marcar páginas · PDF: extraer páginas, girar, marca de agua (texto, opacidad, color), numeración (posición y formato), título |
| **DOCX** | PDF, HTML, TXT, MD | PDF modo **"Conservar diseño original"** (docx-preview + html2canvas, nitidez y compresión) o **"Ajustable"** (mammoth + pdfmake: tamaño de página, orientación, márgenes, tamaño de letra, interlineado, alineación, imágenes, numerar páginas, encabezado) |
| **Imágenes** (PNG, JPG, WEBP, GIF, BMP, SVG, AVIF, ICO) | PNG, JPG, WEBP, PDF | Redimensionar (% o px), rotar, voltear, B/N, calidad, fondo para transparencias, página PDF y márgenes |
| **Hojas** (XLSX, XLS, XLSM, ODS, CSV, TSV, JSON) | XLSX, CSV, JSON, HTML, PDF | Hoja, separador, BOM para Excel, estructura JSON, fijar encabezado, ancho de columnas, PDF (página, orientación, letra, cuadrícula, filas alternas, numeración) |
| **Texto** (TXT, MD, HTML) | PDF, DOCX, HTML, TXT | Página, márgenes, fuente, tamaño, interlineado, alineación, respetar saltos de línea |
| **Unir en un PDF** | PDF | Orden de la lista, tamaño de páginas para imágenes, márgenes, calidad, nombre |

**No soportados en el navegador** (`kind: 'local'`): .doc, .ppt/.pptx, .odt, .odp, .rtf, .epub, .heic, .tif/.tiff, audio y video. La app lo indica y sugiere, por ejemplo, guardar .doc como .docx desde Word.

---

## 6. Mapa del código (buscar por nombre en `index.html`)

### Utilidades y plataforma
- `lib(name)` / `LIB`: cargador diferido de librerías.
- `use(name)`: capacidades de claude.ai (`downloads`, `db`, `user`) cuando la app corre como artefacto de Claude. En GitHub Pages devuelven `null` y se usan los mecanismos normales.
- `saveFile(blob, filename)`: descarga (capacidad `downloads` o `<a download>`).
- `initHistory`, `addHistory`, `clearHistory`, `renderHistory`: historial. En GitHub Pages siempre usa **localStorage**, clave `transforma.history.v1` (se conservó el nombre original para no perder historiales).

### Estado e interfaz
- `state` (`files`, `sel`, `panel`, `mergeOpts`, `mergeResult`), `sessionBlobs` (resultados de la sesión para volver a descargar).
- `KIND_BY_EXT`, `targetsFor(f)`, `defaultTarget(f)`.
- `schemaFor(f)`: opciones de cada conversión. Campos con `key, label, type (select|number|text|toggle|range|color|note|section), def, show(o)`.
- `renderQueue`, `renderOptions`, `renderMergePanel`, `renderMatrix`.
- `runOne`, `runAll`, `runMerge`, `zipAll`, `summarize` (resumen de opciones para el historial).

### Conversión
- `convert(f, report)`: despacha según `f.kind` y `f.target`.
- `htmlToPdf`, `htmlToDocx`, `sourceToHtml`, `wrapHtml`, `htmlToText`, `readWorkbook`.
- `pdfTextBlocks`, `joinParaLines`: modo PDF → Word "Solo texto".

### PDF → Word "Idéntico y editable" (lo más importante)
`pdfToDocxExact(doc, idx, o, report)` **escribe el .docx a mano** (XML WordprocessingML + JSZip), sin la librería `docx`. Para cada página:

1. **`renderCapture(page, ctx, viewport)`**: dibuja la página en un canvas **sin el texto horizontal**. Para eso intercepta temporalmente métodos de `CanvasRenderingContext2D` (`fillText`, `strokeText`, `moveTo`, `lineTo`, `rect`, `fill`, `stroke`, `fillRect`, `drawImage`…) y captura:
   - **glifos**: posición y **color real** (de `fillStyle`/`strokeStyle`);
   - **rectángulos rellenos** (fondos de celdas, bordes finos);
   - **segmentos de línea** (bordes de tabla, subrayados);
   - **imágenes** (posición).
   Solo cuenta lo dibujado en el canvas principal (`ctx`).
2. **Texto**: `page.getTextContent()`. Para cada pieza: tamaño, posición, ancho, **fuente** (`mapFont()` normaliza nombres como `ABCDEF+Arial-BoldMT` → Arial negrita; `usableFont()` cambia a una fuente instalada si la original no existe en la PC), **color** del glifo (respaldo: `sampleTextColor()` por píxeles), contraste mínimo (si el texto casi no se distingue del fondo pasa a negro o blanco). `fixSymbols()` convierte viñetas de fuentes Symbol/Wingdings (U+F0B7, etc.) en caracteres normales. Se descarta el texto invisible (capas OCR).
3. **`detectTables(cap, pw, ph)`**: reúne líneas horizontales y verticales que se tocan, deduce la cuadrícula, detecta **celdas combinadas** (sin borde interno), bordes por lado (grosor y color) y **sombreado** de cada celda.
4. **`detectColumns(rows)`**: texto a dos columnas → tabla sin bordes de 1×2 (cada columna fluye por separado). Ignora filas que son viñetas.
5. **`groupRows()` → `buildParagraphs(rows, box)` → `paraRuns(p, o, measure)`**: arma **párrafos que fluyen**:
   - une líneas consecutivas del mismo párrafo (y quita guiones de corte);
   - detecta **alineación** (izquierda, centrado, derecha, justificado), **sangrías** (izquierda, primera línea, francesa), **viñetas y numeración** (`MARKER`), **tabulaciones**, **interlineado** y espacio antes;
   - agrupa en tramos con el mismo formato (fuente, tamaño, color, negrita, cursiva, **subrayado**);
   - compensa diferencias de ancho de fuente con espaciado de caracteres (`w:spacing`), solo si la fuente está instalada y el párrafo no está justificado.
6. **Imágenes**: se recortan del canvas sin texto (`canvasCrop`) y se insertan como imágenes de Word **en línea** (o **flotantes** si tienen texto al lado), movibles y redimensionables. Las imágenes dentro de celdas van dentro de la celda.
7. **Fondo restante**: se borran del canvas las zonas de tablas, imágenes y subrayados; lo que queda (degradados, recuadros de color, formas) se agrega como **imagen detrás del texto** solo si la página no queda en blanco (`isBlank`).
8. **Márgenes de página** según el texto (`mL`, `mR` simétricos salvo que el texto llegue más lejos; `mT` según el primer bloque). Cada página es una **sección** de Word con su tamaño y orientación.
9. Helpers XML: `runXml`, `pXml`, `inlineImgXml`, `anchorImgXml`, `xmlEsc`, `tw()` (puntos → twips), `EMU = 12700` (EMU por punto).

Opciones del modo (en `schemaFor`): `mode` (`exact` | `text` | `image`), `pages`, `dpi` (110/150/**200**/300), `fitText` (por defecto activado).

---

## 7. Historial de actualizaciones

### v1 — Primera versión (nombre provisional "Transforma")
- App en el navegador con todas las conversiones de la sección 5, opciones por conversión, unir PDF, .zip, historial con estadísticas, filtros y exportación CSV, pestaña de formatos, modo claro/oscuro y diseño adaptable.
- Se empezó una versión local para Windows (FastAPI + LibreOffice + PyMuPDF + FFmpeg). **Se descartó** a pedido del usuario: solo navegador.

### v2 — Nombre nuevo y primer PDF → Word "idéntico"
- Nombre cambiado a **EFMA Convertor** en la interfaz, metadatos y nombres de descarga.
- Se quitaron todas las menciones a la versión local.
- Se creó la versión independiente para GitHub (`index.html` con `<head>` propio) y `README.md`.
- PDF → Word "Idéntico y editable" versión 1: fondo de página sin texto + cada línea como párrafo con **marco** (`w:framePr`) en posición fija.
- **Problema reportado:** al convertir, el texto salía recortado o desordenado.

### v3 — Cuadros de texto
- Se cambió a **cuadros de texto flotantes** (`wps:wsp`) por línea sobre una imagen de fondo; color tomado del glifo; contraste mínimo; fuentes sustituidas por instaladas.
- **Problema reportado (con capturas de un PDF de plan de nutrición "Guía Completa de Nutrición Deportiva – Lean Bulk"):** en Microsoft Word todo el texto quedó amontonado arriba, casi blanco e ilegible, y las tablas salieron como imagen vacía. El usuario exigió **todo el texto manipulable** y **tablas reales**.

### v4 — Reconstrucción con estructura real (versión actual)
- PDF → Word reescrito desde cero (sección 6): **párrafos que fluyen**, **tablas de Word reales** con bordes, sombreado y celdas combinadas, **imágenes independientes**, viñetas, alineación, sangrías, subrayado, dos columnas, fondo solo para lo decorativo. Sin marcos ni cuadros de texto.
- Detección de subrayado que no confunde los bordes de tabla.
- Pruebas (convertido a PDF con LibreOffice y comparado con PyMuPDF) con 4 PDF: plan de nutrición simulado (tablas de 2 columnas, títulos, viñetas en Arial), informe con portada y logo, tabla con encabezado a color, memoria a dos columnas con portada en degradado. Resultados: **mismo número de páginas**, texto a **1–2 pt** de su posición (hasta ~6 pt al final de algunas páginas), cortes de línea iguales al original, todas las demás conversiones sin errores.
- **Aún sin verificar en Microsoft Word real** con el PDF del usuario.

### Publicación
- El repositorio `EFMA9510/efma-convertor` ya existe. El usuario pidió subir la última versión y activar Pages; la subida desde Claude quedó pendiente porque se canceló la autorización de GitHub.

---

## 8. Pendientes y problemas conocidos

1. **Probar PDF → Word en Microsoft Word** con el PDF real del usuario (plan de nutrición). Es la prioridad.
2. Tablas **sin bordes** (alineadas solo con espacios) salen como párrafos con tabulaciones, no como tabla.
3. Encabezados y pies de página repetidos se convierten como texto normal en cada página (no como encabezado/pie de Word).
4. **PDF escaneados:** sin OCR; el texto queda en la imagen de fondo y no es editable.
5. Texto girado (vertical o en diagonal) queda en la imagen de fondo.
6. Más de dos columnas no se detecta como columnas.
7. Volver a descargar desde el historial solo funciona en la misma sesión (los archivos no se guardan).
8. Subir a GitHub la versión v4 y activar GitHub Pages.

### Lo que NO funcionó en PDF → Word (no repetir)
- Marcos `w:framePr` por línea → Word los apila y desordena tablas y viñetas.
- Cuadros de texto `wps` por línea sobre imagen de fondo → en Word el texto se amontona arriba y las tablas quedan como imagen.
- Color calculado por píxeles como fuente principal → grises casi blancos.
- Dibujar la página completa como imagen detrás del texto → tablas no editables.

---

## 9. Cómo probar

**A mano:** abrir `index.html` en Chrome o Edge (necesita internet para cargar las librerías), subir archivos, convertir y abrir el resultado.

**Automático (lo que se usó):**
1. Playwright con Chromium; interceptar `https://cdn.jsdelivr.net/npm/<paquete>@<versión>/<ruta>` y servir copias locales de los paquetes (opcional).
2. Cargar la página, `set_input_files('#file-in', …)`, elegir destino con `select_option('#t-<id>', 'docx')`, clic en `#convert-one`, esperar `.status.ok`, descargar con `[data-act="dl"]`.
3. Convertir el .docx a PDF: `soffice --headless --convert-to pdf archivo.docx`.
4. Comparar con PyMuPDF: número de páginas y `page.get_text('words')` (posición de cada palabra) contra el PDF original; generar imágenes lado a lado con `page.get_pixmap()`.
5. Validar que `word/document.xml` sea XML válido y que `python-docx` abra el archivo.
6. Probar **todas** las combinaciones de la sección 5 tras cada cambio, más unir PDF, PDF→Word en los tres modos y la vista a 400 px de ancho.

---

## 10. Reglas para seguir trabajando

- Mantener **todo en un solo `index.html`**, sin build ni dependencias nuevas fuera de jsDelivr/cdnjs con versión fija.
- Textos de la interfaz en **español claro**; los errores dicen qué pasó y cómo resolverlo.
- No usar `alert()`, `confirm()` ni `prompt()`: las confirmaciones van dentro de la página.
- Envolver todo acceso a `localStorage` en `try/catch`.
- Respetar los tokens de color y tipografía existentes; probar modo claro, oscuro y celular.
- **No romper conversiones existentes**: correr la batería de pruebas completa antes de publicar.
- Para PDF → Word, priorizar: (1) que no se pierda texto, (2) que todo sea editable, (3) que se vea igual.
