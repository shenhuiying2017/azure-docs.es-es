---
title: Traslado de datos con Oracle como origen o destino mediante Data Factory | Microsoft Docs
description: "Obtenga información acerca de cómo mover los datos hacia y desde la base de datos de Oracle local mediante Factoría de datos de Azure."
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
ms.date: 01/22/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: a4b067e732bccb01faa96f23dbfd2ed65b7711a0
ms.openlocfilehash: 62326da2e801a7c6e01d29e2298bd3552f331647
ms.lasthandoff: 02/03/2017


---
# <a name="move-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Transferencia de datos de/a Oracle local mediante Data Factory de Azure
En este artículo se describe cómo se usa la actividad de copia de Data Factory para transferir datos de Oracle a otro almacén de datos y viceversa. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

Data Factory admite la conexión a orígenes de Oracle locales mediante la puerta de enlace de administración de datos. Consulte el artículo [Data Management Gateway](data-factory-data-management-gateway.md) para más información acerca de la puerta de enlace de administración de datos y el artículo [Movimiento de datos entre orígenes locales y la nube con la puerta de enlace de administración de datos](data-factory-move-data-between-onprem-and-cloud.md) para obtener instrucciones detalladas sobre cómo configurar la puerta de enlace de una canalización de datos para mover los datos.

> [!NOTE]
> La puerta de enlace es necesaria incluso si Oracle está hospedado en una máquina virtual de IaaS de Azure. Puede instalar la puerta de enlace en la misma máquina virtual de IaaS como almacén de datos o en una máquina virtual diferente, siempre y cuando la puerta de enlace se pueda conectar a la base de datos.
>

## <a name="supported-versions-and-installation"></a>Versiones compatibles e instalación
El conector de Oracle admite dos versiones de controladores:

- El **controlador de Microsoft para Oracle** se incluye con la puerta de enlace de administración de datos a partir de la versión 2.7. Se **recomienda** utilizar este controlador. Con este controlador, tan solo es necesario instalar la puerta de enlace para conectarse a Oracle y, además, mejora el rendimiento de la copia. Es compatible con bases de datos Oracle 10g Release 2 o versiones posteriores.

    > [!NOTE]
    > Actualmente, el controlador de Microsoft para Oracle solo permite copiar datos de Oracle, pero no escribir en Oracle. Y tenga en cuenta que la funcionalidad de conexión de prueba de la pestaña Diagnósticos de Data Management Gateway. Como alternativa, puede usar al Asistente para copiar para validar la conectividad.
    >

- **Proveedor de datos de Oracle para. NET:** también es posible usar el proveedor de datos de Oracle para copiar datos desde Oracle o en Oracle. Este componente se incluye en [Oracle Data Access Components for Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/) (Componentes de acceso a datos Oracle para Windows). Instale la versión adecuada (32/64 bits) en la máquina en la que está instalada la puerta de enlace. [Proveedor de datos de Oracle para NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) puede tener acceso a bases de datos Oracle 10g Release 2 o posterior.

    Si elige XCopy Installation (Instalación de XCopy), siga los pasos del archivo readme.htm. Se recomienda elegir el programa de instalación con la interfaz de usuario (no puede ser una de XCopy).

    Después de instalar el proveedor, **reinicie** el servicio de host de la puerta de enlace de administración de datos en la máquina con el applet Servicios o el Administrador de configuración de la puerta de enlace de administración de datos.  

## <a name="copy-data-wizard"></a>Asistente para copia de datos
La manera más fácil de crear una canalización que copie datos de una base de datos Oracle a cualquiera de los almacenes de datos receptores admitidos y viceversa es usar el Asistente para copiar datos. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

En el siguiente ejemplo, se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos desde una base de datos de Oracle al almacenamiento de blobs de Azure y viceversa. Sin embargo, los datos se pueden copiar en cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.   

## <a name="sample-copy-data-from-oracle-to-azure-blob"></a>Ejemplo: copia de datos de Oracle a un blob de Azure
En este ejemplo, se muestra cómo copiar datos de una base de datos Oracle local a un Almacenamiento de blobs de Azure. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.  

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado de tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) como origen y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) como receptor.

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

**Servicio vinculado de almacenamiento de blobs de Azure:**

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

Si se establece "external": "true", se informa al servicio Data Factory que el conjunto de datos es externo a Data Factory y que no lo genera ninguna actividad de la factoría de datos.

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

