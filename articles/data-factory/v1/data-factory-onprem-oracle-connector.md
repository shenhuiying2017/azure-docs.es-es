---
title: Copia de datos con Oracle como origen o destino mediante Data Factory | Microsoft Docs
description: Aprenda a copiar datos con una base de datos de Oracle local como origen o destino mediante Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 82fe637b46decfc9c8d09b5c7e03f328a8636263
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Copia de datos con una instancia local Oracle como origen o destino mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](data-factory-onprem-oracle-connector.md)
> * [Versión 2: versión preliminar](../connector-oracle.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory, que está disponible con carácter general. Si usa la versión 2 del servicio Data Factory, que se encuentra en versión preliminar, vea [Oracle connector in V2](../connector-oracle.md) (Conector de Oracle en V2).


En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos con una base de datos de Oracle local como origen o destino. Se basa en la información general ofrecida en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md).

## <a name="supported-scenarios"></a>Escenarios admitidos
Puede copiar datos **de una base de datos de Oracle** a los siguientes almacenes de datos:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Puede copiar datos de los siguientes almacenes de datos **a una base de datos de Oracle**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>requisitos previos
Data Factory admite la conexión a orígenes de Oracle locales mediante la puerta de enlace de administración de datos. Consulte el artículo [Data Management Gateway](data-factory-data-management-gateway.md) para más información acerca de la puerta de enlace de administración de datos y el artículo [Movimiento de datos entre orígenes locales y la nube con la puerta de enlace de administración de datos](data-factory-move-data-between-onprem-and-cloud.md) para obtener instrucciones detalladas sobre cómo configurar la puerta de enlace de una canalización de datos para mover los datos.

La puerta de enlace es necesaria incluso si Oracle está hospedado en una máquina virtual de IaaS de Azure. Puede instalar la puerta de enlace en la misma máquina virtual de IaaS como almacén de datos o en una máquina virtual diferente, siempre y cuando la puerta de enlace se pueda conectar a la base de datos.

