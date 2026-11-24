---
name: document-code
description: Genera la documentación completa de un programa en un directorio (carpeta docs/ con User Guide, Precaution and code assumptions y Examples, en Markdown, en inglés y estilo NumPy, lista para verse en GitHub), interpretando el código (ecuaciones, unidades). Úsala cuando diga "documenta este código", "haz la documentación de TrajStat" o "actualiza los docs del programa".
---

# Document code

## Cuándo usarla
- "Documenta el programa de esta carpeta", "genera docs/ para X".
- **Modo actualización**: "actualiza la documentación", "actualiza los docs". Solo se actualiza cuando el usuario lo pide; no hay actualización periódica automática (ver "Modo actualización").
- El código es un programa o paquete (Python, scripts, etc.) en un directorio que puedo leer.
- NO usarla para: documentar código que no está accesible (primero hay que adjuntarlo o conectar la carpeta), escribir tests, ni refactorizar o corregir el código.

## Entradas esperadas
- Ruta del directorio del código (o archivos adjuntos). Si hay README, `requirements`/`pyproject`/`environment.yml`, ejemplos o tests, los uso.
- **Idioma: el texto de la documentación SIEMPRE en inglés**, sea cual sea el idioma del código, los comentarios o mi conversación. La única excepción es `docs/preguntas_claude.md`, que va en español. Esta skill y los informes que me das están en español.
- **No hago preguntas durante la ejecución.** Trabajo sin interrumpir: ante cualquier duda (de forma de la documentación o de interpretación del código) uso mi mejor lectura, la marco "Unconfirmed" en la documentación, la registro como ambigüedad y la añado a `docs/preguntas_claude.md` para que el usuario la conteste cuando quiera. Si falta algo sin lo cual no se puede empezar (no hay código accesible), lo digo y paro.

## Pasos
1. **Inventario**: listar el árbol del directorio; identificar punto de entrada (CLI, `main`, `__init__`), módulos, dependencias, formatos de entrada/salida, archivos de ejemplo/test y si existe un README en la raíz. Ignorar `.git`, `__pycache__`, datos pesados y binarios.
2. **Leer el código** de cada módulo público: firmas, docstrings, argumentos de línea de comandos, valores por defecto, excepciones, efectos secundarios (archivos que escribe, estado global).
3. **Interpretar el código** (no solo transcribirlo). Para cada función o bloque de cálculo:
   - Identificar qué magnitud física, estadística o algorítmica calcula y escribir la **ecuación** que implementa (en LaTeX `$...$` / `$$...$$`, que GitHub renderiza), con el significado de cada símbolo y su correspondencia con las variables del código.
   - Determinar las **unidades** de entradas, salidas y constantes intermedias (por ejemplo, nm o Å, ps o ns, kJ/mol o kcal/mol, radianes o grados), apoyándome en: comentarios del código, nombres de variables, constantes definidas, y el comportamiento documentado de los paquetes que importa (NumPy, SciPy, MDAnalysis, MDTraj, etc.). Cuando el paquete externo fija la unidad, lo cito (paquete y función).
   - Indicar el método o referencia detrás (nombre del algoritmo, estimador, fórmula conocida) solo si el código o sus comentarios lo respaldan; si es una inferencia mía, decirlo.
   - **Si tengo dudas** (unidad no deducible, ecuación que no reconozco, constante sin origen, comentario que contradice el código), no relleno con una suposición silenciosa ni me paro a preguntar: escribo lo que el código hace literalmente, marco el resto "Unconfirmed", lo registro como ambigüedad (A…) y lo añado a `docs/preguntas_claude.md`.
4. **Registrar las dudas** en `docs/preguntas_claude.md` (formato en la sección siguiente), una pregunta por duda, concreta, con mi mejor suposición dentro de la pregunta y la referencia a la entrada A/F correspondiente. No duplico preguntas que ya estén (pendientes o respondidas).
5. **Crear `docs/`** en la raíz del directorio documentado (si ya existe, actualizar sin borrar nada) con:
   - `docs/user-guide.md`
   - `docs/precautions-and-assumptions.md`
   - `docs/examples.md`
   - `docs/preguntas_claude.md` (en español)
