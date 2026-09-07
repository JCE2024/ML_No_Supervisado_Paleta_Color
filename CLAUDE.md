# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-notebook academic microproject (MAIA, Machine Learning No Supervisado, Módulo 5). The
assignment brief is [Microproyecto1.pdf](Microproyecto1.pdf); all work goes into
[Microproyecto.ipynb](Microproyecto.ipynb). Not a git repository.

**Working language is Spanish** — the brief, the notebook prose, and the required justifications are
all in Spanish. Write markdown cells and explanatory comments in Spanish.

## Goal and required method

Extract dominant colors from paintings using unsupervised clustering and turn each image's clusters
into a color palette ("muestrario"). The pipeline the brief mandates:

1. **Selection** — 6–10 images from the assignment's art repository, spanning at least three styles
   or painters, shown at the top of the notebook with a brief rationale for the choice.
2. **Preparation** — an sklearn-style `Pipeline` holding the image transformations (each pixel is a
   sample in some color space; resizing/subsampling and color-space choice are decisions that must
   be justified in prose).
3. **Clustering** — fit a model **per image**, with hyperparameter search. The cluster count is not
   fixed: it must adapt to each image's characteristics, and the algorithm choice plus the
   validation metrics (e.g. silhouette, Calinski-Harabasz) must be justified.
4. **Palette + projection** — a function mapping clusters to a representative palette, plus a 2D
   view of the image's color distribution via a dimensionality-reduction technique (the brief
   suggests t-SNE).

Final demonstration: palettes and 2D color distributions for at least four images of differing
styles. Clustering carries 30% of the grade, the final demonstration 25%.

## Deliverable constraints

Both `Microproyecto.ipynb` **and** an exported `.html` are submitted. Every cell's output must be
present in the saved notebook — never strip outputs, and prefer running cells over leaving them
unexecuted. Each step needs a markdown cell justifying the decision taken; the rubric grades the
justifications, not just the code.

## Environment

The interpreter that has the project's packages is the conda env **`ml_entorno`**:
`C:\Users\USER\anaconda3\envs\ml_entorno\python.exe` (Python 3.11.14). Installed there: numpy 2.0.2,
pandas 2.3.3, scikit-learn 1.8.0, scipy 1.17.0, matplotlib 3.10.8, Pillow 12.1.1, joblib 1.5.3,
nbconvert 7.16.6, nbclient 0.10.2, ipykernel 6.29.3. **No** seaborn, opencv (`cv2`), scikit-image
or `sklearn_extra`.

Do not call a bare `python` — on PATH it resolves to the Microsoft Store stub, which has nothing
installed. Use the full path above. The registered `python3` kernelspec launches `python` from PATH,
so put the env first when running the notebook headlessly:

```bash
export PATH="/c/Users/USER/anaconda3/envs/ml_entorno:/c/Users/USER/anaconda3/envs/ml_entorno/Scripts:$PATH"
"/c/Users/USER/anaconda3/envs/ml_entorno/python.exe" -m jupyter execute Microproyecto.ipynb --inplace
"/c/Users/USER/anaconda3/envs/ml_entorno/python.exe" -m nbconvert --to html Microproyecto.ipynb
```

A full run of the notebook takes about 70 seconds. Anaconda's base env (Python 3.12) also exists but
has older versions (numpy 1.26, scikit-learn 1.3) — don't use it.

## Estilo de entrega del curso

Las prácticas de referencia (`MLNS-Practica-*.html`, `MLNS-Tutorial-*.ipynb`, exportadas por
nbconvert en cp1252) fijan el estilo que debe seguir el notebook. Conviene respetarlo:

- Secciones numeradas (`1. Importación de librerías requeridas`, `2. Carga de datos`, …) y una
  sección final llamada `Cierre` que resume lo visto y advierte que la solución no es única.
- Celdas cortas: una celda markdown breve en primera persona del plural ("Definiremos…",
  "Podemos observar…") antes de cada celda de código.
- Funciones sencillas, sin clases ni transformadores propios. Docstring con el formato del curso:
  descripción, línea en blanco, `Parametros:`, y cada parámetro como `nombre : tipo` con su
  descripción indentada debajo.
- Nombres de variables y funciones en inglés (`img_list`, `num_clusters`, `centroids`, `labels`),
  prosa y comentarios en español. `random_state=0`.
- Gráficas con `plt.subplot`/`plt.figure`, `marker='o'`, `plt.grid()`, `plt.show()`; tablas con
  `display(pd.DataFrame(...))` o dejando la expresión suelta al final de la celda.
- Tras cada resultado, una celda markdown que lo interpreta.

Las prácticas usan `cv2` (`imread` + `cvtColor`), pero **opencv no está instalado**; el notebook usa
PIL en su lugar.

## Estado del microproyecto

`Microproyecto.ipynb` implementa el método completo (65 celdas) sobre las 7 obras de [Imagenes/](Imagenes/),
**ejecutado y con todas las salidas guardadas** (32 celdas de código, 13 figuras, `execution_count`
consecutivo del 1 al 32), y exportado a `Microproyecto.html`. Tras cualquier edición de código hay que
volver a ejecutar y a exportar, porque el enunciado exige que se vean las ejecuciones de cada celda.

Decisiones que conviene no deshacer sin motivo, porque sostienen la calificación:

- **CIELab, no RGB.** La conversión sRGB↔Lab está implementada a mano (no hay `scikit-image`) porque
  KMeans minimiza distancia euclídea y solo en Lab esa distancia equivale a diferencia percibida.
- **`k` se elige por error de color (ΔE ≤ 8), no por silueta.** Verificado empíricamente: las métricas
  internas mantienen la silueta entre 2 y 4 en las 7 obras y las ordenan al contrario de su riqueza
  cromática (a Pissarro, el más complejo, le dan el mínimo). El criterio ΔE sí discrimina: del Sarto 4,
  Altdorfer y Hopper 5, Kuniyoshi 10, Picasso 11, Van Gogh 12, Pissarro 14. Las métricas internas se
  conservan solo para validar.

Si se cambian `max_side`, `max_pixels` o `max_delta_e`, los valores de `k` se mueven y hay que
reverificar las afirmaciones del texto que citan resultados concretos.
