---
title: "Destinos de datos/salidas compatibles disponibles con Preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona una lista completa de los destinos/salidas compatibles que están disponibles para Preparación de datos de Azure ML"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 415ceba02eb3c8da3de5ab3aa6980fbe5bae2db9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="supported-data-exports-for-this-preview"></a>Exportaciones de datos compatibles para esta versión preliminar 
Es posible exportar a varios formatos diferentes. Estos formatos se pueden usar para conservar los resultados intermedios de la preparación de datos antes de integrarlos al resto del flujo de trabajo de Machine Learning.

## <a name="types"></a>Tipos 
### <a name="csv-file"></a>Archivo CSV 
Escribir un archivo de valores separados por comas en el almacenamiento

#### <a name="options"></a>Opciones
- Fines de línea
- Reemplazar valores NULL por
- Reemplazar errores por 
- Separador


### <a name="parquet"></a>Parquet ###
Escribir un conjunto de datos en el almacenamiento como Parquet

Parquet, como formato, puede adquirir formas diversas en el almacenamiento. En ocasiones se usa un archivo ".parquet" único para conjuntos de datos más pequeños. Varias bibliotecas de Python admiten la lectura y la escritura en archivos ".parquet" únicos. Por el momento, AMLWB se basa en la biblioteca PyArrow de Python para escribir Parquet durante el uso "interactivo" local. Esto significa que el archivo parquet único es actualmente el único formato de salida Parquet permitido durante el uso interactivo local.

Durante las ejecuciones de escalado horizontal (en Spark), AMLWB depende de las capacidades de lectura/escritura de Parquet de Spark. El formato de salida predeterminado de Spark para Parquet (que actualmente es el único admitido) tiene una estructura similar a la de un conjunto de datos HIVE; es decir, una carpeta que contiene muchos archivos ".parquet", cada uno de los cuales son una partición más pequeña de un conjunto de datos mayor. 

#### <a name="caveats"></a>Advertencias ####
Parquet, como formato, es relativamente joven y presenta algunas incoherencias de implementación en distintas bibliotecas. Por ejemplo, Spark impone restricciones en los caracteres que puede haber en los nombres de columnas cuando se escriben en Parquet, mientras que PyArrow no impone ninguna restricción. Ningún carácter del conjunto, ",;{}()\\n\\t=", puede estar en un nombre de columna.

>[!NOTE]
>Para garantizar la compatibilidad con Spark, cada vez que se escriben datos en Parquet, las apariciones de estos caracteres en los nombres de columna se reemplazan por "_" (carácter de subrayado).**

>[!NOTE]
>Para garantizar la coherencia en las ejecuciones locales y en las ejecuciones de escalado horizontal, todos los datos escritos en Parquet (mediante la aplicación, Python o Spark) tienen sus nombres de columnas saneados para garantizar la compatibilidad de Spark. Para garantizar los nombres de columnas previstos al escribirlos en los caracteres de Parquet en Spark, se debe quitar de las columnas el conjunto no válido antes de escribir.



## <a name="locations"></a>Ubicaciones 
### <a name="local"></a>Local 
Unidad de disco duro local o ubicación de almacenamiento de red asignada

### <a name="azure-blob"></a>Azure Blob 
Azure Storage (BLOB) requiere una suscripción de Azure

