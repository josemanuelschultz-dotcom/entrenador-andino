# Auditoría previa a la integración

Fecha de auditoría: 2026-08-25

Archivos revisados:

- `src/index.html`
- `src/comparador.html`
- `CLAUDE.md`
- `docs/MANUAL-INTEGRACION.md`

## 1. Organización y navegación de `index.html`

`index.html` es una aplicación autónoma en un solo archivo: contiene el HTML, el CSS y dos bloques de JavaScript. No usa bundler ni importa módulos JavaScript externos.

### Estructura visible

1. **Cabecera** (`<header>`): marca Entrenador Andino y tres acciones:
   - `Tabla`, que abre el visor de referencia con `tabla.html`.
   - `Curva`, que abre el visor de referencia con `curva.html`.
   - `Historial`, que muestra u oculta el historial de análisis.
2. **Visor de referencia** (`#viewerOv`): una capa fija que contiene dos `iframe`, `#fr-tabla` y `#fr-curva`. Cada iframe se carga de forma diferida al abrirlo.
3. **Contenido principal** (`<main>`):
   - aviso si el archivo se abrió con protocolo `file:`;
   - panel para ingresar la clave de Gemini;
   - confirmación de clave conectada;
   - historial de análisis;
   - zona de carga de video, por selector de archivo o arrastre;
   - ficha del archivo y reproductor;
   - inspector de fotogramas, con avance cuadro a cuadro y capturas;
   - formulario del atleta: nombre, categoría, disciplina, nivel, enfoque y contexto;
   - botón `Analizar Video`;
   - cargador de progreso y mensajes de error;
   - resultados con puntuaciones, pestañas y acciones para compartir/copiar.
4. **Pie** (`<footer>`): fuentes técnicas declaradas por la interfaz.

### Cómo se navega

No hay navegación entre páginas o pantallas principales. La app funciona como una vista única y muestra u oculta bloques del DOM:

- `toggleHist()` alterna la clase `show` de `#hist`.
- `openViewer(which)` muestra `#viewerOv`, oculta un iframe y muestra el elegido. Añade un estado al historial del navegador; `closeViewer()` lo cierra y `popstate` permite cerrarlo con el botón atrás del teléfono.
- `tab(id, btn)` alterna las pestañas de resultados `#tr`, `#te`, `#tx` y `#tf` mediante la clase `on`.
- `setFile()` oculta la zona de carga, muestra la ficha del archivo y el video; `resetFile()` revierte ese estado.
- `analyze()` muestra el cargador, realiza el análisis y luego muestra `#res`.
- `openHist(id)` recupera un análisis guardado y vuelve a renderizar los resultados.

La cabecera no contiene un conmutador entre `Análisis` y `Comparador`; esa segunda pantalla todavía no existe en `index.html`.

## 2. Identificadores (`id`) presentes en ambos archivos

### Intersección exacta

**Ninguno.** La intersección de los 48 `id` de `index.html` con los 122 `id` de `comparador.html` es el conjunto vacío (`∅`). Por tanto, no existe hoy una colisión de identificadores al insertar el HTML del comparador.

### IDs de `index.html`

`abtn`, `aname`, `ctx`, `ec`, `em`, `fi`, `fm`, `fn`, `fp`, `fpw`, `fr-curva`, `fr-tabla`, `fshots`, `fzc`, `fzgo`, `fzone`, `fzt`, `hcount`, `hist`, `hist-list`, `ki`, `ko`, `kw`, `kwarn`, `ldh`, `ldr`, `ln`, `model-name`, `pf`, `pp`, `res`, `res-date`, `res-name`, `sc`, `sd`, `smode`, `sn`, `ss`, `te`, `tf`, `toast`, `tr`, `tx`, `uz`, `vid-inp`, `viewerOv`, `viewerTitle`, `vp`.

### IDs de `comparador.html`