> [!NOTE]
> Consulte [Solución de problemas de la puerta de enlace](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obtener sugerencias para solucionar problemas de conexión o puerta de enlace.

## <a name="supported-versions-and-installation"></a>Versiones compatibles e instalación
Este conector de Oracle admite dos versiones de controladores:

- **Controlador de Microsoft para Oracle (recomendado)**: a partir de Data Management Gateway versión 2.7, un controlador de Microsoft para Oracle se instala automáticamente junto con la puerta de enlace, por lo que no será necesario realizar ningún control adicional en el controlador para establecer la conectividad con Oracle y podrá también experimentar un mejor rendimiento de copia mediante este controlador. Se admiten las siguientes versiones de bases de datos de Oracle:
    - Oracle 12c R1 (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10.2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

> [!IMPORTANT]
> Actualmente, el controlador de Microsoft para Oracle solo permite copiar datos de Oracle, pero no escribir en Oracle. Y tenga en cuenta que la funcionalidad de conexión de prueba de la pestaña Diagnósticos de Data Management Gateway. Como alternativa, puede usar al Asistente para copiar para validar la conectividad.
>

- **Proveedor de datos de Oracle para. NET:** también es posible usar el proveedor de datos de Oracle para copiar datos desde Oracle o en Oracle. Este componente se incluye en [Oracle Data Access Components for Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/) (Componentes de acceso a datos Oracle para Windows). Instale la versión adecuada (32/64 bits) en la máquina en la que está instalada la puerta de enlace. [Proveedor de datos de Oracle para NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) puede tener acceso a bases de datos Oracle 10g Release 2 o posterior.

    Si elige XCopy Installation (Instalación de XCopy), siga los pasos del archivo readme.htm. Se recomienda elegir el programa de instalación con la interfaz de usuario (no puede ser una de XCopy).

    Después de instalar el proveedor, **reinicie** el servicio de host de la puerta de enlace de administración de datos en la máquina con el applet Servicios o el Administrador de configuración de la puerta de enlace de administración de datos.  

Si utiliza el asistente para copia para crear la canalización de copia, el tipo de controlador se determinará automáticamente. El controlador de Microsoft se usará de forma predeterminada, a menos que la versión de la puerta de enlace sea inferior a 2.7 o elija Oracle como receptor.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con una actividad de copia que mueva datos desde una base de datos de Oracle local o hacia ella mediante el uso de diferentes herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

También puede usar las herramientas siguientes para crear una canalización: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **plantilla de Azure Resource Manager**, **API de .NET** y **API de REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia.

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Crear una **factoría de datos**. Una factoría de datos puede contener una o más canalizaciones. 
2. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos. Por ejemplo, si copia datos de una base de datos de Oracle a Azure Blob Storage, crea dos servicios vinculados para vincular la base de datos de Oracle y la cuenta de Azure Storage a su factoría de datos. Para conocer las propiedades de los servicios vinculados que son específicas de Oracle, consulte la sección [Propiedades del servicio vinculado](#linked-service-properties).
3. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia. En el ejemplo mencionado en el último paso, cree un conjunto de datos para especificar la tabla de la base de datos de Oracle que contiene los datos de entrada. Cree también otro conjunto de datos para especificar el contenedor de blobs y la carpeta que contiene los datos copiados de la base de datos de Oracle. Para conocer las propiedades del conjunto de datos que son específicas de Oracle, consulte la sección [Propiedades del conjunto de datos](#dataset-properties).
4. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida. En el ejemplo mencionado anteriormente, se usa OracleSource como origen y BlobSink como receptor para la actividad de copia. De igual forma, si realiza la copia de Azure Blob Storage a Oracle Database, usa BlobSource y OracleSink en la actividad de copia. Para conocer las propiedades de la actividad de copia que son específicas de la base de datos de Oracle, consulte la sección [Propiedades de la actividad de copia](#copy-activity-properties). Para obtener más información sobre cómo usar un almacén de datos como origen o receptor, haga clic en el vínculo de la sección anterior para el almacén de datos. 

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para ver ejemplos con definiciones de JSON de entidades de Data Factory que se usan para copiar datos a una base de datos de Oracle local o desde ella, consulte la sección [Ejemplos de JSON](#json-examples-for-copying-data-to-and-from-oracle-database) de este artículo.

Las secciones siguientes proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory:

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos al servicio vinculado de Oracle.

| Propiedad | DESCRIPCIÓN | Obligatorio |
| --- | --- | --- |
| Tipo |La propiedad type debe establecerse en: **OnPremisesOracle** |Sí |
| driverType | Especifique qué controlador usar para copiar datos en bases de datos de Oracle o desde ellas. Los valores permitidos son **Microsoft** u **ODP** (valor predeterminado). Consulte la sección [Versiones compatibles e instalación](#supported-versions-and-installation) para obtener información detallada sobre los controladores. | Sin  |
| connectionString | Especifique la información necesaria para conectarse a la instancia de Base de datos de Oracle para la propiedad connectionString. | Sí |
| gatewayName | Nombre de la puerta de enlace que se usa para conectarse al servidor de Oracle local |Sí |

**Ejemplo: Uso del controlador de Microsoft**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Ejemplo: uso del controlador de ODP**

Consulte [este sitio](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/) para conocer los formatos permitidos.

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (Oracle, Azure Blob, Azure Table, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo OracleTable tiene las propiedades siguientes:

| Propiedad | DESCRIPCIÓN | Obligatorio |
| --- | --- | --- |
| tableName |Nombre de la tabla en la instancia de Base de datos de Oracle a la que hace referencia el servicio vinculado. |No (si se especifica **oracleReaderQuery** de **OracleSource**) |

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

> [!NOTE]
> La actividad de copia toma solo una entrada y genera una única salida.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

### <a name="oraclesource"></a>OracleSource
En la actividad de copia, si el origen es de tipo **OracleSource**, están disponibles las propiedades siguientes en la sección **typeProperties**:

| Propiedad | DESCRIPCIÓN | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| oracleReaderQuery |Utilice la consulta personalizada para leer los datos. |Cadena de consulta SQL. Por ejemplo: select * from MyTable. <br/><br/>Si no se especifica, la instrucción SQL que se ejecuta es select * from MyTable. |No (si se especifica **tableName** de **dataset**) |

### <a name="oraclesink"></a>OracleSink
**OracleSink** admite las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| writeBatchTimeout |Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera. |timespan<br/><br/> Ejemplo: 00:30:00 (30 minutos). |Sin  |
| writeBatchSize |Inserta datos en la tabla SQL cuando el tamaño del búfer alcanza el valor writeBatchSize. |Entero (número de filas) |No (valor predeterminado: 100) |
| sqlWriterCleanupScript |Especifique una consulta para que se ejecute la actividad de copia de tal forma que se limpien los datos de un segmento específico. |Una instrucción de consulta. |Sin  |
| sliceIdentifierColumnName |Especifique el nombre de columna para que la rellene la actividad de copia con un identificador de segmentos generado automáticamente, que se usará para limpiar los datos de un segmento específico cuando se vuelva a ejecutar. |Nombre de columna de una columna con el tipo de datos binarios (32). |Sin  |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>Ejemplos de JSON para copiar datos a la base de datos de Oracle y desde esta
En el siguiente ejemplo, se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos desde una base de datos de Oracle a Azure Blob Storage y viceversa. Sin embargo, los datos se pueden copiar en cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Azure Data Factory.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Ejemplo: Copia de datos de Oracle a un blob de Azure

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado de tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) como origen y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) como receptor.

En el ejemplo de copian datos de una tabla de una base de datos de Oracle local en un blob cada hora. Para más información sobre las distintas propiedades usadas en el ejemplo, consulte la documentación de las secciones que vienen a continuación de los ejemplos.

**Servicio vinculado de Oracle:**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Servicio vinculado de Azure Blob Storage:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Conjunto de datos de entrada de Oracle:**

El ejemplo supone que ha creado una tabla "MyTable" en Oracle que contiene una columna denominada "timestampcolumn" para los datos de serie temporales.

Si se establece "external": "true", se informa al servicio Data Factory que el conjunto de datos es externo a la factoría de datos y que no lo genera ninguna actividad de la factoría de datos.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {     
            "externalData": {        
                "retryInterval": "00:01:00",    
                "retryTimeout": "00:10:00",       
                "maximumRetry": 3       
            }     
        }
    }
}
```

**Conjunto de datos de salida de blob de Azure:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **OracleSource** y el tipo **sink**, en **BlobSink**.  La consulta SQL especificada para la propiedad **oracleReaderQuery** selecciona los datos de la última hora que se van a copiar.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Ejemplo: Copia de datos de un blob de Azure a Oracle
En este ejemplo se muestra cómo se copian datos de Azure Blob Storage a una base de datos Oracle local. Sin embargo, se pueden copiar datos **directamente** desde cualquiera de los orígenes indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia de Azure Data Factory.  

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado de tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) como origen y [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) como receptor.

El ejemplo copia datos de un blob a una tabla de una base de datos de Oracle local cada hora. Para más información sobre las distintas propiedades usadas en el ejemplo, consulte la documentación de las secciones que vienen a continuación de los ejemplos.

**Servicio vinculado de Oracle:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Servicio vinculado Azure Blob Storage:**
```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Conjunto de datos de entrada de blob de Azure**

Los datos se seleccionan de un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa la parte year, month y day de la hora de inicio y el nombre de archivo, la parte hour. El valor "external": "true" informa al servicio Data Factory que esta tabla es externa a la factoría de datos y no la produce una actividad de la factoría de datos.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Conjunto de datos de salida de Oracle:**

En el ejemplo se supone que ha creado una tabla "MyTable" en Oracle. Cree la tabla en Oracle con el mismo número de columnas que espera que contenga el archivo CSV de Blob. Se agregan nuevas filas a la tabla cada hora.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **BlobSource** y el tipo **sink**, en **OracleSink**.  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```


## <a name="troubleshooting-tips"></a>Sugerencias de solución de problemas
### <a name="problem-1-net-framework-data-provider"></a>Problema 1: proveedor de datos .NET Framework

Se muestra el siguiente **mensaje de error**:

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**Causas posibles:**

1. No se instaló el proveedor de datos de .NET Framework para Oracle.
2. El proveedor de datos de .NET Framework para Oracle se instaló en .NET Framework 2.0 y no se encuentra en las carpetas de .NET Framework 4.0.

**Resolución o solución alternativa:**

1. Si no ha instalado el proveedor de .NET para Oracle, [instálelo](http://www.oracle.com/technetwork/topics/dotnet/downloads/) e intente de nuevo este escenario.
2. Si recibe el mensaje de error incluso después de instalar el proveedor, lleve a cabo los siguientes pasos:
   1. Abra la configuración de máquina de .NET 2.0 desde la carpeta: <system disk>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Busque **Proveedor de datos de Oracle para .NET**; debería encontrar una entrada tal y como se muestra en el siguiente ejemplo, en **system.data** -> **DbProviderFactories**: “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Proveedor de datos de Oracle para .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Copie esta entrada en el archivo machine.config en la siguiente carpeta v4.0: <system disk>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config, y cambie la versión a 4.xxx.x.x.
4. Instale “<Ruta de instalación de ODP.NET>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll” en la caché global de ensamblados (GAC) ejecutando `gacutil /i [provider path]`.## Sugerencias de solución de problemas

### <a name="problem-2-datetime-formatting"></a>Problema 2: formato de fecha y hora

Se muestra el siguiente **mensaje de error**:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Resolución o solución alternativa:**

Es posible que deba ajustar la cadena de consulta en la actividad de copia en función de cómo estén configuradas las fechas en la base de datos de Oracle, tal y como se muestra en el ejemplo siguiente (con la función to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Asignación de tipos para Oracle
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos de Oracle, se usan las siguientes asignaciones del tipo de datos de Oracle al tipo de .NET, y viceversa.

| Tipo de datos de Oracle | Tipo de datos de .NET Framework |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(solo se admite en Oracle 10g y versiones posteriores al utilizar el controlador de Microsoft) |
| CHAR |string |
| CLOB |string |
| DATE |Datetime |
| FLOAT |Decimal, String (si la precisión > 28) |
| INTEGER |Decimal, String (si la precisión > 28) |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |timespan |
| LONG |string |
| LONG RAW |Byte[] |
| NCHAR |string |
| NCLOB |string |
| NUMBER |Decimal, String (si la precisión > 28) |
| NVARCHAR2 |string |
| RAW |Byte[] |
| ROWID |string |
| TIMESTAMP |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |Datetime |
| TIMESTAMP WITH TIME ZONE |Datetime |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |string |
| XML |string |

> [!NOTE]
> Los tipos de datos **INTERVAL YEAR TO MONTH** e **INTERVAL DAY TO SECOND** no se admiten cuando se utiliza el controlador de Microsoft.

## <a name="map-source-to-sink-columns"></a>Asignación de columnas de origen a columnas de receptor
Para obtener más información sobre la asignación de columnas del conjunto de datos de origen a las del conjunto de datos receptor, consulte [Asignación de columnas de conjunto de datos de Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lectura repetible de orígenes relacionales
Cuando se copian datos desde almacenes de datos relacionales, hay que tener presente la repetibilidad para evitar resultados imprevistos. En Azure Data Factory, puede volver a ejecutar un segmento manualmente. También puede configurar la directiva de reintentos para un conjunto de datos con el fin de que un segmento se vuelva a ejecutar cuando se produce un error. Cuando se vuelve a ejecutar un segmento, debe asegurarse de que los mismos datos se lean sin importar el número de ejecuciones. Consulte [Lectura repetible de orígenes relacionales](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.
