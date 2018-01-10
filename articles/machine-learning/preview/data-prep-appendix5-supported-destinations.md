---
title: "Destinos y salidas de datos compatibles disponibles para la preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona una lista completa de los destinos y salidas de datos compatibles disponibles para la preparación de datos de Azure Machine Learning"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 50d2d481b91199630bbfbf3cfdd21a1bf3062ff0
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="supported-data-exports-for-this-preview"></a>Exportaciones de datos compatibles para esta versión preliminar 
Es posible exportar a varios formatos diferentes. Estos formatos se pueden usar para conservar los resultados intermedios de la preparación de datos antes de integrar los resultados al resto del flujo de trabajo de Machine Learning.

## <a name="types"></a>Tipos 
### <a name="csv-file"></a>Archivo CSV 
Escribe un archivo de valores separados por comas para el almacenamiento.

#### <a name="options"></a>Opciones
- Fin de línea
- Reemplaza valores null por
- Reemplaza errores por 
- Separador


### <a name="parquet"></a>Parquet 
Escribir un conjunto de datos en el almacenamiento como Parquet

Parquet, como formato, puede adquirir formas diversas en el almacenamiento. Para conjuntos de datos más pequeños, a veces se utiliza un archivo .parquet único. Distintas bibliotecas de Python admiten la lectura y la escritura en archivos .parquet únicos. 

Por el momento, Azure Machine Learning Workbench se basa en la biblioteca PyArrow de Python para escribir Parquet durante el uso interactivo local. Esto significa que el archivo Parquet único actualmente es el único formato de salida Parquet admitido durante el uso interactivo local.

Durante las ejecuciones de escalado horizontal (en Spark), Azure Machine Learning Workbench depende de las capacidades de lectura y escritura de Parquet de Spark. El formato de salida predeterminado de Spark para Parquet (que actualmente es el único admitido) tiene una estructura similar a la de un conjunto de datos Hive; esto significa que una carpeta contiene muchos archivos .parquet que son cada uno una partición de un conjunto de datos mayor. 

#### <a name="caveats"></a>Advertencias 
Parquet, como formato, es relativamente joven y presenta algunas incoherencias de implementación en distintas bibliotecas. Por ejemplo, Spark impone restricciones de validez en los caracteres de los nombres de columna cuando se escriben en Parquet. PyArrow, no. Los siguientes caracteres no pueden estar en un nombre de columna: 
- .
- ;
- {}
- ()
- \\n
- \\t
- =

>[!NOTE]
>- Para garantizar la compatibilidad con Spark, cada vez que se escriben datos en Parquet, las apariciones de estos caracteres en los nombres de columna se reemplazan por un carácter de subrayado ("_").
>- Para garantizar la coherencia en las ejecuciones locales y de escalado horizontal, todos los nombres de columna de los datos escritos en Parquet (mediante la aplicación, Python o Spark) se sanean para que sean compatibles con Spark. Para garantizar los nombres de columna previstos al escribirlos en los caracteres de Parquet en Spark, quite el conjunto no válido de las columnas antes de la escritura.



## <a name="locations"></a>Ubicaciones 
### <a name="local"></a>Local 
Unidad de disco duro local o ubicación de almacenamiento de red asignada.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob Storage requiere una suscripción de Azure.

