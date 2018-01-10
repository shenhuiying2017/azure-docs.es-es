---
title: "Orígenes de datos admitidos disponibles con la preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona una lista completa de los orígenes de datos admitidos disponibles para la preparación de datos de Azure Machine Learning."
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
ms.date: 09/12/2017
ms.openlocfilehash: 32707a8089abef6caebedea168f5891161b1b480
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Orígenes de datos admitidos para la preparación de datos de Azure Machine Learning 
En este artículo se describen los orígenes de datos admitidos actualmente en la preparación de datos de Azure Machine Learning.

Los orígenes de datos admitidos para esta versión son los siguientes.

## <a name="types"></a>Tipos 

### <a name="sql-server"></a>SQL Server
Lectura desde el servidor SQL local o la base de datos SQL de Azure.

#### <a name="options"></a>Opciones
- Dirección del servidor
- Confíe en el servidor (incluso cuando el certificado en el servidor no sea válido. Úselo con precaución)
- Tipo de autenticación (Windows, Server)
- User Name
- Password
- Base de datos a la que conectarse
- Consulta SQL

#### <a name="notes"></a>Notas
- No se admiten columnas Sql-variant
- La columna de hora se convierte en fecha y hora al anexar la hora de la base de datos a la fecha 1970/1/1
- Cuando se ejecuta en el clúster de Spark, todos los datos relacionados con las columnas (date, datetime, datetime2, datetimeoffset) evaluarán valores incorrectos para las fechas anteriores a 1583
- Los valores de columnas decimales pueden perder precisión debido a la conversión a formato decimal

### <a name="directory-vs-file"></a>Directorio frente a archivo
Elija un único archivo y léalo en la preparación de datos. El tipo de archivo se analiza para determinar los parámetros predeterminados para la conexión de archivo que se encuentra en la pantalla siguiente.

Elija un directorio o un conjunto de archivos en un directorio (el selector de archivos permite la selección múltiple). Con cualquiera de estos enfoques, los archivos se leen como un único flujo de datos y se anexan entre sí, con los encabezados eliminados si es necesario.

Los tipos de archivo admitidos son:
- Delimitado (.csv, .tsv, .txt, etc.)
- Ancho fijo
- Texto sin formato
- Archivo JSON

### <a name="csv-file"></a>Archivo CSV
Lea desde el almacenamiento un archivo de valores separados por comas.

#### <a name="options"></a>Opciones
- Separador
- Comentario
- encabezados
- Símbolo decimal
- Codificación de archivos
- Líneas que se van a omitir

### <a name="tsv-file"></a>Archivo TSV
Lea desde el almacenamiento un archivo de valores separados por tabulaciones.

#### <a name="options"></a>Opciones
- Comentario
- encabezados
- Codificación de archivos
- Líneas que se van a omitir

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Lea un archivo de Excel, una hoja de cada vez, mediante la especificación del nombre o número de hoja.

#### <a name="options"></a>Opciones
- Nombre o número de hoja
- encabezados
- Líneas que se van a omitir

### <a name="json-file"></a>Archivo JSON
Lea un archivo JSON del almacenamiento. El archivo es "plano" en la lectura.

#### <a name="options"></a>Opciones
- None

### <a name="parquet"></a>Parquet
Lea un conjunto de datos de Parquet, ya sea un solo archivo o una carpeta.

Parquet, como formato, puede adquirir formas diversas en el almacenamiento. Para conjuntos de datos más pequeños, a veces se utiliza un archivo .parquet único. Distintas bibliotecas de Python admiten la lectura o escritura en archivos .parquet únicos. Por el momento, la preparación de datos de Azure Machine Learning se basa en la biblioteca PyArrow de Python para leer Parquet durante el uso interactivo local. Admite archivos .parquet únicos (siempre y cuando se hayan escrito como tal, no como parte de un conjunto de datos más grande), así como conjuntos de datos de Parquet.

Un conjunto de datos de Parquet es una colección de más de un archivo .parquet, cada uno de los cuales representa una partición más pequeña de un conjunto de datos mayor. Los conjuntos de datos suelen encontrarse en una carpeta y son el formato de salida predeterminado de Parquet para plataformas como Spark y Hive.

>[!NOTE]
>Al leer los datos de Parquet que se encuentran en una carpeta con varios archivos .parquet, es más seguro seleccionar el directorio para la lectura y la opción **Parquet Dataset** (Conjunto de datos de Parquet). Esto hace que PyArrow lea toda la carpeta en lugar de los archivos individuales. Esto garantiza la compatibilidad con la lectura de formas más complicadas de almacenamiento de Parquet en el disco, como la creación de particiones de carpetas.

La ejecución de la escalabilidad horizontal se basa en las funcionalidades de lectura de Parquet de Spark y admite tanto archivos individuales como carpetas, de forma similar al uso interactivo local.

#### <a name="options"></a>Opciones
- Conjunto de datos de Parquet. Esta opción determina si la preparación de datos de Azure Machine Learning expande un directorio determinado e intenta leer cada archivo individualmente (el modo no seleccionado), o bien si trata el directorio como el conjunto de datos completo (el modo seleccionado). Con el modo seleccionado, PyArrow elige la mejor forma de interpretar los archivos.


## <a name="locations"></a>Ubicaciones
### <a name="local"></a>Local
Una unidad de disco duro local o una ubicación de almacenamiento de red asignada.

### <a name="sql-server"></a>SQL Server
Servidor SQL local o base de datos SQL de Azure.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob Storage, que requiere una suscripción de Azure.

