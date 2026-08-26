# Manual de integración

Fundir el comparador dentro de la app, en un solo archivo, sin romper lo que ya funciona.

Son **siete sesiones**. Cada una: una rama, una orden que copias y pegas, una comprobación
tuya en el navegador, y un commit. No empieces la siguiente hasta que la anterior esté
probada.

Si nunca has usado Claude Code, lee antes `EMPEZAR.md`.

> **Aviso para Claude Code:** las trampas conocidas están al final de este documento, en
> "Lo que va a fallar". Léelas antes de la sesión 5. No son hipótesis: son consecuencias
> directas de cómo está escrito `comparador.html`.

---

## Cómo es cada sesión

```bash
git checkout -b integracion-1-auditoria    # rama nueva
claude                                     # entrar
```

Pegas la orden de la sesión. Cuando termine, sales con `/exit`, pruebas en el navegador
lo que diga la comprobación, y si va bien:

```bash
git add -A
git commit -m "el mensaje de la sesión"
git checkout main
git merge integracion-1-auditoria
```

Si va mal: `git checkout main` y esa rama queda ahí, sin haber tocado nada.

Antes de la primera sesión, marca el punto de retorno:

```bash
git tag antes-de-integrar
```

---

## Sesión 1 — Auditoría

Rama: `integracion-1-auditoria` · No cambia nada, solo lee.

```
/plan Lee CLAUDE.md, docs/MANUAL-INTEGRACION.md, src/index.html y src/comparador.html.

No cambies nada todavía. Quiero un informe con:

1. Cómo está organizado index.html: secciones, dónde está la cabecera, cómo se navega
   entre pantallas si es que hay más de una.
2. Identificadores (id) que existan en los dos archivos. Lista exacta.
3. Selectores CSS globales del comparador que puedan pisar a los de index.html:
   los que tocan body, html, *, y los nombres de clase repetidos en ambos.
4. Nombres de funciones y variables globales repetidos en los dos scripts.
5. Si hay service worker y manifest, y qué archivos declaran.
6. Cómo guarda index.html la clave de Gemini y cómo llama a la API.

Escribe el informe en docs/AUDITORIA.md.
```

**Comprobación:** abre `docs/AUDITORIA.md` y léelo. Si dice que hay muchos identificadores
repetidos, no te asustes: la sesión 4 los resuelve.

**Commit:** `Auditar los dos archivos antes de integrar`

---

## Sesión 2 — El armazón

Rama: `integracion-2-armazon` · Solo la navegación, sin traer nada del comparador.

```
/plan Lee docs/AUDITORIA.md.

En src/index.html quiero navegación entre dos pantallas: la actual, que se llamará
"Análisis", y una nueva llamada "Comparador" que por ahora estará vacía, con un texto
que diga "Aquí irá el comparador".

Requisitos:
- La cabecera y el conmutador de pantallas se ven siempre.
- Cambiar de pantalla no recarga la página, solo muestra una y esconde la otra.
- Todo lo que hoy funciona en index.html debe seguir funcionando igual.
- Sin librerías. Español con tuteo en los textos.
```

**Comprobación:** abre `src/index.html`. Todo lo de antes funciona, y al pulsar
"Comparador" aparece la pantalla vacía. Vuelve a "Análisis" y comprueba que sigue bien.

**Commit:** `Añadir navegación entre Análisis y Comparador`

---

## Sesión 3 — Los estilos

Rama: `integracion-3-estilos`

```
/plan Lee docs/AUDITORIA.md.

Trae los estilos de src/comparador.html a src/index.html, dentro de la pantalla
"Comparador", sin que afecten a la pantalla "Análisis".

Cómo hacerlo:
- Envuelve la pantalla del comparador en un contenedor con clase "comparador".
- Todo selector que venía del comparador pasa a colgar de esa clase.
- Los selectores que tocan body, html o * NO se copian tal cual: lo que necesiten se
  aplica al contenedor. El comparador ocupaba toda la ventana; ahora ocupa su pantalla.
- Las variables de color y tipografía van a :root, compartidas, pero si alguna choca
  con una que ya use index.html, renómbrala con prefijo --cmp-.

Todavía no traigas el HTML ni el JavaScript.
```

**Comprobación:** la pantalla "Análisis" se ve exactamente igual que antes. Míralo con
atención: colores, tamaños de letra, márgenes. Si algo cambió, la clase no está
conteniendo bien los estilos.

**Commit:** `Traer los estilos del comparador, contenidos en su pantalla`

---

## Sesión 4 — El HTML

Rama: `integracion-4-html`

```
/plan Lee docs/AUDITORIA.md.

Trae el HTML del comparador — el que va desde la cabecera hasta el aviso flotante —
dentro del contenedor .comparador de la pantalla Comparador.

Reglas:
- La cabecera propia del comparador (marca y botones de carga) se funde con la cabecera
  de la app: los botones de cargar video y el selector de modo se conservan, el título
  duplicado se elimina.
- Cualquier identificador que ya exista en index.html se renombra en el comparador con
  prefijo cmp-, y se actualiza cada referencia en su JavaScript.
- No se pierde ningún control. Al final debe estar todo: los tres modos, los carriles,
  las lanzaderas, la paleta de formas, los mandos flotantes y el panel de ajustes.

Al terminar, dime qué identificadores renombraste.
```

**Comprobación:** la pantalla del comparador se ve completa, aunque todavía no haga nada.
Ningún botón responde: es lo esperado, el JavaScript llega en la sesión siguiente.

