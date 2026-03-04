1) Título

Forecasting de Demanda por Salida (Trip-Level) para Control Operativo en una Naviera de Ferries — LevanteFerries

2) Contexto y problema

En transporte marítimo de pasajeros, la demanda es altamente variable por:

estacionalidad (verano vs invierno),

día de semana (fines de semana),

eventos/festivos,

y condiciones operativas (p.ej. mala mar, retrasos).

Esta variabilidad genera dos riesgos operativos:

Saturación / pérdida de ingresos: ventas limitadas por capacidad cuando la demanda supera el inventario disponible.

Infrautilización / coste por plaza vacía: salidas con baja ocupación aumentan el coste unitario y erosionan el margen.

3) Objetivo del proyecto

Construir un sistema de forecasting de demanda por salida (ruta–fecha–hora–tipo de buque) para habilitar una “torre de control” que permita:

anticipar picos de demanda y riesgo de saturación,

detectar salidas con sobrecapacidad,

y activar decisiones operativas (capacidad/pricing/promos) basadas en predicciones.

4) Preguntas clave (control efectivo de la demanda)

¿Cuántos pasajeros (y vehículos) habrá en cada salida futura por ruta y franja horaria?

¿Qué rutas presentan mayor riesgo de saturación (ocupación > 92%)?

¿En qué rutas/salidas hay margen para activar campañas o ajustar precios?

¿Cuánto mejora un modelo ML frente a un baseline operativo explicable?

5) Dataset (sintético, realista)

Por confidencialidad, se utiliza un dataset inventado pero realista basado en una naviera tipo Levante–Baleares:

Unidad de análisis: 1 fila = 1 salida (trip-level)

Periodo: 2024-01-01 → 2025-12-31

Volumen:

trips: 8,948 salidas

bookings: 319,231 registros (curva de reservas diaria)

Variables principales (trip-level)

Identificación: trip_id, route_id, departure_datetime_local, weekday, month, ship_type

Capacidad: capacity_pax, capacity_veh

Demanda real: pax_real, veh_real, occupancy_real_pax, occupancy_real_veh

Precio e ingresos: avg_ticket_price, price_index, revenue_real

Operación: delay_minutes

Externas proxy: is_holiday_proxy, sea_bad_proxy

Curva de reserva: bookings_df con days_to_departure, cum_net_bookings

6) Estructura del repo
notebooks/
  01_generate_synthetic_data.ipynb
  02_data_prep_feature_store.ipynb
  03_eda_demand_patterns.ipynb
  04_baseline_forecast.ipynb
  05_ml_forecast.ipynb
  06_backtesting_control_tower.ipynb

data/
  raw/
  processed/   (parquets generados por notebooks)

reports/
  figures/
  model_cards/
7) Metodología (pipeline)

Generación de datos (sintéticos, estilo naviera)

Feature Store (lags + rolling sin fuga de información)

EDA (patrones de demanda, saturación, estacionalidad)

Baseline (operativo y explicable)

ML Forecast (mejora de precisión con features)

Backtesting + Torre de Control (alertas + playbook)

8) Hallazgos EDA (resultados actuales)
Demanda media por ruta (pax/Salida)

Orden observado en el dataset:

DEN–IBZ ≈ 758.55

DEN–FOR ≈ 665.84

DEN–PMI ≈ 638.82

VAL–IBZ ≈ 517.42

Interpretación operativa:

DEN–IBZ y DEN–FOR son rutas “core” con demanda alta (mayor presión de capacidad).

VAL–IBZ es más débil → candidata a estrategias de estímulo en valle.

Riesgo de saturación (ocupación p90)

DEN–IBZ: occ_p90 = 1.00 → saturación recurrente (capacidad llega al límite)

DEN–FOR: occ_p90 ≈ 0.929 → vigilancia (cerca del umbral)

DEN–PMI: occ_p90 ≈ 0.729

VAL–IBZ: occ_p90 ≈ 0.582

Por qué esto importa:
Estas rutas justifican el valor del forecasting: donde el p90 se acerca a 1.0, pequeñas desviaciones del forecast pueden implicar ventas perdidas o necesidad de refuerzo.

(Las tablas detalladas se guardan en reports/route_kpis_summary.csv y figuras en reports/figures/.)

9) Modelos
9.1 Baseline (operativo)

LY same-slot + trend adjust:

Busca la demanda del “mismo slot” del año anterior (ruta + weekday + hora).

Ajusta por tendencia usando rolling mean reciente vs rolling mean LY.

Aplica cap por capacidad (imprescindible en transporte).

Métricas baseline: (pendiente de Notebook 04)

9.2 Modelo ML (feature-based)

Modelo tipo árbol (Random Forest / Gradient Boosting) con:

lags (1w, 2w)

rolling mean/std

calendario (weekday/mes/festivo)

operación (retrasos rolling)

precio (price_index)

meteo proxy (sea_bad_proxy)

Métricas ML: (pendiente de Notebook 05)

10) Evaluación

Se utiliza backtesting temporal (sin shuffle) y métricas:

MAE (error medio absoluto en pax)

WAPE (error ponderado, útil en operaciones)

análisis por ruta y por temporada

11) Torre de control (decisiones accionables)

Semáforo basado en ocupación forecast:

ROJO: occ_pred ≥ 0.92 → riesgo de saturación
Acción: evaluar refuerzo/cambio buque + ajustar inventario/precio

ÁMBAR: 0.80–0.92 → vigilar curva de reserva
Acción: microajustes + seguimiento

VERDE: < 0.60 → sobrecapacidad
Acción: promo táctica / bundles / campañas

Tabla final para BI: (pendiente de Notebook 06)

12) Cómo reproducir

Ejecutar notebooks en orden 01 → 06

Los outputs se guardan en data/processed y reports/

13) Próximos pasos (cuando haya datos reales)

Sustituir proxies por:

festivos reales por región,

meteo marítima real (oleaje/viento),

histórico de cancelaciones reales,

pricing real por segmento.

Implementar intervalos P50/P90 para decisiones con riesgo.# Ferries-Demand-Forecasting
