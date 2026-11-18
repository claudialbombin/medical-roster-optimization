# Cuadrantes de guardias médicas justos y factibles

**Segunda entrega intermedia (checkpoint 2), con el formato de la entrega final.**
Optimización y Simulación, curso 2026–2027.
Equipo: Miguel Troyano Martín · Claudia María López Bombín · María Teresa Sangil Terrés.

El documento principal de la entrega es **`informe_final.pdf`**. Contiene el problema, los datos y supuestos, la formulación matemática completa, la implementación, los resultados, el análisis de sensibilidad y las conclusiones. El resto de la carpeta es el código y los datos necesarios para reproducir todos sus resultados.

> Todos los datos son **sintéticos** y se generan de forma reproducible. Los resultados no describen ningún hospital real.

## Contenido

| Ruta | Contenido |
|---|---|
| `informe_final.pdf` | Informe final (documento principal). |
| `report/` | Código fuente LaTeX del informe (`final_report.tex` y las partes que incluye). |
| `notebooks/01_synthetic_data_generation.ipynb` | Generador de las 7 instancias sintéticas (sección 2 del informe). |
| `notebooks/02_baseline_model.ipynb` | Modelo MILP en Gurobi, resolución, validación independiente del cuadrante e indicadores (secciones 3 y 4). |
| `notebooks/03_analysis.ipynb` | Todos los resultados, tablas y figuras del informe (secciones 5 y 6). |
| `data/synthetic/` | Instancias: `jan2027_base`, `dec2026_christmas`, `aug2027_summer` y `feb2027`–`may2027`. Cada una con sus CSV y un `meta.json` con la configuración y la semilla. |
| `results/figures/`, `results/tables/` | Figuras y tablas generadas por los notebooks. Las del informe llevan el prefijo `final_`. |
| `docs/formulation.md` | Formulación de referencia del código (en inglés; coincide con la sección 3 del informe). |
| `requirements.txt` | Dependencias de Python. |

Los notebooks se entregan ejecutados, con sus salidas.

## Correspondencia entre el informe y el código

| Informe | Notebook | Salida |
|---|---|---|
| Tabla 2 (instancias) | 01, sección 5 | `data/synthetic/*` |
| Sección 3 (modelo), restricciones (1)–(10) | 02, `build_model` (un bloque por restricción) | — |
| Tabla 3 (tres meses de referencia), figuras 1–2 | 03, sección 2 | `final_baseline_months.csv`, `final_roster_jan2027.png`, `final_fairness_jan2027.png` |
| Sección 6.1 (plantilla), figura 3, tabla 4 | 03, sección 3.1 | `final_staffing_curve.csv`, `final_min_staff.csv`, `final_staffing_vs_regime.png` |
| Sección 6.2 (quién falta) | 03, sección 3.2 | `final_composition.csv` |
| Sección 6.3 (guardias frente a planta), figura 4 | 03, sección 3.3 | `final_duties_vs_ordinary.csv`, `final_ordinary_PL.csv`, `final_duties_vs_ordinary.png` |
| Sección 6.4 (equidad en varios meses), figura 5 | 03, sección 3.4 | `final_fairness_rolling.csv`, `final_fairness_rolling.png` |
| Sección 6.5 (qué aporta cada objetivo), tabla 5 | 03, sección 3.5 | `final_objective_variants.csv` |

## Cómo reproducir los resultados

Requisitos: Python 3.10 o superior y una **licencia académica de Gurobi** (gratuita en el [Gurobi Academic Program](https://www.gurobi.com/academia/academic-program-and-licenses/)). La licencia que viene con `pip install gurobipy` limita el modelo a 2 000 variables y restricciones, lo que no basta para los experimentos con plantillas ampliadas.

```bash
python -m venv .venv
.venv\Scripts\activate             # Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt
cd notebooks
jupyter lab
```

Ejecutar los notebooks en orden desde la carpeta `notebooks/`:

1. `01_synthetic_data_generation.ipynb`: regenera `data/synthetic/` (los ficheros salen idénticos a los entregados).
2. `02_baseline_model.ipynb`: modelo base de enero de 2027 (menos de un minuto).
3. `03_analysis.ipynb`: análisis completo (unos 11 minutos).

Probado con Python 3.12, Gurobi 13.0.3, pandas 2.2, NumPy 1.26 y Matplotlib 3.8.

Los valores de los objetivos $f_1$–$f_4$ son únicos, pero puede haber varios cuadrantes óptimos. Los indicadores que el modelo no optimiza (por ejemplo, el rango de guardias por residente) pueden variar ligeramente entre versiones de Gurobi.