**Commit:** `Traer el HTML del comparador`

---

## Sesión 5 — El JavaScript

Rama: `integracion-5-logica` · **La sesión delicada.**

```
/plan Lee docs/AUDITORIA.md y la sección "Lo que va a fallar" de
docs/MANUAL-INTEGRACION.md. Esa sección es obligatoria: describe cuatro problemas
concretos que aparecerán en esta sesión.

Trae el JavaScript del comparador a index.html, resolviendo esos cuatro puntos:
arranque diferido, atajos de teclado limitados a su pantalla, bucle de dibujo detenido
cuando no se ve, y nombres globales sin colisión.

Al terminar, dime exactamente qué debo probar en el navegador.
```

**Comprobación:** carga un video en el comparador y prueba, en este orden — reproducir,
avanzar cuadro a cuadro, cambiar de modo, dibujar un ángulo, pantalla completa. Después
vuelve a "Análisis" y comprueba que sigue funcionando, sobre todo escribir en cualquier
campo de texto: si al escribir un espacio se te pone a reproducir un video, el punto de
los atajos no quedó resuelto.

**Commit:** `Traer la lógica del comparador`

---

## Sesión 6 — Limpieza

Rama: `integracion-6-limpieza`

```
/plan Ahora que el comparador vive dentro de index.html:

- Borra src/comparador.html.
- Si hay service worker, actualiza la lista de archivos que precarga.
- Si hay manifest, revisa que siga correcto.
- Busca código que quedara duplicado entre las dos partes (avisos, formato de tiempo,
  carga de archivos) y déjalo una sola vez.
- Actualiza CLAUDE.md y README.md para que reflejen que ahora es una sola app.

Dime qué eliminaste.
```

**Comprobación:** todo sigue funcionando, las dos pantallas. Si tienes service worker,
prueba también sin conexión.

**Commit:** `Fundir en una sola app y limpiar lo duplicado`

---

## Sesión 7 — Lo que hace que valga la pena

Rama: `integracion-7-analisis-doble`

```
/plan Con el comparador dentro de la app, quiero que el análisis con Gemini pueda
trabajar sobre los dos esquiadores a la vez.

- Un botón en el comparador que capture el fotograma actual de cada video, ya
  sincronizados y con el encuadre aplicado.
- Que esos dos fotogramas se manden juntos a Gemini, con una consigna que pida comparar
  a los dos atletas en ese instante, no describir a uno.
- El resultado se muestra en la pantalla de Análisis.
- Reutiliza la forma en que index.html ya llama a la API y guarda la clave. No dupliques
  esa lógica.

Muéstrame el plan antes de tocar nada.
```

**Comprobación:** sincroniza dos bajadas, pulsa el botón, y comprueba que el texto que
vuelve habla de los dos esquiadores y no de uno.

**Commit:** `Analizar con IA los dos esquiadores sincronizados`

---

## Lo que va a fallar

Cuatro problemas que vienen de cómo está escrito `comparador.html`. No son opinables.

### 1. Arranque con la pantalla oculta

El comparador mide elementos al arrancar: `dimPizarra()` toma el tamaño del escenario, y
`transformar()` y `disponer()` dependen de las dimensiones de los marcos. Si la pantalla
está oculta con `display:none`, todas esas medidas valen cero y el lienzo de anotaciones
queda de 0×0: las formas no se dibujan y los ángulos aparecen fuera de sitio.

**Solución:** que el comparador no arranque al cargar la página, sino la primera vez que
se abre su pantalla. Y que al volver a mostrarla se recalculen las medidas, igual que ya
se hace al cambiar el tamaño de la ventana.

### 2. Los atajos de teclado se comen la escritura

`document.addEventListener("keydown")` captura espacio y flechas en toda la página. Con
las dos pantallas en el mismo documento, escribir en un campo de la pantalla de Análisis
dispara el play del comparador.

**Solución:** que el manejador no haga nada si la pantalla visible no es la del
comparador. El código ya ignora los eventos que vienen de un `input` o un `select`;
hay que ampliarlo también a `textarea` y a elementos editables.

### 3. El bucle de dibujo no para nunca

Hay un `requestAnimationFrame` que llama a `dibujar()` sesenta veces por segundo, esté o
no visible el comparador. En un tablet eso es batería quemada mientras el entrenador
escribe sus notas.

**Solución:** que el bucle se detenga cuando la pantalla no está visible y se reanude al
volver.

### 4. Nombres globales

Los dos scripts declaran variables y funciones en el ámbito global. Nombres como `$`,
`estado`, `clips`, `dibujar` o `avisar` pueden existir en los dos. En el mismo documento,
el segundo pisa al primero y el fallo es desconcertante: algo deja de funcionar sin
mensaje de error.

**Solución:** envolver el script del comparador en una función que se ejecuta a sí misma,
de modo que sus nombres no salgan fuera, y exponer solo lo que la app necesite llamar:
abrirlo, cerrarlo, y capturar los fotogramas.

---

## Si algo se rompe y no sabes por qué

Dentro de la sesión, `/rewind` deshace. Si ya cerraste, en la terminal:

```bash
git checkout main                    # abandona la rama en curso
git checkout antes-de-integrar -- src/    # vuelve al estado previo a todo
```

Y cuéntame qué pasó, con detalle: en qué sesión, qué probaste, qué esperabas y qué
ocurrió.
