# Empezar con Claude Code

Guía para quien nunca ha usado una terminal. Sigue los pasos en orden; no te saltes
ninguno aunque parezca obvio.

---

## Antes de nada

Claude Code necesita una cuenta de pago: Pro, Max, Team, Enterprise o Console.
El plan gratuito de Claude.ai no lo incluye.

---

## Paso 1 — Abrir la terminal

- **Mac**: pulsa `Cmd + Espacio`, escribe `Terminal`, Enter.
- **Windows**: menú Inicio, escribe `PowerShell`, Enter.

Verás una ventana con texto y un cursor parpadeando. Ahí se escriben órdenes y se
confirman con Enter. Nada de lo que escribas hace daño si sigues esta guía.

---

## Paso 2 — Instalar

Copia esta línea, pégala y pulsa Enter.

**Mac o Linux:**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows (PowerShell):**

```powershell
irm https://claude.ai/install.ps1 | iex
```

Tarda un poco. Cuando termine, **cierra la terminal y abre una nueva**. Este paso se
salta mucha gente y es la causa más común del error "command not found": la instalación
solo queda disponible en ventanas abiertas después.

Comprueba que quedó bien:

```bash
claude --version
```

Debe imprimir un número de versión. Si da error, ejecuta `claude doctor`, que revisa la
instalación y te dice qué falta.

---

## Paso 3 — Ir a la carpeta del proyecto

La terminal siempre está "parada" en una carpeta. Hay que llevarla a la del proyecto con
la orden `cd` (change directory):

```bash
cd ruta/de/tu/carpeta
```

Truco para no escribir la ruta a mano: escribe `cd ` con un espacio al final y **arrastra
la carpeta desde el explorador de archivos hasta la ventana de la terminal**. La ruta se
escribe sola. Pulsa Enter.

Para confirmar que estás donde toca, `ls` en Mac o Linux, `dir` en Windows: debe listar
`CLAUDE.md`, `README.md`, `src` y `docs`.

---

## Paso 4 — Arrancar

```bash
claude
```

La primera vez se abre el navegador para que inicies sesión con tu cuenta. Acepta y
vuelve a la terminal.

Ya estás dentro. Ahora escribes en español, como en una conversación normal.

Para salir: `/exit`.

---

## Paso 5 — La primera orden

Escribe esto tal cual y pulsa Enter:

```
Lee CLAUDE.md y dime en tres frases de qué va este proyecto.
```

No cambia nada, solo lee. Sirve para ver cómo responde y confirmar que encontró los
archivos.

---

## Paso 6 — Entender los permisos

Cuando quiera modificar un archivo o ejecutar algo, **se detiene y te pregunta**. Verás
una lista de opciones para permitir esa vez, permitir siempre ese tipo de acción, o
rechazar.

Al principio, permite de una en una y lee qué te pide. No uses "permitir siempre" hasta
que entiendas qué está haciendo.

---

## Paso 7 — Pedir un cambio de verdad

**Antes de tocar código, crea una rama.** Es una copia de trabajo: si sale mal, la tiras
y no pasa nada. Sal con `/exit`, y en la terminal:

```bash
git checkout -b tarea-1-arranque
```

Vuelve a entrar con `claude` y pídele que planifique primero:

```
/plan Lee CLAUDE.md y docs/PLAN-REFACTOR.md. Vamos con la tarea 1.
```

Con `/plan` entra en modo plan: analiza y te propone qué hará, **sin modificar nada**
hasta que lo apruebes. Léelo. Si algo no te cuadra, díselo en palabras antes de aprobar.

---

## Paso 8 — Revisar lo que hizo

Dentro de la sesión:

```
/diff
```

Te muestra lo que cambió. No hace falta entender cada línea; sí comprobar que no tocó
cosas que no venían al caso.

Si algo salió mal:

```
/rewind
```

Deshace y vuelve a un punto anterior.

---

## Paso 9 — Probarlo tú

**Este paso no lo puede hacer él.** No ve tu pantalla ni tiene tus videos.

Abre `src/comparador.html` en el navegador, carga un video y comprueba lo que diga la
tarea del plan. Si no funciona, vuelve a la sesión y descríbele qué pasó, con detalle:
qué hiciste, qué esperabas, qué ocurrió.

---

## Paso 10 — Guardar

Si funciona, sal con `/exit` y guarda el trabajo:

```bash
git add -A
git commit -m "Evitar que un elemento ausente rompa el arranque"
git checkout main
git merge tarea-1-arranque
git push
```

Si no funciona y prefieres empezar de cero, `git checkout main` y esa rama queda ahí sin
haber ensuciado nada.

---

## Las órdenes que vale la pena memorizar

| Orden | Para qué |
|---|---|
| `/help` | Lista todas las órdenes disponibles |
| `/plan` | Que proponga antes de tocar. Úsalo siempre en cambios grandes |
| `/diff` | Ver qué cambió |
| `/rewind` | Deshacer y volver atrás |
| `/clear` | Empezar conversación nueva sin salir. Una por tarea |
| `/context` | Ver cuánta memoria de la conversación queda |
| `/compact` | Resumir la conversación cuando se alarga |
| `/exit` | Salir |

---

## Cuatro costumbres que evitan disgustos

**Una rama por tarea.** Es tu red de seguridad y cuesta un segundo crearla.

**Conversación nueva por tarea**, con `/clear`. Arrastrar el contexto de lo anterior lo
dispersa y empieza a mezclar cambios.

**Plan antes de editar.** En un archivo de 2.000 líneas con todo acoplado, es más barato
leer un plan que revisar un desastre.

**Descríbele los fallos como a una persona que no ve la pantalla.** "No funciona" no
sirve de nada. "Pulso Encuadre automático y no pasa nada, la consola dice X" sí.

---

## Si te atascas

- `claude doctor` — revisa la instalación y propone arreglos
- `/help` — dentro de la sesión
- Documentación oficial: https://code.claude.com/docs
