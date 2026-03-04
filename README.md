# Forecasting de Demanda por Salida (Trip-Level) para Control Operativo en una Naviera de Ferries — LevanteFerries

## 1. Contexto y problema
En el transporte marítimo de pasajeros, la demanda es altamente variable por:
* **Estacionalidad** (verano vs invierno).
* **Día de semana** (fines de semana).
* **Eventos/festivos**.
* **Condiciones operativas** (p.ej. mala mar, retrasos).

Esta variabilidad genera dos riesgos operativos críticos:
1.  **Saturación / pérdida de ingresos:** Ventas limitadas por capacidad cuando la demanda supera el inventario disponible.
2.  **Infrautilización / coste por plaza vacía:** Salidas con baja ocupación que aumentan el coste unitario y erosionan el margen.

---

## 2. Objetivo del proyecto
Construir un sistema de forecasting de demanda por salida (ruta–fecha–hora–tipo de buque) para habilitar una **“torre de control”** que permita:
* Anticipar picos de demanda y riesgo de saturación.
* Detectar salidas con sobrecapacidad.
* Activar decisiones operativas (capacidad/pricing/promos) basadas en predicciones.

---

## 3. Preguntas clave (Control efectivo de la demanda)
* ¿Cuántos pasajeros (y vehículos) habrá en cada salida futura por ruta y franja horaria?
* ¿Qué rutas presentan mayor riesgo de saturación (ocupación > 92%)?
* ¿En qué rutas/salidas hay margen para activar campañas o ajustar precios?
* ¿Cuánto mejora un modelo ML frente a un baseline operativo explicable?

---

## 4. Dataset (Sintético, realista)
Por confidencialidad, se utiliza un dataset inventado pero realista basado en una naviera tipo Levante–Baleares:

* **Unidad de análisis:** 1 fila = 1 salida (trip-level).
* **Periodo:** 2024-01-01 → 2025-12-31.
* **Volumen:** 8,948 salidas y 319,231 registros de reservas (curva de reservas diaria).

### Variables principales (trip-level)
| Categoría | Variables |
| :--- | :--- |
| **Identificación** | `trip_id`, `route_id`, `departure_datetime_local`, `weekday`, `month`, `ship_type` |
| **Capacidad** | `capacity_pax`, `capacity_veh` |
| **Demanda real** | `pax_real`, `veh_real`, `occupancy_real_pax`, `occupancy_real_veh` |
| **Precio/Ingresos** | `avg_ticket_price`, `price_index`, `revenue_real` |
| **Operación** | `delay_minutes` |
| **Externas proxy** | `is_holiday_proxy`, `sea_bad_proxy` |
| **Curva reserva** | `bookings_df` con `days_to_departure`, `cum_net_bookings` |

---

## 5. Estructura del repositorio
* **notebooks/**
    * `01_generate_synthetic_data.ipynb`
    * `02_data_prep_feature_store.ipynb`
    * `03_eda_demand_patterns.ipynb`
    * `04_baseline_forecast.ipynb`
    * `05_ml_forecast.ipynb`
    * `06_backtesting_control_tower.ipynb`
* **data/**
    * `raw/`
    * `processed/` (parquets generados por notebooks)
* **reports/**
    * `figures/`
    * `model_cards/`

---

## 6. Metodología (Pipeline)
1.  **Generación de datos:** Sintéticos, estilo naviera.
2.  **Feature Store:** Lags + rolling sin fuga de información.
3.  **EDA:** Patrones de demanda, saturación y estacionalidad.
4.  **Baseline:** Operativo y explicable.
5.  **ML Forecast:** Mejora de precisión con features avanzadas.
6.  **Backtesting + Torre de Control:** Alertas y playbook de acciones.

---

## 7. Hallazgos EDA (Resultados actuales)

### Demanda media por ruta (pax/Salida)
* **DEN–IBZ:** ≈ 758.55
* **DEN–FOR:** ≈ 665.84
* **DEN–PMI:** ≈ 638.82
* **VAL–IBZ:** ≈ 517.42

**Interpretación operativa:** DEN–IBZ y DEN–FOR son rutas “core” con demanda alta (mayor presión de capacidad). VAL–IBZ es más débil, candidata a estrategias de estímulo en periodos valle.

### Riesgo de saturación (ocupación p90)
* **DEN–IBZ:** occ_p90 = 1.00 (saturación recurrente).
* **DEN–FOR:** occ_p90 ≈ 0.929 (vigilancia, cerca del umbral).
* **DEN–PMI:** occ_p90 ≈ 0.729.
* **VAL–IBZ:** occ_p90 ≈ 0.582.

> **Importancia:** Donde el p90 se acerca a 1.0, pequeñas desviaciones del forecast implican ventas perdidas o necesidad de refuerzo.

---

## 8. Modelos

### 8.1 Baseline (Operativo)
**LY same-slot + trend adjust:**
* Busca la demanda del “mismo slot” del año anterior (ruta + weekday + hora).
* Ajusta por tendencia usando rolling mean reciente vs rolling mean LY.
* Aplica cap por capacidad.
* *Métricas:* (Pendiente de Notebook 04)

### 8.2 Modelo ML (Feature-based)
**Modelo tipo árbol (Random Forest / Gradient Boosting):**
* **Features:** Lags (1w, 2w), rolling mean/std, calendario (festivos), operación (retrasos) y precio.
* *Métricas:* (Pendiente de Notebook 05)

---

## 9. Evaluación
Se utiliza backtesting temporal (sin shuffle) y métricas:
* **MAE** (error medio absoluto en pax).
* **WAPE** (error ponderado, útil en operaciones).
* Análisis por ruta y por temporada.

---

## 10. Torre de control (Decisiones accionables)
Semáforo basado en ocupación forecast:
* 🔴 **ROJO (occ_pred ≥ 0.92):** Riesgo de saturación. Acción: Evaluar refuerzo/cambio buque + ajustar inventario.
* 🟡 **ÁMBAR (0.80–0.92):** Vigilar curva de reserva. Acción: Microajustes + seguimiento.
* 🟢 **VERDE (< 0.60):** Sobrecapacidad. Acción: Promo táctica / bundles / campañas.

---

## 11. Cómo reproducir
1. Ejecutar notebooks en orden del `01` al `06`.
2. Los outputs se guardan automáticamente en `data/processed` y `reports/`.

---

## 12. Próximos pasos (con datos reales)
* Sustituir proxies por festivos regionales y meteo marítima real.
* Integrar histórico de cancelaciones y pricing real por segmento.
* Implementar intervalos P50/P90 para decisiones con gestión de riesgo.
