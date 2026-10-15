# Penguin Species Methodologies

Aplicación de tres metodologías de minería de datos, **SEMMA**, **KDD** y **CRISP-DM**, a una misma base de datos: los pingüinos del archipiélago Palmer, en la Antártida. En cada metodología se entrena un modelo que clasifica la especie del pingüino.

> **Proyecto de demostración:** su propósito es mostrar cómo se aplica cada metodología de principio a fin, no comparar sus resultados.

## Estado

| Metodología | Modelo | Estado |
| --- | --- | --- |
| SEMMA | Regresión logística | Terminada |
| KDD | Random Forest | Terminada |
| CRISP-DM | Máquina de vectores de soporte (SVM) | Terminada |

## Objetivo

Mostrar cómo se trabaja una misma base de datos siguiendo metodologías distintas, aplicadas de forma **independiente**:

- Cada metodología tiene su propia carpeta, con sus notebooks, datos intermedios, modelos, figuras y reporte de resultados.
- El único recurso compartido es la base de datos original, en `data/raw/`.
- Cada metodología usa un modelo de clasificación distinto.
- Cada reporte documenta solo su metodología, sin referencias a los resultados de las demás.

**Problema de modelado:** clasificar la especie del pingüino (Adelie, Chinstrap o Gentoo) a partir de sus medidas, su isla y su sexo, con `penguins_size.csv`.

**Partición de los datos:** 80 % entrenamiento y 20 % prueba, estratificada por especie, en las tres metodologías.

**Reproducibilidad:** cada notebook con procesos aleatorios define su propia semilla (`SEED = 1`). Puede modificarse o eliminarse (`SEED = None`).

## Datos

