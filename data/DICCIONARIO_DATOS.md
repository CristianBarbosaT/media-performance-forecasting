# Diccionario de datos — `media_performance_raw.csv`

Dataset **sintético** de performance de medios digitales, generado para el proyecto de
portafolio *"Análisis de performance de medios con forecasting"*. Imita la estructura
que verías en una cuenta real de agencia (Datorama, GA4, plataformas de medios), pero
sin datos confidenciales. **Declara en tu README que es sintético** — es lo honesto y no
le resta valor al proyecto: lo que se evalúa es tu análisis, no la procedencia.

## Estructura

- **Granularidad:** un registro por **día × canal**
- **Rango temporal:** 2023-01-01 a 2024-12-31 (2 años completos → suficiente para
  capturar estacionalidad semanal y anual, requisito para forecasting)
- **~3.659 filas** en crudo (incluye duplicados; tras limpieza esperas **~3.600**
  filas limpias sobre **5 canales canónicos**)

## Columnas

| Columna       | Tipo      | Descripción                                              |
|---------------|-----------|----------------------------------------------------------|
| `date`        | fecha     | Día de la métrica                                        |
| `channel`     | texto     | Canal de medios (5 canónicos: Paid Search, Paid Social, Display, Video, Email) |
| `impressions` | entero    | Impresiones servidas (en Email = envíos)                 |
| `clicks`      | entero    | Clics registrados                                        |
| `spend`       | decimal   | Inversión en USD                                         |
| `conversions` | entero    | Conversiones atribuidas                                  |
| `revenue`     | decimal   | Ingresos atribuidos en USD                               |

> Las métricas derivadas (CTR, CPC, CVR, ROAS, CPA) **no** vienen calculadas a propósito:
> calcularlas es parte del EDA.
> - CTR = clicks / impressions
> - CPC = spend / clicks
> - CVR = conversions / clicks
> - ROAS = revenue / spend
> - CPA = spend / conversions

## Estructura "real" embebida (para que el análisis tenga sustancia)

- **Tendencia:** crecimiento gradual (~+24% en los 2 años)
- **Estacionalidad anual:** pico fuerte en Q4 (Nov–Dic), valle en verano y enero
- **Estacionalidad semanal:** cada canal se comporta distinto entre semana vs. fin de
  semana (Email baja el finde, Social/Video suben)
- **Picos de promoción:** varias ventanas tipo Black Friday / sales con spend y
  conversiones elevados
- **Ruido** multiplicativo realista en todas las métricas

## ⚠️ Problemas de calidad — PLANTADOS A PROPÓSITO

El JD de PGD repite dos veces que les importa la **atención al detalle** y la
**capacidad de QA / revisar tu propio trabajo**. Por eso el dataset trae errores
deliberados: encontrarlos, documentarlos y corregirlos es tu mejor pieza de portafolio
para *ese* criterio.

Te doy las **categorías** (no las filas exactas — encontrarlas es el ejercicio):

1. **Etiquetas inconsistentes** en `channel`. Hay 10 valores únicos pero solo 5 canales
   reales. Vas a tener que normalizar variantes como `paid search`, `Paid_Search`,
   `PaidSearch`, `Social`, `paid_social`.
2. **Valores imposibles:** algunas filas con `clicks > impressions`.
3. **Spend negativo:** errores de signo en `spend`.
4. **Outlier extremo:** al menos un `revenue` disparado por error de captura (pista: el
   máximo está en cientos de miles cuando lo normal son miles).
5. **Valores faltantes (NaN)** en `spend` y en `revenue`.
6. **Filas duplicadas exactas** (dispersas, no adyacentes).
7. **Días faltantes (gaps)** en la serie de un canal — relevante para forecasting, donde
   un índice de fechas continuo importa.
8. **Formatos de fecha mixtos:** la mayoría en `YYYY-MM-DD`, unas pocas en `MM/DD/YYYY`.

**Auto-chequeo:** después de un QA correcto deberías terminar con ~3.600 filas, exactamente
5 canales, sin duplicados, sin valores imposibles ni negativos, fechas parseadas a un solo
formato y los gaps de fechas resueltos (rellenados o explícitamente marcados). Si tus números
no cuadran, ahí tienes algo que revisar.

## Alternativas con datos reales (para sumar credibilidad al portafolio)

El dataset sintético es la **columna vertebral** del proyecto. Si además quieres una pieza
con datos reales:

- **Google Analytics Sample (BigQuery public dataset)** — `bigquery-public-data.google_analytics_sample`.
  Datos reales de e-commerce/web con sesiones, fuente/medio y transacciones. Encaja perfecto
  porque **ya manejas BigQuery y GCP** — puedes hacer la capa SQL ahí mismo.
- **Kaggle — "Marketing Analytics" / campañas de marketing digital.** Busca datasets de
  campañas con spend/impresiones/conversiones para variar de esquema (ataca tu tendencia a
  apoyarte en esquemas conocidos).