`adelante`, `agujaA`, `agujaB`, `agujaR`, `ajustes`, `atras`, `autoEnc`, `autoSync`, `aviso`, `barraA`, `barraB`, `borrarTodo`, `bucle`, `bucleA`, `bucleB`, `calibrar`, `cambiaAtleta`, `cargaA`, `cargaA2`, `cargaB`, `cargaB2`, `carrilA`, `carrilB`, `cerrarPanel`, `clipA`, `clipB`, `colorAnot`, `copiarEnc`, `corte`, `ctrCorte`, `ctrFusion`, `ctrOpacidad`, `ctrReparto`, `deshacer`, `encaje`, `escena`, `espejo`, `faltante`, `fileA`, `fileB`, `finA`, `finB`, `fps`, `fusion`, `giro`, `giroV`, `guias`, `herramientas`, `igualarPartida`, `jog2`, `jogPuno2`, `jogTxt2`, `lienzo`, `mA`, `mAdelante`, `mAguja`, `mAmas`, `mAmenos`, `mandos`, `marcaIn`, `marcaOut`, `mAtleta`, `mAtras`, `mB`, `mBarra`, `mBmas`, `mBmenos`, `mDiv`, `mFormas`, `mGuias`, `mJog`, `mJogPuno`, `mJogTxt`, `mModo`, `mPlay`, `mSalir`, `mSolo`, `mSup`, `mTc`, `mUno`, `mVel`, `nomA`, `nomB`, `notaEnc`, `notaSync`, `opac`, `opacV`, `panel`, `pantalla`, `pistaA`, `pistaB`, `pista-vacio`, `pizarra`, `play`, `posX`, `posXV`, `posY`, `posYV`, `refA`, `refB`, `regla`, `relojA`, `relojB`, `reparto`, `repartoV`, `reset`, `resetBarra`, `resetSync`, `selA`, `selB`, `tc`, `vA`, `vacio`, `vB`, `vel`, `verA`, `verB`, `verEncuadre`, `verFormas`, `verGuias`, `zoom`, `zoomV`.

## 3. Selectores CSS del comparador con riesgo de pisar `index.html`

### Selectores globales

Estos selectores no están contenidos dentro de una clase del comparador y pueden afectar a cualquier HTML si ambos documentos se integran:

- `:root { ... }`: declara variables globales con nombres propios del comparador (`--sombra`, `--panel`, `--texto`, `--azul`, etc.). También puede compartir o sobrescribir variables si se reutilizan nombres.
- `* { box-sizing: border-box; -webkit-tap-highlight-color: transparent }`: aplica a todos los elementos.
- `html, body { height: 100% }`: cambia el tamaño base de ambas raíces.
- `body { ... }`: cambia margen, fondo, color, tipografía, tamaño, `display:flex`, dirección, `overflow:hidden` y `padding` de toda la app.
- `body.completa .mandos, body.inmersivo .mandos { ... }`: regla dependiente de clases globales del `body`.
- `:focus-visible { ... }`: cambia el foco visible de todos los elementos.
- `body.inmersivo .barra, body.inmersivo .transporte, body.inmersivo .ajustes { ... }`: oculta elementos por clases del `body`.
- `body.inmersivo .escena { ... }`: fija `#escena` a toda la ventana.

El comparador también define `@media (prefers-reduced-motion:reduce) { * { transition:none!important } }`, cuyo selector `*` afecta a la aplicación integrada.

### Nombres de clase repetidos

La intersección exacta de clases declaradas en los dos HTML es:

- `.et`
- `.nota`
- `.on`

Reglas del comparador que usan clases repetidas y pueden cambiar elementos de `index.html`:

- `.mandos button.on`
- `.herramientas button.on`
- `.guias.on`
- `.btn.on`
- `.grupo .et`
- `.candado.on`
- `.nota`, `.nota b`

Además, aunque no son clases repetidas, las reglas genéricas del comparador para `.btn`, `.modos`, `input[type=range]`, `input[type=file]` y `button` dentro de varios contenedores deben quedar contenidas al integrar. El riesgo más directo para la pantalla de análisis viene de `.on`, `.nota`, `.et`, `:focus-visible`, `*`, `html`, `body` y `:root`.

## 4. Funciones y variables globales repetidas

La comparación de declaraciones de nivel superior de los dos scripts no encuentra nombres repetidos.

