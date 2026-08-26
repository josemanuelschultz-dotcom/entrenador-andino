# Estructura del proyecto

Esto viene **después** de `PLAN-REFACTOR.md`. Aquello sanea el comparador por dentro;
esto ordena el proyecto entero para que la app principal y el comparador dejen de ser
dos islas.

---

## Antes de decidir nada: la auditoría

`index.html` no está revisado todavía. El primer paso es mirarlo con los mismos ojos con
que se miró el comparador.

Pídeselo así:

```
/plan Lee CLAUDE.md y src/index.html. Descríbeme cómo está organizado y qué comparte
con src/comparador.html: estilos, funciones, estructura, convenciones. No cambies nada.
```

Lo que interesa saber:
- Qué colores, tipografías y medidas usa cada uno, y cuánto se parecen
- Si hay lógica repetida en los dos (carga de archivos, avisos, formato de tiempo)
- Cómo guarda la clave de Gemini y cómo llama a la API
- Si hay service worker y manifest, y qué archivos declara

Sin eso, cualquier plan de integración es adivinar.

---

## Tarea 6 — Base común de estilo

**Problema previsible.** Dos archivos con su propia paleta y sus propias medidas. Cambiar
un color de marca obliga a tocar dos sitios, y con el tiempo dejan de parecer la misma app.

**Qué hacer.** Un `src/estilos-base.css` con solo las variables: colores, tipografías,
radios, alturas de botón. Los dos HTML lo enlazan y siguen teniendo sus estilos propios
para lo suyo.

**Ojo con esto.** Un archivo CSS aparte se carga bien al abrir el HTML directamente. Un
archivo JS como módulo (`type="module"`) **no**: el navegador lo bloquea si abres el
archivo con doble clic. Si más adelante quieres separar el JS en módulos, tendrás que
servirlo con un servidor local. Mientras quieras seguir abriendo el archivo a mano,
usa `<script src="...">` normal, sin `type="module"`.

**Commit.** `Extraer las variables de estilo a un archivo común`

---

## Tarea 7 — Lo que ambos necesitan, escrito una vez

Candidatos, a confirmar tras la auditoría:
- El aviso flotante (`avisar`)
- La carga de archivos de video y su validación
- El formato de tiempo
- La lectura y el guardado de la clave de Gemini

A `src/comun.js`, enlazado por los dos con `<script src="comun.js">` antes del script
propio de cada página.

**Commit.** `Mover a comun.js lo que usan las dos pantallas`

---

## Tarea 8 — La sesión de análisis como dato

**El cambio con más futuro de la lista.** Hoy nada se guarda: cierras y pierdes marcas,
encuadre y anotaciones.

**Qué hacer.** Definir en `comun.js` qué es una sesión de análisis: atleta, fecha, videos
usados, marcas de sincronía, encuadre de cada video, anotaciones, notas del entrenador.
Un objeto, con funciones para guardarlo y recuperarlo.

Los videos no se guardan, son demasiado grandes: se guarda el nombre del archivo y el
usuario lo vuelve a abrir. Todo lo demás se restaura.

Esto abre lo que de verdad querías: comparar al mismo chico en marzo y en agosto.

**Commit.** `Guardar y recuperar sesiones de análisis`

---

## Tarea 9 — Integrar de verdad

Con lo anterior hecho, el comparador deja de ser una página suelta y pasa a ser una
sección de la app, compartiendo cabecera, estilo y datos.

Lo que hace que valga la pena: **enviar a Gemini el par de fotogramas sincronizados**, no
uno solo. El análisis pasa de describir una postura a comparar dos, que es lo que un
entrenador quiere leer.

**Commit.** `Integrar el comparador como sección de la app`

---

## Tarea 10 — Que las cuentas no mientan

Las funciones de geometría, tiempo y ángulos son matemáticas puras y se pueden probar sin
navegador. Ya se validaron así durante el desarrollo, pero esas pruebas no quedaron en el
repositorio.

**Qué hacer.** Una carpeta `test/` con pruebas que se ejecuten con `node --test`, cubriendo:
- La ida y vuelta de la geometría: un punto convertido a pantalla y de vuelta debe dar el mismo punto
- El calce de trayectorias con desfases conocidos
- Los ángulos en casos de 45°, 90°, 135° y 180°
- El registro de encuadre entre dos vistas de una misma escena sintética

Cuesta una tarde y a partir de ahí sabes en segundos si un cambio rompió las cuentas.

**Commit.** `Añadir pruebas de las funciones de cálculo`

---

## Un comando propio

Cuando repitas siempre la misma comprobación antes de cada commit, pídele que te la
convierta en un comando del proyecto:

```
Créame un comando propio llamado revisar que ejecute las pruebas de test/, compruebe que
ningún $("id") apunta a un elemento inexistente en el HTML, y me resuma el git diff.
```

A partir de ahí, `/revisar` antes de cada commit.

---

## El orden completo

1–5: saneamiento del comparador (`PLAN-REFACTOR.md`)
Auditoría de `index.html`
6–7: base común
10: pruebas — conviene adelantarla si vas a tocar geometría
8: sesiones de análisis
9: integración
