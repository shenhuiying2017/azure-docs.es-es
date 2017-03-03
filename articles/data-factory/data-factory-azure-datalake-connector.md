---
title: Movimiento de datos hacia y desde Azure Data Lake Store | Microsoft Docs
description: "Obtenga información sobre cómo mover datos hacia y desde el almacén de Azure Data Lake mediante la Factoría de datos de Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b2d1a740782a20a7c6b7b8cec8335a41f16231f5
ms.openlocfilehash: 5a6a14e5fc8f6915b34f9667c4294a46c8591633
ms.lasthandoff: 02/09/2017


---
# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>Movimiento de datos hacia y desde el almacén de Azure Data Lake mediante la Factoría de datos de Azure
En este artículo se describe cómo se puede usar la actividad de copia en una factoría de datos de Azure para mover datos entre Azure Data Lake Store y otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que presenta información general sobre el movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

> [!NOTE]
> Antes de crear una canalización con una actividad de copia para mover datos hacia y desde un almacén de Azure Data Lake, cree una cuenta de Azure Data Lake Store. Para más información sobre Azure Data Lake Store, consulte la [introducción a Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md).
>

## <a name="supported-authentication-types"></a>Tipos de autenticación que se admiten
El conector de Azure Data Lake Store admite la autenticación de **entidad de servicio** y la autenticación de **credenciales de usuario**. Se recomienda utilizar el primero, especialmente para la copia de datos programada, con el fin de evitar el comportamiento de expiración del token del último. En la sección [Propiedades del servicio vinculado de Almacén de Azure Data Lake](#azure-data-lake-store-linked-service-properties), puede ver los detalles de la configuración.

## <a name="copy-data-wizard"></a>Asistente para copia de datos
La manera más sencilla de crear una canalización que copie datos hasta o desde Azure Data Lake Store es usar el Asistente para copia de datos. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Muestran cómo copiar datos entre Azure Data Lake Store y el Almacenamiento de blobs de Azure. En cambio, los datos pueden copiarse **directamente** desde cualquiera de los orígenes a cualquiera de los receptores admitidos. Para más información, vea la sección "Almacenes de datos y formatos que se admiten" del artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md).  

