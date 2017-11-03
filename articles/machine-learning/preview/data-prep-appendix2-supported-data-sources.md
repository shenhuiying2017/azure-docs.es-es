---
title: "Orígenes de datos admitidos disponibles con la preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona una lista completa de los orígenes de datos admitidos disponibles para la preparación de datos de Azure Machine Learning."
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
ms.date: 09/12/2017
ms.openlocfilehash: 1ef4c5c33d98cfeb566e8fe23bda9e0d3f041781
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="supported-data-sources-for-this-release"></a>Orígenes de datos admitidos para esta versión 
En el documento siguiente se describe la lista de orígenes de datos admitidos actualmente en la preparación de datos de Azure Machine Learning.

Los orígenes de datos admitidos para esta versión son los siguientes.

## <a name="types"></a>Tipos 
### <a name="directory-versus-file"></a>Directorio frente a archivo
*Archivos o directorios*: elija un único archivo y léalo en Preparación de los datos. El tipo de archivo se analiza para determinar los parámetros predeterminados para la conexión de archivo que se encuentra en la pantalla siguiente. Elija un directorio o un conjunto de archivos en un directorio (el selector de archivos permite la selección múltiple). Con cualquiera de estos enfoques, los archivos se leen como un único flujo de datos con los archivos anexados entre sí (los encabezados se eliminan si es necesario).

Los tipos de archivo son los siguientes:
- Delimitado (.csv, .tsv, .txt, etc.) 
- Ancho fijo
- Texto sin formato
- Archivo JSON

### <a name="csv-file"></a>Archivo CSV
Lee un archivo CSV del almacenamiento.

#### <a name="options"></a>Opciones
- Separador
- Comentario
- Encabezados
- Símbolo decimal
- Codificación de archivos
- Líneas que se van a omitir

### <a name="tsv-file"></a>Archivo TSV
Lee un archivo de valores TSV del almacenamiento.

#### <a name="options"></a>Opciones
- Comentario
- Encabezados
- Codificación de archivos
- Líneas que se van a omitir

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Lee un archivo de Excel, una hoja cada vez, mediante la especificación del nombre o el número de hoja.

#### <a name="options"></a>Opciones
- Nombre o número de hoja
- Encabezados
- Líneas que se van a omitir

### <a name="json-file"></a>Archivo JSON
Lea un archivo JSON del almacenamiento. Tenga en cuenta que el archivo es "plano" en la lectura.

#### <a name="options"></a>Opciones
None

### <a name="parquet"></a>Parquet
Lea un conjunto de datos de Parquet, ya sea un solo archivo o una carpeta.

Parquet, como formato, puede adquirir formas diversas en el almacenamiento. Para conjuntos de datos más pequeños, a veces se utiliza un archivo .parquet único. Distintas bibliotecas de Python admiten la lectura o escritura en un archivo .parquet único. Por el momento, Azure Machine Learning Workbench se basa en la biblioteca PyArrow de Python para leer Parquet durante el uso interactivo local. Admite archivos .parquet únicos (siempre y cuando se hayan escrito como tal, no como parte de un conjunto de datos más grande). También admite conjuntos de datos de Parquet. 

Un conjunto de datos de Parquet es una colección de más de un archivo .parquet, cada uno de los cuales representa una partición más pequeña de un conjunto de datos mayor. Los conjuntos de datos suelen encontrarse en una carpeta. Se trata del formato de salida de Parquet predeterminado para las plataformas comunes, como Spark y Hive.

>[!NOTE]
>Al leer los datos de Parquet que se encuentran en una carpeta con varios archivos .parquet, es más seguro seleccionar el directorio para la lectura y marcar la opción **Parquet Dataset** (Conjunto de datos de Parquet). Esto hace que PyArrow lea toda la carpeta en lugar de los archivos individuales. Esto garantiza la compatibilidad con la lectura de formas más complicadas de almacenamiento de Parquet en el disco (por ejemplo, la creación de particiones de carpetas).

La ejecución de la escalabilidad horizontal se basa en las funciones de lectura de Parquet de Spark y admite tanto archivos individuales como carpetas.

#### <a name="options"></a>Opciones
*Parquet dataset* (Conjunto de datos de parquet): esta opción determina si Azure Machine Learning Workbench usa el modo desactivado o el modo activado. El modo desactivado expande un directorio determinado e intenta leer cada uno de los archivos que contiene individualmente. El modo activado trata el directorio como el conjunto de datos completo y permite a PyArrow averiguar la mejor manera de interpretar los archivos.


## <a name="locations"></a>Ubicaciones
### <a name="local"></a>Local
Unidad de disco duro local o ubicación de almacenamiento de red asignada.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Requiere una suscripción a Azure.