6. **Escribir cada archivo** en inglés (salvo `preguntas_claude.md`) con la estructura de abajo (estilo NumPy).
7. **README raíz**: añadir una sección breve "Documentation" con enlaces relativos a los tres archivos de documentación en inglés (por ejemplo `[User Guide](docs/user-guide.md)`); `preguntas_claude.md` no se enlaza desde el README (solo desde `precautions-and-assumptions.md`). Si ya hay un README, solo se añade o actualiza esa sección, sin tocar el resto. **Si no hay README, lo creo** (en inglés), aunque haya un solo script: título con el nombre del directorio, una frase de descripción, una tabla "Scripts" (script con enlace, descripción de una o dos líneas, enlace a su documentación) y la sección "Documentation" con los tres enlaces. Lo anoto en el informe.
7a. **Si faltan datos o módulos** (script que necesita datos de simulación o un módulo propio que no está en el directorio): no los invento en la documentación. Para probar, genero datos sintéticos y, si hace falta, un *stand-in* mínimo del módulo ausente, **solo en la copia aislada y en los ejemplos**, rotulado como tal ("not the author's code; numbers have no physical meaning"). Lo que depende del módulo ausente se marca *Unconfirmed*, se anota como fallo (el script no corre desde el repositorio) y se pregunta al usuario. Los scripts que ejecutan todo al importarse se prueban completos (no se importan); las funciones se extraen con `ast` para comprobarlas aisladas; las afirmaciones numéricas se comprueban con una copia instrumentada del script.
7b. **Ejecutar en una copia aislada** (nunca dentro del directorio del usuario; sin escribir `__pycache__`): instalar el paquete en un entorno virtual limpio, ejecutar los tests y scripts de prueba del propio código y anotar resultado y causa (FAILED/PASSED/NOT RUN) para la sección 5.1; reproducir cada sospecha antes de listarla como fallo verificado; comparar las ecuaciones documentadas con una implementación independiente.
8. **Validar**: comprobar con un script que cada función/opción mencionada existe con ese nombre y esos valores por defecto (`inspect.signature`); que las ecuaciones y unidades coinciden con lo que hace el código; que los enlaces relativos y anclas funcionan y que las tablas tienen columnas consistentes; y ejecutar TODOS los bloques de código de `examples.md`, pegando en el documento la salida real y comprobando que se reproduce.
9. **Informar** con el formato de la sección Salida.

## Formato de `docs/preguntas_claude.md` (en español)
- Cabecera breve que explica cómo usarlo: el usuario escribe su respuesta tras `R:`; en blanco = sin respuesta; se incorporan cuando el usuario dice "actualiza la documentación".
- Secciones, en este orden: `## Pendientes` (con subsecciones por tema), `## Respondidas que falta aclaratoria` y `## Respondidas e incorporadas`.
- Cada entrada de "Respondidas e incorporadas" conserva `P:` y `R:` intactos y añade una línea `Incorporada (fecha): <archivos y entradas A/F donde se incorporó y qué comprobación se hizo con el código>`. Las secciones se separan con una sola línea `---`.
- Cada entrada de "Respondidas que falta aclaratoria" conserva `P:` y `R:` (con las palabras del usuario intactas) y añade dos líneas; la línea `Aclaración:` queda en blanco para el usuario:

```
P: ¿El parámetro `verbose` es un booleano?
R: Puede ser un número entero.
Contradicción (2026-11-15): `mod.py`, línea 42, función `run`: el código hace `if verbose is True` y lanza `TypeError` con cualquier otro tipo; tu respuesta admite enteros, así que no coinciden.
Aclaración: 
```
- Cada pregunta, exactamente así (la línea `R:` queda en blanco para el usuario):

```
P: ¿Las unidades de distancia son nanómetros? (ref. A4)
R: 
```

- Una línea `P:` y una línea `R:` por pregunta, separadas de la siguiente por una línea en blanco. La referencia a la entrada de la documentación va al final de la pregunta, entre paréntesis.

