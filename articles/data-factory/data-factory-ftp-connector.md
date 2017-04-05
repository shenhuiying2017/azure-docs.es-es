---
title: Movimiento de datos de un servidor de FTP mediante Azure Data Factory | Microsoft Docs
description: Aprenda a mover datos de un servidor FTP mediante Azure Data Factory.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 3f859aed911b04353273d1faa5ead8d4c48c2a05
ms.lasthandoff: 03/27/2017


---
# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Movimiento de datos de un servidor FTP mediante Azure Data Factory
En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos desde un servidor FTP. Se basa en la información general ofrecida en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md).

Puede copiar datos de un servidor FTP a cualquier almacén de datos receptor admitido. Consulte la tabla de [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como receptores. Data Factory solo admite actualmente el movimiento de datos de un servidor FTP a otros almacenes de datos, pero no viceversa. Admite servidores FTP tanto locales como en la nube.

## <a name="enabling-connectivity"></a>Habilitación de la conectividad
Si va a mover datos desde un servidor FTP **local** a un almacén de datos de nube (ejemplo: Azure Blob Storage), instale y utilice la puerta de enlace de administración de datos. La puerta de enlace de administración de datos es un agente de cliente instalado en la máquina local, que permite a los servicios en la nube conectarse al recurso local. Consulte [Puerta de enlace de administración de datos](data-factory-data-management-gateway.md) para detalles sobre la puerta de enlace. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para instrucciones paso a paso sobre cómo configurar y usar la puerta de enlace. Utilice la puerta de enlace para conectarse a un servidor FTP, aunque este se encuentre en una máquina virtual de Azure IaaS.

Puede instalar la puerta de enlace en la misma máquina local o la máquina virtual de Azure IaaS como servidor FTP. Sin embargo, se recomienda que instale la puerta de enlace en una máquina o máquina virtual de Azure IaaS independiente para evitar la contención de recursos y mejorar el rendimiento. Al instalar la puerta de enlace en una máquina independiente, esta última debería poder acceder al servidor FTP.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con una actividad de copia que mueva datos desde un origen FTP mediante diferentes herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copia**. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

También puede usar las herramientas siguientes para crear una canalización: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, la **plantilla de Azure Resource Manager**, la **API de .NET** y la **API de REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia. 

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor: 

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia. 
3. Cree una **canalización** con una actividad de copia que tome un conjunto de datos como entrada y un conjunto de datos como salida. 

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Si desea obtener un ejemplo con definiciones de JSON para entidades de Data Factory que se utilizan con el fin de copiar datos desde un almacén de datos FTP, consulte la sección [Ejemplo de JSON: Copia de datos de un servidor FTP a un blob de Azure](#json-example-copy-data-from-ftp-server-to-azure-blob) de este artículo. 

Las secciones siguientes proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de FTP:

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de FTP.

| Propiedad | Descripción | Obligatorio | Valor predeterminado |
| --- | --- | --- | --- |
| type |La propiedad type debe establecerse en FtpServer |yes |&nbsp; |
| host |Nombre o dirección IP del servidor FTP |Sí |&nbsp; |
| authenticationType |Especificar el tipo de autenticación |yes |Basic, Anonymous |
| nombre de usuario |Usuario que tiene acceso al servidor FTP |No |&nbsp; |
| contraseña |Contraseña para el usuario (nombre de usuario) |No |&nbsp; |
| encryptedCredential |Credenciales cifradas para acceder al servidor FTP |No |&nbsp; |
| gatewayName |Nombre de la puerta de enlace de administración de datos para conectarse a un servidor FTP local |No |&nbsp; |
| puerto |Puerto en el que escucha el servidor FTP |No |21 |
| enableSsl |Especificar si desea usar FTP a través del canal SSL/TLS |No |true |
| enableServerCertificateValidation |Especificar si desea habilitar la validación de certificados de servidor SSL al usar FTP sobre el canal SSL/TLS |No |true |

### <a name="using-anonymous-authentication"></a>Uso de autenticación anónima

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {        
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>Uso de nombre de usuario y contraseña en texto sin formato para la autenticación básica

```JSON
{
    "name": "FTPLinkedService",
      "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
}
```

### <a name="using-port-enablessl-enableservercertificatevalidation"></a>Uso de puerto, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>Uso de encryptedCredential para la autenticación y la puerta de enlace

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
      }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como estructura, disponibilidad y directiva de un JSON de conjunto de datos son similares para todos los tipos de conjunto de datos.

La sección **typeProperties** es diferente para cada tipo de conjunto de datos. Proporciona información específica del tipo de conjunto de datos. La sección typeProperties del conjunto de datos de tipo **FileShare** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| folderPath |Subruta de acceso a la carpeta. Use el carácter de escape "\" para los caracteres especiales de la cadena. Consulte los casos que se exponen en [Ejemplos de definiciones de servicio vinculado y conjunto de datos](#sample-linked-service-and-dataset-definitions) .<br/><br/>Puede combinar esta propiedad con **partitionBy** para que las rutas de acceso de carpeta se basen en las fechas y horas de inicio y finalización del segmento. |Sí |
| fileName |Especifique el nombre del archivo en **folderPath** si quiere que la tabla haga referencia a un archivo específico de la carpeta. Si no especifica ningún valor para esta propiedad, la tabla apunta a todos los archivos de la carpeta.<br/><br/>Si no se especifica fileName para un conjunto de datos de salida, el nombre del archivo generado estaría en el siguiente formato: <br/><br/>Data<Guid>.txt (por ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| fileFilter |Especifique el filtro que se va a usar para seleccionar un subconjunto de archivos de folderPath, en lugar de todos los archivos.<br/><br/>Valores permitidos son: `*` (varios caracteres) y `?` (un único individual).<br/><br/>Ejemplos 1: `"fileFilter": "*.log"`<br/>Ejemplo 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter es aplicable a un conjunto de datos FileShare de entrada. Esta propiedad no es compatible con HDFS. |No |
| partitionedBy |partitionedBy se puede usar para especificar un valor de folderPath dinámico, un nombre de archivo para datos de series temporales. Por ejemplo, folderPath se parametriza por cada hora de datos. |No |
| formato | Se admiten los siguientes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** y **ParquetFormat**. Establezca la propiedad **type** de formato en uno de los siguientes valores. Para más información, consulte las secciones [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format) y [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Si desea **copiar los archivos tal cual** entre los almacenes basados en archivos (copia binaria), omita la sección de formato en las definiciones de los conjuntos de datos de entrada y salida. |No |
| compresión | Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son: **GZip**, **Deflate**, **BZip2** y **ZipDeflate**, y los niveles que se admiten son: **Optimal** (Óptimo) y **Fastest** (Más rápido). Para obtener más información, consulte el artículo sobre [formatos de archivo y compresión en Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useBinaryTransfer |Especifica si se usará el modo de transferencia binario. Su valor es true para el modo binario y false para ASCII. Valor predeterminado: true. Esta propiedad solo puede usarse cuando el tipo de servicio vinculado asociado es: FtpServer. |No |

> [!NOTE]
> filename y fileFilter no pueden usarse simultáneamente.

### <a name="using-partionedby-property"></a>Uso de la propiedad partitionedBy
Como ya se ha indicado en la sección anterior, se puede especificar un valor dinámico de folderPath y filename para datos de series temporales con la propiedad **partitionedBy**, [funciones de Data Factory y las variables del sistema](data-factory-functions-variables.md).

Para aprender más sobre los conjuntos de datos, la programación y los segmentos de series temporales, consulte [Creación de conjuntos de datos](data-factory-create-datasets.md), [Programación y ejecución](data-factory-scheduling-and-execution.md) y [Creación de canalizaciones](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Muestra 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
En este ejemplo, {Slice} se reemplaza por el valor de la variable del sistema SliceStart de Data Factory en el formato (YYYYMMDDHH) especificado. SliceStart hace referencia a la hora de inicio del segmento. folderPath es diferente para cada segmento. Ejemplo: wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Ejemplo 2:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
En este ejemplo, year, month, day y time de SliceStart se extraen en variables independientes que se usan en las propiedades folderPath y fileName.



## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, las propiedades de tipo varían en función de los tipos de orígenes y receptores.

En Actividad de copia, si el origen es del tipo **FileSystemSource**, estarán disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| recursive |Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. |True, False (predeterminada) |No |


## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Ejemplo de JSON: Copia de datos de un servidor FTP a un blob de Azure
En este ejemplo se muestra cómo copiar datos un servidor FTP a Azure Blob Storage. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.  

En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

* Un servicio vinculado de tipo [FtpServer](#linked-service-properties).
* Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [FileShare](#dataset-properties).
* Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [FileSystemSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

En el ejemplo se copian los datos de servidor FTP a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado de FTP:** en este ejemplo se utiliza la autenticación básica con nombre de usuario y contraseña en texto sin formato. También se puede hacer de una de las siguientes maneras:

* Autenticación anónima
* Autenticación básica con credenciales cifradas
* FTP sobre SSL/TLS (FTPS)

Para los diferentes tipos de autenticación que se pueden usar, consulte la sección [Propiedades del servicio vinculado de FTP](#linked-service-properties).

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
    "typeProperties": {
        "host": "myftpserver.com",           
        "authenticationType": "Basic",
        "username": "Admin",
        "password": "123456"
    }
  }
}
```
**Servicio vinculado de Almacenamiento de Azure:**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de datos de entrada de FTP:** este conjunto de datos hace referencia a la carpeta FTP `mysharedfolder` y el archivo `test.csv`. La canalización copia el archivo en el destino.

Si se establece "external": "true", se informa al servicio Data Factory de que el conjunto de datos es externo a la factoría de datos y que no lo genera ninguna actividad de la factoría de datos.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Conjunto de datos de salida de blob de Azure**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


**Actividad de copia en una canalización con origen del sistema de archivos y receptor blob:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **FileSystemSource** y el tipo **sink** se establece en **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Para asignar columnas del conjunto de datos de origen a las del conjunto de datos receptor, consulte el artículo [Asignación de columnas de conjuntos de datos en Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes:

* [Tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para la creación de una canalización con una actividad de copia.

