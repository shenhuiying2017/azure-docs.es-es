---
title: "Orígenes de datos admitidos disponibles con la preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona una lista completa de los orígenes de datos admitidos para la preparación de datos de Azure ML."
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
ms.openlocfilehash: 0a77b31e07f118155094fab4d3393ffdb37c9c6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="supported-data-sources-for-this-release"></a>Orígenes de datos admitidos para esta versión 
En el documento siguiente se describe la lista de orígenes de datos admitidos actualmente en la preparación de datos.

A continuación se indican los orígenes de datos admitidos para esta versión.

## <a name="types"></a>Tipos 
### <a name="directory-vs-file"></a>Directorio frente a archivo
Archivos y directorios: elija un solo archivo y léalo en la preparación de datos. El tipo de archivo se analiza para determinar los parámetros predeterminados para la conexión de archivo que se encuentra en la pantalla siguiente. Elija un directorio o un conjunto de archivos de un directorio (el selector de archivos permite realizar una selección múltiple), y use los resultados de cualquiera de estos enfoques en los archivos que se están leyendo como un solo flujo de datos con los archivos anexados entre sí (eliminando los encabezados si es necesario).

Los tipos de archivo admitidos son:
- Delimitado (csv, tsv, txt, etc.) 
- Ancho fijo
- Texto sin formato
- Archivo JSON

### <a name="csv-file"></a>Archivo CSV
Lea desde el almacenamiento un archivo de valores separados por comas.

#### <a name="options"></a>Opciones
- Separador
- Comentario
- Encabezados
- Símbolo decimal
- Codificación de archivos
- Líneas que se van a omitir

### <a name="tsv-file"></a>Archivo TSV
Lea desde el almacenamiento un archivo de valores separados por tabulaciones.

#### <a name="options"></a>Opciones
- Comentario
- Encabezados
- Codificación de archivos
- Líneas que se van a omitir

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Lea un archivo de Excel, una hoja de cada vez, mediante la especificación del nombre o número de hoja.

#### <a name="options"></a>Opciones
- Nombre o número de hoja
- Encabezados
- Líneas que se van a omitir

### <a name="json-file"></a>Archivo JSON
Lea desde el almacenamiento un archivo JSON. Tenga en cuenta que el archivo se "aplana" en la lectura.

#### <a name="options"></a>Opciones
- None

### <a name="parquet"></a>Parquet
Lea un conjunto de datos de Parquet, ya sea un solo archivo o una carpeta.

Parquet, como formato, puede adquirir formas diversas en el almacenamiento. En ocasiones se usa un archivo ".parquet" único para conjuntos de datos más pequeños. Varias bibliotecas de Python admiten la lectura y la escritura en archivos ".parquet" únicos. Por el momento, AMLWB se basa en la biblioteca PyArrow de Python para leer Parquet durante el uso "interactivo" local. Admite archivos ".parquet" únicos (siempre y cuando se hayan escrito como tal, no como parte de un conjunto de datos más grande), así como conjuntos de datos de Parquet. Un conjunto de datos de Parquet es una colección de más de un archivo ".parquet", cada uno de los cuales representa una partición más pequeña de un conjunto de datos mayor. Los conjuntos de datos suelen encontrarse en una carpeta y son el formato de salida predeterminado de Parquet para plataformas comunes, como Spark y Hive.

>[!NOTE]
>Al leer los datos de Parquet que se encuentran en una carpeta con varios archivos ".parquet", es más seguro seleccionar el directorio para la lectura y marcar la opción Parquet Dataset (Conjunto de datos de Parquet). Esto hará que PyArrow lea toda la carpeta en lugar de los archivos individuales, lo que garantiza que se puedan leer formas más complicadas de almacenar Parquet en disco (por ejemplo, creación de particiones de carpeta). **

La ejecución de la escalabilidad horizontal se basa en las funciones de lectura de Parquet de Spark y admite tanto archivos individuales como carpetas, de forma similar al uso interactivo local.

#### <a name="options"></a>Opciones
- Conjunto de datos de Parquet
  - Esta opción determina si AMLWB expandirá un directorio determinado e intentará leer cada archivo individualmente (modo desactivado) o si tratará el directorio como el conjunto de datos completo y dejará que PyArrow averigüe la mejor forma de interpretar los archivos (modo activado).


## <a name="locations"></a>Ubicaciones
### <a name="local"></a>Local
Unidad de disco duro local o ubicación de almacenamiento de red asignada

### <a name="azure-blob"></a>Azure Blob
Azure Storage (BLOB) requiere una suscripción de Azure

