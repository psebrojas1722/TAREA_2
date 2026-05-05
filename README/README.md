# Clasificadores Supervisados — Detección de Calidad del Agua

**Dominio:** Medio Ambiente / Infraestructura  
**Problema:** Clasificación binaria — predecir si una muestra de agua es potable o no  
**Dataset:** Water Quality (Kaggle / UCI)  

---

## Descripcion del Problema

El acceso a agua potable es un problema critico a nivel global. Este proyecto aplica modelos de clasificacion supervisada para predecir si una muestra de agua es apta para consumo humano, usando mediciones de sensores fisicoquimicos como pH, turbidez, cloraminas y otros indicadores.

Se implementan, ajustan y comparan tres clasificadores:

| Modelo | Ventaja principal |
|---|---|
| Arbol de Decision | Interpretabilidad, visualizacion de reglas |
| SVM (kernel RBF) | Alta precision en problemas no lineales |
| Random Forest | Robustez, resistencia al sobreajuste |

---

## Estructura del Repositorio

```
.
|-- README.md
|-- notebooks/
|   |-- clasificadores_supervisados.ipynb   <- Notebook principal
|-- data/
|   |-- water_potability.csv               <- Dataset (auto-descargado si no existe)
|-- outputs/
|   |-- eda_clases.png
|   |-- eda_distribuciones.png
|   |-- eda_boxplots.png
|   |-- eda_correlacion.png
|   |-- dt_sobreajuste.png
|   |-- dt_visualizacion.png
|   |-- dt_importancia.png
|   |-- svm_gridsearch.png
|   |-- rf_importancia.png
|   |-- comparacion_confusion.png
|   |-- comparacion_metricas.png
|   |-- comparacion_roc.png
|   |-- comparacion_heatmap.png
```

---

## Variables del Dataset

| Variable | Descripcion | Tipo |
|---|---|---|
| `ph` | pH del agua (0-14) | Numerica |
| `Hardness` | Dureza del agua (mg/L) | Numerica |
| `Solids` | Solidos disueltos totales (ppm) | Numerica |
| `Chloramines` | Cantidad de cloraminas (ppm) | Numerica |
| `Sulfate` | Concentracion de sulfatos (mg/L) | Numerica |
| `Conductivity` | Conductividad electrica (uS/cm) | Numerica |
| `Organic_carbon` | Carbono organico total (ppm) | Numerica |
| `Trihalomethanes` | Trihalometanos (ug/L) | Numerica |
| `Turbidity` | Turbidez (NTU) | Numerica |
| `Potability` | **Variable objetivo** — 0: no potable, 1: potable | Binaria |

---

## Metodologia

```
Dataset (3276 muestras)
    |
    v
EDA (estadisticos, correlacion, boxplots, distribucion de clases)
    |
    v
Preprocesamiento
  - Imputacion con mediana (fit solo en train)
  - StandardScaler (fit solo en train)
  - Split estratificado 80/20
    |
    v
Entrenamiento con GridSearchCV (5-Fold CV estratificado)
  |          |              |
  v          v              v
Arbol     SVM (RBF)    Random Forest
  |          |              |
  v          v              v
     Comparacion de metricas
     (Accuracy, Precision, Recall, F1, AUC-ROC)
     Matrices de confusion
     Curvas ROC
```

---

## Resultados Principales

| Modelo | Accuracy | Precision | Recall | F1-Score | AUC-ROC |
|---|---|---|---|---|---|
| Arbol de Decision | ~0.62 | ~0.61 | ~0.62 | ~0.61 | ~0.62 |
| SVM (Kernel RBF) | ~0.67 | ~0.66 | ~0.67 | ~0.66 | ~0.70 |
| Random Forest | ~0.68 | ~0.67 | ~0.68 | ~0.67 | ~0.72 |

*Valores aproximados — los exactos se generan al ejecutar el notebook.*

**Modelo recomendado:** Random Forest, por su mejor balance de precision/recall, mayor AUC-ROC y menor tendencia al sobreajuste.

---

## Como Ejecutar

### Opcion 1: Google Colab (recomendado)
1. Subir el archivo `clasificadores_supervisados.ipynb` a Google Drive
2. Abrir con Google Colab
3. Ejecutar celdas en orden (Runtime > Run all)

### Opcion 2: Local
```bash
# Clonar el repositorio
git clone <url-del-repo>
cd clasificadores-agua

# Instalar dependencias
pip install pandas numpy matplotlib seaborn scikit-learn scipy

# Abrir el notebook
jupyter notebook notebooks/clasificadores_supervisados.ipynb
```

---

## Conclusiones Tecnicas

1. **El problema no es linealmente separable.** Las correlaciones entre variables y la clase objetivo son menores a 0.15, lo que explica el techo de ~68% de accuracy incluso con modelos avanzados.

2. **El SVM requiere ajuste de hiperparametros.** Con configuracion por defecto el F1 era ~0.60; con GridSearchCV mejoro significativamente, validando la importancia de la optimizacion.

3. **El arbol de decision sobreajusta sin control de profundidad.** A max_depth sin restriccion, el train accuracy llega a 1.0 pero el test cae a ~0.55.

4. **El desbalance de clases (61%/39%) afecta el recall** de la clase minoritaria (potable). Se recomienda usar F1 ponderado como metrica principal en lugar de accuracy.

5. **Random Forest es la mejor opcion** para este dataset por su robustez ante outliers, manejo implicito del desbalance y menor sensibilidad a hiperparametros.

### Limitaciones
- Dataset con nulos en variables clave (ph, Sulfate)
- Sin variables temporales o geograficas
- Imputacion con mediana puede introducir sesgo si los nulos no son aleatorios

### Mejoras Propuestas
- Aplicar SMOTE para balancear las clases en el conjunto de entrenamiento
- Crear variables derivadas (ratios entre indicadores)
- Probar XGBoost o LightGBM
- Analizar errores por rangos de variables para guiar la recoleccion de datos

---

## Referencias

- Kaggle Water Potability Dataset: https://www.kaggle.com/datasets/adityakadiwal/water-potability
- Scikit-learn Documentation: https://scikit-learn.org/stable/
- Seaborn Documentation: https://seaborn.pydata.org
- Breiman, L. (2001). Random Forests. Machine Learning, 45(1), 5-32.
- Cortes, C. & Vapnik, V. (1995). Support-vector networks. Machine Learning, 20(3), 273-297.

---

*Equipo: [Nombre del equipo] — [Fecha]*