## <a name="sample-copy-data-from-azure-blob-to-oracle"></a>Ejemplo: copia de datos de un blob de Azure a Oracle
En este ejemplo se muestra cómo se copian datos de un Almacenamiento de blobs de Azure a una base de datos Oracle local. Sin embargo, se pueden copiar datos **directamente** desde cualquiera de los orígenes indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia de Data Factory de Azure.  

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado de tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) como origen y [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) como receptor.

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

**Servicio vinculado de almacenamiento de blobs de Azure:**
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

Los datos se seleccionan de un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa la parte year, month y day de la hora de inicio y el nombre de archivo, la parte hour. El valor "external": "true" informa al servicio Data Factory que esta tabla es externa a la factoría y no la produce una actividad de la factoría de datos.

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

## <a name="oracle-linked-service-properties"></a>Propiedades del servicio vinculado de Oracle
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos al servicio vinculado de Oracle.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type debe establecerse en: **OnPremisesOracle** |Sí |
| driverType | Especifique qué controlador usar para copiar datos en bases de datos de Oracle o desde ellas. Los valores permitidos son **Microsoft** u **ODP** (valor predeterminado). Consulte la sección [Versiones compatibles e instalación](#supported-versions-and-installation) para obtener información detallada sobre los controladores. | No |
| connectionString | Especifique la información necesaria para conectarse a la instancia de Base de datos de Oracle para la propiedad connectionString. Vea los siguientes ejemplos. | Sí |
| gatewayName | Nombre de la puerta de enlace que se usa para conectarse al servidor de Oracle local |Sí |

Consulte [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información sobre cómo establecer las credenciales para un origen de datos de Oracle local.

**Ejemplo: uso del controlador de Microsoft**
```JSON
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

Visite [este sitio](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/) para ver otros formatos permitidos.
```JSON
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

## <a name="oracle-dataset-type-properties"></a>Propiedades del tipo de conjunto de datos de Oracle
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (Oracle, Azure Blob, Azure Table, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo OracleTable tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| tableName |Nombre de la tabla en la instancia de Base de datos de Oracle a la que hace referencia el servicio vinculado. |No (si se especifica **oracleReaderQuery** de **OracleSource**) |

## <a name="oracle-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de Oracle
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

> [!NOTE]
> La actividad de copia toma solo una entrada y genera una única salida.
>
>

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

### <a name="oraclesource"></a>OracleSource
En la actividad de copia, si el origen es de tipo **OracleSource**, están disponibles las propiedades siguientes en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| oracleReaderQuery |Utilice la consulta personalizada para leer los datos. |Cadena de consulta SQL. Por ejemplo: select * from MyTable <br/><br/>Si no se especifica, la instrucción SQL que se ejecuta: select * from MyTable |No (si se especifica **tableName** de **dataset**) |

### <a name="oraclesink"></a>OracleSink
**OracleSink** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| writeBatchTimeout |Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera. |timespan<br/><br/> Ejemplo: 00:30:00 (30 minutos). |No |
| writeBatchSize |Inserta datos en la tabla SQL cuando el tamaño del búfer alcanza el valor writeBatchSize. |Entero (número de filas) |No (valor predeterminado: 100) |
| sqlWriterCleanupScript |Especifique una consulta para que se ejecute la actividad de copia de tal forma que se limpien los datos de un segmento específico. |Una instrucción de consulta. |No |
| sliceIdentifierColumnName |Especifique el nombre de columna para que la rellene la actividad de copia con un identificador de segmentos generado automáticamente, que se usará para limpiar los datos de un segmento específico cuando se vuelva a ejecutar. |Nombre de columna de una columna con el tipo de datos binarios (32). |No |

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


[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-oracle"></a>Asignación de tipos para Oracle
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos de Oracle, se usan las siguientes asignaciones del tipo de datos de Oracle al tipo de .NET, y viceversa.

| Tipo de datos de Oracle | Tipo de datos de .NET Framework |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[] |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal |
| INTEGER |Decimal |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |timespan |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |DateTime |
| TIMESTAMP WITH TIME ZONE |DateTime |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Los tipos de datos **INTERVAL YEAR TO MONTH** e **INTERVAL DAY TO SECOND** no se admiten cuando se utiliza el controlador de Microsoft.
>

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.

