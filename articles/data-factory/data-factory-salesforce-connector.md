<properties
	pageTitle="Movimiento de datos de Salesforce mediante el uso de Data Factory de Azure | Microsoft Azure"
	description="Aprenda a mover datos de Salesforce usando Data Factory de Azure."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="spelluru"/>

# Movimiento de datos de Salesforce mediante el uso de Data Factory de Azure
En este artículo se describe cómo puede usar la actividad de copia en Data Factory de Azure para copiar datos de Salesforce en cualquier almacén de datos que aparezca en la columna Receptores en la tabla de [orígenes y receptores admitidos](data-factory-data-movement-activities.md#supported-data-stores). Este artículo se basa en el artículo sobre [movimiento de datos y actividad de copia](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones de almacén de datos admitidas.

Actualmente, Data Factory de Azure solo admite mover datos de Salesforce a [almacenes de datos receptores compatibles]((data-factory-data-movement-activities.md#supported-data-stores), pero no de otros almacenes de datos a Salesforce.

## Requisitos previos
- Tiene que usar una de las siguientes ediciones de Salesforce: Developer Edition, Professional Edition, Enterprise Edition o Unlimited Edition.
- Debe estar habilitado el permiso API. Consulte [How do I enable API access in Salesforce by permission set?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (¿Cómo se habilita el acceso de API en Salesforce con un conjunto de permisos?)
- Para copiar datos de Salesforce en almacenes de datos locales, tiene que tener Data Management Gateway versión 2.0 o posterior instalado en su entorno local.

## Asistente para copia de datos
La manera más fácil de crear una canalización que copie datos de Salesforce a cualquiera de los almacenes de datos receptores admitidos es usar el Asistente para copia. Consulte [Tutorial: Creación de una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización utilizando el Asistente para copia de datos.

En el siguiente ejemplo, se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización usando el [Portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos desde la base de datos Salesforce al almacenamiento de blobs de Azure. Sin embargo, los datos se pueden copiar en cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en Data Factory de Azure.

## Ejemplo: Copia de datos de Salesforce a un blob de Azure
El ejemplo copia los datos de Salesforce a un blob de Azure cada hora. Las propiedades JSON que se usan en estos ejemplos se describen en las secciones después de los ejemplos. Puede copiar directamente los datos en cualquiera de los receptores que se indican en el artículo sobre [movimiento de datos](data-factory-data-movement-activities.md#supported-data-stores) con la actividad de copia de Data Factory de Azure.

Aquí tiene los artefactos de Data Factory que necesita crear para implementar el escenario. Las secciones que siguen a la lista proporcionan detalles acerca de estos pasos.

- Un servicio vinculado del tipo [Salesforce](#salesforce-linked-service-properties)
- Un servicio vinculado del tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
- Un [conjunto de datos](data-factory-create-datasets.md) de entrada del tipo [RelationalTable](#salesforce-dataset-properties)
- Un [conjunto de datos](data-factory-create-datasets.md) de salida del tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
- Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](#relationalsource-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)

**Servicio vinculado de Salesforce**

Este ejemplo usa el servicio vinculado de **Salesforce**. Consulte la sección sobre el [servicio vinculado de Salesforce](#salesforce-linked-service-properties) para ver las propiedades que admite este servicio vinculado. Consulte [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Obtención de un token de seguridad) para ver instrucciones sobre cómo restablecer u obtener un token de seguridad.

	{
		"name": "SalesforceLinkedService",
		"properties":
		{
			"type": "Salesforce",
			"typeProperties":
			{
				"username": "<user name>",
				"password": "<password>",
				"securityToken": "<security token>"
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

**Conjunto de datos de entrada de Salesforce**

	{
		"name": "SalesforceInput",
		"properties": {
			"linkedServiceName": "SalesforceLinkedService",
			"type": "RelationalTable",
			"typeProperties": {
				"tableName": "AllDataType__c"  
			},
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

Si se establece **external** en **true**, se informa al servicio Data Factory que el conjunto de datos es externo a Data Factory y que no lo genera ninguna actividad de la factoría de datos.

> [AZURE.IMPORTANT] La parte "\_\_c" del nombre de la API es necesaria para cualquier objeto personalizado.

![Data Factory - Conexión a Salesforce - Nombre de la API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Conjunto de datos de salida de blob de Azure**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1).

	{
	    "name": "AzureBlobOutput",
	    "properties":
	    {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties":
	        {
	            "folderPath": "adfgetstarted/alltypes_c"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida anteriores y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**.

Consulte [Propiedades del tipo RelationalSource](#relationalsource-type-properties) para obtener la lista de propiedades admitidas por RelationalSource.

	{  
		"name":"SamplePipeline",
		"properties":{  
			"start":"2016-06-01T18:00:00",
			"end":"2016-06-01T19:00:00",
			"description":"pipeline with copy activity",
			"activities":[  
			{
				"name": "SalesforceToAzureBlob",
				"description": "Copy from Salesforce to an Azure blob",
				"type": "Copy",
				"inputs": [
				{
					"name": "SalesforceInput"
				}
				],
				"outputs": [
				{
					"name": "AzureBlobOutput"
				}
				],
				"typeProperties": {
					"source": {
						"type": "RelationalSource",
						"query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"				
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

> [AZURE.IMPORTANT] La parte "\_\_c" del nombre de la API es necesaria para cualquier objeto personalizado.

![Data Factory - Conexión a Salesforce - Nombre de la API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Propiedades del servicio vinculado de Salesforce

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos al servicio vinculado de Salesforce.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| type | La propiedad type debe establecerse en: **Salesforce**. | Sí |
| nombre de usuario |Especifique el nombre de usuario de la cuenta de usuario. | Sí |
| contraseña | Especifique la contraseña para la cuenta de usuario. | Sí |
| securityToken | Especifique el token de seguridad para la cuenta de usuario. Consulte [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Obtención de un token de seguridad) para ver instrucciones sobre cómo restablecer u obtener un token de seguridad. Para más información acerca de los tokens de seguridad en general, consulte [Security and the API](https://developer.salesforce.com/docs/atlas.es-ES.api.meta/api/sforce_api_concepts_security.htm) (Seguridad y la API). | Sí |

## Propiedades del conjunto de datos de Salesforce

Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo sobre [creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, y demás).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos del tipo **RelationalTable** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| tableName | Nombre de la tabla de Salesforce. | No (si se especifica una **query** de **RelationalSource**) |

> [AZURE.IMPORTANT]  La parte "\_\_c" del nombre de la API es necesaria para cualquier objeto personalizado.

![Data Factory - Conexión a Salesforce - Nombre de la API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## Propiedades del tipo RelationalSource
Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como name, description, tablas input y output y varias directivas están disponibles para todos los tipos de actividades.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

En el caso de la actividad de copia cuando el origen es del tipo **RelationalSource** (lo que incluye Salesforce), en la sección typeProperties están disponibles las propiedades siguientes:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Utilice la consulta personalizada para leer los datos. | Consulta de SQL-92 o de [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.es-ES.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Por ejemplo: select * from MyTable\_\_c. | No (si se especifica **tableName** de **dataset**) |

> [AZURE.IMPORTANT] La parte "\_\_c" del nombre de la API es necesaria para cualquier objeto personalizado.<br> Al especificar una consulta con la cláusula **where** en la columna DateTime, utilice SOQL. Por ejemplo, $$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd), or SQL query e.g. $$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts'{0:yyyy-MM-dd HH:mm:ss}'}} AND LastModifiedDate < {{ts'{1:yyyy-MM-dd HH:mm:ss}'}}', WindowStart, WindowEnd).

![Data Factory - Conexión a Salesforce - Nombre de la API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Recuperación de datos de informes de Salesforce
Puede recuperar datos de informes de Salesforce especificando las consultas como {call "<nombre del informe>"}; por ejemplo, "query": "{call "TestReport"}".

## Límites de solicitudes de Salesforce
Salesforce tiene límites para el número total de solicitudes de API y el de solicitudes de API simultáneas. Consulte la sección API Request Limits (Límites de solicitudes de API) del artículo [Salesforce Developer Limits](http://resources.docs.salesforce.com/200/20/es-ES/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Límites de desarrollador de Salesforce) para más detalles.

Si el número de solicitudes simultáneas supera el límite, se produce la limitación y verá errores aleatorios. Si el número total de solicitudes supera el límite, la cuenta de Salesforce se bloqueará durante 24 horas. También podría recibir el error "REQUEST\_LIMIT\_EXCEEDED" en ambos escenarios.


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Asignación de tipos para Salesforce
Tipo de Salesforce | Tipo basado en .NET
--------------- | ---------------
Numeración automática | String
Casilla de verificación | Booleano
Moneda | Doble
Date | DateTime
Fecha y hora | DateTime
Email | String
Id | String
Relación de búsqueda | String
Lista desplegable de selección múltiple | String
Number | Doble
Percent | Doble
Teléfono | String
Lista desplegable | String
Texto | String
Área de texto | String
Área de texto (largo) | String
Área de texto (enriquecido) | String
Texto (cifrado) | String
URL | String

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Data Factory de Azure y las diversas formas de optimizarlo.

<!---HONumber=AcomDC_0817_2016-->