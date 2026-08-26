# Entrenador Andino

App de análisis técnico de esquí alpino para entrenadores. Categorías U8 a U16.
Base técnica: USST, NSF, CSIA, FISI y Alpine Race Method.

## Qué hay aquí

- `src/comparador.html` — herramienta de análisis y comparación de video.
  Funciona sola, abriéndola en el navegador. Sin build, sin dependencias, sin servidor.
- `src/index.html` — la app principal (análisis con IA vía Gemini). **Pendiente de subir al repositorio.**
  El comparador se va a fundir aquí dentro: ver `docs/MANUAL-INTEGRACION.md`.

## Reglas del proyecto

### Idioma
- Toda la interfaz va en español de Chile, con **tuteo** (tú): "arrastra", "tienes", "prueba".
  Nunca voseo: no "arrastrá", no "tenés", no "probá".
- Los comentarios del código también en español.
- Los nombres de variables y funciones en español, como está el código actual
  (`clips`, `estado`, `dibujar`, `aplicarModo`, `moverSolo`). Mantén esa convención.

### Técnicas
- **Un solo archivo por herramienta.** HTML, CSS y JS en el mismo `.html`. Sin bundler,
  sin npm, sin frameworks. Se distribuye como PWA en un ZIP.
- **Sin dependencias externas** salvo la fuente de Google Fonts, que degrada a la del sistema.
- **Nada de localStorage ni de red** en el comparador: todo el video es local, con `URL.createObjectURL`.
- Objetivo: tablet. Botones de 38 px mínimo, gestos táctiles, y que todo mando esencial
  siga alcanzable en pantalla completa.
- La clave de la API de Gemini la aporta el usuario final; nunca se guarda en el repo.

### Al tocar el comparador
Antes de dar por buena una edición, comprueba estas tres cosas:
1. Que el script arranca: no debe quedar ningún `$("id")` apuntando a un elemento
   que ya no existe en el HTML. Es el fallo más frecuente al quitar cosas.
2. Que no hay identificadores duplicados.
3. Que los cambios de geometría (encuadre, sincronía, ángulos) siguen dando los valores
   esperados. Esas funciones son matemáticas puras y se prueban aparte, sin navegador.

## Cómo se prueba

No hay framework de test. Para lo que es lógica pura, extrae la función y ejecútala
con node contra casos con resultado conocido. Ejemplos que ya se validaron así:

- `calzarTrayectorias` — recupera desfases de 0,35 a 2 s con menos de 0,02 s de error.
- `registrar` — recupera escala y giro entre dos encuadres del mismo terreno.
- `anguloEntre` — exacto en los casos de 45°, 90°, 135° y 180°.

Para lo visual, abre el archivo en el navegador y carga un video de prueba.

## Estado actual del comparador

Funciona:
- Tres modos: individual, pantalla dividida, superposición con mezcla y modo diferencia.
- Sincronía: marcas de referencia, igualación de ritmo por tramo, calce automático
  por trayectoria del esquiador, alineación por ritmo de movimiento, reinicio.
- Encuadre: automático por registro del terreno, calibración por dos puntos,
  copia entre videos, zoom anclado al cursor, giro, espejo.
- Formas y medición de ángulos ancladas al fotograma.
- Bucle entre marcas, guías, lanzaderas con objetivo A/B/ambos, pantalla completa con mandos.

Retirado a petición del usuario:
- Estela de movimiento (StroMotion). El recorte del esquiador contra el fondo daba mal
  resultado con material real. Si se retoma, el camino acordado es que el usuario marque
  la posición con un toque por fotograma, en vez de detectarla automáticamente.

## Lo que viene

- `docs/EMPEZAR.md` — guía de Claude Code para el usuario, que empieza desde cero.
- `docs/PLAN-REFACTOR.md` — saneamiento de la estructura. **Hazlo antes de añadir funciones nuevas.**
- `docs/MANUAL-INTEGRACION.md` — **en curso.** Fundir el comparador dentro de index.html en siete sesiones.
  Incluye una sección "Lo que va a fallar" de lectura obligatoria antes de mover el JavaScript.
- `docs/PLAN-ESTRUCTURA.md` — orden del proyecto entero e integración con la app principal. Va después del refactor.
- `docs/ROADMAP.md` — funcionalidades pendientes.

## Cómo trabajamos

Una tarea por rama. Commits pequeños y en español, en imperativo:
"Unificar la geometría", no "unificada la geometría" ni "cambios varios".
Antes de editar, muestra el plan y espera confirmación. Después de editar, di qué hay que
probar en el navegador: tú no puedes verlo, así que dilo en concreto.
