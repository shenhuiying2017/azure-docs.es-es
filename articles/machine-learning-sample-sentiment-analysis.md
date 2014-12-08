<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Ejemplo de Aprendizaje automático: análisis de opiniones | Azure" description="A sample Azure Machine Learning experiment that uses sentiment classification to predict product reviews." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />
 

# Ejemplo de Aprendizaje automático de Azure: análisis de opiniones

>[AZURE.NOTE]
>El [experimento de ejemplo] y el [conjunto de datos de ejemplo] asociado a este modelo están disponibles en Estudio de aprendizaje automático. Vea más abajo para obtener información adicional.
[Experimento de ejemplo]: #sample-experiment
[Conjunto de datos de ejemplo]: #sample-dataset


##Descripción del problema
Predecir la valoración en la crítica de un producto. Las valoraciones pueden ser 1,2,3,4,5. Se trata de un problema de regresión ordinal, que se puede solucionar también como un problema de regresión y un problema de clasificación de varias clases.
 
##Datos
Críticas de libros en Amazon, tomadas del sitio de Amazon por investigadores de UPenn ([http://www.cs.jhu.edu/~mdredze/datasets/sentiment/](http://www.cs.jhu.edu/~mdredze/datasets/sentiment/)). El conjunto de datos original cuenta con 975.000 críticas con valoraciones de 1,2,3,4,5. Para acelerar el experimento, redujimos la muestra hasta las 10.000 críticas. Todas las críticas están en inglés y se redactaron entre 1997 y 2007. 
 
##Modelo
Utilizamos el módulo de creación de hash de características para transformar el texto en inglés en características con valores enteros. Comparamos tres modelos:  
 
1. regresión lineal   
2. regresión ordinal utilizando regresión logística de dos clases como clasificador básico
3. clasificación de varias clases utilizando un clasificador de regresión logística de varias clases
 
##Resultados

Algoritmo                 | Error absoluto medio | Error cuadrático medio
:---------                | :-----------------: | :--------------------:
Regresión ordinal        | 0,82                | 1,41
Regresión lineal         | 1,04                | 1,36
Clasificación de varias clases | 0,85                | 1,57
 
En función de estos resultados, optamos por el modelo de regresión ordinal y creamos un servicio web basado en él.
 
<!-- Removed until this part is fixed
##API
We have built a web service that takes a plain text review and predicts its rating.
-->


## Experimento de ejemplo

El siguiente experimento de ejemplo asociado a este modelo está disponible en Estudio de aprendizaje automático en la sección **EXPERIMENTOS** bajo la pestaña **EJEMPLOS**.

> **Experimento de ejemplo - Clasificación de opiniones - Desarrollo**


## Conjunto de datos de ejemplo

El siguiente conjunto de datos de ejemplo que se usa en este experimento está disponible en Estudio de aprendizaje automático en la paleta de módulos bajo **Conjuntos de datos guardados**.

###Reseñas de libros de Amazon
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
