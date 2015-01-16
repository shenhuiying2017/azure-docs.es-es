<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Ejemplo de aprendizaje automático: tarea de CRM | Azure" description="Un experimento de aprendizaje automático de Azure de ejemplo para desarrollar varios modelos que predicen la tendencia a comprar un producto nuevo, el incremento de las ventas y la renovación de cliente." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Ejemplo de Aprendizaje automático de Azure: tarea de CRM

>[AZURE.NOTE]
>El [experimento de ejemplo] y los [conjuntos de datos de ejemplo] asociados a este modelo están disponibles en Estudio de aprendizaje automático. Consulte la siguiente información para obtener más detalles.
[Experimento de ejemplo]: #sample-experiment
[Conjuntos de datos de ejemplo]: #sample-datasets

<!--
- [Problem Description]
- [Data]
- [Model]
- [Results]
- [API]
- [Sample Experiment]
-->
[Descripción del problema]: #problem-description
[Datos]: #data
[Modelo]: #model
[Resultados]: #results
[API]: #api

## Descripción del problema ##

Predicción de la rotación de clientes (cambio de proveedor), mejora de ventas (compra de productos o complementos de mayor valor) y propensión a comprar un producto nuevo (apetencia) 

## Datos ##

Conjunto de datos de 50.000 clientes de la empresa French Telecom Company Orange en Francia. Cada cliente tiene 230 características de carácter anónimo. Estos datos proceden de la competición KDD Cup 2009. 

Se trata de características tanto numéricas como de cadena. Las características están muy dispersas.
 
## Modelo ##

La única tarea que se hizo previamente al procesar los datos fue tratar los valores que faltaban. Sustituimos por "0" los valores faltantes en las características de cadena. Casi todas las características numéricas tienen valores no negativos. Agregamos 1 a los valores existentes de las características numéricas y sustituimos 0 en las entradas con valor ausente. De este modo, distinguimos entre los 0 originales y los 0 que indicaban un valor ausente. Lo hicimos aplicando en primer lugar una operación matemática (+1) solo a las características numéricas. Después de esto, sustituimos todos los valores ausentes por 0 (o "0" en las cadenas). 

Dado que tenemos características tanto numéricas como categóricas, utilizamos un clasificador de árbol de decisión ampliado. Los 3 problemas están desequilibrados, ya que los ejemplos positivos son minoría. En algunos casos, esto puede provocar que el clasificador ignore regiones en el espacio de características con un número reducido de ejemplos positivos. Para probarlo, hemos entrenado dos modelos para cada problema: uno utilizando datos sin procesar y el otro replicando ejemplos positivos de manera que el nuevo número de ejemplos positivos sea prácticamente igual al número de ejemplos de prueba negativos. Esto se consiguió utilizando un script R sencillo que dividió los ejemplos positivos y negativos y adjuntando a continuación 13 copias del conjunto positivo al conjunto negativo. 

## Resultados ##

Los modelos dan como resultado una puntuación relacionada con la probabilidad para un resultado positivo en cada una de las tareas. Dado que se puede seleccionar un umbral de corte arbitrario para etiquetar una observación como positiva, medimos el rendimiento mediante el área bajo la curva (AUC) de característica operativa del receptor (ROC). Tenga en cuenta que tanto en los casos de rotación de clientes como de mejora de ventas, la replicación de muestras positivas para crear un conjunto más equilibrado mejoró marginalmente el rendimiento del modelo.  

<table border="1">
<tr><td>Rotación de clientes</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.711</td></tr>
<tr><td>positivos replicados</td><td>0,728</td></tr>
</table>


<table border="0">
<tr><td>Mejora de ventas</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.853</td></tr>
<tr><td>positivos replicados</td><td>0,865</td></tr>
</table>


<table border="0">
<tr><td>Apetencia</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.805</td></tr>
<tr><td>positivos replicados</td><td>0,8</td></tr>
</table>

## API ##

No llevamos el modelo a la práctica porque tiene características anónimas.



## Experimento de ejemplo

Puede encontrar el siguiente experimento de ejemplo asociado a este modelo en Estudio de aprendizaje automático, en la sección **EXPERIMENTOS** bajo la pestaña **EJEMPLOS**.

> **Experimentos de ejemplo - CRM - Desarrollo**

## Conjuntos de datos de ejemplo

Los siguientes conjuntos de datos de ejemplo que se usan en este experimento están disponibles en Estudio de aprendizaje automático en la paleta de módulos bajo **Conjuntos de datos guardados**.

###Conjunto de datos CRM compartido
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]

###Etiquetas de apetencia CRM compartidas
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]

###Etiquetas de rotación de clientes de CRM compartidas
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]

###Etiquetas de mejora de ventas de CRM compartidas
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
