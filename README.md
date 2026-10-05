# EFMA Convertor

Convertidor de archivos que funciona completamente en el navegador. Los archivos nunca se suben a un servidor: todo se procesa en tu computadora. Sin límite de uso.

## Qué convierte

| Origen | Destino |
|---|---|
| PDF | Word (.docx) idéntico y editable, JPG, PNG, TXT, PDF (extraer páginas, girar, marca de agua, numeración) |
| Word (.docx) | PDF (conservando diseño o ajustable), HTML, TXT, Markdown |
| Imágenes (PNG, JPG, WEBP, GIF, BMP, SVG, AVIF, ICO) | PNG, JPG, WEBP, PDF |
| Hojas de cálculo (XLSX, XLS, ODS, CSV, TSV, JSON) | XLSX, CSV, JSON, HTML, PDF |
| Texto (TXT, Markdown, HTML) | PDF, Word, HTML, TXT |

También une imágenes y PDF en un solo PDF, descarga todo en un .zip y guarda un historial de cada conversión (fecha, tamaños, duración y opciones usadas), exportable a CSV.

### PDF → Word "Idéntico y editable"

El PDF se reconstruye como un documento de Word real:

- **Texto**: párrafos editables que fluyen, con la misma fuente, tamaño, color, negrita, cursiva, subrayado, alineación, sangrías y viñetas.
- **Tablas**: tablas de Word reales, con sus bordes, colores de celda, celdas combinadas, anchos y altos.
- **Imágenes**: imágenes independientes que se pueden mover y cambiar de tamaño.
- **Texto a dos columnas**: cada columna queda en su propia celda (sin bordes) y fluye por separado.
- **Portadas y fondos de color**: se conservan como imagen detrás del texto.

Para el mejor resultado, ten instaladas en tu PC las fuentes que usa el PDF (las de Office ya vienen con Windows).

## Publicar en GitHub Pages

1. Crea un repositorio nuevo en GitHub (por ejemplo `efma-convertor`).
2. Sube `index.html` y este `README.md` a la raíz del repositorio.
3. En el repositorio ve a **Settings → Pages**, en *Source* elige **Deploy from a branch**, rama `main` y carpeta `/ (root)`, y guarda.
4. En uno o dos minutos la app queda disponible en `https://TU-USUARIO.github.io/efma-convertor/`.

También puedes abrir `index.html` con doble clic y usarla directamente desde tu PC (necesita internet la primera vez para cargar las librerías).

## Librerías utilizadas

Se cargan desde jsDelivr al momento de usarlas: pdf.js, pdf-lib, docx, docx-preview, mammoth, pdfmake, html-to-pdfmake, SheetJS (xlsx), marked, html2canvas y JSZip.

## Historial

El historial se guarda en el almacenamiento del navegador (`localStorage`). Si borras los datos del navegador o usas otro navegador, el historial empieza vacío. Desde la pestaña Historial puedes exportarlo a CSV.
