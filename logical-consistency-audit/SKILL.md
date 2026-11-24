---
name: logical-consistency-audit
description: Audita de forma crítica un texto científico mío (manuscrito, sección o borrador de análisis) y enumera en una tabla las inconsistencias lógicas, numéricas, de unidades, de terminología, entre métodos y resultados, y las conclusiones que los datos no sostienen. Úsala cuando diga "busca inconsistencias en mi artículo", "revisa la lógica de mi análisis", "qué contradicciones hay en este borrador", "¿se sostienen mis conclusiones?" o "audit the logical consistency of my manuscript". Escribe todo en español en el directorio audit/. No corrige estilo ni verifica citas.
---

# Logical consistency audit

## Cuándo usarla
- "Revisa la consistencia lógica de este manuscrito / de la sección de Results / de mi Discussion".
- "Busca contradicciones entre el abstract, los métodos y los resultados."
- "¿Mis conclusiones se sostienen con lo que muestro?" antes de enviar a coautores o a una revista.
- Borradores de análisis (texto de un informe, capítulo de tesis) en los que quiero refinar el razonamiento.
- NO usarla para:
  - corregir ortografía, gramática o estilo (eso es copyediting);
  - verificar que las citas dicen lo que afirmo (para eso, Zotero / `manuscript-audit`);
  - comprobar si un resultado es cierto frente a la literatura o a datos que no están en el texto;
  - recalcular análisis a partir de datos crudos (solo reviso la aritmética que el propio texto permite comprobar);
  - reescribir el texto: reporto y sugiero cómo resolver, no sustituyo mis frases.

## Entradas esperadas
- El texto: manuscrito completo o sección en `.docx`, `.tex`, `.md`, `.pdf` o pegado en el chat. Cuantas más secciones (Abstract, Methods, Results, Discussion/Conclusions, captions de figuras y tablas, Supplementary si lo hay), más cruces posibles.
- **Fuente `.tex`:** se numeran las líneas del archivo y todas las ubicaciones del informe usan `L<línea>`; se ignoran las líneas comentadas con `%`; las macros de notas del autor (`\AB{}`, `\rev{}`, `\REV{}`, `\HV{}`, `\RP{}` y similares) se leen como notas propias y no como afirmaciones del manuscrito, salvo `\rev{}` cuando contiene una explicación que forma parte del argumento (decirlo en el Resumen). Las referencias a figuras o tablas del SI (`si_fig:*`, `externaldocument`) van a "Fuera de alcance o no verificable".
- **Texto en forma de esquema o borrador incompleto** (bullets, placeholders tipo "Details on…", Abstract vacío): que falte contenido no es un error. Se revisa lo que hay y lo pendiente se lista en "Fuera de alcance o no verificable", indicando qué hallazgos podrían cambiar cuando exista. Si el título o el objetivo prometen algo que ningún texto sustenta todavía, se dice en el veredicto de la cadena central, no en la tabla.
- Opcional: sección o aspecto en el que centrarse; tipo de artículo o revista (afecta a lo que es "afirmación fuerte"); figuras como imagen si quiero que se lean (si no, solo se usan sus captions).
- **Idioma de salida: TODO lo que la skill escribe va en español** (archivos del informe y de preguntas, con sus cabeceras, tabla, notas y el resumen que da en el chat), sea cual sea el idioma del manuscrito. Las citas del manuscrito se copian literalmente en su idioma original; si no están en español, se añade a continuación una traducción breve entre corchetes: `[ES: …]`. El nombre de la skill y los términos técnicos pueden quedar en inglés.
- No hago preguntas durante la ejecución. Solo si no hay ningún texto que revisar, lo digo y pido el archivo (una sola pregunta). Ante cualquier otra duda (p. ej. qué sección es la de Methods) uso mi mejor lectura y lo anoto en el Resumen del informe.