## Modo actualización (solo cuando el usuario diga "actualiza la documentación")
1. Leer `docs/preguntas_claude.md`. Las preguntas con `R:` no vacía se consideran **contestadas**; las vacías siguen pendientes. Las que ya están en "Respondidas que falta aclaratoria" y tienen `Aclaración:` no vacía se reevalúan (paso 2c). Las respuestas del usuario mandan sobre mis suposiciones, pero no sobre lo que el código hace.
2. **Contrastar cada respuesta con el código antes de incorporarla.** Para cada pregunta contestada, comprobar lo que dice la respuesta contra lo que el código hace de verdad: tipo de los valores (booleano, entero, flotante, cadena, `None`), valores permitidos y rangos, unidades, valores por defecto, nombres, comportamiento y orden de las operaciones. Comprobar leyendo el código y, cuando haga falta, ejecutando una prueba mínima en la copia aislada. Clasificar:
   - a) **Consistente** (el código hace lo que la respuesta dice): incorporar a la documentación (quitar la marca "Unconfirmed", resolver o reformular la entrada A/F asociada y corregir unidades, ecuaciones o descripciones afectadas en los tres archivos y en los ejemplos) y mover a `## Respondidas e incorporadas`.
   - b) **No verificable en el código** (p. ej. la unidad de un dato externo): incorporar y anotar en la documentación "según el autor, no verificable en el código"; mover a `## Respondidas e incorporadas`.
   - c) **Contradice el código** (o es en parte consistente y en parte no, p. ej. el «Sí» coincide pero la razón dada no: decir qué parte coincide y cuál no): NO incorporar como hecho. Mover a `## Respondidas que falta aclaratoria` con la contradicción explicada (formato abajo). En la documentación sigue mandando el código: describir lo que hace y añadir una nota "Author states (fecha): «…»; the code differs at `archivo:línea`; see preguntas_claude.md", registrar o actualizar la ambigüedad A… y, si la respuesta indica el comportamiento deseado y el código no lo cumple, registrar un fallo F… ("behaviour differs from the author's intent"). Si el usuario responde en `Aclaración:` que la respuesta era errónea, se incorpora la corrección; si confirma que el código está mal, se mantiene la nota y el fallo.
3. Mover las preguntas resueltas (a, b, y las aclaradas) a `## Respondidas e incorporadas` con la fecha, **sin modificar ninguna palabra del usuario** (ni su `R:` ni su `Aclaración:`). Dejar las pendientes donde están. Añadir las dudas nuevas que surjan, sin duplicar. Si la sección "Respondidas que falta aclaratoria" no existe en el archivo, crearla (entre Pendientes y Respondidas e incorporadas) y completar la cabecera explicativa.
4. Revisar el código por cambios desde la última versión (firmas, valores por defecto, funciones nuevas o eliminadas) y actualizar solo lo afectado; repetir los pasos 7b y 8 (tests, validación, ejemplos reejecutados). Los fallos ya corregidos se marcan "Resolved" con la fecha en lugar de borrarse.
4b. **Instrucciones dentro de las respuestas**: si el usuario pide algo concreto en su `R:` o `Aclaración:` (marcar en rojo una función que no funciona, citar una fuente externa, añadir una lista al final del README, dejar un recordatorio), hacerlo y anotarlo en la línea `Incorporada`. Convenciones: en rojo, `$\color{red}{\textbf{NOT WORKING}}$ 🔴` (GitHub lo renderiza) en la guía y en la entrada F; recordatorio = pregunta nueva P/R en Pendientes; fuente externa = URL y limitación (qué no confirma) en la línea `Incorporada`, comprobada con WebFetch, nunca de memoria; lista de versiones probadas = tabla al final del README raíz, obtenida ejecutando los ejemplos en cada versión (venvs aislados), y se mantiene en cada actualización.
4c. Si la `Aclaración:` corrige la respuesta y ahora coincide con el código → incorporar (conservar las 4 líneas del usuario y la `Contradicción` original, añadir `Incorporada`), marcar el fallo F asociado como resuelto ("Resolved", sin borrar la fila) y ajustar el recuento. Si sigue sin coincidir → mantener en "falta aclaratoria", añadir una nueva línea `Contradicción (fecha, segunda revisión)` con números comprobados y una nueva línea `Aclaración:` en blanco; la documentación sigue describiendo lo que hace el código.
5. Informar, empezando por lo más importante: **las contradicciones entre tus respuestas y el código** (cada una con la pregunta, `archivo:línea`, qué hace el código, qué implica la respuesta y por qué no coinciden); después, qué respuestas se incorporaron, qué cambió en el código desde la última versión y qué sigue pendiente. Si no hay contradicciones, decirlo expresamente.
- No hay actualización periódica: no actualizar nada por iniciativa propia ni por fecha.

### Estructura de `docs/user-guide.md`
1. Title and one-sentence description (what it does and for whom).
2. Contents (links) and links to the other two files.
3. Installation and requirements (Python version and dependencies as found in the code).
4. Overview: workflow (a ```mermaid``` diagram is fine, GitHub renders it) and code map (folder → contents).
5. Reference: one entry per public function, class or CLI option, in NumPy docstring style: one-line summary, extended description, `Parameters` (name, type, default, **units**), `Returns` (type, **units**), `Raises`, `Notes` (the **equation** implemented, with symbol definitions and the method/reference when supported), `See Also`.
6. Input and output formats (columns, units, generated file names).

