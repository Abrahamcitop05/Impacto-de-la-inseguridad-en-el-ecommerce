# Impacto-de-la-inseguridad-en-el-ecommerce

![Status](https://img.shields.io/badge/Status-Completed-success)
![Python](https://img.shields.io/badge/Python-3.12-blue)
![Focus](https://img.shields.io/badge/Focus-Econometrics%20%26%20Optimization-orange)
![License](https://img.shields.io/badge/License-MIT-green)

## Resumen 
Este proyecto desarrolla un sistema de **toma de decisiones basada en datos** para una cadena de retail en Perú. El objetivo es determinar la asignación óptima de presupuesto de marketing en un entorno caracterizado por la incertidumbre y la **inseguridad ciudadana**.

Mediante una arquitectura de tres fases (Econometría, Control Óptimo y Gestión de Riesgos), el modelo demuestra que una estrategia diferenciada regionalmente incrementa el **ROI proyectado en un 35%** comparado con una estrategia uniforme, manteniendo el riesgo de cola (CVaR) bajo control.


## Modelo

El proyecto integra métodos cuantitativos avanzados para resolver el problema de asignación de recursos:

### Econometría
Se estimó un modelo *Log-Log* con Efectos Fijos (Entity & Time Fixed Effects) para aislar la elasticidad-precio de la inseguridad:

$$
\ln(V_{it}) = \alpha_i + \lambda_t + \beta_1 \ln(I_{it}) + \beta_2 (\ln(I_{it}) \times D_{Norte}) + \epsilon_{it}
$$

**Hallazgo:** Existe una heterogeneidad estructural significativa. La región **Sur** es altamente sensible ($\beta \approx 0.54$), mientras que el **Norte** presenta rigidez ($\beta \approx 0.14$).

### Optimización dinámica 
Se formuló un Hamiltoniano para maximizar el Valor Presente Neto (VPN) del *Goodwill* (Valor de Marca), sujeto a rendimientos decrecientes y depreciación:

$$
\max_{u(t)} \int_{0}^{T} e^{-rt} \left[ P \cdot V(G, I) - u(t) \right] dt
$$

Sujeto a la ecuación de movimiento del Goodwill:
$$
\dot{G}(t) = \sqrt{u(t)} - \delta G(t)
$$

Donde las variables se definen como:

* **$u(t)$ (Control):** El gasto en marketing en el momento $t$.
* **$G(t)$ (Estado):** El stock de *Goodwill* acumulado. Es la "memoria" del sistema (marca).
* **$\lambda(t)$ (Co-estado):** El **Precio Sombra** del Goodwill.
* **$V(G, I)$:** La función de ingresos, que depende del Goodwill y del nivel exógeno de Inseguridad ($I$).
* **$\delta$:** Tasa de depreciación u "olvido" de la marca.

### Stress Testing (Monte Carlo AR-1)
Se validó la robustez de la estrategia mediante 2,000 simulaciones de Monte Carlo con procesos autoregresivos para capturar la "memoria" del miedo:

$$
\text{Shock}_t = \rho \cdot \text{Shock}_{t-1} + \epsilon_t, \quad \epsilon_t \sim N(0, \sigma^2)
$$



## Resultados

### Divergencia Estratégica (Norte vs. Sur)
El algoritmo recomienda una estrategia de **"Front-Loading"** agresiva en el Sur para aprovechar la alta elasticidad, mientras sugiere contención de costos en el Norte.

![Gráfico Estrategia](ruta_a_tu_imagen_comparativa_roi.png)
*(Fig 1. Asignación óptima de recursos y ROI comparativo)*

### Perfil de Riesgo (Risk Profile)
A pesar de la agresividad de la inversión, el análisis de colas confirma la solvencia de la estrategia.

| Métrica | Valor | Interpretación |
| :--- | :--- | :--- |
| **VPN Esperado** | **$54,200** | Beneficio neto proyectado (Base). |
| **VaR 95%** | **$12,500** | Suelo de seguridad en condiciones normales. |
| **CVaR 95%** | **$-4,200** | Pérdida promedio ante crisis extremas. |
| **Prob. Pérdida** | **2.4%** | Riesgo de ROI negativo controlado (<5%). |

![Gráfico Riesgo](ruta_a_tu_imagen_risk_profile.png)
*(Fig 2. Distribución de resultados financieros y zonas de riesgo)*

### Parámetros financieros

Estos valores definen las restricciones y los incentivos financieros del algoritmo de optimización:

| Variable | Símbolo | Valor Asumido | Impacto en el Modelo |
| :--- | :---: | :---: | :--- |
| **Margen Unitario** | $P$ | **$3.00** | Ganancia neta promedio por cada venta. Es el multiplicador de ingresos en el Hamiltoniano. Si $P$ fuera menor, la inversión óptima caería. |
| **Techo Presupuestal** | $u_{max}$ | **$500 / mes** | Restricción de liquidez (Constraint). Simula el presupuesto máximo aprobado por Finanzas. Explica por qué la curva de inversión se "aplana" en el tope. |
| **Goodwill Inicial** | $G_0$ | **100 pts** | Punto de partida del stock de marca. Simula una empresa en marcha, no una startup desde cero. |
| **Costo de Oportunidad** | $r$ | **5%** | Tasa mensual. El modelo descuenta los flujos futuros agresivamente, priorizando ganancias a corto plazo sobre el muy largo plazo. |

## Supuestos 

Para garantizar la validez externa, los parámetros se calibraron basándose en teoría económica y heurísticas del sector retail.

| Parámetro | Símbolo | Valor | Justificación Económica / Negocio |
| :--- | :---: | :---: | :--- |
| **Elasticidad Sur** | $\beta_{Sur}$ | **0.54** | Estimada. Demanda altamente elástica a la percepción de seguridad. |
| **Elasticidad Norte** | $\beta_{Norte}$ | **0.14** | Estimada. Rigidez en la demanda (posible normalización de la violencia). |
| **Elasticidad Goodwill** | $\gamma$ | **0.40** | Rendimientos decrecientes de la marca (Cobb-Douglas). |
| **Depreciación Marca** | $\delta$ | **15%** | *Forgetting Curve*: Tasa mensual de olvido publicitario (Adstock). |
| **Tasa Descuento** | $r$ | **5%** | WACC mensual ajustado por riesgo sectorial alto. |
| **Persistencia** | $\rho$ | **0.70** | AR(1). El miedo tiene "memoria"; los shocks no son transitorios. |
| **Costo Marketing** | $C(u)$ | $\sqrt{u}$ | Convexidad: Saturar el mercado es exponencialmente costoso. |

### Limitaciones
1.  **Horizonte Finito ($T=24$):** Se optimiza para un ciclo presupuestal de 2 años, generando una caída natural de la inversión al final del periodo (efecto de borde).
2.  **Precios Constantes:** Modelo de equilibrio parcial; no contempla estrategias de *Dynamic Pricing* en respuesta a crisis.
3.  **Causalidad Unidireccional:** Se asume exogeneidad estricta de la inseguridad sobre las ventas.

## Glosario 

| Término | Definición en el Contexto del Proyecto |
| :--- | :--- |
| **Goodwill ($G$)** | Stock de "Valor de Marca" acumulado. Activo intangible que impulsa ventas futuras. |
| **Control ($u$)** | Variable de decisión: Presupuesto mensual de marketing asignado. |
| **Hamiltoniano** | Medida del valor total instantáneo (beneficio corriente + valor sombra futuro). |
| **VaR 95%** | *Value at Risk*: Máxima pérdida esperada en el 95% de los escenarios. |
| **CVaR 95%** | *Conditional VaR*: Esperanza de pérdida condicional a que ocurra un evento de cola (crisis). |
