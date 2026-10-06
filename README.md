# PoC 02: contrafactuales con DiCE (resultado negativo)

Parte del proyecto de tesis "Plataforma web para la detección temprana del riesgo de sobreendeudamiento en mujeres emprendedoras" (UPC, Taller de Proyecto I, 2026-20).

## Objetivo único
Comprobar que `dice_ml` genera contrafactuales válidos sobre el mismo modelo de la PoC 01, modificando solo las variables declaradas como accionables.

## Datos
Los mismos datos sintéticos de la PoC 01 (18 variables, semilla 42).

## Cómo correrla
```
pip install -r requirements.txt
python poc_dice.py
```

## Resultado
| Chequeo | Resultado |
|---|---|
| Contrafactuales generados | 8 de 8 solicitados |
| Probabilidad original y de los contrafactuales | 92.85 % → entre 5.62 % y 36.58 % |
| Variables inmutables modificadas | 0 |
| Contrafactuales con variables derivadas incoherentes | 1 de 8 (CF5) |
| Contrafactuales que proponen acciones contrarias a la prevención | 6 de 8 |

Las acciones contrarias a la prevención fueron estas:
- Subir el gasto mensual de S/ 911.71 a S/ 2,266.82 (CF1), a S/ 2,363.05 (CF2) o a S/ 3,955.42 (CF7).
- Empezar a usar crédito informal (CF4).
- Subir la cuota mensual de S/ 1,690.02 a S/ 2,471.72 (CF5).
- Pasar de 2 a 4.30 créditos en los últimos 12 meses (CF8).

Además, las variables binarias salieron con decimales (tener cuenta bancaria pasó de 0 a 0.60 en el CF6).

## Hallazgos
1. DiCE exige `predict_proba`, que el `Booster` nativo de LightGBM no tiene. Se entrena un `LGBMClassifier` y se entrega su `booster_` a SHAP. Ambos devuelven 0.928498, así que son el mismo modelo.
2. Un contrafactual no es una recomendación: la dirección del cambio no está controlada.
3. DiCE trata cada variable como independiente y rompe las relaciones entre variables derivadas.

## Decisión de diseño
DiCE se descartó (ADR-004). La capa de recomendaciones se construyó con RAG y un modelo de lenguaje sobre fuentes de educación financiera (ver PoC 03).

## Archivos
- `poc_dice.py`: script.
- `resultado_poc2.json`: salida estructurada.
- `salida_consola.txt`: salida completa de la consola.

## Corrida de referencia
6 de octubre de 2026, Linux, Python 3.12.3. Los resultados son idénticos a la corrida original de septiembre de 2026.
