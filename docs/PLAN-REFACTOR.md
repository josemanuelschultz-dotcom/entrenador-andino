# Plan de saneamiento del comparador

Cinco tareas, en este orden. Cada una es una rama, un commit y una comprobación.
No empieces la siguiente hasta que la anterior esté probada en el navegador con un video real.

El orden importa: van de menos a más riesgo, y las tres primeras dejan el terreno
preparado para las dos últimas.

---

## Tarea 1 — Que un elemento ausente no mate la app

**Problema.** Los escuchadores se registran al cargar con `$("id").addEventListener(...)`.
Si ese elemento no existe en el HTML, el script lanza una excepción en esa línea y todo
lo que viene después no se ejecuta: la app queda en blanco sin explicación.

**Qué hacer.**
- Que `$` avise por consola cuando no encuentra el elemento, en vez de devolver `null` a secas.
- Añadir un ayudante `al(id, evento, fn)` que registre el escuchador solo si el elemento existe.
- Sustituir los `$("x").addEventListener(...)` por ese ayudante.
- Al final del script, un repaso que recorra los identificadores esperados y liste por consola
  los que falten.

**Cómo comprobar.** Borra a mano un botón del HTML, recarga, y verifica que la app sigue
funcionando y que la consola nombra el que falta.

**Commit.** `Evitar que un elemento ausente rompa el arranque`

---

## Tarea 2 — Una sola fuente para la geometría

**Problema.** La transformación de cada video está escrita tres veces: `transformar()` la
aplica en CSS, `aFotograma()` la invierte y `aPantalla()` la reconstruye. Si cambia el orden
del `transform`, la imagen se ve bien pero la calibración, el encuadre automático y las
anotaciones devuelven valores erróneos sin avisar.

**Qué hacer.**
- Un bloque `geometria` con tres funciones y nada más que las use: `cssDe(clip)`,
  `pantallaDesdeFotograma(clip, punto)` y `fotogramaDesdePantalla(clip, punto)`.
- Que las tres deriven del mismo orden de operaciones, declarado una sola vez.
- Reemplazar los cálculos repartidos por llamadas a esas funciones.

**Cómo comprobar.** Antes de tocar nada, anota los valores que da la calibración por dos
puntos con un par de videos. Después del cambio deben salir los mismos. Además: aplica un
zoom y un giro cualesquiera, dibuja un ángulo sobre la imagen, cambia de modo, y comprueba
que la marca sigue sobre el mismo punto del fotograma.

**Commit.** `Unificar la geometría de encuadre en un solo bloque`

---

## Tarea 3 — Repartir `dibujar()`

**Problema.** `dibujar()` se ejecuta 60 veces por segundo y repinta el cronómetro, las agujas,
las barras de los carriles, las marcas de la regla, los relojes, las anotaciones y los mandos
flotantes, aunque nada haya cambiado y el video esté en pausa.

**Qué hacer.**
- Separar en funciones por responsabilidad: `pintarTransporte`, `pintarCarriles`,
  `pintarAnotaciones`, `refrescarMandos`.
- Un sistema simple de marcas: `hayQueRepintar.carriles = true` cuando cambia un desfase,
  y el bucle solo repinta lo marcado.
- Lo que depende del tiempo se repinta solo mientras se reproduce o se arrastra.

**Cómo comprobar.** Con el video en pausa, la app no debe consumir CPU apreciable. Todo lo
demás debe seguir respondiendo igual: mover un carril actualiza su reloj al instante.

**Commit.** `Repintar solo lo que cambió`

---

## Tarea 4 — Una sola fórmula para el tiempo

**Problema.** `tiempoDelClip = off + t * esc` aparece en ocho sitios. Existen `aClip`,
`iniClip` y `finClip`, pero no se usan en todas partes.

**Qué hacer.** Que ningún cálculo de tiempo por clip quede fuera de esos tres ayudantes.
Buscar `\.off` y `\.esc` en el archivo y revisar caso por caso.

**Cómo comprobar.** Sincroniza dos videos con las marcas de referencia, marca también un
fin de tramo para que se aplique la igualación de ritmo, y verifica que los dos siguen
calzados al reproducir de principio a fin.

**Commit.** `Centralizar el cálculo de tiempo por clip`

---

## Tarea 5 — Un solo juego de mandos

**Problema.** El transporte normal y los mandos flotantes duplican play, velocidad y
lanzadera, sincronizados a mano por `refrescarMandos()`. Cada control nuevo hay que
acordarse de añadirlo dos veces.

**Qué hacer.** Definir los mandos como datos —identificador, rótulo, acción, cuándo se
muestra— y generar los dos juegos desde ahí.

**Cómo comprobar.** Los dos juegos deben reflejar el mismo estado en todo momento: pausa
desde uno y el otro cambia su icono; cambia la velocidad en uno y el otro lo muestra.

**Commit.** `Generar los dos juegos de mandos desde una sola definición`

---

## Después

Con esto hecho, lo que hoy da miedo tocar deja de darlo, y tiene sentido plantearse:
guardar sesiones de análisis, exportar el fotograma anotado, y la integración con la app
principal.