## Pasos
1. **Leer y estructurar.** Extraer el texto completo (`.docx`: skill `docx`/pandoc; `.pdf`: skill `pdf-reading`; `.tex`/`.md`: directamente). Numerar secciones, párrafos y captions (o líneas, en `.tex`) para poder localizar cada cita (p. ej. `Results §2, ¶3`). Anotar qué partes faltan (sin Methods, sin tablas…), porque limitan los cruces posibles.
2. **Construir un registro de afirmaciones** (interno, no se entrega). Para todo el texto, listar con su ubicación:
   - objetivos e hipótesis;
   - parámetros y condiciones de métodos (sistema, réplicas, tiempos, temperatura, software, versiones, criterios de análisis);
   - cantidades con su valor, unidad, n y estadístico asociado;
   - definiciones y términos clave (y abreviaturas);
   - afirmaciones de resultado y de conclusión, con su cuantificador ("todos", "siempre", "demuestra", "sugiere");
   - referencias cruzadas a figuras, tablas, ecuaciones y secciones.
3. **Contrastar sistemáticamente** el registro con esta taxonomía (un recorrido por categoría, no una lectura única). Los códigos se mantienen; los nombres de tipo van en español en el informe:

   | Código | Tipo (en el informe) | Qué busca |
   |--------|----------------------|-----------|
   | LOG-1 | Conclusión no sostenida | Alcance de la conclusión mayor que el de la evidencia (condiciones, escalas, muestras o sistemas no estudiados); hipótesis u objetivo que nunca se contrasta |
   | LOG-2 | Contradicción directa | Dos afirmaciones que no pueden ser ciertas a la vez, o una que niega otra de otra sección |
   | LOG-3 | Inferencia inválida | Causalidad sin control o intervención, circularidad, non sequitur, "demuestra" con evidencia correlacional, interpretación errónea de un estadístico (p. ej. "no significativo" con p < α) |
   | NUM | Numérica | Mismo valor distinto en abstract/texto/tabla/figura; medias, sumas, porcentajes y totales que no cuadran; n o grados de libertad inconsistentes; cifras significativas incompatibles con el error |
   | UNI | Unidades y escalas | Unidades mezcladas o mal convertidas (nm/Å, ps/ns, kJ/mol/kcal/mol, rad/grados), órdenes de magnitud |
   | TER | Terminología | Un término con dos definiciones, dos términos para lo mismo sin decirlo, umbrales o criterios que cambian entre secciones, abreviaturas sin definir o redefinidas |
   | MET | Métodos vs. resultados | Condiciones descritas que no coinciden con las reportadas; elementos que aparecen en resultados y no existen en métodos (o al revés); análisis anunciados y no mostrados |
   | REF | Referencia interna | Figura, tabla, ecuación o sección citada que no existe o no contiene lo que se dice |
   | ORD | Orden y premisas | Concepto usado antes de definirse; premisa necesaria para el argumento que no se establece en ningún sitio |

4. **Verificar cada candidato antes de listarlo.**
   - Releer las dos (o más) ubicaciones implicadas y copiar la cita literal de cada una (≤ 25 palabras).
   - **Toda aritmética se ejecuta** con Python (medias, sumas, conversiones de unidades), no se hace de cabeza. Anotar en el informe el cálculo y su resultado cuando el hallazgo sea numérico.
   - **Lectura benevolente (steelman):** buscar en el resto del texto una explicación legítima (otra condición, otra definición, un matiz ya explicado). Si la lectura benevolente resuelve el problema, descartar el hallazgo; si solo lo resuelve con una suposición mía, bajarlo a confianza Baja y decirlo.
   - Si no puedo señalar la ubicación exacta de la evidencia, el hallazgo se descarta.
5. **Clasificar** cada hallazgo (etiquetas en español, tal como aparecen en el informe):
   - Severidad: **Crítica** (invalida o contradice un resultado o conclusión principal), **Mayor** (un revisor lo señalaría y cambia la interpretación o la reproducibilidad), **Menor** (se arregla con una corrección local).
   - Confianza: **Alta** (contradicción literal comprobada con citas y, si es numérica, con cálculo), **Media** (depende de una interpretación razonable), **Baja** (posible debilidad; hay una lectura benevolente plausible).
   - Si el propio manuscrito ya contiene una nota del autor sobre el mismo problema, añadir "Ya señalado (L…)" en la celda "Inconsistencia" y contarlo en el Resumen.