| Campo | Detalle |
| --- | --- |
| Nombre | Palmer Archipelago (Antarctica) penguin data |
| Fuente utilizada | [Kaggle, parulpandey](https://www.kaggle.com/datasets/parulpandey/palmer-archipelago-antarctica-penguin-data) |
| Origen de los datos | Dra. Kristen Gorman y Palmer Station, Antarctica LTER |
| Periodo de muestreo | 2007 a 2009 |
| Licencia | CC0 (dominio público), según la política de datos de Palmer Station LTER |
| Registros | 344 pingüinos |

### Archivos

| Archivo | Descripción |
| --- | --- |
| `data/raw/penguins_size.csv` | Versión simplificada: especie, isla, medidas y sexo. Es la que usa el proyecto. |
| `data/raw/penguins_lter.csv` | Datos originales de las tres especies, con variables adicionales (identificadores, fechas, isótopos). |

### Variables de `penguins_size.csv`

| Variable | Tipo | Descripción |
| --- | --- | --- |
| `species` | Categórica | Especie: Adelie, Chinstrap o Gentoo |
| `island` | Categórica | Isla: Biscoe, Dream o Torgersen |
| `culmen_length_mm` | Numérica | Longitud del culmen (mm) |
| `culmen_depth_mm` | Numérica | Profundidad del culmen (mm) |
| `flipper_length_mm` | Numérica | Longitud de la aleta (mm) |
| `body_mass_g` | Numérica | Masa corporal (g) |
| `sex` | Categórica | Sexo del individuo |

El culmen es la cresta superior del pico. En el paquete de R `palmerpenguins` estas variables se llaman `bill_length_mm` y `bill_depth_mm`.

### Distribución de los registros

| Especie | Registros |
| --- | --- |
| Adelie | 152 |
| Gentoo | 124 |
| Chinstrap | 68 |

| Isla | Registros |
| --- | --- |
| Biscoe | 168 |
| Dream | 124 |
| Torgersen | 52 |

Las clases están desbalanceadas: Chinstrap representa cerca del 20 % del total. Por eso la partición es estratificada y las métricas principales dan el mismo peso a las tres especies.

## Metodologías

| Metodología | Origen | Fases |
| --- | --- | --- |
| **SEMMA** | SAS Institute (2014) | Sample, Explore, Modify, Model, Assess |
| **KDD** | Fayyad et al. (1996) | Selección, preprocesamiento, transformación, minería de datos, interpretación y evaluación |
| **CRISP-DM** | Chapman et al. (2000) | Comprensión del negocio, comprensión de los datos, preparación de los datos, modelado, evaluación, despliegue |

- **SEMMA** organiza el trabajo técnico de minería de datos en cinco etapas. La definió SAS para su herramienta Enterprise Miner y no incluye la comprensión del negocio ni el despliegue.
- **KDD** describe el proceso completo para descubrir conocimiento en bases de datos. La minería de datos es solo uno de sus pasos.
- **CRISP-DM** es un estándar abierto, creado por un consorcio de empresas. Parte de los objetivos del negocio y termina con el despliegue del modelo; sus fases pueden repetirse.

## Estructura del repositorio

```text
penguin-species-methodologies/
├── data/
│   └── raw/                  # Base original, compartida y sin modificar
├── semma/
│   ├── notebooks/            # 01_sample a 05_assess
│   ├── data/
│   │   ├── interim/          # Datos intermedios
│   │   └── processed/        # Datos transformados y resultados
│   ├── models/               # Modelos entrenados
│   ├── figures/              # Figuras de los notebooks
│   └── report/               # Reporte de resultados
├── kdd/                      # Misma organización
├── crisp_dm/                 # Misma organización
├── requirements.txt
└── README.md
```

**Convenciones:**

- Los archivos de `data/raw/` nunca se modifican.
- Cada metodología lee solo de `data/raw/` y de su propia carpeta.
- Los notebooks se ejecutan en orden numérico; cada uno lee los archivos que genera el anterior.

### Archivos de SEMMA

Rutas relativas a `semma/`.

| Notebook | Entradas | Salidas |
| --- | --- | --- |
| `01_sample.ipynb` | `data/raw/penguins_size.csv` | `data/interim/train.csv`, `data/interim/test.csv`, `data/interim/sample_summary.csv` |
| `02_explore.ipynb` | `data/interim/train.csv` | `data/interim/explore_stats.csv`, `figures/explore_*.png` |
| `03_modify.ipynb` | `data/interim/train.csv`, `data/interim/test.csv` | `data/interim/train_clean.csv`, `data/interim/test_clean.csv`, `data/processed/train_prepared.csv`, `data/processed/test_prepared.csv`, `models/preprocessor.joblib` |
| `04_model.ipynb` | `data/interim/train_clean.csv`, `models/preprocessor.joblib` | `models/logistic_regression.joblib`, `models/baseline.joblib`, `data/processed/cv_results.csv`, `data/processed/lr_coefficients.csv`, `figures/model_cv_c.png` |
| `05_assess.ipynb` | `data/interim/test_clean.csv`, modelos de `models/`, `data/processed/cv_results.csv` | `data/processed/metrics.csv`, `data/processed/class_report.csv`, `data/processed/misclassified.csv`, `figures/assess_confusion_matrix.png` |

El reporte está en [`semma/report/resultados.md`](semma/report/resultados.md).

### Archivos de KDD

Rutas relativas a `kdd/`.

| Notebook | Entradas | Salidas |
| --- | --- | --- |
| `01_selection.ipynb` | `data/raw/penguins_size.csv` | `data/interim/train.csv`, `data/interim/test.csv`, `data/interim/selection_summary.csv` |
| `02_preprocessing.ipynb` | `data/interim/train.csv`, `data/interim/test.csv` | `data/interim/train_clean.csv`, `data/interim/test_clean.csv`, `figures/preprocessing_boxplots.png` |
| `03_transformation.ipynb` | `data/interim/train_clean.csv`, `data/interim/test_clean.csv` | `data/processed/train_transformed.csv`, `data/processed/test_transformed.csv`, `models/encoder.joblib`, `figures/transformation_culmen_ratio.png` |
| `04_data_mining.ipynb` | `data/processed/train_transformed.csv` | `models/random_forest.joblib`, `models/baseline.joblib`, `data/processed/cv_results.csv`, `figures/mining_cv_results.png` |
| `05_interpretation_evaluation.ipynb` | `data/processed/test_transformed.csv`, `data/interim/test_clean.csv`, modelos de `models/`, `data/processed/cv_results.csv` | `data/processed/metrics.csv`, `data/processed/class_report.csv`, `data/processed/misclassified.csv`, `data/processed/feature_importance.csv`, `figures/evaluation_confusion_matrix.png`, `figures/interpretation_feature_importance.png` |

El notebook 04 tarda alrededor de un minuto por la búsqueda de hiperparámetros. El reporte está en [`kdd/report/resultados.md`](kdd/report/resultados.md).

### Archivos de CRISP-DM

Rutas relativas a `crisp_dm/`.

| Notebook | Entradas | Salidas |
| --- | --- | --- |
| `01_business_understanding.ipynb` | Ninguna | `data/interim/success_criteria.json` |
| `02_data_understanding.ipynb` | `data/raw/penguins_size.csv` | `data/interim/train.csv`, `data/interim/test.csv`, `data/interim/split_summary.csv`, `data/interim/data_quality.csv`, `figures/understanding_*.png` |
| `03_data_preparation.ipynb` | `data/interim/train.csv`, `data/interim/test.csv` | `data/interim/train_clean.csv`, `data/interim/test_clean.csv`, `data/processed/train_prepared.csv`, `data/processed/test_prepared.csv`, `models/preprocessor.joblib` |
| `04_modeling.ipynb` | `data/interim/train_clean.csv`, `models/preprocessor.joblib` | `models/svm.joblib`, `models/baseline.joblib`, `data/processed/cv_results.csv`, `figures/modeling_cv_results.png` |
| `05_evaluation.ipynb` | `data/interim/test_clean.csv`, `data/interim/success_criteria.json`, modelos de `models/`, `data/processed/cv_results.csv` | `data/processed/metrics.csv`, `data/processed/class_report.csv`, `data/processed/misclassified.csv`, `data/processed/success_check.csv`, `figures/evaluation_confusion_matrix.png` |
| `06_deployment.ipynb` | `models/svm.joblib`, `data/interim/success_criteria.json` | `data/processed/sample_predictions.csv` |

El reporte está en [`crisp_dm/report/resultados.md`](crisp_dm/report/resultados.md).

## Ejecución

Requiere Python 3.13.

```bash
pip install -r requirements.txt
```

Los notebooks de cada metodología se ejecutan en orden, desde su propia carpeta `notebooks/`. Por ejemplo, para SEMMA:

| Orden | Notebook |
| --- | --- |
| 1 | `semma/notebooks/01_sample.ipynb` |
| 2 | `semma/notebooks/02_explore.ipynb` |
| 3 | `semma/notebooks/03_modify.ipynb` |
| 4 | `semma/notebooks/04_model.ipynb` |
| 5 | `semma/notebooks/05_assess.ipynb` |

## Uso de inteligencia artificial

El código y la redacción de los documentos se generaron con apoyo de un asistente de inteligencia artificial. El autor definió el planteamiento, tomó las decisiones del proyecto y revisó y validó todo el contenido.

## Referencias

Chapman, P., Clinton, J., Kerber, R., Khabaza, T., Reinartz, T., Shearer, C. y Wirth, R. (2000). *CRISP-DM 1.0: Step-by-step data mining guide*. SPSS Inc.

Fayyad, U., Piatetsky-Shapiro, G. y Smyth, P. (1996). From data mining to knowledge discovery in databases. *AI Magazine*, *17*(3), 37-54. <https://doi.org/10.1609/aimag.v17i3.1230>

Gorman, K. B., Williams, T. D. y Fraser, W. R. (2014). Ecological sexual dimorphism and environmental variability within a community of Antarctic penguins (genus *Pygoscelis*). *PLoS ONE*, *9*(3), e90081. <https://doi.org/10.1371/journal.pone.0090081>

SAS Institute Inc. (2014). *Data mining using SAS Enterprise Miner: A case study approach* (3.ª ed.). SAS Institute Inc. <https://support.sas.com/documentation/cdl/en/emcs/66392/HTML/default/n0pejm83csbja4n1xueveo2uoujy.htm>

Conjuntos de datos originales (Environmental Data Initiative):

- Adelie: <https://doi.org/10.6073/pasta/98b16d7d563f265cb52372c8ca99e60f>
- Gentoo: <https://doi.org/10.6073/pasta/7fca67fb28d56ee2ffa3d9370ebda689>
- Chinstrap: <https://doi.org/10.6073/pasta/c14dfcfada8ea13a17536e73eb6fbe9e>
