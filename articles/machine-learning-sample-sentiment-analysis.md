<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Machine Learning Sample: Sentiment analysis | Azure" description="A sample Azure Machine Learning experiment that uses sentiment classification to predict product reviews." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Ejemplo de Aprendizaje automático de Azure: análisis de opiniones

*Puede ver el experimento de ejemplo asociado a este modelo en Estudio de aprendizaje automático, en la sección **EXPERIMENTOS** que se encuentra bajo la pestaña **EJEMPLOS**. El nombre del experimento es:*

    Sample Experiment - Sentiment Classification - Development

## Descripción del problema

Predecir la valoración en la crítica de un producto. Las valoraciones pueden ser 1,2,3,4,5. Se trata de un problema de regresión ordinal, que se puede solucionar también como un problema de regresión y un problema de clasificación de varias clases.

## Datos

Críticas de libros en Amazon, tomadas del sitio de Amazon por investigadores de UPenn ([][]<http://www.cs.jhu.edu/~mdredze/datasets/sentiment/></a>). El conjunto de datos original cuenta con 975.000 críticas con valoraciones de 1,2,3,4,5. Para acelerar el experimento, redujimos la muestra hasta las 10.000 críticas. Todas las críticas están en inglés y se redactaron entre 1997 y 2007.

## Modelo

Utilizamos el módulo de creación de hash de características para transformar el texto en inglés en características con valores enteros. Comparamos tres modelos:

1.  regresión lineal
2.  regresión ordinal utilizando regresión logística de dos clases como clasificador básico
3.  clasificación de varias clases utilizando un clasificador de regresión logística de varias clases

## Resultados

Algoritmo| Error medio absoluto| Raíz cuadrada del error cuadrático medio
---------|
Regresión ordinal | 0,82| 1,41|
Regresión lineal | 1,04| 1,36|
Clasificación de varias clases | 0,85 | 1,57

En función de estos resultados, optamos por el modelo de regresión ordinal y creamos un servicio web basado en él.

<!-- Removed until this part is fixed ##API We have built a web service that takes a plain text review and predicts its rating. -->

  []: http://www.cs.jhu.edu/~mdredze/datasets/sentiment/
