<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Machine Learning Sample: CRM task | Azure" description="A sample Azure Machine Learning experiment to develop multiple models that predict customer churn, upsell, and propensity to buy a new product." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Ejemplo de Aprendizaje automático de Azure: tarea de CRM

*Puede ver el experimento de ejemplo asociado a este modelo en Estudio de aprendizaje automático, en la sección **EXPERIMENTOS** que se encuentra bajo la pestaña **EJEMPLOS**. El nombre del experimento es:*

    Sample Experiments - CRM - Development

## Descripción del problema

Predicción de la rotación de clientes (cambio de proveedor), mejora de ventas (compra de productos o complementos de mayor valor) y propensión a comprar un producto nuevo (apetencia)

## Datos

Conjunto de datos de 50.000 clientes de la empresa French Telecom Company Orange en Francia. Cada cliente tiene 230 características de carácter anónimo. Estos datos proceden de la competición KDD Cup 2009.

Se trata de características tanto numéricas como de cadena. Las características están muy dispersas.

## Modelo

La única tarea que se hizo previamente al procesar los datos fue tratar los valores que faltaban. Sustituimos por "0" los valores faltantes en las características de cadena. Casi todas las características numéricas tienen valores no negativos. Agregamos 1 a los valores existentes de las características numéricas y sustituimos 0 en las entradas con valor ausente. De este modo, distinguimos entre los 0 originales y los 0 que indicaban un valor ausente. Lo hicimos aplicando en primer lugar una operación matemática (+1) solo a las características numéricas. Después de esto, sustituimos todos los valores ausentes por 0 (o "0" en las cadenas).

Dado que tenemos características tanto numéricas como categóricas, utilizamos un clasificador de árbol de decisión ampliado. Los 3 problemas están desequilibrados, ya que los ejemplos positivos son minoría. En algunos casos, esto puede provocar que el clasificador ignore regiones en el espacio de características con un número reducido de ejemplos positivos. Para probarlo, hemos entrenado dos modelos para cada problema: uno utilizando datos sin procesar y el otro replicando ejemplos positivos de manera que el nuevo número de ejemplos positivos sea prácticamente igual al número de ejemplos de prueba negativos. Esto se consiguió utilizando un script R sencillo que dividió los ejemplos positivos y negativos y adjuntando a continuación 13 copias del conjunto positivo al conjunto negativo.

## Resultados

Los modelos dan como resultado una puntuación relacionada con la probabilidad para un resultado positivo en cada una de las tareas. Dado que se puede seleccionar un umbral de corte arbitrario para etiquetar una observación como positiva, medimos el rendimiento mediante el área bajo la curva (AUC) de característica operativa del receptor (ROC). Tenga en cuenta que tanto en los casos de rotación de clientes como de mejora de ventas, la replicación de muestras positivas para crear un conjunto más equilibrado mejoró marginalmente el rendimiento del modelo.

|----------------------|-------|
| Rotación de clientes | AUC   |
| sin replicación      | 0.711 |
| positivos replicados | 0.728 |

|----------------------|-------|
| Mejora de ventas     | AUC   |
| sin replicación      | 0.853 |
| positivos replicados | 0.865 |

|----------------------|-------|
| Apetencia            | AUC   |
| sin replicación      | 0.805 |
| positivos replicados | 0.8   |

## API

No llevamos el modelo a la práctica porque tiene características anónimas.

