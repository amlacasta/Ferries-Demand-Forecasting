# LevanteFerries — Forecasting de Demanda y Torre de Control Operativa

![Python](https://img.shields.io/badge/Python-3.x-blue)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-lightgrey)
![XGBoost](https://img.shields.io/badge/Model-XGBoost-orange)
![Forecasting](https://img.shields.io/badge/Use%20Case-Demand%20Forecasting-green)
![Dashboard](https://img.shields.io/badge/Dashboard-GitHub%20Pages-brightgreen)
![Status](https://img.shields.io/badge/Status-Portfolio%20Project-success)

## Dashboard interactivo

El proyecto incluye una torre de control HTML publicada con GitHub Pages:

[**Abrir dashboard LevanteFerries**](https://amlacasta.github.io/Ferries-Demand-Forecasting/dashboard/dashboard_levanteferries_v2.html)

El dashboard permite cargar archivos CSV generados por el proyecto, filtrar por ruta, fecha y semáforo operativo, visualizar KPIs, analizar patrones de demanda y detectar salidas con riesgo de saturación o baja ocupación.

Archivos recomendados para cargar en el dashboard:

```text
control_tower_table.csv
future_forecast_30d.csv
trip_feature_store.csv
```

---

## 1. Resumen ejecutivo

Este proyecto desarrolla un sistema de **forecasting de demanda por salida** para una naviera ficticia llamada **LevanteFerries**, inspirada en operaciones reales de transporte marítimo de pasajeros entre el Levante peninsular y Baleares.

El objetivo es anticipar la demanda futura de pasajeros a nivel operativo:

```text
ruta + fecha + hora + capacidad del buque
```

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

```text
1 fila = 1 salida de ferry
```

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

| Notebook | Objetivo | Output principal |
|---|---|---|
| `01_generate_synthetic_data.ipynb` | Generación de datos sintéticos realistas | `trips.parquet`, `bookings.parquet` |
| `02_data_prep_feature_store.ipynb` | Limpieza, validación y creación de features temporales | `trip_feature_store.parquet` |
| `03_eda_demand_patterns.ipynb` | Análisis exploratorio de demanda, ocupación y rutas | `route_kpis_summary.csv`, figuras |
| `04_baseline_forecast.ipynb` | Baseline operativo explicable | `baseline_predictions.parquet` |
| `05_ml_forecast.ipynb` | Modelo ML con XGBoost | `ml_predictions.parquet` |
| `06_backtesting_control_tower.ipynb` | Comparativa, backtesting y torre de control | `control_tower_table.csv` |
| `07_future_forecast_generation.ipynb` | Predicción futura a 30 días | `future_forecast_30d.csv` |

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

### 8.2 Riesgo de saturación

| Ruta | Ocupación p90 |
|---|---:|
| DEN–IBZ | 1.00 |
| DEN–FOR | 0.929 |
| DEN–PMI | 0.729 |
| VAL–IBZ | 0.582 |

### 8.3 Interpretación operativa

- **DEN–IBZ** es la ruta con mayor demanda media y muestra saturación recurrente.
- **DEN–FOR** también presenta alta demanda y se aproxima al umbral de riesgo.
- **DEN–PMI** mantiene una demanda relevante con menor presión relativa de capacidad.
- **VAL–IBZ** es la ruta con menor demanda media, candidata a acciones de estímulo en periodos valle.

---

## 9. Modelos de forecasting

### 9.1 Baseline operativo

El baseline se basa en una lógica sencilla y explicable:

```text
Predicción = demanda del mismo slot del año anterior × ajuste de tendencia
```

Donde el slot se define como:

```text
ruta + día de la semana + hora de salida
```

Este baseline representa una aproximación razonable que podría usar un equipo operativo antes de aplicar machine learning.

### 9.2 Modelo ML — XGBoost

Se entrena un modelo **XGBoost Regressor** para capturar relaciones no lineales entre demanda, calendario, capacidad, precio y variables operativas.

Variables utilizadas:

| Tipo | Variables |
|---|---|
| Calendario | `weekday`, `month`, `is_holiday_proxy` |
| Capacidad | `capacity_pax` |
| Precio | `avg_ticket_price`, `price_index` |
| Condición externa | `sea_bad_proxy` |
| Histórico de demanda | `pax_lag_1w`, `pax_lag_2w`, `pax_roll_mean_20`, `pax_roll_std_20` |
| Operación | `delay_roll_mean_20` |

---

## 10. Evaluación

La evaluación se realiza mediante **backtesting temporal**, evitando particiones aleatorias.

Esto es importante porque en forecasting el orden temporal debe respetarse:

```text
entrenar con pasado → predecir futuro
```

### 10.1 Resultados globales

| Modelo | MAE | WAPE |
|---|---:|---:|
| Baseline | 90.33 | 12.93% |
| XGBoost | 66.50 | 9.52% |

### 10.2 Mejora frente al baseline

| Métrica | Mejora |
|---|---:|
| WAPE | -3.41 puntos porcentuales |
| Error relativo | -26% aprox. |
| MAE | -23.83 pax/salida |

### 10.3 Mejora por ruta

| Ruta | WAPE Baseline | WAPE ML |
|---|---:|---:|
| DEN–IBZ | 11.01% | 8.18% |
| DEN–FOR | 11.77% | 9.34% |
| VAL–IBZ | 14.98% | 10.51% |
| DEN–PMI | 15.75% | 10.98% |

El modelo ML supera al baseline en todas las rutas. Las mayores mejoras aparecen en rutas con mayor variabilidad relativa, especialmente **DEN–PMI** y **VAL–IBZ**.

---

## 11. Torre de control

Una vez obtenidas las predicciones, se crea una tabla de control operativo.

La lógica principal es:

```text
ocupación prevista = pax_pred_ml / capacity_pax
```

A partir de esa ocupación prevista se asigna un semáforo.

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

---

## 12. Predicción futura

El Notebook 07 convierte el modelo validado en un flujo de predicción futura.

Flujo principal:

1. Entrenar el modelo final con todo el histórico disponible.
2. Generar un calendario futuro de salidas.
3. Crear features futuras.
4. Predecir pasajeros esperados.
5. Calcular ocupación prevista.
6. Asignar semáforo operativo.
7. Exportar resultados para dashboard.

Output principal:

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

| Flag | Salidas |
|---|---:|
| VERDE | 232 |
| NORMAL | 102 |
| ÁMBAR | 33 |
| ROJO | 5 |

Este paso transforma el proyecto en una herramienta de soporte a la decisión.

---

## 13. Dashboard HTML interactivo

El dashboard está desarrollado en HTML con **Plotly.js** y está publicado en GitHub Pages.

[**Abrir dashboard LevanteFerries**](https://amlacasta.github.io/Ferries-Demand-Forecasting/dashboard/dashboard_levanteferries_v2.html)

Funcionalidades principales:

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

El dashboard permite explorar tanto la torre de control histórica como las predicciones futuras generadas por el modelo.

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

### Capacidad

Permite detectar salidas con alta ocupación prevista y revisar:

- buque asignado,
- frecuencia,
- inventario,
- refuerzo operativo,
- preparación portuaria.

### Pricing

Permite ajustar precios según presión de demanda:

- mayor presión → revisión de pricing,
- baja ocupación → promociones tácticas.

### Marketing y ventas

Permite activar acciones sobre salidas verdes:

- campañas por ruta,
- bundles,
- residentes,
- ida/vuelta,
- coche + pasajero.

### Operaciones

Permite anticipar:

- colas,
- carga operativa,
- necesidades de personal,
- ventanas de mayor presión.

### Control de gestión

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