## <a name="sample-copy-data-from-azure-blob-to-azure-data-lake-store"></a>Ejemplo: copia de datos de un blob de Azure al almacén de Azure Data Lake
El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [AzureStorage](#azure-storage-linked-service-properties)
2. Un servicio vinculado de tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](#azure-blob-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
5. Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que usa [BlobSource](#azure-blob-copy-activity-type-properties) y [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties).

El ejemplo copia los datos de la serie temporal desde una instancia de Almacenamiento de blobs de Azure en Azure Data Lake Store cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado de Almacenamiento de Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Servicio vinculado de Azure Data Lake:**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Vea los pasos en la sección [Propiedades del servicio vinculado de Almacén de Azure Data Lake](#azure-data-lake-store-linked-service-properties) con los detalles de la configuración.
>

**Conjunto de datos de entrada de blob de Azure:**

Los datos se seleccionan de un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa la parte year, month y day de la hora de inicio y el nombre de archivo, la parte hour. El valor "external": "true" informa al servicio Data Factory de que la tabla es externa a la factoría de datos y no la produce ninguna actividad de dicha factoría.

```JSON
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
    "external": true,
    "availability": {
      "frequency": "Hour",
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

**Conjunto de datos de salida de Azure Data Lake:**

El ejemplo copia los datos en un almacén de Azure Data Lake. Los nuevos datos se copian en el almacén de Data Lake cada hora.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
      "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
              "frequency": "Hour",
              "interval": 1
        }
      }
}
```


**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de la canalización, el tipo **source** está establecido en **BlobSource** y el tipo **sink** en **AzureDataLakeStoreSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":
    {  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [  
              {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureBlobInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureDataLakeStoreOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                      },
                      "sink": {
                        "type": "AzureDataLakeStoreSink"
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

## <a name="sample-copy-data-from-azure-data-lake-store-to-azure-blob"></a>Ejemplo: copia de datos del almacén de Azure Data Lake a un blob de Azure
El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](#azure-storage-linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](#azure-blob-dataset-type-properties).
5. Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que usa [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) y [BlobSink](#azure-blob-copy-activity-type-properties)

El ejemplo copia los datos de la serie temporal desde un almacén de Azure Data Lake a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado del almacén de Azure Data Lake:**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Vea los pasos en la sección [Propiedades del servicio vinculado de Almacén de Azure Data Lake](#azure-data-lake-store-linked-service-properties) con los detalles de la configuración.
>

**Servicio vinculado de Almacenamiento de Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de datos de entrada de Azure Data Lake:**

Si se establece **"external": true** , se informa al servicio Data Factory de que la tabla es externa a la factoría de datos y no la produce ninguna actividad de dicha factoría.

```JSON
{
    "name": "AzureDataLakeStoreInput",
      "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
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
**Conjunto de datos de salida de blob de Azure:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```JSON
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

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **AzureDataLakeStoreSource** y el tipo **sink** en **BlobSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureDataLakeStoreInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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

## <a name="azure-data-lake-store-linked-service-properties"></a>Propiedades del servicio vinculado de Almacén de Azure Data Lake
En la tabla siguiente se proporciona una descripción de los elementos JSON específicos del servicio vinculado Azure Data Lake Store y puede elegir entre autenticación de **entidad de servicio** y de **credenciales de usuario**.

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **AzureDataLakeStore** | Sí |
| dataLakeStoreUri | Especifica información sobre la cuenta de Almacén de Azure Data Lake. Tiene el formato siguiente: **https://[nombredecuenta].azuredatalakestore.net/webhdfs/v1** o **adl://[nombredecuenta].azuredatalakestore.net/**. | Sí |
| subscriptionId | Identificador de suscripción de Azure al que pertenece Data Lake Store. | Necesario para el receptor |
| resourceGroupName | Nombre del grupo de recursos de Azure al que pertenece Data Lake Store. | Necesario para el receptor |

### <a name="using-service-principal-authentication-recommended"></a>Uso de la autenticación de entidad de servicio (se recomienda)
Para usar la autenticación de entidad de servicio, en primer lugar, es preciso registrar una entidad de la aplicación en Azure Active Directory (AAD) y concederle acceso en Data Lake Store. Después, se pueden especificar las propiedades siguientes en Azure Data Factory con el identificador de aplicación correspondiente, información de clave e inquilino de aplicación para copiar datos desde Data Lake Store o a este. Consulte [Autenticación entre servicios con Data Lake Store mediante Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) para averiguar cómo configurarlo y recuperar la información necesaria.

> [!IMPORTANT]
> Cuando use el asistente para copia, asegúrese de conceder a la entidad de servicio al menos el permiso de lectura para la raíz ADLS ("/") o el rol de lector para la cuenta ADLS, para poder navegar correctamente entre las carpetas. De lo contrario, puede que aparezca el error "Las credenciales proporcionadas no son válidas".
>
> Si ha creado o actualizado recientemente una entidad de servicio desde AAD, puede tardar unos minutos en entrar en vigor. Primero, compruebe la entidad de servicio y la configuración ACL de ADLS: si todavía ve el error que dice "Las credenciales proporcionadas no son válidas", espere unos instantes y vuelva a intentarlo.
>

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique el id. de cliente de la aplicación. | Sí |
| servicePrincipalKey | Especifique la clave de la aplicación. | Sí |
| tenant | Especifique la información del inquilino (nombre de dominio o identificador de inquilino) en el que reside la aplicación. Para recuperarlo, mantenga el puntero del mouse en la esquina superior derecha de Azure Portal. | Sí |

**Ejemplo: uso de la autenticación de entidad de servicio**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="using-user-credential-authentication"></a>Uso de la autenticación de credenciales de usuario
Como alternativa, puede utilizar la autenticación de credenciales de usuario para realizar copias a Data Lake Store, o desde este, mediante la especificación de propiedades siguientes.

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| authorization | Haga clic en el botón **Autorizar** de **Data Factory Editor** y escriba sus credenciales; de esta forma, se asigna la dirección URL de autorización generada automáticamente a esta propiedad. | Sí |
| sessionId | Id. de sesión de OAuth de la sesión de autorización de OAuth. Cada id. de sesión es único y solo se puede usar una vez. Esta configuración se genera automáticamente al usar el Editor de Data Factory. | Sí |

**Ejemplo: uso de la autenticación de credenciales de usuario**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Expiración del token
El código de autorización que genera al hacer clic en el botón **Autorizar** expira después de un tiempo. Consulte la tabla siguiente para conocer el momento en que expiran los distintos tipos de cuentas de usuario. Puede ver el siguiente mensaje de error cuando el **token de autenticación expira**: Error de operación de credencial: invalid_grant - AADSTS70002: error al validar las credenciales. AADSTS70008: la concesión de acceso proporcionada expiró o se revocó. Id. de seguimiento: d18629e8-af88-43c5-88e3-d8419eb1fca1 Id. de correlación: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Marca de tiempo: 2015-12-15 21-09-31Z".

| Tipo de usuario | Expira después de |
|:--- |:--- |
| Cuentas de usuario NO administradas por Azure Active Directory (@hotmail.com, @live.com, etc). |12 horas |
| Cuentas de usuario administradas por Azure Active Directory (AAD) |14 días después de la ejecución del último segmento. <br/><br/>Noventa días, si un segmento basado en el servicio vinculado basado en OAuth se ejecuta al menos una vez cada catorce días. |

Si cambia la contraseña antes de esta hora de expiración del token, el token expira inmediatamente y aparece el error mencionado en esta sección.

Para evitar o resolver este error, vuelva a dar la autorización con el botón **Autorizar** cuando el **token expire** e implemente de nuevo el servicio vinculado. También puede generar valores para las propiedades **sessionId** y **authorization** mediante programación, para lo que usará el código de la sección siguiente:

#### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Para generar los valores de sessionId y authorization mediante programación

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Para más información sobre las clases de Data Factory que se usan en el código, consulte los temas [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) y [AuthorizationSessionGetResponse Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Agregue una referencia a la versión **2.9.10826.1824** de **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll** para la clase WindowsFormsWebAuthenticationDialog utilizada en el código.

## <a name="azure-data-lake-dataset-type-properties"></a>Propiedades de tipo del conjunto de datos de Azure Data Lake
Para obtener una lista completa de las secciones y propiedades JSON disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos y proporciona información acerca de la ubicación, el formato, etc. de los datos del almacén de datos. La sección typeProperties para conjuntos de datos de tipo **AzureDataLakeStore** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| folderPath |Ruta de acceso al contenedor y a la carpeta del almacén de Azure Data Lake. |Sí |
| fileName |Nombre del archivo en el almacén de Azure Data Lake. La propiedad fileName es opcional y distingue entre mayúsculas y minúsculas. <br/><br/>Si especifica fileName, la actividad (incluida la copia) funciona en el archivo específico.<br/><br/>Cuando no se especifica fileName, la copia incluirá todos los archivos de folderPath para el conjunto de datos de entrada.<br/><br/>Si no se especifica fileName para un conjunto de datos de salida, el nombre del archivo tendría este formato:Data.<Guid>.txt (por ejemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy es una propiedad opcional. Puede usarla para especificar un folderPath dinámico y un nombre de archivo para datos de series temporales. Por ejemplo, se puede parametrizar folderPath por cada hora de datos. Consulte la sección [Uso de la propiedad partitionedBy](#using-partitionedby-property) para ver información detallada y ejemplos. |No |
| formato | Se admiten los siguientes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** y **ParquetFormat**. Establezca la propiedad **type** de formato en uno de los siguientes valores. Para más información, consulte las secciones [Formato de texto](#specifying-textformat), [Formato Json](#specifying-jsonformat), [Formato Avro](#specifying-avroformat), [Formato Orc](#specifying-orcformat) y [Formato Parquet](#specifying-parquetformat). <br><br> Si desea **copiar los archivos tal cual** entre los almacenes basados en archivos (copia binaria), omita la sección de formato en las definiciones de los conjuntos de datos de entrada y salida. |No |
| compresión | Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son: **GZip**, **Deflate**, **BZip2** y **ZipDeflate**, y los niveles que se admiten son: **Optimal** (Óptimo) y **Fastest** (Más rápido). Para más información, consulte la sección [Especificación de la compresión](#specifying-compression). |No |

### <a name="using-partitionedby-property"></a>Uso de la propiedad partitionedBy
Puede especificar un valor folderPath dinámico y un nombre de archivo para los datos de series temporales con la sección **partitionedBy** , macros de Data Factory y las variables del sistema SliceStart y SliceEnd, que indican las horas de inicio y de finalización de un segmento de datos especificado.

Consulte los artículos sobre la [creación de conjuntos de datos](data-factory-create-datasets.md) y la [programación y ejecución](data-factory-scheduling-and-execution.md) para conocer más detalles sobre los conjuntos de datos de las series temporales, la programación y los segmentos.

#### <a name="sample-1"></a>Ejemplo 1

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
En este ejemplo, {Slice} se reemplaza por el valor de la variable del sistema SliceStart de Data Factory en el formato (YYYYMMDDHH) especificado. SliceStart hace referencia a la hora de inicio del segmento. folderPath es diferente para cada segmento. Por ejemplo: wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Ejemplo 2
```JSON
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

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="azure-data-lake-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de Azure Data Lake
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

**AzureDataLakeStoreSource** admite las siguientes propiedades **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| recursive |Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. |True (valor predeterminado), False |No |

**AzureDataLakeStoreSink** admite las siguientes propiedades **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| copyBehavior |Especifica el comportamiento de copia. |**PreserveHierarchy:** conserva la jerarquía de archivos en la carpeta de destino. La ruta de acceso relativa del archivo de origen que apunta a la carpeta de origen es idéntica a la ruta de acceso relativa del archivo de destino que apunta a la carpeta de destino.<br/><br/>**FlattenHierarchy:** todos los archivos de la carpeta de origen se crean en el primer nivel de la carpeta de destino. Los archivos de destino se crean con un nombre generado automáticamente.<br/><br/>**MergeFiles:** combina todos los archivos de la carpeta de origen en un archivo. Si se especifica el nombre de archivo/blob, el nombre de archivo combinado sería el nombre especificado; de lo contrario, sería el nombre de archivo generado automáticamente. |No |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización

En función de si se ha planeado el movimiento de datos inicial con un gran volumen de datos históricos o una carga de datos de producción incremental, Data Factory de Azure tiene opciones para mejorar el rendimiento de esas tareas. El parámetro de simultaneidad es una parte de la **actividad de copia** y define el número de ventanas de actividad diferentes que se procesarán en paralelo. El parámetro **parallelCopies** define el paralelismo de la única ejecución de actividad. Es importante tener en cuenta estos parámetros al diseñar las canalizaciones de movimiento de datos con Data Factory de Azure para lograr el mejor rendimiento.

Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.