6. **Valorar la cadena central** (hipótesis/objetivo → métodos → resultados → conclusión): ¿cada eslabón se sigue del anterior? Resumirlo en 2–3 líneas, aunque no haya fallos.
7. **Crear `audit/` si no existe** (junto al manuscrito) y **redactar en español** los dos archivos de la sección Salida, con los hallazgos ordenados de mayor a menor severidad (y, a igualdad, por orden de aparición en el texto).
8. **Validar la salida**: cada fila tiene ubicación y cita literal; las citas existen tal cual en el texto (comprobarlo con un script que normalice espacios en blanco y verifique que cada cita, de ≤ 25 palabras, aparece como subcadena del manuscrito; ninguna sin encontrar) y los números de línea citados existen; el recuento del Resumen coincide con la tabla (por severidad, tipo y confianza); las cuentas reportadas se reprodujeron; los IDs de la tabla y de las preguntas coinciden; ningún hallazgo es de estilo; **no queda texto propio en otro idioma que no sea español** (solo las citas literales del manuscrito y los términos técnicos).

## Salida
- Todo se guarda en un directorio **`audit/`** junto al manuscrito. **Si `audit/` no existe, la skill lo crea** (`mkdir -p audit`) antes de escribir nada, sin preguntar; si ya existe, lo reutiliza y no toca nada que no sea de esta skill. En él conviven los archivos de otros agentes de auditoría, así que cada archivo de esta skill lleva el sufijo **`_consistencia`** y nunca se sobrescribe ni se borra un archivo ajeno. Si no puedo escribir junto al manuscrito, entrego `audit/` aparte y lo digo.
- Dos archivos, **íntegramente en español**:
  - `audit/informe_consistencia.md`: informe con Resumen, tabla, comprobaciones superadas y lo que quedó fuera de alcance.
  - `audit/preguntas_consistencia.md`: las preguntas al autor, en el formato de `preguntas_claude.md` (el autor contesta tras `R:`; en blanco = sin respuesta).
- Si ya existen en `audit/` versiones de estos dos archivos, no las sobrescribo: conservo las respuestas `R:` ya escritas, y guardo la nueva ejecución como `informe_consistencia_AAAA-MM-DD.md` y `preguntas_consistencia_AAAA-MM-DD.md`, avisándolo en el resumen.
- Resumen corto en el chat, también en español: los 3–5 hallazgos más graves y la ruta de los archivos.

### Estructura de `audit/informe_consistencia.md`

**Cabecera.** Nombre del manuscrito, fecha, versión de la skill, y una explicación breve: la tabla es de solo lectura; las respuestas van en `preguntas_consistencia.md`.

**`## Resumen`**
- Texto revisado (secciones cubiertas; secciones ausentes o ilegibles).
- Recuento por severidad y por tipo; cuántos están "Ya señalados" por el autor.
- Veredicto de la cadena central (paso 6).
- Suposiciones de lectura (p. ej., qué sección se tomó como Methods).

**`## Tabla de inconsistencias`**

| ID | Tipo | Sev. | Conf. | Inconsistencia | Evidencia (cita y ubicación) | Cómo resolver (opciones) |
|----|------|------|-------|----------------|------------------------------|--------------------------|
| I1 | MET | Crítica | Alta | Una frase: qué no cuadra | A: «…» (Abstract) ↔ B: «…» (Methods §1) | Corregir el valor erróneo, o explicar la diferencia si se refieren a cosas distintas |

- Una fila por hallazgo. Celdas cortas; el detalle va a las preguntas.
- "Cómo resolver" da opciones (corregir X, aclarar Y, suavizar el cuantificador), nunca el texto reescrito ni cuál de los dos valores es el cierto.

