---
title: "Movimiento de datos desde orígenes OData | Microsoft Docs"
description: "Obtenga información sobre cómo mover datos desde orígenes de OData mediante Factoría de datos de Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 4521a236bfc13e6aca7e13e7400c11d353bc3a66
ms.openlocfilehash: 9c385adfa3da73bef2d05352049d1f71aa5c5847


---
# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>Movimiento de datos de un origen de OData mediante Factoría de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia en Factoría de datos de Azure para mover datos de un origen de OData a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

## <a name="supported-versions-and-authentication-types"></a>Versiones admitidas y tipos de autenticación
Este conector OData admite la versión de OData 3.0 y 4.0, y puede copiar datos de orígenes OData locales y en la nube. Por último, debe instalar Data Management Gateway. Consulte el artículo [Mover datos entre orígenes locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información acerca de Data Management Gateway.

Estos son los tipos de autenticación admitidos:

* Para acceder a la fuente OData **en la nube**, puede usar la autenticación anónima y básica (nombre de usuario y contraseña), o la autenticación OAuth basada en Azure Active Directory.
* Para acceder a la fuente OData **local**, puede usar la autenticación anónima y básica (nombre de usuario y contraseña), o la autenticación de Windows.

## <a name="copy-data-wizard"></a>Asistente para copia de datos
La manera más sencilla de crear una canalización que copie datos hacia o desde un origen OData es usar el Asistente para copia de datos. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). En ellos se muestra cómo copiar datos de un origen de OData en Almacenamiento de blobs de Azure. Sin embargo, los datos se pueden copiar en cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.

## <a name="sample-copy-data-from-odata-source-to-azure-blob"></a>Ejemplo: copia de datos de un origen de OData a un blob de Azure
En este ejemplo se muestra cómo copiar datos de un origen de OData en Almacenamiento de blobs de Azure. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.  

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado de tipo [OData](#odata-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [ODataResource](#odata-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](#odata-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

En el ejemplo se copian los datos de la consulta en un origen de OData a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado de OData** En este ejemplo se usa la autenticación anónima. Consulte la sección [Propiedades del servicio vinculado de OData](#odata-linked-service-properties) para conocer los diferentes tipos de autenticación que se pueden usar.

    {
        "name": "ODataLinkedService",
           "properties":
        {
            "type": "OData",
               "typeProperties":
            {
                "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
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

**Conjunto de datos de entrada de OData**

Si se establece "external": "true", se informa al servicio Data Factory que el conjunto de datos es externo a Data Factory y que no lo genera ninguna actividad de la factoría de datos.

    {
        "name": "ODataDataset",
        "properties":
        {
            "type": "ODataResource",
            "typeProperties":
            {
                 "path": "Products"
            },
            "linkedServiceName": "ODataLinkedService",
            "structure": [],
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3                
            }
        }
    }

La especificación de **path** en la definición del conjunto de datos es opcional.

**Conjunto de datos de salida de blob de Azure**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

    {
        "name": "AzureBlobODataDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los últimos datos (los más recientes) del origen de OData.

    {
        "name": "CopyODataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "?$select=Name, Description&$top=5",
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "ODataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobODataDataSet"
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
                    "name": "ODataToBlob"
                }
            ],
            "start": "2016-02-01T18:00:00Z",
            "end": "2016-02-03T19:00:00Z"
        }
    }


La especificación de **query** en la definición de la canalización es opcional. Dirección **URL** que el servicio Data Factory usa para recuperar los datos: dirección URL especificada en el servicio vinculado (obligatorio) + ruta de acceso especificada en el conjunto de datos (opcional) + consulta en la canalización (opcional).

## <a name="odata-linked-service-properties"></a>Propiedades del servicio vinculado de OData
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de OData.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type debe establecerse en **OData** |Sí |
| URL |Dirección URL del servicio de OData. |Sí |
| authenticationType |Tipo de autenticación que se usa para conectarse al origen de OData. <br/><br/> Para OData en la nube, los valores posibles son Anonymous, Basic y OAuth (tenga en cuenta que Azure Data Factory en estos momentos solo admite la autenticación OAuth basada en Azure Active Directory). <br/><br/> Para OData local, los valores posibles son: Anonymous, Basic y Windows. |Sí |
| nombre de usuario |Especifique el nombre de usuario si usa la autenticación básica. |Sí (solo si usa la autenticación básica) |
| contraseña |Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. |Sí (solo si usa la autenticación básica) |
| authorizedCredential |Si está usando OAuth, haga clic en el botón **Autorizar** del Editor o el Asistente para copiar de Data Factory y escriba su credencial. Después, el valor de esta propiedad se generará automáticamente. |Sí (solo si usa la autenticación OAuth) |
| gatewayName |Nombre de la puerta de enlace que debe usar el servicio Factoría de datos para conectarse al servicio de OData local. Especifique solo si va a copiar datos del origen de OData local. |No |

### <a name="using-basic-authentication"></a>Uso de la autenticación básica
    {
        "name": "inputLinkedService",
        "properties":
        {
            "type": "OData",
               "typeProperties":
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
               "username": "username",
               "password": "password"
           }
       }
    }

### <a name="using-anonymous-authentication"></a>Uso de autenticación anónima
    {
        "name": "ODataLinkedService",
           "properties":
        {
            "type": "OData",
            "typeProperties":
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Uso de la autenticación de Windows para acceder al origen de OData local
    {
        "name": "inputLinkedService",
        "properties":
        {
            "type": "OData",
               "typeProperties":
            {
               "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
               "authenticationType": "Windows",
               "username": "domain\\user",
               "password": "password",
               "gatewayName": "mygateway"
           }
       }
    }

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Uso de la autenticación OAuth para tener acceso al origen de OData en la nube
    {
        "name": "inputLinkedService",
        "properties":
        {
            "type": "OData",
               "typeProperties":
            {
               "url": "<endpoint of cloud OData source e.g. https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc">",
               "authenticationType": "OAuth",
               "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
           }
       }
    }

## <a name="odata-dataset-type-properties"></a>Propiedades del tipo de conjunto de datos de OData
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos del tipo **ODataResource** (que incluye el conjunto de datos de OData) tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| path |Ruta de acceso al recurso de OData |No |

## <a name="odata-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de OData
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

Cuando la actividad de copia es de tipo **RelationalSource** (lo que incluye OData), están disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Ejemplo | Obligatorio |
| --- | --- | --- | --- |
| query |Utilice la consulta personalizada para leer los datos. |"?$select=Name, Description&$top=5" |No |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-odata"></a>Asignación de tipos para OData
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Cuando se mueven datos desde almacenes de datos de OData, los tipos de datos de OData se asignan a tipos. NET.

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.



<!--HONumber=Dec16_HO2-->


