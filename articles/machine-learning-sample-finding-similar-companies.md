<properties title="Azure Machine Learning Sample: Finding similar companies" pageTitle="Ejemplo de aprendizaje automático: búsqueda de empresas similares | Azure" description="A sample Azure Machine Learning experiment that uses a clustering model to find similar companies." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Ejemplo de Aprendizaje automático de Azure: búsqueda de empresas similares

>[AZURE.NOTE]
>El [experimento de ejemplo] y el [conjunto de datos de ejemplo] asociado a este modelo están disponibles en Estudio de aprendizaje automático. Consulte la siguiente información para obtener más detalles.
[Experimento de ejemplo]: #sample-experiment
[Conjunto de datos de ejemplo]: #sample-dataset


## Descripción del problema ##
Agrupar empresas del índice S&P 500 en función de los artículos de Wikipedia de cada una de ellas. Este modelo podría utilizarse, por ejemplo, para identificar empresas emergentes en función de sus similitudes con empresas existentes de S&P 500.

## Datos ##
Los datos se derivan de un volcado XML de Wikipedia. Se procesaron previamente fuera de Estudio de aprendizaje automático para extraer el contenido de texto de cada una de las empresas, quitar las características de formato de wiki, quitar caracteres no alfanuméricos y convertir todo el texto a minúsculas. Además, se agregaron categorías de empresas conocidas. Tenga en cuenta que no se encontraron artículos para algunas de las empresas. Por eso, el número de registros es inferior a 500.

## Modelo ##
En primer lugar, el contenido del artículo se pasó al módulo Hash de características, que transforma los datos de texto a formato numérico en función del valor de hash de cada token. Estos datos tenían una alta dimensión y una dispersión tales que impedían su agrupamiento directo mediante K-Means. Por tanto, se utilizó PCA dentro del módulo Ejecutar script R para reducir la dimensionalidad hasta 10 variables. Tenga que cuenta que es posible visualizar los resultados de PCA abriendo el resultado derecho del módulo R. 

A partir de prueba y error, se detectó que la primera variable (la que tiene una mayor varianza) de los datos transformados por PCA parece que tiene un efecto perjudicial sobre la agrupación. Por tanto, se quitó del conjunto de características utilizando el módulo Columnas de proyecto.

A continuación, los datos se agruparon mediante el algoritmo K-Means en 3 clústeres. Mediante prueba y error, se descubrió que este número producía resultados razonables; se probaron también 4 y 5 clústeres.

Por último, se utilizó el Editor de metadatos para convertir la variable de etiqueta de clúster a categórico. Además, se convirtieron los resultados a un archivo CSV para su descarga.

## Resultados ##
Los resultados pueden verse a través de los resultados del Editor de metadatos y el trazado de la columna Categoría (conocida por los datos de la Wikipedia) en comparación con la columna Asignaciones. Los 3 clústeres se corresponden aproximadamente con las siguientes categorías conocidas.

Clúster 0: consumo discrecional, consumo básico, finanzas, asistencia sanitaria

Clúster 1: tecnología de la información

Clúster 2: energía, servicios públicos, servicios de telecomunicaciones.

**Nota:** la línea que separa los diferentes grupos en ocasiones es difusa. El clúster 0 tiene varias empresas relacionadas con la energía y la tecnología de la información, y los bienes industriales y materiales se dividen entre el clúster 0 y el clúster 1.

**Nota:** la agrupación puede variar entre las diferentes ejecuciones. 



## Experimento de ejemplo

Puede encontrar el siguiente experimento de ejemplo asociado a este modelo en Estudio de aprendizaje automático, en la sección **EXPERIMENTOS** bajo la pestaña **EJEMPLOS**.

> **Experimento de ejemplo - Agrupación de empresas del índice S & P 500 - Desarrollo**


## Conjunto de datos de ejemplo

El siguiente conjunto de datos de ejemplo que se usa en este experimento está disponible en Estudio de aprendizaje automático en la paleta de módulos bajo **Conjuntos de datos guardados**.

###Conjunto de datos de SP 500 de Wikipedia
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