**`## Comprobaciones superadas`**
Lista breve de lo que se contrastó y cuadra (p. ej. "tiempo total = 3 × 300 ns = 900 ns", "la media de la Tabla 1 coincide con el texto"), para que sepa qué se miró.

**`## Fuera de alcance o no verificable`**
Lo que no se pudo comprobar y por qué (valores solo visibles en una figura que no se leyó, falta de la sección de Methods, afirmaciones que dependen de la literatura o de datos no incluidos, contenido pendiente del borrador).

### Estructura de `audit/preguntas_consistencia.md`
Cabecera breve: cómo usarlo (escribe tu respuesta tras `R:`; en blanco = sin respuesta) y a qué informe corresponde. Después, una pregunta por hallazgo que necesite la decisión del autor, exactamente así (la línea `R:` queda en blanco), con la referencia al ID al final y una línea en blanco entre preguntas:

```
P: ¿Cuál es el diseño real de las simulaciones: 5 réplicas de 500 ns (Abstract) o 3 réplicas de 300 ns (Methods)? (ref. I1)
R: 
```

## Reglas y errores comunes
- Todo texto propio de los archivos y del resumen en el chat va en español; solo se conservan en su idioma original las citas literales del manuscrito (con `[ES: …]` si no están en español).
- Todos los archivos van en `audit/` con el sufijo `_consistencia`; nunca se tocan archivos de otros agentes de auditoría.
- Cada hallazgo lleva cita literal y ubicación. Sin evidencia localizable, no se reporta.
- Un hallazgo es un hecho comprobable en el texto (confianza Alta/Media) o una posible debilidad (confianza Baja); nunca presentar como hecho lo que es una inferencia mía.
- No decido cuál de dos valores en conflicto es el correcto: lo pregunto.
- Preguntas al autor: una por hallazgo que necesite su decisión. No se pregunta por erratas ni por problemas que el autor ya marcó y cuya resolución es mecánica (p. ej. replotear con un valor que él mismo ya midió).
- No evalúo si la ciencia es cierta frente a la literatura; solo si el texto es coherente consigo mismo. Cuando una conclusión parezca demasiado fuerte para el diseño descrito, lo reporto como LOG-1/LOG-3 indicando qué evidencia falta, sin afirmar que sea falsa.
- No reporto estilo, redacción ni erratas que no cambien el sentido.
- No modifico el manuscrito original (trabajo sobre una copia de texto) ni reescribo sus frases.
- Error frecuente: marcar como contradicción dos cosas que son condiciones distintas (p. ej. dos sistemas, dos réplicas, dos ventanas de análisis). Antes de reportar, buscar si el texto las distingue.
- Error frecuente: cuentas de cabeza. Ejecutarlas siempre.
- Error frecuente: confundir "no está en el texto que me diste" con "el autor no lo dijo". Si falta una sección, va a "Fuera de alcance o no verificable", no a la tabla.
- Error frecuente: rellenar la tabla para parecer exhaustivo. Un informe corto y defendible vale más que uno largo con falsos positivos; si no hay inconsistencias, decirlo expresamente.
- Error frecuente: contagiarse del idioma del manuscrito (si está en inglés, escribir el informe en inglés). La salida es siempre en español.
- Error frecuente: sobrescribir respuestas `R:` de una ejecución anterior.
- Texto muy largo: procesar por secciones, pero mantener el registro de afirmaciones global para cruzar secciones lejanas; indicar en el Resumen la cobertura real.

## Casos de prueba
Prefijo de archivos: `logical-consistency-audit__`

La clave de respuestas del caso 01 está en un archivo aparte (`logical-consistency-audit__caso01_clave.md`) que NO debe cargarse al ejecutar la prueba.

