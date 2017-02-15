---
title: "Movimiento de datos desde orígenes de datos ODBC | Microsoft Docs"
description: "Obtenga información sobre cómo mover datos desde almacenes de datos ODBC mediante Factoría de datos de Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 4d23fb04a238656128447e21772ca92069424440


---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Movimiento de datos desde almacenes de datos ODBC mediante Factoría de datos de Azure
En este artículo se describe cómo se puede usar la actividad de copia en Factoría de datos de Azure para mover datos de un almacén de datos ODBC local a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

Data Factory solo admite actualmente el movimiento de datos desde un almacén de datos ODBC local a otros almacenes de datos. No admite el traslado de datos desde otros almacenes de datos a un almacén de datos ODBC local.

## <a name="enabling-connectivity"></a>Habilitación de la conectividad
El servicio Factoría de datos admite la conexión a orígenes ODBC locales mediante Data Management Gateway. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace. Use la puerta de enlace para conectar con un almacén de datos ODBC, incluso si está hospedado en una máquina virtual de IaaS de Azure.

Puede instalar la puerta de enlace en el mismo equipo local o en la máquina virtual de Azure como almacén de datos ODBC. Sin embargo, se recomienda que instale la puerta de enlace en un equipo o máquina virtual IaaS de Azure independiente para evitar la contención de recursos y mejorar el rendimiento. Al instalar la puerta de enlace en una máquina independiente, la máquina debería poder acceder a la máquina con el almacén de datos ODBC.

Aparte de Data Management Gateway, también debe instalar el controlador ODBC para el almacén de datos en la máquina de puerta de enlace.

> [!NOTE]
> Consulte [Solución de problemas de la puerta de enlace](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obtener sugerencias para solucionar problemas de conexión o puerta de enlace.
>
>

## <a name="copy-data-wizard"></a>Asistente para copia de datos
La manera más sencilla de crear una canalización que copie datos hacia o desde uno origen ODBC es usar el Asistente para copia de datos. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). En ellos se muestra cómo copiar datos de un origen ODBC local a Almacenamiento de blobs de Azure. Sin embargo, los datos se pueden copiar en cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.

## <a name="sample-copy-data-from-odbc-data-store-to-azure-blob"></a>Ejemplo: copiar datos del almacén de datos ODBC a un blob de Azure
En este ejemplo, se muestra cómo copiar datos de un almacén de datos ODBC al Almacenamiento de blobs de Azure. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.  

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado del tipo [OnPremisesOdbc](#odbc-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](#odbc-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](#odbc-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia los datos del resultado de una consulta en un almacén de datos ODBC en un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

En primer lugar, configure la puerta de enlace de administración de datos. Las instrucciones se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

**Servicio vinculado de ODBC** En este ejemplo se usa la autenticación básica. Consulte la sección [Propiedades del servicio vinculado de ODBC](#odbc-linked-service-properties) para conocer los diferentes tipos de autenticación que se pueden usar.

    {
        "name": "OnPremOdbcLinkedService",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
                "userName": "username",
                "password": "password",
                "gatewayName": "mygateway"
            }
        }
    }

**Servicio vinculado de Almacenamiento de Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de datos de entrada de ODBC**

El ejemplo asume que ha creado una tabla, "MyTable", en una base de datos ODBC y que contiene una columna denominada "timestampcolumn" para los datos de series temporales.

Si se establece "external": "true", se informa al servicio Data Factory que el conjunto de datos es externo a Data Factory y que no lo genera ninguna actividad de la factoría de datos.

    {
        "name": "ODBCDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremOdbcLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }



**Conjunto de datos de salida de blob de Azure**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

    {
        "name": "AzureBlobOdbcDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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



**Canalización con actividad de copia**

La canalización contiene una actividad de copia que está configurada para usar estos conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la última hora que se van a copiar.

    {
        "name": "CopyODBCToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "OdbcDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOdbcDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "OdbcToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="odbc-linked-service-properties"></a>Propiedades del servicio vinculado de ODBC
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de ODBC.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type tiene que establecerse en: **OnPremisesOdbc** |Sí |
| connectionString |La parte de la credencial de no acceso de la cadena de conexión, así como una credencial cifrada opcional. Vea ejemplos en las secciones siguientes. |Sí |
| credential |La parte de la credencial de acceso de la cadena de conexión especificada en formato de valor de propiedad específico del controlador. Ejemplo: “Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”. |No |
| authenticationType |Tipo de autenticación que se usa para conectarse al almacén de datos ODBC. Los valores posibles son: Anonymous y Basic. |Sí |
| nombre de usuario |Especifique el nombre de usuario si usa la autenticación básica. |No |
| contraseña |Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. |No |
| gatewayName |Nombre de la puerta de enlace que el servicio Factoría de datos debe usar para conectarse al almacén de datos ODBC. |yes |

Para más información acerca de cómo establecer las credenciales de un almacén de datos ODBC local, consulte [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-authentication"></a>Uso de la autenticación básica
    {
        "name": "odbc",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
                "userName": "username",
                "password": "password",
                "gatewayName": "mygateway"
            }
        }
    }

### <a name="using-basic-authentication-with-encrypted-credentials"></a>Uso de la autenticación básica con credenciales cifradas
Las credenciales se pueden cifrar mediante el cmdlet [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) (versión 1.0 de Azure PowerShell) o [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (versión 0.9 o una versión anterior de Azure PowerShell).  

    {
        "name": "odbc",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
                "gatewayName": "mygateway"
            }
        }
    }


### <a name="using-anonymous-authentication"></a>Uso de autenticación anónima
    {
        "name": "odbc",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
                "credential": "UID={uid};PWD={pwd}",
                "gatewayName": "mygateway"
            }
        }
    }



