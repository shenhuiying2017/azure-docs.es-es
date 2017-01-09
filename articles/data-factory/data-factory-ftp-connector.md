---
title: Movimiento de datos desde el servidor FTP | Microsoft Docs
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
ms.date: 10/20/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: f27d978eab3aba52ee9a51a5f284b592e3edd6af


---
# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Movimiento de datos de un servidor FTP mediante Azure Data Factory
En este artículo se resume el uso de la actividad de copia en Azure Data Factory para mover datos de un servidor FTP a otro almacén de datos compatible. Este artículo se basa en el artículo sobre las [actividades de movimiento de datos](data-factory-data-movement-activities.md), que presenta una introducción general del movimiento de datos con la actividad de copia la lista de almacenes de datos compatibles como orígenes/receptores.

Data Factory solo admite actualmente el movimiento de datos de un servidor FTP a otros almacenes de datos, pero no viceversa. Admite servidores FTP tanto locales como en la nube.

Si va a mover datos desde un servidor FTP **local** a un almacén de datos de nube (ejemplo: Azure Blob Storage), instale y utilice la puerta de enlace de administración de datos. La puerta de enlace de administración de datos es un agente de cliente instalado en la máquina local, que permite a los servicios en la nube conectarse al recurso local. Consulte [Puerta de enlace de administración de datos](data-factory-data-management-gateway.md) para detalles sobre la puerta de enlace. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para instrucciones paso a paso sobre cómo configurar y usar la puerta de enlace. Utilice la puerta de enlace para conectarse a un servidor FTP, aunque este se encuentre en una máquina virtual de Azure IaaS.

Puede instalar la puerta de enlace en la misma máquina local o la máquina virtual de Azure IaaS como servidor FTP. Sin embargo, se recomienda que instale la puerta de enlace en una máquina o máquina virtual de Azure IaaS independiente para evitar la contención de recursos y mejorar el rendimiento. Al instalar la puerta de enlace en una máquina independiente, esta última debería poder acceder al servidor FTP.

## <a name="copy-data-wizard"></a>Asistente para copia de datos
La manera más sencilla de crear una canalización que copie datos de un servidor FTP es usar el Asistente para copia de datos. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>Ejemplo: copia de datos del servidor FTP a un blob de Azure
En este ejemplo se muestra cómo copiar datos un servidor FTP a Azure Blob Storage. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.  

El ejemplo consta de las siguientes entidades de factoría de datos:

* Un servicio vinculado de tipo [FtpServer](#ftp-linked-service-properties).
* Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)
* Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [FileShare](#fileshare-dataset-type-properties).
* Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
* Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [FileSystemSource](#ftp-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

En el ejemplo se copian los datos de servidor FTP a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado de FTP** En este ejemplo se utiliza la autenticación básica con nombre de usuario y contraseña en texto sin formato. También se puede hacer de una de las siguientes maneras:

* Autenticación anónima
* Autenticación básica con credenciales cifradas
* FTP sobre SSL/TLS (FTPS)

Para los diferentes tipos de autenticación que se pueden usar, consulte la sección [Propiedades del servicio vinculado de FTP](#ftp-linked-service-properties).

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
**Servicio vinculado de Almacenamiento de Azure**

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
**Conjunto de datos de entrada de FTP** Este conjunto de datos hace referencia a la carpeta FTP `mysharedfolder` y el archivo `test.csv`. La canalización copia el archivo en el destino.

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


**Canalización con actividad de copia**

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

## <a name="ftp-linked-service-properties"></a>Propiedades del servicio vinculado de FTP
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

Consulte [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para más información acerca de cómo establecer las credenciales para un origen de datos de FTP local.

[!INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de FTP
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, las propiedades de tipo varían en función de los tipos de orígenes y receptores.

[!INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes:

* [Tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para la creación de una canalización con una actividad de copia.



<!--HONumber=Nov16_HO3-->


