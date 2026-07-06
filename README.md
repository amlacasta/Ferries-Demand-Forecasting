# LevanteFerries — Forecasting de Demanda y Torre de Control Operativa

![Python](https://img.shields.io/badge/Python-3.x-blue)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-lightgrey)
![XGBoost](https://img.shields.io/badge/Model-XGBoost-orange)
![Forecasting](https://img.shields.io/badge/Use%20Case-Demand%20Forecasting-green)
![Status](https://img.shields.io/badge/Status-Portfolio%20Project-brightgreen)

## 1. Resumen ejecutivo

Este proyecto desarrolla un sistema de **forecasting de demanda por salida** para una naviera ficticia llamada **LevanteFerries**, inspirada en operaciones reales de transporte marítimo de pasajeros entre el Levante peninsular y Baleares.

El objetivo es anticipar la demanda futura de pasajeros a nivel operativo, es decir:

**ruta + fecha + hora + capacidad del buque**

El proyecto convierte un problema clásico de operaciones —variabilidad de demanda, saturación e infrautilización— en una solución analítica capaz de:

- predecir pasajeros por salida,
- detectar riesgo de saturación,
- identificar salidas con sobrecapacidad,
- activar decisiones comerciales y operativas,
- generar predicciones futuras,
- y visualizar los resultados en una torre de control interactiva.

El modelo final, basado en **XGBoost**, mejora de forma clara el rendimiento frente a un baseline operativo explicable.

| Modelo | MAE | WAPE |
|---|---:|---:|
| Baseline operativo | 90.33 pax/salida | 12.93% |
| XGBoost ML | 66.50 pax/salida | 9.52% |

La mejora supone una reducción aproximada del **26% del error relativo**, permitiendo tomar decisiones más fiables sobre capacidad, pricing, campañas y planificación operativa.

---

## 2. Contexto de negocio

En el transporte marítimo de pasajeros, la demanda cambia de forma importante según:

- estacionalidad,
- fines de semana,
- festivos,
- puentes,
- eventos locales,
- precio,
- capacidad disponible,
- condiciones meteorológicas,
- y comportamiento histórico de reservas.

Esta variabilidad genera dos riesgos principales.

### 2.1 Saturación y pérdida de ingresos

Cuando la demanda prevista supera la capacidad disponible, la empresa puede perder ingresos por no disponer de plazas suficientes.

Ejemplos de decisiones asociadas:

- cambio de buque,
- refuerzo de frecuencia,
- ajuste de inventario,
- revisión de pricing,
- preparación de operativa portuaria.

### 2.2 Infrautilización y coste por plaza vacía

Cuando una salida tiene baja ocupación, aumenta el coste unitario por pasajero y se reduce la eficiencia operativa.

Ejemplos de decisiones asociadas:

- campañas tácticas,
- bundles coche + pasajero,
- promociones por ruta,
- acciones específicas en periodos valle,
- revisión de frecuencia o capacidad asignada.

---

## 3. Objetivo del proyecto

Construir un sistema de forecasting de demanda por salida para habilitar una **torre de control operativa** que permita:

- anticipar picos de demanda,
- detectar riesgo de saturación,
- identificar salidas con sobrecapacidad,
- comparar un baseline operativo con un modelo de machine learning,
- generar predicciones futuras,
- y transformar predicciones en acciones recomendadas.

---

## 4. Preguntas de negocio

El proyecto busca responder a preguntas como:

- ¿Cuántos pasajeros se esperan en cada salida futura?
- ¿Qué rutas presentan mayor presión de capacidad?
- ¿Qué salidas tienen riesgo de saturación?
- ¿Qué salidas presentan baja ocupación y pueden beneficiarse de campañas?
- ¿Qué modelo predice mejor la demanda: una regla operativa simple o un modelo ML?
- ¿Cómo convertir una predicción en una decisión operativa accionable?

---

## 5. Dataset

Por motivos de confidencialidad, se utiliza un dataset **sintético pero realista**, diseñado para simular la operación de una naviera de ferries en rutas Levante–Baleares.

### 5.1 Unidad de análisis

La unidad principal es la salida:

**1 fila = 1 salida de ferry**

Cada salida contiene información sobre ruta, fecha, hora, capacidad, demanda real, precio, ocupación y variables operativas.

### 5.2 Periodo simulado

```text
2024-01-01 → 2025-12-31
```

### 5.3 Volumen de datos

| Tabla | Descripción | Registros |
|---|---|---:|
| `trips_df` | Salidas a nivel trip-level | 8.948 |
| `bookings_df` | Curva diaria de reservas | 319.231 |

### 5.4 Rutas simuladas

| Ruta | Descripción |
|---|---|
| DEN–IBZ | Denia → Ibiza |
| DEN–FOR | Denia → Formentera |
| DEN–PMI | Denia → Palma |
| VAL–IBZ | Valencia → Ibiza |

### 5.5 Variables principales

| Categoría | Variables |
|---|---|
| Identificación | `trip_id`, `route_id`, `origin_port`, `dest_port`, `departure_datetime_local` |
| Calendario | `date`, `dep_time`, `weekday`, `month`, `year` |
| Capacidad | `ship_type`, `capacity_pax`, `capacity_veh` |
| Demanda real | `pax_real`, `veh_real`, `occupancy_real_pax`, `occupancy_real_veh` |
| Precio e ingresos | `avg_ticket_price`, `price_index`, `revenue_real` |
| Operación | `delay_minutes`, `cancel_rate`, `no_show_rate` |
| Variables externas proxy | `is_holiday_proxy`, `sea_bad_proxy` |
| Features temporales | `pax_lag_1w`, `pax_lag_2w`, `pax_roll_mean_20`, `pax_roll_std_20` |

---

## 6. Estructura del repositorio

```text
Ferries-Demand-Forecasting/
│
├── notebooks/
│   ├── 01_generate_synthetic_data.ipynb
│   ├── 02_data_prep_feature_store.ipynb
│   ├── 03_eda_demand_patterns.ipynb
│   ├── 04_baseline_forecast.ipynb
│   ├── 05_ml_forecast.ipynb
│   ├── 06_backtesting_control_tower.ipynb
│   └── 07_future_forecast_generation.ipynb
│
├── data/
│   ├── raw/
│   └── processed/
│
├── reports/
│   ├── figures/
│   └── model_cards/
│
├── dashboard/
│   └── dashboard_levanteferries_v2.html
│
├── README.md
├── requirements.txt
└── .gitignore
```

---

## 7. Metodología

El proyecto se desarrolla en siete notebooks diferenciados, siguiendo un flujo reproducible y orientado a negocio.

### 7.1 Notebook 01 — Generación de datos sintéticos

**Archivo:** `01_generate_synthetic_data.ipynb`

Objetivo:

- generar un dataset sintético realista,
- simular salidas por ruta y fecha,
- crear demanda de pasajeros y vehículos,
- incorporar precio, capacidad, retrasos, mala mar y festivos proxy,
- crear una tabla adicional de reservas diarias.

Outputs principales:

```text
data/processed/trips.parquet
data/processed/bookings.parquet
```

---

### 7.2 Notebook 02 — Feature Store

**Archivo:** `02_data_prep_feature_store.ipynb`

Objetivo:

- limpiar y ordenar los datos,
- validar calidad básica,
- crear lags y rolling features sin fuga de información,
- preparar el dataset final para forecasting.

Features creadas:

- `pax_lag_1w`
- `pax_lag_2w`
- `veh_lag_1w`
- `veh_lag_2w`
- `pax_roll_mean_20`
- `pax_roll_std_20`
- `delay_roll_mean_20`
- `occ_pax_roll_mean_20`

Output principal:

```text
data/processed/trip_feature_store.parquet
```

---

### 7.3 Notebook 03 — Análisis exploratorio

**Archivo:** `03_eda_demand_patterns.ipynb`

Objetivo:

- analizar patrones de demanda,
- detectar rutas de mayor presión,
- estudiar estacionalidad,
- revisar ocupación,
- observar diferencias por ruta,
- generar visualizaciones para el análisis de negocio.

Outputs principales:

```text
reports/figures/
reports/route_kpis_summary.csv
reports/sea_effect_by_route.csv
```

---

### 7.4 Notebook 04 — Baseline Forecast

**Archivo:** `04_baseline_forecast.ipynb`

Objetivo:

Construir un baseline operativo explicable basado en:

**demanda del mismo slot del año anterior + ajuste por tendencia reciente**

El slot se define como:

```text
ruta + día de la semana + hora de salida
```

Este baseline representa una aproximación razonable que podría usar un equipo operativo antes de aplicar machine learning.

Outputs principales:

```text
data/processed/baseline_predictions.parquet
reports/baseline_per_route_metrics.csv
```

---

### 7.5 Notebook 05 — Modelo ML con XGBoost

**Archivo:** `05_ml_forecast.ipynb`

Objetivo:

Entrenar un modelo de machine learning para mejorar la predicción de demanda.

Modelo utilizado:

```text
XGBoost Regressor
```

Features utilizadas:

- calendario,
- capacidad,
- precio,
- festivo proxy,
- mala mar proxy,
- lags de demanda,
- medias móviles,
- retraso medio reciente.

Outputs principales:

```text
data/processed/ml_predictions.parquet
reports/ml_per_route_metrics.csv
reports/ml_feature_importances.csv
```

---

### 7.6 Notebook 06 — Backtesting y Torre de Control

**Archivo:** `06_backtesting_control_tower.ipynb`

Objetivo:

- comparar baseline vs modelo ML,
- medir rendimiento global y por ruta,
- generar una tabla de control operativo,
- asignar semáforos de riesgo,
- proponer acciones operativas.

Outputs principales:

```text
data/processed/control_tower_table.csv
data/processed/control_tower_table.parquet
reports/compare_baseline_vs_ml_global.csv
reports/compare_baseline_vs_ml_per_route.csv
reports/control_tower_flag_summary.csv
```

---

### 7.7 Notebook 07 — Predicción futura

**Archivo:** `07_future_forecast_generation.ipynb`

Objetivo:

Pasar de la validación histórica a un escenario de uso futuro.

Este notebook:

- entrena el modelo final con todo el histórico disponible,
- genera un calendario futuro de salidas,
- construye features futuras,
- calcula predicciones por salida,
- estima ocupación futura,
- asigna semáforos,
- y exporta un archivo listo para explorar en el dashboard.

Outputs principales:

```text
data/processed/future_forecast_30d.csv
data/processed/future_forecast_30d.parquet
```

---

## 8. Análisis exploratorio de datos

El análisis exploratorio permitió identificar diferencias claras entre rutas.

### 8.1 Demanda media por ruta

| Ruta | Pax medios por salida |
|---|---:|
| DEN–IBZ | 758.55 |
| DEN–FOR | 665.84 |
| DEN–PMI | 638.82 |
| VAL–IBZ | 517.42 |

### 8.2 Interpretación

- **DEN–IBZ** es la ruta con mayor demanda media.
- **DEN–FOR** también presenta una demanda elevada.
- **DEN–PMI** tiene una demanda relevante, pero menor presión relativa de capacidad.
- **VAL–IBZ** es la ruta con menor demanda media, candidata a acciones de estímulo en periodos valle.

### 8.3 Riesgo de saturación

| Ruta | Ocupación p90 |
|---|---:|
| DEN–IBZ | 1.00 |
| DEN–FOR | 0.929 |
| DEN–PMI | 0.729 |
| VAL–IBZ | 0.582 |

### 8.4 Interpretación operativa

- **DEN–IBZ** muestra saturación recurrente.
- **DEN–FOR** se aproxima al umbral de riesgo.
- **DEN–PMI** y **VAL–IBZ** presentan mayor margen de capacidad.
- Las rutas con ocupación alta justifican el uso de forecasting para anticipar ventas limitadas por capacidad.

---

## 9. Modelos de forecasting

### 9.1 Baseline operativo

El baseline se basa en una lógica sencilla y explicable:

```text
Predicción = demanda del mismo slot del año anterior × ajuste de tendencia
```

Donde el slot es:

```text
ruta + día de la semana + hora
```

Además, la predicción se capa por la capacidad máxima del buque.

### Resultados del baseline

Periodo de test:

```text
desde 2025-07-01
```

Número de salidas evaluadas:

```text
n = 2.346
```

| Métrica | Valor |
|---|---:|
| MAE | 90.33 pax/salida |
| WAPE | 12.93% |

### Rendimiento por ruta

| Ruta | WAPE |
|---|---:|
| DEN–IBZ | 11.01% |
| DEN–FOR | 11.77% |
| VAL–IBZ | 14.98% |
| DEN–PMI | 15.75% |

El baseline establece un punto de comparación robusto. Cualquier modelo ML debe superar este rendimiento para justificar su uso.

---

### 9.2 Modelo ML — XGBoost

Se entrena un modelo **XGBoost Regressor** para capturar relaciones no lineales entre demanda, calendario, capacidad, precio y variables operativas.

### Variables utilizadas

| Tipo | Variables |
|---|---|
| Calendario | `weekday`, `month`, `is_holiday_proxy` |
| Capacidad | `capacity_pax` |
| Precio | `avg_ticket_price`, `price_index` |
| Condición externa | `sea_bad_proxy` |
| Histórico de demanda | `pax_lag_1w`, `pax_lag_2w`, `pax_roll_mean_20`, `pax_roll_std_20` |
| Operación | `delay_roll_mean_20` |

### Resultados del modelo ML

| Modelo | MAE | WAPE |
|---|---:|---:|
| Baseline | 90.33 | 12.93% |
| XGBoost | 66.50 | 9.52% |

### Mejora frente al baseline

| Métrica | Mejora |
|---|---:|
| WAPE | -3.41 puntos porcentuales |
| Error relativo | -26% aprox. |
| MAE | -23.83 pax/salida |

### Mejora por ruta

| Ruta | WAPE Baseline | WAPE ML |
|---|---:|---:|
| DEN–IBZ | 11.01% | 8.18% |
| DEN–FOR | 11.77% | 9.34% |
| VAL–IBZ | 14.98% | 10.51% |
| DEN–PMI | 15.75% | 10.98% |

### Interpretación

El modelo ML supera al baseline en todas las rutas. Las mayores mejoras aparecen en rutas con mayor variabilidad relativa, especialmente **DEN–PMI** y **VAL–IBZ**.

Esto confirma que el modelo no solo mejora la media global, sino que aporta valor en rutas donde una regla operativa simple resulta menos precisa.

---

## 10. Evaluación

La evaluación se realiza mediante **backtesting temporal**, evitando particiones aleatorias.

Esto es importante porque en forecasting el orden temporal debe respetarse:

```text
entrenar con pasado → predecir futuro
```

### Métricas utilizadas

| Métrica | Descripción |
|---|---|
| MAE | Error medio absoluto en pasajeros |
| WAPE | Error absoluto ponderado, útil para operaciones |
| WAPE por ruta | Permite detectar rutas más difíciles de predecir |

### Por qué WAPE

WAPE es especialmente útil en operaciones porque pondera el error según el volumen total de demanda. Esto evita que rutas pequeñas distorsionen la lectura global del rendimiento.

---

## 11. Torre de control

Una vez obtenidas las predicciones, se crea una tabla de control operativo.

La lógica principal es:

```text
ocupación prevista = pax_pred_ml / capacity_pax
```

A partir de esa ocupación prevista se asigna un semáforo.

### Semáforos

| Flag | Condición | Interpretación | Acción sugerida |
|---|---|---|---|
| ROJO | `occ_pred_ml >= 0.92` | Riesgo de saturación | Evaluar refuerzo, cambio de buque, inventario o pricing |
| ÁMBAR | `0.80 <= occ_pred_ml < 0.92` | Zona de vigilancia | Monitorizar curva de reservas y microajustes |
| VERDE | `occ_pred_ml < 0.60` | Sobrecapacidad | Activar promociones, bundles o campañas |
| NORMAL | Resto | Operación estable | Monitor estándar |

### Distribución observada en el set evaluado

| Flag | Proporción |
|---|---:|
| VERDE | 41.4% |
| NORMAL | 33.8% |
| ÁMBAR | 14.4% |
| ROJO | 10.4% |

### Interpretación operativa

- Aproximadamente 1 de cada 10 salidas presenta riesgo de saturación.
- Una parte relevante de salidas aparece en verde, lo que indica oportunidades comerciales para mejorar ocupación.
- Las salidas ámbar permiten actuar de forma preventiva antes de llegar a saturación.

---

## 12. Predicción futura

El Notebook 07 convierte el modelo validado en un flujo de predicción futura.

### Flujo

1. Entrenar el modelo final con todo el histórico disponible.
2. Generar un calendario futuro de salidas.
3. Crear features futuras:
   - ruta,
   - fecha,
   - hora,
   - día de semana,
   - mes,
   - capacidad planificada,
   - precio esperado,
   - proxies de festivo y mala mar,
   - lags y rolling features desde histórico.
4. Predecir pasajeros esperados.
5. Calcular ocupación prevista.
6. Asignar semáforo operativo.
7. Exportar resultados para dashboard.

### Output futuro

```text
future_forecast_30d.csv
```

Columnas principales:

| Columna | Descripción |
|---|---|
| `route_id` | Ruta |
| `departure_datetime_local` | Fecha y hora de salida |
| `capacity_pax` | Capacidad planificada |
| `pax_pred_ml` | Pasajeros previstos |
| `occ_pred_ml` | Ocupación prevista |
| `flag` | Semáforo operativo |
| `action` | Acción sugerida |

### Resultado del forecast futuro

Distribución de flags en el forecast futuro generado:

| Flag | Salidas |
|---|---:|
| VERDE | 232 |
| NORMAL | 102 |
| ÁMBAR | 33 |
| ROJO | 5 |

### Interpretación

- **VERDE:** oportunidad de estimular demanda.
- **NORMAL:** operación estable.
- **ÁMBAR:** salidas a vigilar.
- **ROJO:** salidas críticas que requieren revisión operativa.

Este paso transforma el proyecto en una herramienta de soporte a la decisión.

---

## 13. Dashboard HTML interactivo

Se ha desarrollado un dashboard en HTML con **Plotly.js**, pensado para explorar los datos de forma sencilla sin necesidad de servidor.

### Archivos compatibles

El dashboard permite subir archivos CSV como:

```text
control_tower_table.csv
trip_feature_store.csv
future_forecast_30d.csv
```

### Funcionalidades

- carga manual de CSV,
- filtros por ruta,
- filtros por flag,
- filtros por rango de fechas,
- selector de métrica,
- KPIs principales,
- distribución de flags,
- demanda por ruta,
- serie temporal real vs predicción,
- histograma de ocupación,
- heatmap mes × día de semana,
- ranking de salidas con mayor riesgo,
- exportación del CSV filtrado.

### Uso

1. Abrir el archivo HTML en navegador.
2. Subir uno de los CSV generados.
3. Filtrar por ruta, fecha o flag.
4. Analizar patrones e identificar oportunidades operativas.

---

## 14. Cómo reproducir el proyecto

### 14.1 Requisitos

Instalar dependencias:

```bash
pip install -r requirements.txt
```

Dependencias principales:

```text
pandas
numpy
matplotlib
scikit-learn
xgboost
plotly
pyarrow
```

### 14.2 Orden de ejecución

Ejecutar los notebooks en este orden:

```text
01_generate_synthetic_data.ipynb
02_data_prep_feature_store.ipynb
03_eda_demand_patterns.ipynb
04_baseline_forecast.ipynb
05_ml_forecast.ipynb
06_backtesting_control_tower.ipynb
07_future_forecast_generation.ipynb
```

### 14.3 Outputs principales

| Archivo | Descripción |
|---|---|
| `trips.parquet` | Salidas sintéticas |
| `bookings.parquet` | Curva de reservas |
| `trip_feature_store.parquet` | Dataset model-ready |
| `baseline_predictions.parquet` | Predicciones baseline |
| `ml_predictions.parquet` | Predicciones ML |
| `control_tower_table.csv` | Tabla histórica para dashboard |
| `future_forecast_30d.csv` | Predicciones futuras |
| `compare_baseline_vs_ml_global.csv` | Comparativa global |
| `control_tower_flag_summary.csv` | Resumen de flags |

---

## 15. Impacto de negocio

Este proyecto demuestra cómo un sistema de forecasting puede ayudar a una naviera a tomar mejores decisiones.

### 15.1 Capacidad

Permite detectar salidas con alta ocupación prevista y revisar:

- buque asignado,
- frecuencia,
- inventario,
- refuerzo operativo,
- preparación portuaria.

### 15.2 Pricing

Permite ajustar precios según presión de demanda:

- mayor presión → revisión de pricing,
- baja ocupación → promociones tácticas.

### 15.3 Marketing y ventas

Permite activar acciones sobre salidas verdes:

- campañas por ruta,
- bundles,
- residentes,
- ida/vuelta,
- coche + pasajero.

### 15.4 Operaciones

Permite anticipar:

- colas,
- carga operativa,
- necesidades de personal,
- ventanas de mayor presión.

### 15.5 Control de gestión

Permite monitorizar:

- error del modelo,
- rutas más difíciles de predecir,
- evolución de ocupación,
- riesgo de saturación,
- oportunidades de mejora de eficiencia.

---

## 16. Limitaciones

El proyecto utiliza datos sintéticos, por lo que sus resultados no deben interpretarse como resultados reales de ninguna compañía.

Limitaciones principales:

- festivos simulados mediante proxy,
- mala mar simulada,
- precios sintéticos,
- demanda generada artificialmente,
- no se incluyen datos reales de competencia,
- no se incluyen eventos reales,
- no se incluyen restricciones operativas reales de flota.

---

## 17. Próximos pasos con datos reales

Con datos reales, el sistema podría evolucionar incorporando:

- calendario real de festivos nacionales, autonómicos y locales,
- meteorología marítima real,
- histórico real de reservas,
- curva real de booking,
- cancelaciones y no-show reales,
- segmentación por tipo de cliente,
- pricing real por tarifa,
- eventos en destino,
- restricciones de flota,
- optimización de capacidad,
- intervalos de predicción P50/P90,
- integración con Power BI o Streamlit.

---

## 18. Conclusión

Este proyecto muestra un flujo completo de analítica aplicada al transporte marítimo:

```text
datos → exploración → baseline → modelo ML → backtesting → torre de control → predicción futura → dashboard
```

La solución permite pasar de una visión reactiva de la demanda a una gestión más anticipativa y accionable.

El valor principal no está solo en predecir pasajeros, sino en transformar cada predicción en una decisión operativa:

- reforzar,
- vigilar,
- promocionar,
- o mantener operación estándar.

Esto convierte el forecasting en una herramienta real de control efectivo de la demanda.
