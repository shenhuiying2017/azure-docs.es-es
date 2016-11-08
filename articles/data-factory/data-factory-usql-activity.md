---
title: Ejecución del script de U-SQL en Análisis de Azure Data Lake desde Factoría de datos de Azure
description: Aprenda a procesar datos ejecutando scripts U-SQL en el servicio de proceso de Análisis de Azure Data Lake.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: spelluru

---
# Ejecución del script de U-SQL en Análisis de Azure Data Lake desde Factoría de datos de Azure
Una canalización en una factoría de datos de Azure procesa los datos de los servicios de almacenamiento vinculados mediante el uso de servicios de proceso vinculados. Contiene una secuencia de actividades donde cada actividad realiza una operación de procesamiento específica. En este artículo se describe la **actividad U-SQL de Análisis de Data Lake** que ejecuta un script de **U-SQL** en un servicio vinculado de proceso de **Análisis de Azure Data Lake**.

> [!NOTE]
> Debe crear una cuenta de Azure Data Lake Analytics antes de crear una canalización con una actividad de U-SQL de este servicio. Para obtener más información sobre Azure Data Lake Analytics, consulte el artículo de [introducción a Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
> 
> Revise el [tutorial sobre la compilación de la primera canalización ](data-factory-build-your-first-pipeline.md) para ver los pasos detallados para crear una factoría de datos, servicios vinculados, conjuntos de datos y una canalización. Use los fragmentos de código JSON con el Editor de Data Factory, Visual Studio o Azure PowerShell para crear las entidades de Data Factory.
> 
> 

## Servicio vinculado con Análisis de Azure Data Lake
Cree un servicio vinculado de **Azure Data Lake Analytics** para vincular un servicio de proceso de Azure Data Lake Analytics a una instancia de Azure Data Factory. La actividad de U-SQL de Data Lake Analytics de la canalización hace referencia a este servicio vinculado.

En el ejemplo siguiente se proporciona la definición de JSON para un servicio vinculado de Análisis de Azure Data Lake.

    {
        "name": "AzureDataLakeAnalyticsLinkedService",
        "properties": {
            "type": "AzureDataLakeAnalytics",
            "typeProperties": {
                "accountName": "adftestaccount",
                "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
                "authorization": "<authcode>",
                "sessionId": "<session ID>", 
                "subscriptionId": "<subscription id>",
                "resourceGroupName": "<resource group name>"
            }
        }
    }


En la siguiente tabla se ofrecen descripciones de las propiedades que se usan en la definición de JSON.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| Tipo |La propiedad type se debe establecer en: **AzureDataLakeAnalytics**. |Sí |
| accountName |Nombre de la cuenta de Análisis de Azure Data Lake |Sí |
| dataLakeAnalyticsUri |Identificador URI de Análisis de Azure Data Lake. |No |
| authorization |El código de autorización se recupera automáticamente después de hacer clic en el botón **Autorizar** situado en el Editor de Factoría de datos y de completar el inicio de sesión de OAuth. |Sí |
| subscriptionId |Identificador de suscripción de Azure |No (si no se especifica, se usa la suscripción de Data Factory). |
| resourceGroupName |Nombre del grupo de recursos de Azure. |No (si no se especifica, se usa el grupo de recursos de la factoría de datos). |
| sessionId |Identificador de sesión de la sesión de autorización de OAuth. Cada identificador de sesión es único y solo puede usarse una vez. Este se genera automáticamente en el Editor de Data Factory. |Sí |

El código de autorización que se generó al hacer clic en el botón **Autorizar** expira poco tiempo después. Consulte la tabla siguiente para conocer el momento en que expiran los distintos tipos de cuentas de usuario. Puede ver el siguiente mensaje de error cuando el **token de autenticación expira**: Error de operación de credencial: invalid\_grant - AADSTS70002: error al validar las credenciales. AADSTS70008: la concesión de acceso proporcionada expiró o se revocó. Id. de seguimiento: d18629e8-af88-43c5-88e3-d8419eb1fca1 Id. de correlación: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Marca de tiempo: 2015-12-15 21:09:31Z

| Tipo de usuario | Expira después de |
|:--- |:--- |
| Cuentas de usuario NO administradas por Azure Active Directory (@hotmail.com, @live.com, etc.) |12 horas |
| Cuentas de usuario administradas por Azure Active Directory (AAD) |14 días después de la ejecución del último segmento. <br/><br/>Noventa días, si un segmento basado en el servicio vinculado basado en OAuth se ejecuta al menos una vez cada catorce días. |

Para evitar o resolver este error, vuelva a dar la autorización con el botón **Autorizar** cuando el **token expire** e implemente de nuevo el servicio vinculado. También puede generar valores para las propiedades **sessionId** y **authorization** mediante programación, para lo que usará el código de la sección siguiente.

### Para generar los valores de sessionId y authorization mediante programación
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

Para más información sobre las clases de Data Factory que se usan en el código, consulte los temas [AzureDataLakeStoreLinkedService (Clase)](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService (Clase)](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) y [AuthorizationSessionGetResponse (Clase)](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Agregue una referencia a Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para la clase WindowsFormsWebAuthenticationDialog.

## Actividad U-SQL de Análisis de Data Lake
El siguiente fragmento JSON define una canalización con una actividad U-SQL de Análisis de Data Lake. La definición de actividad tiene una referencia al servicio vinculado de Análisis de Azure Data Lake que creó anteriormente.

    {
        "name": "ComputeEventsByRegionPipeline",
        "properties": {
            "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
            "activities": 
            [
                {
                    "type": "DataLakeAnalyticsU-SQL",
                    "typeProperties": {
                        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                        "scriptLinkedService": "StorageLinkedService",
                        "degreeOfParallelism": 3,
                        "priority": 100,
                        "parameters": {
                            "in": "/datalake/input/SearchLog.tsv",
                            "out": "/datalake/output/Result.tsv"
                        }
                    },
                    "inputs": [
                        {
                            "name": "DataLakeTable"
                        }
                    ],
                    "outputs": 
                    [
                        {
                            "name": "EventsByRegionTable"
                        }
                    ],
                    "policy": {
                        "timeout": "06:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "EventsByRegion",
                    "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
                }
            ],
            "start": "2015-08-08T00:00:00Z",
            "end": "2015-08-08T01:00:00Z",
            "isPaused": false
        }
    }


En la tabla siguiente se describen los nombres y descripciones de las propiedades que son específicas de esta actividad.

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type |La propiedad type debe establecerse en **DataLakeAnalyticsU-SQL**. |Sí |
| scriptPath |Ruta de acceso a la carpeta que contiene el script U-SQL. El nombre del archivo distingue mayúsculas de minúsculas. |No (si se utiliza el script) |
| scriptLinkedService |Servicio vinculado que se vincula al almacenamiento que contiene el script para la factoría de datos |No (si se utiliza el script) |
| script |Especifique el script en línea en lugar de scriptPath y scriptLinkedService. Por ejemplo: "script": "Prueba CREAR BASE DE DATOS". |No (si usa scriptPath y scriptLinkedService) |
| degreeOfParallelism |Número máximo de nodos que se usará de forma simultánea para ejecutar el trabajo. |No |
| prioridad |Determina qué trabajos de todos los están en cola deben seleccionarse para ejecutarse primero. Cuanto menor sea el número, mayor será la prioridad. |No |
| parameters |Parámetros del script SQL U |No |

Para ver la definición del script, vea [Definición del script SearchLogProcessing.txt](#script-definition).

## Conjuntos de datos de entrada y salida de ejemplo
### Conjunto de datos de entrada
En este ejemplo, los datos de entrada residen en Almacén de Azure Data Lake (archivo SearchLog.tsv en la carpeta de datalake/input).

    {
        "name": "DataLakeTable",
        "properties": {
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
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }    

### Conjunto de datos de salida
En este ejemplo, los datos de salida generados por el script U-SQL se almacenan en Almacén de Azure Data Lake (carpeta datalake/output).

    {
        "name": "EventsByRegionTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### Ejemplo de servicio vinculado de Data Lake Store
Aquí está la definición del servicio vinculado de Azure Data Lake Store de ejemplo que usan los conjuntos de datos de entrada y salida.

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

Consulte el artículo sobre cómo [mover datos a Azure Data Lake Store como origen y destino](data-factory-azure-datalake-connector.md) para ver descripciones de las propiedades JSON.

## Script U-SQL
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv(nullEscape:"#NULL#");

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start <= DateTime.Parse("2012/02/19");

    OUTPUT @rs1   
        TO @out
          USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

ADF pasa dinámicamente los valores de los parámetros **@in** y **@out** del script de U-SQL usando la sección parameters. Consulte la sección parameters de la definición de canalización.

También puede especificar otras propiedades como degreeOfParallelism y priority en la definición de canalización de los trabajos que se ejecutan en el servicio Azure Data Lake Analytics.

## Parámetros dinámicos
En la definición de canalización de ejemplo, se asignan los parámetros in y out con valores codificados de forma rígida.

    "parameters": {
        "in": "/datalake/input/SearchLog.tsv",
        "out": "/datalake/output/Result.tsv"
    }

Es posible usar los parámetros dinámicos en su lugar. Por ejemplo:

    "parameters": {
        "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
        "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
    }

En este caso, los archivos de entrada se siguen tomando de la carpeta /datalake/input; los de salida se generan en la carpeta /datalake/output. Sin embargo, los nombres de archivo son dinámicos según la hora de inicio del segmento.

<!---HONumber=AcomDC_0914_2016-->