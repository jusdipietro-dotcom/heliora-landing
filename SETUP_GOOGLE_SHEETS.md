# Setup: Google Sheets como base de datos del formulario

## Paso 1 — Crear el Google Sheet

1. Ir a https://sheets.google.com y crear una hoja nueva
2. Nombrarla: **HELIORA - Leads Landing**
3. En la fila 1 (encabezados), escribir exactamente:

| A | B | C | D | E | F |
|---|---|---|---|---|---|
| Fecha | Nombre | Telefono | Email | Tipo Propiedad | Factura Mensual |

4. Copiar el ID del Sheet de la URL (es el string largo entre /d/ y /edit):
   `https://docs.google.com/spreadsheets/d/ESTE_ES_EL_ID/edit`

## Paso 2 — Crear el Apps Script

1. Dentro del Google Sheet, ir a **Extensiones > Apps Script**
2. Borrar todo el codigo que aparece
3. Pegar el siguiente codigo:

```javascript
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    var data = JSON.parse(e.postData.contents);

    sheet.appendRow([
      new Date().toLocaleString('es-AR', { timeZone: 'America/Argentina/Buenos_Aires' }),
      data.nombre || '',
      data.telefono || '',
      data.email || '',
      data.tipo || '',
      data.factura || ''
    ]);

    return ContentService
      .createTextOutput(JSON.stringify({ status: 'ok' }))
      .setMimeType(ContentService.MimeType.JSON);

  } catch (error) {
    return ContentService
      .createTextOutput(JSON.stringify({ status: 'error', message: error.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

4. Guardar (Ctrl+S)

## Paso 3 — Deployar como Web App

1. Click en **Implementar > Nueva implementacion**
2. En tipo, seleccionar **App web**
3. Configurar:
   - Descripcion: "HELIORA Lead Form"
   - Ejecutar como: **Yo** (tu cuenta)
   - Quien tiene acceso: **Cualquier persona**
4. Click en **Implementar**
5. Autorizar cuando lo pida (es tu propia cuenta)
6. **Copiar la URL** que te da (empieza con `https://script.google.com/macros/s/...`)

## Paso 4 — Pegar la URL en el index.html

Abrir `index.html` y buscar esta linea:

```
const GOOGLE_SCRIPT_URL = 'TU_URL_DE_GOOGLE_APPS_SCRIPT_AQUI';
```

Reemplazar `TU_URL_DE_GOOGLE_APPS_SCRIPT_AQUI` con la URL que copiaste.

## Paso 5 — Pegar el numero de WhatsApp

Buscar `54XXXXXXXXXX` en el archivo y reemplazar con el numero real (ej: `541123456789`).

## Paso 6 — Probar

1. Abrir index.html en el navegador
2. Completar el formulario y enviarlo
3. Verificar que:
   - Se abre WhatsApp con el mensaje pre-armado
   - Los datos aparecen en el Google Sheet

## Como verificar que funciona

- Abrir el Google Sheet y ver si aparece la fila nueva
- Si no aparece: ir a Apps Script > Ejecuciones para ver errores
- El formulario muestra "Datos guardados" al enviar correctamente