| Caso | Entrada | Resultado esperado | Estado |
|------|---------|--------------------|--------|
| 01 | `logical-consistency-audit__caso01_entrada.md` (manuscrito sintético en inglés de MD de ARN, con inconsistencias plantadas y varias trampas que parecen errores y no lo son) | Crea `audit/` con `informe_consistencia.md` y `preguntas_consistencia.md`, íntegramente en español; detecta todas las inconsistencias plantadas (clave aparte) con cita y ubicación correctas; ningún falso positivo en las trampas; aritmética ejecutada; tabla ordenada por severidad | pendiente |
| 02 | `logical-consistency-audit__caso02_entrada.tex` (tu `main.tex`, esquema del artículo CFREX) | Tabla con citas localizables en tu texto; tú marcas cuáles son falsos positivos y qué omisiones hay | ejecutado 2026-11-23 con v0.3 (23 hallazgos: 1 Crítica, 13 Mayor, 9 Menor; 62 citas verificadas por script) con salida en inglés; pendiente repetir con v0.4 (salida en español en `audit/`) y de tu revisión de falsos positivos |
| 03 | `logical-consistency-audit__caso03_control.md` (misma estructura que el 01, corregido, sin inconsistencias) | Cero hallazgos de confianza Alta/Media; como mucho 2 debilidades de confianza Baja; el Resumen dice expresamente que no hay inconsistencias | pendiente |
| 04 | Solo una Discussion pegada en el chat, sin Methods ni tablas | Revisa la consistencia interna; lista en "Fuera de alcance o no verificable" lo que exige Methods o tablas; no inventa | pendiente |
| 05 | Manuscrito largo (> 5000 palabras) en `.docx` o `.tex` | Cubre todas las secciones; encuentra al menos un cruce entre secciones lejanas; el Resumen indica la cobertura; los archivos se generan en `audit/` | pendiente |
| 07 | Directorio con el manuscrito y sin `audit/` | La skill crea `audit/` y deja dentro los dos archivos `_consistencia`; no pregunta | pendiente |
| 06 | Directorio con `audit/` que ya contiene un archivo de otro agente (p. ej. `informe_citas.md`) y un `preguntas_consistencia.md` con respuestas `R:` | No toca el archivo ajeno; no sobrescribe las respuestas; guarda la nueva ejecución con sufijo de fecha y lo avisa | pendiente |

## Notas de desarrollo
- Versión: 0.4. Cambios: nombre de la skill en inglés (`logical-consistency-audit`; antes `revisa-consistencia-logica`); toda la salida y el resumen del chat en español (antes en inglés); salida en el directorio `audit/` con el sufijo `_consistencia`; informe y preguntas en archivos separados (`P:`/`R:` como en `preguntas_claude.md`); protección de archivos de otros agentes y de respuestas previas. 0.3: manejo de `.tex`, borradores en forma de esquema, "Ya señalado", validación de citas por script. 0.2: salida en inglés. 0.1: primer borrador a partir de `skill-plantilla.md`.
- Decisiones tomadas:
  - Nombre en inglés, instrucciones y salida en español; citas del manuscrito en su idioma con traducción `[ES: …]`.
  - `audit/` compartido con otros agentes de auditoría (citas, estilo, etc.); el sufijo `_consistencia` evita colisiones.
  - Sin preguntas durante la ejecución (salvo que no haya texto).
  - Taxonomía de nueve tipos, con severidad y confianza separadas; paso obligatorio de lectura benevolente para reducir falsos positivos.
- Pendiente (por completar):
  - Convención de nombres para los demás agentes de `audit/` (se propone `informe_<aspecto>.md` y `preguntas_<aspecto>.md`; por confirmar cuando existan).
  - Modo "revisa mis respuestas": reevaluar el texto tras tus `R:` y moverlas a "Respondidas" (análogo al modo actualización de `document-code`).
  - Lectura de figuras como imagen (ahora solo captions, salvo que se adjunten).
  - Umbral de longitud a partir del cual trocear el manuscrito.
  - Relación con `manuscript-audit`, cuya tercera pasada ya incluye una revisión de consistencia lógica: decidir si esta skill la sustituye, la complementa o se invoca desde ella.
  - Repetir el caso 02 con v0.4 y probar los casos 01 y 03–06.