### Estructura de `docs/precautions-and-assumptions.md`
1. Assumptions about input data (format, units, ordering, required preprocessing).
2. Assumptions of the method/algorithm and limits of validity.
3. Precautions: common mistakes, results that look valid but are not, performance and memory, reproducibility (seeds, versions).
4. Known limitations and behaviors not documented in the code (marked "Unconfirmed").
5. **Failures and ambiguities** (this exact section title), with subsections:
   - 5.1 *Test results*: table of every test or test script shipped with the code (pytest tests, scripts) with result FAILED / PASSED / NOT RUN and the cause or evidence; one-line summary count; environment (Python and package versions).
   - 5.2 *Verified failures*: numbered F1, F2… (reproduced by running code): where, what happens (message), consequence and workaround.
   - 5.3 *Suspected failures*: seen when reading, not executed (S1, S2…).
   - 5.4 *Verification checks that passed*: what I compared against an independent implementation or a known value.
   - 5.5 *Ambiguities*: numbered A1, A2…, each with file and line/function, what is ambiguous, why it matters, and how it was handled ("Unconfirmed" or the user's answer). Includes: units not stated or inconsistent, magic numbers without origin, comments that contradict the code, functions with undocumented defaults or side effects, dead or unreachable code, unclear conventions (0/1-based indices, axis order, normalization), silent failure modes, and mismatches between README and code. Links to `preguntas_claude.md`.
   - The other files and the Precautions section 3 refer to these IDs (F/S/A).
6. What the program does NOT do.

### Estructura de `docs/examples.md`
1. Minimal example (the shortest that works end to end).
2. Examples per real use case, each with: goal, input (which file and where it comes from), full command or code, expected output (files, shape of tables/figures), how to interpret it (including units).
3. Typical errors with their message and the fix.
- Every code block must be copy-pasteable and runnable as is. If there is no example data, use the examples/tests folder or synthetic data generated in the example itself; never invent results.

## Salida
- Los archivos dentro de `docs/` del directorio documentado, listos para `git push` (`user-guide.md`, `precautions-and-assumptions.md`, `examples.md`, `preguntas_claude.md`), más el README raíz (creado si no existía, o con la sección "Documentation" añadida).
- Informe final corto (en español): qué archivos se crearon o cambiaron, qué ambigüedades quedaron registradas, cuántas preguntas quedan pendientes en `preguntas_claude.md` (y cuáles se incorporaron, en modo actualización) y qué debería revisar yo.
- Si no puedo escribir en el directorio original, entrego `docs/` aparte (y el texto de la sección para el README) y lo digo.

## Reglas y errores comunes
- Texto de la documentación siempre en inglés; solo `preguntas_claude.md` en español.
- No hago preguntas durante la ejecución: las dudas van a `preguntas_claude.md` y a la documentación como "Unconfirmed".
- Solo escribo en `docs/` y en el README raíz (la sección "Documentation"; el README completo solo si no existía). No modifico el código fuente, no borro nada, no hago commit ni push.
- Nunca invento parámetros, valores por defecto, unidades, ecuaciones ni resultados de ejemplo. Si algo no es deducible, lo marco "Unconfirmed", lo registro en las ambigüedades y lo pregunto en `preguntas_claude.md`.
- Nunca modifico las palabras que el usuario escribió tras `R:`.
- Si el código y un README o comentario se contradicen, gana el código; lo registro como ambigüedad.
- Mantener nombres de funciones y opciones exactamente como en el código.
- Error frecuente: describir lo que el código "debería" hacer según su nombre; hay que leer qué hace realmente.
- Error frecuente: dar por buena la unidad de un paquete sin comprobarla (p. ej., MDAnalysis trabaja en Å y ps por defecto).
- Error frecuente: ejemplos con rutas absolutas del autor; usar rutas relativas.
- Error frecuente: borrar una pregunta respondida en vez de moverla a "Respondidas e incorporadas".
- Error frecuente: incorporar una respuesta sin contrastarla con el código; si la contradice va a "Respondidas que falta aclaratoria", con `archivo:línea` y el motivo, y la documentación sigue describiendo lo que el código hace.
- Si el directorio es muy grande, documentar primero la interfaz pública y avisar de lo que quedó fuera.

## Casos de prueba
Prefijo de archivos: `document-code__`

| Caso | Entrada | Resultado esperado | Estado |
|------|---------|--------------------|--------|
| 01 | Directorio de TrajStat (paquete Python de análisis de trayectorias, entregado como zip) | `docs/` con los 3 archivos en inglés; ecuaciones y unidades por función; sección "Failures and ambiguities" con tests; enlace en el README; todos los ejemplos ejecutados | ejecutado y validado (2026-10-28) |
| 02 | Directorio con un único script sin README (`test_claude1/Site_BindingAnalysis.py`, analiza distancias ión–sitio con MDAnalysis/PLUMED/TrajStat; depende de un módulo propio ausente) | Los 3 archivos + `preguntas_claude.md`; se crea un README con tabla de scripts y enlaces a los 3 archivos; ninguna pregunta al usuario; dudas en `preguntas_claude.md`; ejemplos ejecutados | ejecutado 2026-10-29: 10 fallos verificados (F1–F10), 3 sospechas, 16 ambigüedades, 11 salidas de ejemplos reproducidas; sin datos reales, con datos sintéticos y stand-in rotulado |
| 03 | Directorio que ya tiene `docs/` y README | Actualiza sin borrar; solo cambia la sección "Documentation" del README; el informe lista qué cambió | pendiente |
| 04 | TrajStat con `preguntas_claude.md` con respuestas rellenadas y "actualiza la documentación" | Las respuestas consistentes o no verificables incorporadas a los docs y movidas a "Respondidas e incorporadas" con sus palabras intactas; las vacías siguen pendientes; "Unconfirmed" eliminado donde corresponde | ejecutado 2026-10-28 con TrajStat: 3 respuestas incorporadas, palabras intactas, sin pérdidas |
| 05 | Una respuesta que contradice el código (p. ej. a la pregunta del valor por defecto de `block_hist_bins`: "None, que signifique sin histograma de bloques"; el setter convierte `None` en 10) | Va a "Respondidas que falta aclaratoria" con `archivo:línea`, lo que hace el código y el motivo; los docs siguen describiendo el código con la nota "Author states…"; el informe empieza por esa contradicción | ejecutado 2026-10-28 con TrajStat: 2 contradicciones detectadas (`std_mean`, `block_hist_bins`), con `archivo:línea` y comprobación por ejecución |
| 06 | Caso 05 más `Aclaración:` rellenada por el usuario | Se reevalúa: respuesta corregida → se incorpora; código mal → se mantiene la nota y se registra el fallo F; la entrada pasa a "Respondidas e incorporadas" con las 4 líneas intactas | ejecutado 2026-10-28: una aclaración corregida y coincidente (`block_hist_bins`) se incorpora y F19 queda resuelto; otra que sigue sin coincidir (`std_mean`: desviación estándar de las medias frente a error estándar) se mantiene con nueva Contradicción y `Aclaración:` en blanco |

## Notas de desarrollo
- Versión: 0.10 (README creado si no existe; paso 7a para datos/módulos ausentes)
- Versión anterior: 0.9 (actualización solo bajo petición; sin día 15)
- Cambios 0.8 (caso 06 y segunda ronda de actualización): pasos 4b y 4c (instrucciones dentro de las respuestas, marca en rojo, recordatorios, fuentes externas, lista de versiones probadas en el README, aclaraciones que resuelven o no la contradicción).
- Cambios 0.7 (tras probar el modo actualización con TrajStat): respuestas en parte consistentes van a "Respondidas que falta aclaratoria" indicando qué parte no coincide; las incorporadas llevan una línea `Incorporada (fecha)`; una sola línea `---` entre secciones.
- Cambios respecto a 0.1: documentación siempre en inglés; interpretar ecuaciones y unidades; enlace a `docs/` en el README raíz.
- Cambios 0.4 (tras el caso 01): la sección de Precautions se llama "Failures and ambiguities" e incluye resultados de los tests del código (fallidos y pasados), fallos verificados, sospechas y comprobaciones superadas; ejecución en copia aislada; los ejemplos se ejecutan y su salida real se pega en el documento; validación con script (firmas, enlaces, tablas).
- Cambios 0.5: la skill ya no pregunta durante la ejecución; deja las dudas en `docs/preguntas_claude.md` (español, formato P:/R:) y las incorpora en el modo actualización (solo con "actualiza la documentación").
- Cambios 0.6: en el modo actualización cada respuesta se contrasta con el código; las que lo contradicen van a la sección "Respondidas que falta aclaratoria" (con `archivo:línea`, qué hace el código y por qué no coincide, y una línea `Aclaración:` para el usuario) y el informe empieza por esas contradicciones.
- Decisión: ante una duda se documenta lo que el código hace literalmente y se marca "Unconfirmed".
- Pendiente: probar el caso 03 (directorio que ya tiene `docs/` y README); decidir si se proponen docstrings NumPy dentro del código (ahora no); la actualización periódica del día 15 se eliminó (0.9).
