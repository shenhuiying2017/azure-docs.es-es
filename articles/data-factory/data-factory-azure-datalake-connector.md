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
ms.date: 03/13/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 1d7ba169675e822ae08a6c679301a0db01e6e338
ms.lasthandoff: 03/24/2017


---
# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>Movimiento de datos hacia y desde el almacén de Azure Data Lake mediante la Factoría de datos de Azure
En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos hacia Azure Data Lake Store y desde este servicio. Se basa en la información general que ofrece el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md). 

Puede copiar datos de cualquier almacén de datos de origen compatible a Azure Data Lake Store o desde Azure Data Lake Store a cualquier almacén de datos del receptor compatible. Para ver la lista de almacenes de datos admitidos como orígenes o receptores por actividad de copia, consulte la tabla [Almacenes de datos y formatos que se admiten](data-factory-data-movement-activities.md#supported-data-stores-and-formats).  

> [!NOTE]
> Antes de crear una canalización con una actividad de copia para mover datos hacia y desde un almacén de Azure Data Lake, cree una cuenta de Azure Data Lake Store. Para más información sobre Azure Data Lake Store, consulte la [introducción a Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="getting-started"></a>Introducción
Puede crear una canalización con actividad de copia que mueva los datos hacia Azure Data Lake Store y desde este servicio mediante el uso de diferentes herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copia**. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

También puede usar las herramientas siguientes para crear una canalización: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **plantilla de Azure Resource Manager**, **API de .NET** y **API de REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para la creación de una canalización con una actividad de copia. 

Si usa las herramientas o las API, realice los pasos siguientes para crear una canalización que mueva los datos de un almacén de datos de origen a un almacén de datos del receptor: 

1. Cree **servicios vinculados** para vincular los almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** que representen los datos de entrada y salida para la operación de copia. 
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida. 

Cuando se utiliza el asistente, se crean automáticamente las definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para obtener ejemplos con definiciones de JSON para entidades de Data Factory que se utilizan para copiar datos a Azure Data Lake Store y desde este servicio, consulte la sección [Ejemplos de JSON](#json-examples) de este artículo. 

En las secciones siguientes se proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de Azure Data Lake Store: 

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
Un servicio vinculado vincula un almacén de datos a una factoría de datos. Se crea un servicio vinculado de tipo **AzureDataLakeStore** para vincular Azure Data Lake Store a la factoría de datos. En la tabla siguiente se proporciona una descripción de los elementos JSON específicos del servicio vinculado Azure Data Lake Store y puede elegir entre autenticación de **entidad de servicio** y de **credenciales de usuario**.

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **AzureDataLakeStore** | Sí |
| dataLakeStoreUri | Especifica información sobre la cuenta de Almacén de Azure Data Lake. Tiene el siguiente formato: `https://[accountname].azuredatalakestore.net/webhdfs/v1` o `adl://[accountname].azuredatalakestore.net/`. | Sí |
| subscriptionId | Identificador de suscripción de Azure al que pertenece Data Lake Store. | Necesario para el receptor |
| resourceGroupName | Nombre del grupo de recursos de Azure al que pertenece Data Lake Store. | Necesario para el receptor |

### <a name="using-service-principal-authentication-recommended"></a>Uso de la autenticación de entidad de servicio (se recomienda)
Para usar la autenticación de entidad de servicio, registre una entidad de la aplicación en Azure Active Directory (AAD) y concédale acceso a Data Lake Store. Vea [Autenticación entre servicios](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) para obtener pasos detallados. Tome nota de los siguientes valores: **id. de aplicación**, **clave de aplicación** e **id. de inquilino**. Utilice esta información para la definición de servicio vinculado. 

> [!IMPORTANT]
> Si usa el Asistente para copia con el fin de crear canalizaciones de datos, asegúrese de conceder a la entidad de servicio al menos el rol de lectura en Control de acceso (IAM) para la cuenta de Data Lake Store y, al menos, el permiso de lectura y ejecución para la raíz de Data Lake Store ("/") y sus elementos secundarios. De lo contrario, puede que aparezca el error "Las credenciales proporcionadas no son válidas".
>
> Después de crear o actualizar una entidad de servicio en AAD, pueden transcurrir unos minutos hasta que los cambios entren en vigor. En primer lugar, compruebe la entidad de servicio y la configuración de ACL de Data Lake Store. Si aún aparece el error "Las credenciales proporcionadas no son válidas", espere unos instantes e inténtelo de nuevo.

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
También puede utilizar la autenticación de credenciales de usuario para realizar operaciones de copias con Data Lake Store mediante la especificación de las propiedades siguientes:

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
El código de autorización que genera al hacer clic en el botón **Autorizar** expira después de un tiempo. Consulte la tabla siguiente para conocer el momento en que expiran los distintos tipos de cuentas de usuario. Puede ver el mensaje de error siguiente cuando **expira el token** de autenticación:
 
```
"Credential operation error: invalid_grant - AADSTS70002: Error validating credentials. AADSTS70008: The provided access grant is expired or revoked. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z".
```

| Tipo de usuario | Expira después de |
|:--- |:--- |
| Cuentas de usuario NO administradas por Azure Active Directory (@hotmail.com, @live.com, etc.) |12 horas |
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

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para especificar un conjunto de datos para representar datos de entrada en Azure Blob Storage, establezca la propiedad de tipo del conjunto de datos en **AzureDataLakeStore**. Establezca la propiedad **linkedServiceName** del conjunto de datos en el nombre del servicio vinculado Azure Data Lake Store. Para obtener una lista completa de las secciones y propiedades JSON disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.). La sección **typeProperties** es diferente para cada tipo de conjunto de datos y proporciona información acerca de la ubicación, el formato, etc. de los datos del almacén de datos. La sección typeProperties para conjuntos de datos de tipo **AzureDataLakeStore** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| folderPath |Ruta de acceso al contenedor y a la carpeta del almacén de Azure Data Lake. |Sí |
| fileName |Nombre del archivo en el almacén de Azure Data Lake. La propiedad fileName es opcional y distingue entre mayúsculas y minúsculas. <br/><br/>Si especifica fileName, la actividad (incluida la copia) funciona en el archivo específico.<br/><br/>Cuando no se especifica fileName, la copia incluirá todos los archivos de folderPath para el conjunto de datos de entrada.<br/><br/>Si no se especifica fileName para un conjunto de datos de salida, el nombre del archivo tendría este formato:Data<Guid>.txt (por ejemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy es una propiedad opcional. Puede usarla para especificar un folderPath dinámico y un nombre de archivo para datos de series temporales. Por ejemplo, se puede parametrizar folderPath por cada hora de datos. Consulte la sección [Uso de la propiedad partitionedBy](#using-partitionedby-property) para ver información detallada y ejemplos. |No |
| formato | Se admiten los siguientes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** y **ParquetFormat**. Establezca la propiedad **type** de formato en uno de los siguientes valores. Para más información, consulte las secciones [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format) y [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Si desea **copiar los archivos tal cual** entre los almacenes basados en archivos (copia binaria), omita la sección de formato en las definiciones de los conjuntos de datos de entrada y salida. |No |
| compresión | Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son **GZip**, **Deflate**, **BZip2** y **ZipDeflate**. Los niveles admitidos son **Optimal** y **Fastest**. Para más información, consulte [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de archivo y compresión en Azure Data Factory). |No |

### <a name="using-partitionedby-property"></a>Uso de la propiedad partitionedBy
Como ya se ha indicado en la sección anterior, se puede especificar un valor dinámico de folderPath y filename para datos de series temporales con la propiedad **partitionedBy**, [funciones de Data Factory y las variables del sistema](data-factory-functions-variables.md).

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

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

**AzureDataLakeStoreSource** admite las siguientes propiedades **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| recursive |Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. |True (valor predeterminado), False |No |

**AzureDataLakeStoreSink** admite las siguientes propiedades **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| copyBehavior |Especifica el comportamiento de copia. |<b>PreserveHierarchy:</b> conserva la jerarquía de archivos en la carpeta de destino. La ruta de acceso relativa del archivo de origen que apunta a la carpeta de origen es idéntica a la ruta de acceso relativa del archivo de destino que apunta a la carpeta de destino.<br/><br/><b>FlattenHierarchy:</b> todos los archivos de la carpeta de origen se crean en el primer nivel de la carpeta de destino. Los archivos de destino se crean con un nombre generado automáticamente.<br/><br/><b>MergeFiles:</b> combina todos los archivos de la carpeta de origen en un archivo. Si se especifica el nombre de archivo/blob, el nombre de archivo combinado sería el nombre especificado; de lo contrario, sería el nombre de archivo generado automáticamente. |No |

## <a name="json-examples"></a>Ejemplos de JSON
En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Muestran cómo copiar datos entre Azure Data Lake Store y el Almacenamiento de blobs de Azure. En cambio, los datos pueden copiarse **directamente** desde cualquiera de los orígenes a cualquiera de los receptores admitidos. Para más información, vea la sección "Almacenes de datos y formatos que se admiten" del artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md).  
## <a name="example-copy-data-from-azure-blob-to-azure-data-lake-store"></a>Ejemplo: copia de datos de un blob de Azure a Azure Data Lake Store
El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
2. Un servicio vinculado de tipo [AzureDataLakeStore](#linked-service-properties).
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureDataLakeStore](#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) y [AzureDataLakeStoreSink](#copy-activity-properties).

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

**Servicio vinculado de Azure Data Lake Store**

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
> Vea los pasos en la sección [Propiedades del servicio vinculado de Almacén de Azure Data Lake](#linked-service-properties) con los detalles de la configuración.
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

**Conjunto de datos de salida de Azure Data Lake Store:**

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


**Una actividad de copia en una canalización con el origen de blob y el receptor de Azure Data Lake Store:** 

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

## <a name="example-copy-data-from-azure-data-lake-store-to-azure-blob"></a>Ejemplo: copia de datos de Azure Data Lake Store a Blob de Azure
El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [AzureDataLakeStore](#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureDataLakeStore](#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que usa [AzureDataLakeStoreSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

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
> Vea los pasos en la sección [Propiedades del servicio vinculado de Almacén de Azure Data Lake](#linked-service-properties) con los detalles de la configuración.
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

**Una actividad de copia en una canalización con el origen de Azure Data Lake Store y el receptor de blob:**

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

También puede asignar columnas del conjunto de datos de origen a columnas del conjunto de datos del receptor en la definición de actividad de copia. Para más información, consulte [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Asignación de columnas de conjunto de datos de Azure Data Factory).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.