**Funciones globales compartidas: ninguna.**

**Variables/constantes globales compartidas: ninguna.**

Esto significa que no hay una colisión nominal exacta entre, por ejemplo, `analyze`, `render`, `clips`, `estado`, `dibujar` o `avisar`: los nombres del comparador son distintos de los nombres de `index.html`. Sin embargo, el comparador sí declara gran cantidad de nombres en el ámbito global y usa atributos `onclick`/handlers del documento. La integración debe encapsularlo igualmente, tal como advierte `MANUAL-INTEGRACION.md`, para evitar colisiones futuras y limitar sus atajos y eventos.

## 5. Service worker y manifest

### Manifest

`src/index.html` declara:

```html
<link rel="manifest" href="./manifest.json">
```

El archivo `manifest.json` **no está presente** en el workspace auditado.

### Service worker

El segundo bloque de script de `src/index.html` declara y registra:

```js
navigator.serviceWorker.register('./sw.js')
```

La registración ocurre después del evento `load`, solo si existe `serviceWorker` en `navigator`. El archivo `sw.js` **no está presente** en el workspace auditado.

`src/comparador.html` no declara manifest ni registra service worker.

Por tanto, la app principal tiene referencias PWA preparadas, pero en este estado no hay archivos locales que esas referencias puedan cargar. En la sesión 6 habrá que revisar si esos archivos se generan fuera de este workspace o eliminar/actualizar las declaraciones según el estado real del repositorio.

## 6. Clave de Gemini y llamadas a la API

### Cómo guarda la clave

La clave se mantiene en la variable global `key` durante la sesión:

```js
let key = '', file = null, lastText = '', lastMeta = null;
```

Al iniciar, la app intenta recuperar la clave desde `localStorage` con la clave de almacenamiento `g_key`:

```js
key = localStorage.getItem('g_key') || '';
```

Al pulsar `Guardar key`, `saveKey()` toma el valor de `#ki`, lo recorta, lo asigna a `key` y lo guarda en `localStorage` bajo `g_key`. Al detectar una clave inválida, `handleKeyFailure()` la elimina tanto de memoria como de `localStorage`.

El historial de análisis se guarda separadamente en `localStorage` bajo `ac_hist`; no es la clave de Gemini.

### Cómo llama a Gemini

La app hace tres tipos de llamadas `fetch`, siempre incorporando la clave como parámetro de consulta `?key=`:

1. **Detección de modelo** (`pickModel()`):
   - `GET https://generativelanguage.googleapis.com/v1beta/models?key=<clave>&pageSize=200`
   - filtra modelos que soporten `generateContent`;
   - elige uno según una lista de preferencias y lo guarda en `cachedModel`.
2. **Subida del video** (`uploadFile(f)`):
   - `POST https://generativelanguage.googleapis.com/upload/v1beta/files?uploadType=multipart&key=<clave>`;
   - envía un `FormData` con metadatos JSON y el archivo.
   - Luego consulta el estado del archivo con `GET https://generativelanguage.googleapis.com/v1beta/<nombre-del-archivo>?key=<clave>` hasta que quede `ACTIVE`.
3. **Generación del análisis** (`generate(uri, mime)`):
   - `POST https://generativelanguage.googleapis.com/v1beta/models/<modelo>:generateContent?key=<clave>`;
   - envía JSON con `system_instruction`, el `file_data` del video y el prompt de análisis;
   - primero solicita metadatos de video a `fps: 5` y, si Gemini responde `400`, reintenta sin `video_metadata`;
   - usa `Content-Type: application/json`, temperatura `0.3` y un límite de tokens que depende del modelo.

La función `analyze()` coordina el flujo: comprueba que existan archivo y clave, sube el video, obtiene/genera el análisis, guarda el resultado en el historial y renderiza la respuesta. Los códigos `400`, `401`, `403` y `429`, además de mensajes que indiquen problemas de clave, provocan que se borre la clave local y se solicite otra.

La clave viaja en la URL de cada llamada a Google y queda persistida en el almacenamiento local del navegador. No se guarda en el repositorio, pero tampoco se cifra en `localStorage`.
