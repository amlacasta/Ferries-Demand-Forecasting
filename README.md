# 🚢 LevanteFerries: Forecasting de Demanda (Trip-Level)
> **Sistema de Control Operativo y Torre de Control para el Transporte Marítimo**

[![Python](https://img.shields.io/badge/Python-3.9+-blue.svg)](https://www.python.org/)
[![Status](https://img.shields.io/badge/Status-In--Development-orange.svg)]()
[![Sector](https://img.shields.io/badge/Sector-Maritime%20Logistics-brightgreen.svg)]()

## 📌 1. Contexto y Problema
En el transporte marítimo de pasajeros, la demanda es altamente volátil debido a factores estacionales, operativos y climáticos. Esta variabilidad genera dos riesgos críticos para **LevanteFerries**:

1.  **Saturación y Lucro Cesante:** Pérdida de ingresos cuando la demanda supera la capacidad del buque (especialmente en temporada alta).
2.  **Infrautilización:** Costes por plaza vacía elevados que erosionan el margen operativo en salidas de baja ocupación.



## 🎯 2. Objetivo del Proyecto
Construir un sistema de **Forecasting de Demanda por Salida** (ruta–fecha–hora–buque) para habilitar una **"Torre de Control"** que permita:
* Anticipar picos de demanda y riesgos de saturación.
* Detectar salidas con sobrecapacidad.
* Activar decisiones de *Capacity Management* y *Dynamic Pricing* basadas en datos.

## 📊 3. Dataset (Sintético-Realista)
Para este proyecto se utiliza un dataset sintético que replica el comportamiento real de una naviera en la zona Levante-Baleares.

* **Periodo:** 2024-01-01 al 2025-12-31.
* **Volumen:** 8,948 salidas (trips) y 319,231 registros de reserva.
* **Variables Clave:**
    * `Identification`: ID de ruta, tipo de buque, horario local.
    * `Capacity`: Plazas disponibles (PAX y Vehículos).
    * `Demand`: Ocupación real, ingresos y precio medio.
    * `Exogenous`: Estado del mar (proxy), festivos, retrasos.

## 📂 4. Estructura del Repositorio

├── notebooks/
│   ├── 01_generate_synthetic_data.ipynb  # Motor de simulación
│   ├── 02_data_prep_feature_store.ipynb   # Ingeniería de variables
│   ├── 03_eda_demand_patterns.ipynb       # Análisis de patrones
│   ├── 04_baseline_forecast.ipynb         # Modelo base explicable
│   ├── 05_ml_forecast.ipynb               # Modelo ML avanzado
│   └── 06_backtesting_control_tower.ipynb # Simulación operativa
├── data/
│   ├── raw/
│   └── processed/
└── reports/
    ├── figures/
    └── model_cards/


## 📊 3. Dataset (Sintético-Realista)
