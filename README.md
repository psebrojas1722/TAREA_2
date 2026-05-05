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
|   |-- water_potability.csv               <- Dataset
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

Resultados obtenidos al ejecutar el notebook con el dataset real (water_potability.csv):

| Modelo | Accuracy | Precision | Recall | F1-Score | AUC-ROC |
|---|---|---|---|---|---|
| Arbol de Decision | 0.6037 | 0.5632 | 0.6037 | 0.4625 | **0.5159** |
| SVM (Kernel RBF) | 0.5381 | 0.5001 | 0.5381 | 0.5058 | 0.4866 |
| Random Forest | 0.5640 | 0.5161 | 0.5640 | **0.5129** | 0.4884 |

- **Mejor F1-Score:** Random Forest (0.5129)
- **Mejor AUC-ROC:** Arbol de Decision (0.5159)
- **Mejor Accuracy:** Arbol de Decision (0.6037)

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

1. **El problema es inherentemente dificil de separar.** Ningun modelo supero el 60% de accuracy, lo cual es consistente con las bajas correlaciones entre variables y la clase objetivo (menores a 0.15). El dataset tiene un limite natural de separabilidad con las variables disponibles.

2. **El Arbol de Decision obtuvo la mayor accuracy (0.6037) y AUC-ROC (0.5159)**, pero su F1-Score fue el mas bajo (0.4625), lo que indica que clasifica bien la clase mayoritaria (no potable) pero falla en detectar el agua potable.

3. **Random Forest obtuvo el mejor F1-Score (0.5129)**, siendo el modelo mas equilibrado entre precision y recall para ambas clases. Es la opcion recomendada para este problema.

4. **El SVM tuvo el peor desempeno general** en este dataset (Accuracy=0.5381, AUC-ROC=0.4866), lo que sugiere que la frontera de decision del kernel RBF no logra separar bien las clases con las variables disponibles. Incluso con GridSearchCV, el problema no tiene suficiente estructura para que SVM destaque.

5. **El desbalance de clases (61% no potable / 39% potable) explica el bajo F1** de todos los modelos en la clase minoritaria. Los modelos tienden a predecir "no potable" con mas frecuencia, penalizando el recall de la clase potable.

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

*Equipo: GRUPO 8 — 4/5/2026