## <a name="odbc-dataset-type-properties"></a>Propiedades del tipo de conjunto de datos ODBC
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos del tipo **RelationalTable** (que incluye el conjunto de datos ODBC) tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| tableName |Nombre de la tabla en el almacén de datos ODBC. |Sí |

## <a name="odbc-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de ODBC
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

En la actividad de copia, si el origen es del tipo **RelationalSource** (que incluye ODBC), las propiedades siguientes están disponibles en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| query |Utilice la consulta personalizada para leer los datos. |Cadena de consulta SQL. Por ejemplo: select * from MyTable. |Sí |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-odbc"></a>Asignación de tipos para ODBC
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos desde almacenes de datos ODBC, los tipos de datos ODBC se asignan a tipos de .NET, tal y como se mencionó en el tema [Asignar tipos de datos ODBC](https://msdn.microsoft.com/library/cc668763.aspx) .

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="ge-historian-store"></a>Almacén GE Historian
Cree un servicio vinculado ODBC para vincular un almacén de datos [GE Proficy Historian (ahora GE Historian)](http://www.geautomation.com/products/proficy-historian) a Data Factory de Azure, tal y como se muestra en el ejemplo siguiente:

    {
        "name": "HistorianLinkedService",
        "properties":
        {
            "type": "OnPremisesOdbc",
            "typeProperties":
            {
                "connectionString": "DSN=<name of the GE Historian store>",
                "gatewayName": "<gateway name>",
                "authenticationType": "Basic",
                "userName": "<user name>",
                "password": "<password>"
            }
        }
    }

Debe instalar Data Management Gateway en un equipo local y registrar la puerta de enlace con el portal. La puerta de enlace instalada en su equipo local utiliza el controlador ODBC para que GE Historian se conecte al almacén de datos GE Historian. Por lo tanto, instale el controlador si no está instalado aún en el equipo de puerta de enlace. Consulte la sección [Habilitación de la conectividad](#enabling-connectivity) para más información.

Antes de usar el almacén GE Historian en una solución de Data Factory, compruebe si la puerta de enlace puede conectarse al almacén de datos mediante instrucciones en la sección siguiente.

Lea el artículo desde el principio para obtener información general detallada de uso de almacenes de datos ODBC como almacenes de datos de origen en una operación de copia.  

## <a name="troubleshoot-connectivity-issues"></a>Solución de problemas de conectividad
Use la pestaña **Diagnósticos** del **Administrador de configuración de Data Management Gateway**para solucionar problemas de conexión.

1. Inicie el **Administrador de configuración de Data Management Gateway**. Puede ejecutar C:\Archivos de programa\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe directamente, o bien buscar **Gateway** para encontrar un vínculo a la aplicación **Microsoft Data Management Gateway**, tal y como se muestra en la imagen siguiente.

    ![Buscar puerta de enlace](./media/data-factory-odbc-connector/search-gateway.png)
2. Cambie a la pestaña **Diagnósticos** .

    ![Diagnóstico de puerta de enlace](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Seleccione el **tipo** de almacén de datos (el servicio vinculado).
4. Especifique la **autenticación** y escriba las **credenciales**, o bien escriba la **cadena de conexión** que se usa para conectarse al almacén de datos.
5. Haga clic en **Probar conexión** para probar la conexión con el almacén de datos.

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.



<!--HONumber=Nov16_HO3-->


