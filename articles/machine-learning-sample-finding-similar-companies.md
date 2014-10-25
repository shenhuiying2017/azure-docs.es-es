<properties title="Azure Machine Learning Sample: Finding similar companies" pageTitle="Machine Learning Sample: Finding similar companies | Azure" description="A sample Azure Machine Learning experiment that uses a clustering model to find similar companies." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Ejemplo de Aprendizaje automático de Azure: búsqueda de empresas similares

*Puede ver el experimento de ejemplo asociado a este modelo en Estudio de aprendizaje automático, en la sección **EXPERIMENTOS** que se encuentra bajo la pestaña **EJEMPLOS**. El nombre del experimento es:*

    Sample Experiment - S & P 500 Company Clustering - Development

## Descripción del problema

Agrupar empresas del índice S&P 500 en función de los artículos de Wikipedia de cada una de ellas. Este modelo podría utilizarse, por ejemplo, para identificar empresas emergentes en función de sus similitudes con empresas existentes de S&P 500.

## Datos

Los datos se derivan de un volcado XML de Wikipedia. Se procesaron previamente fuera de Estudio de aprendizaje automático para extraer el contenido de texto de cada una de las empresas, quitar las características de formato de wiki, quitar caracteres no alfanuméricos y convertir todo el texto a minúsculas. Además, se agregaron categorías de empresas conocidas. Tenga en cuenta que no se encontraron artículos para algunas de las empresas. Por eso, el número de registros es inferior a 500.

## Modelo

En primer lugar, el contenido del artículo se pasó al módulo Hash de características, que transforma los datos de texto a formato numérico en función del valor de hash de cada token. Estos datos tenían una alta dimensión y una dispersión tales que impedían su agrupamiento directo mediante K-Means. Por tanto, se utilizó PCA dentro del módulo Ejecutar script R para reducir la dimensionalidad hasta 10 variables. Tenga que cuenta que es posible visualizar los resultados de PCA abriendo el resultado derecho del módulo R.

A partir de prueba y error, se detectó que la primera variable (la que tiene una mayor varianza) de los datos transformados por PCA parece que tiene un efecto perjudicial sobre la agrupación. Por tanto, se quitó del conjunto de características utilizando el módulo Columnas de proyecto.

A continuación, los datos se agruparon mediante el algoritmo K-Means en 3 clústeres. Mediante prueba y error, se descubrió que este número producía resultados razonables; se probaron también 4 y 5 clústeres.

Por último, se utilizó el Editor de metadatos para convertir la variable de etiqueta de clúster a categórico. Además, se convirtieron los resultados a un archivo CSV para su descarga.

## Resultados

Los resultados pueden verse a través de los resultados del Editor de metadatos y el trazado de la columna Categoría (conocida por los datos de la Wikipedia) en comparación con la columna Asignaciones. Los 3 clústeres se corresponden aproximadamente con las siguientes categorías conocidas.

Clúster 0: consumo discrecional, consumo básico, finanzas, asistencia sanitaria

Clúster 1: tecnología de la información

Clúster 2: energía, servicios públicos, servicios de telecomunicaciones.

**Nota:** la línea que separa los diferentes grupos en ocasiones es difusa. El clúster 0 tiene varias empresas relacionadas con la energía y la tecnología de la información, y los bienes industriales y materiales se dividen entre el clúster 0 y el clúster 1.

**Nota:** la agrupación puede variar entre las diferentes ejecuciones.

