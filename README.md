# 🧪 Priorización de Hipótesis y Análisis de Test A/B

![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-2.x-150458?logo=pandas&logoColor=white)
![SciPy](https://img.shields.io/badge/SciPy-Stats-8CAAE6?logo=scipy&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completado-brightgreen)

Proyecto de experimentación y toma de decisiones basada en datos para una tienda online. El análisis cubre dos etapas: 
- **Priorizar qué hipótesis implementar** usando frameworks de scoring
- **Evaluar si el cambio implementado funcionó** mediante un test A/B con validación estadística.


## 🎯 Contexto del problema

El equipo de marketing recopiló una lista de hipótesis de mejora que podrían aumentar los ingresos. Con recursos limitados, no es posible implementarlas todas — hay que decidir cuál probar primero y cómo medir si funcionó.

**El reto no era solo analizar datos, sino responder dos preguntas de negocio:**
1. ¿Cuál hipótesis tiene el mayor potencial de impacto real sobre la base total de usuarios?
2. ¿Los resultados del test A/B son suficientemente sólidos para adoptar el cambio de forma permanente?

## 📁 Estructura del proyecto

```
ab-testing-hypothesis/
│
├── data/
│   ├── hypotheses_us.csv     # Hipótesis con scores estimados (Reach, 
│   │                           Impact, Confidence, Effort)
│   ├── orders_us.csv         # Transacciones del test A/B (grupo A y B)
│   └── visits_us.csv         # Visitas diarias por grupo
│
├── images/                   # Visualizaciones exportadas (para el README)
├── project02_notebook_enhanced.ipynb   # Notebook principal
└── README.md
```

## 🧰 Stack tecnológico

| Herramienta | Uso |
|---|---|
| `pandas` | Manipulación de datos y construcción de métricas acumuladas |
| `numpy` | Operaciones numéricas y cálculo de percentiles |
| `scipy.stats` | Prueba estadística Mann-Whitney U |
| `matplotlib` | Visualizaciones de métricas acumuladas y dispersión |

## 📐 Metodología

### Parte 1 — Priorización de Hipótesis

Se aplicaron dos frameworks de scoring para ordenar las hipótesis según su potencial:

| Framework | Fórmula | Diferencia clave |
|---|---|---|
| **ICE** | (Impact × Confidence) / Effort | Ignora cuántos usuarios impacta |
| **RICE** | (Reach × Impact × Confidence) / Effort | Penaliza/premia el alcance real |

La comparación entre rankings ICE y RICE reveló cambios significativos de posición: iniciativas con alto impacto individual pero bajo alcance (como una promoción de cumpleaños) pierden prioridad frente a iniciativas que impactan a la mayoría del tráfico (como un formulario de suscripción en todas las páginas).

**Decisión:** Se priorizó la **Hipótesis 7** (formulario de suscripción) por su alcance prácticamente universal sobre el tráfico total.

---

### Parte 2 — Análisis del Test A/B

El análisis siguió cuatro pasos para garantizar validez estadística:

**1. Limpieza de datos**
- Eliminación de 58 usuarios duplicados en ambos grupos A y B (contaminación cruzada)

**2. Análisis de métricas acumuladas**
- Ingreso acumulado, ticket promedio acumulado y tasa de conversión acumulada, día a día
- Identificación visual de anomalías: salto abrupto en ingresos del Grupo B alrededor del 18 de agosto

**3. Detección y filtrado de outliers**

| Tipo de anomalía | Umbral | Criterio |
|---|---|---|
| Usuarios con muchos pedidos | > 2 pedidos | Percentil 99 |
| Pedidos de precio extremo | > $415 | Percentil 95 |

**4. Prueba estadística Mann-Whitney U** — en datos brutos y filtrados

Se eligió Mann-Whitney (no prueba t) porque los datos de conversión y revenue **no siguen distribución normal**: están fuertemente sesgados a la derecha con una cola larga de outliers, condición en la que la prueba t pierde validez.

## 🔍 Resultados del Test A/B

| Métrica | P-value bruto | P-value filtrado | Diferencia relativa (filtrado) |
|---|---|---|---|
| **Tasa de conversión** | 0.011 ✅ | **0.012 ✅** | **+18.2% a favor de Grupo B** |
| Ticket promedio | 0.862 ❌ | 0.680 ❌ | -4.7% (no significativo) |

**El hallazgo crítico:**
La ventaja inicial de Grupo B en ingresos (+27.8% en ticket promedio) desapareció completamente al filtrar outliers, confirmando que era un artefacto estadístico. La mejora en conversión, en cambio, se mantuvo y se fortaleció — señal de que el efecto es real y robusto.

## 📊 Principales Insights

| # | Insight | Implicación |
|---|---|---|
| 1 | RICE revierte las prioridades ICE al incorporar alcance | Usar siempre RICE para priorización de roadmap |
| 2 | El ingreso promedio es engañoso sin filtrar outliers | Reportar mediana + p-value filtrado, nunca solo el promedio bruto |
| 3 | La conversión es la métrica más robusta ante datos sucios | Definirla como métrica primaria en tests de e-commerce |
| 4 | Los outliers filtrados son un segmento valioso, no solo ruido | Analizarlos por separado como posibles clientes VIP o B2B |
| 5 | Parar un test tiene criterios técnicos objetivos | Definir criterios de parada antes de lanzar el experimento |

## ✅ Decisión Final

**El test se detiene y se adopta el Grupo B.**

- ✅ Conversión: +18.2%, estadísticamente significativa (p=0.012), resultado robusto ante filtrado
- ✅ Ticket promedio: sin diferencia real — el cambio no daña el gasto por usuario
- ✅ Las métricas acumuladas se estabilizaron — continuar el test no aportaría nueva información

## ▶️ Cómo ejecutar el proyecto

```bash
# 1. Clonar el repositorio
git clone https://github.com/inbemero-tech/ab-testing-hypothesis.git
cd ab-testing-hypothesis

# 2. Instalar dependencias
pip install pandas numpy matplotlib scipy jupyter

# 3. Abrir el notebook
jupyter notebook project_analysis_2_notebook.ipynb
```

## 🔗 Conexión con análisis de riesgo crediticio

Las técnicas aplicadas en este proyecto tienen equivalencia directa en el ámbito de **riesgo crediticio en fintech**:

- La **lógica de Grupo A vs. Grupo B** equivale al análisis de **cosechas (vintages)**: comparar el comportamiento de carteras originadas bajo distintas políticas de crédito — con la misma necesidad de aislar el efecto del cambio de variables externas
- La **prueba Mann-Whitney** es aplicable para comparar distribuciones de morosidad entre segmentos sin asumir normalidad — condición frecuente en datos de crédito con muchos ceros y colas largas
- La **detección de outliers por percentiles** replica la identificación de **comportamiento atípico de pago** (usuarios que pagan en exceso, fraude, o patrones anómalos) en portfolios crediticios
- El framework **ICE/RICE** tiene análogo directo en la **priorización de reglas de scoring**: antes de implementar una nueva variable en un modelo, se estima su reach (% de solicitudes que la activan), su impacto esperado en el Gini y el esfuerzo de integración

---
Desarrollado por **Inti Alberto Romero González**  
Data Analyst · Construyendo expertise en Riesgo Crediticio | Fintech & Remoto  
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Perfil-0A66C2?logo=linkedin)](https://linkedin.com/in/inti-romero)