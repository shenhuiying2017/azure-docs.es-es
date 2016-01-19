<properties
   pageTitle="Carga de datos con Factoría de datos de Azure | Microsoft Azure"
   description="Más información sobre Factoría de datos de Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Carga de datos con la Factoría de datos de Azure

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

 Este tutorial le mostrará cómo crear una canalización en Factoría de datos de Azure que desplazará los datos del almacenamiento de blobs de Azure a un Almacenamiento de datos SQL. Con los siguiente pasos, hará lo siguiente:

+ Configurar datos de ejemplo en un blob de Almacenamiento de Azure.
+ Conectarse a recursos en Factoría de datos de Azure.
+ Crear una canalización para mover datos de los blobs de Almacenamiento al Almacenamiento de datos SQL.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]

## Recursos

Para este tutorial, necesitará los recursos siguientes:

   + **Blob de almacenamiento de Azure**: el blob de almacenamiento de Azure será el origen de datos de la canalización. Puede usar un blob existente o [aprovisionar uno nuevo](../storage/storage-create-storage-account/).

   + **Almacenamiento de datos SQL**: en este tutorial va a mover datos al Almacenamiento de datos SQL. Si no dispone de una instalación de la instancia, aprenderá a hacerlo [aquí](sql-data-warehouse-get-started-provision.md). Además, la instancia deberá estar configurada con nuestro conjunto de datos de AdventureWorksDW. Si no aprovisionó el almacenamiento de datos con los datos de ejemplo, puede [cargarlos manualmente](sql-data-warehouse-get-started-manually-load-samples.md).

   + **Factoría de datos de Azure**: Factoría de datos de Azure completará la carga real y, si necesita más información sobre la configuración de Factoría de datos de Azure o la creación de canalizaciones, puede consultarla [aquí](../data-factory/data-factory-build-your-first-pipeline-using-editor/).

Una vez que tenga todas las piezas preparadas, puede preparar los datos y crear la canalización de Factoría de datos de Azure.

## Datos de ejemplo

Además de las diferentes partes de la canalización, necesitaremos también algunos datos de ejemplo que se puedan usar para practicar la carga de datos en Factoría de datos de Azure.

1. En primer lugar, [descargue los datos de ejemplo](https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv). Estos datos funcionan con los datos de ejemplo que se encuentran en sus datos de ejemplo, y ofrecen otros tres años de datos de datos de ventas.

2. Una vez descargados los datos, puede moverlos al almacenamiento de blobs mediante la ejecución del script siguiente en AZCopy:

        AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv

	Consulte la [documentación de AZCopy](../storage/storage-use-azcopy/) para obtener información adicional acerca de cómo instalar y trabajar con AZCopy.

Ahora que ya tenemos nuestros datos, podemos moverlos a su factoría de datos para crear la canalización que desplazará los datos de la cuenta de almacenamiento al Almacenamiento de datos SQL.

## Uso de Factoría de datos de Azure

Ahora que hemos reunido todas las piezas, podemos comenzar a configurar la canalización, mediante el desplazamiento a la instancia de Factoría de datos de Azure en el Portal de Azure. Esto puede hacerse desde el [Portal de Azure clásico](portal.azure.com) y mediante la selección de la factoría de datos en el menú izquierdo.

A partir de aquí, se necesitan tres pasos para configurar una canalización de Factoría de datos de Azure para transferir datos a su almacenamiento de datos: vinculación de sus servicios, definición de los conjuntos de datos y creación de la canalización.

### Creación de servicios vinculados

El primer paso es vincular la cuenta de almacenamiento de Azure y el Almacenamiento de datos SQL a la factoría de datos.

1. En primer lugar, empiece el proceso de registro haciendo clic en la sección "Servicios vinculados" de la factoría de datos y después haga clic en 'Nuevo almacén de datos'. A continuación, elija un nombre para registrar el Almacenamiento de Azure, seleccione Almacenamiento de Azure como tipo y escriba su nombre de cuenta y clave de cuenta.

2. Para registrar el Almacenamiento de datos SQL, debe navegar hasta la sección 'Crear e implementar'; después, seleccione 'Nuevo almacén de datos' y 'Almacenamiento de datos SQL de Azure'. A continuación, rellene la siguiente plantilla:

		{
		    "name": "<Linked Service Name>",
		    "properties": {
		        "description": "",
		        "type": "AzureSqlDW",
		        "typeProperties": {
		            "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
		        }
		    }
		}

### Registro de conjuntos de datos

Después de crear los servicios vinculados, debemos definir los conjuntos de datos. Esto significa que hay que definir la estructura de los datos que se desplazan desde el almacenamiento al almacenamiento de datos. Puede leer más sobre creación.

1. Inicie este proceso desplazándose a la sección 'Crear e implementar' de Factoría de datos.

2. Haga clic en 'Nuevo conjunto de datos' y después en 'Almacenamiento de blobs de Azure' para vincular el almacenamiento a Factoría de datos. Puede usar el script siguiente para definir los datos de almacenamiento de blobs de Azure:

		{
			"name": "<Dataset Name>",
			"properties": {
				"type": "AzureBlob",
				"linkedServiceName": "<linked storage name>",
				"typeProperties": {
					"folderPath": "<containter name>",
					"fileName": "FactInternetSales.csv",
					"format": {
					"type": "TextFormat",
					"columnDelimiter": ",",
					"rowDelimiter": "\n"
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



3. Ahora definiremos también nuestro conjunto de datos para Almacenamiento de datos SQL. Comenzamos en la misma forma, haciendo clic en 'Nuevo conjunto de datos' y después en 'Almacenamiento de datos SQL Azure'.

		{
		  "name": "<dataset name>",
		  "properties": {
		    "type": "AzureSqlDWTable",
		    "linkedServiceName": "<linked data warehouse name>",
		    "typeProperties": {
		      "tableName": "FactInternetSales"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		{
		  "name": "DWDataset",
		  "properties": {
			"type": "AzureSqlDWTable",
			"linkedServiceName": "AzureSqlDWLinkedService",
			"typeProperties": {
			  "tableName": "FactInternetSales"
			},
			"availability": {
			  "frequency": "Hour",
			  "interval": 1
			}
		  }
		}

### Configuración de la canalización

Por último, vamos a configurar y ejecutar la canalización en Factoría de datos de Azure. Se trata de la operación que completará el movimiento real de datos. Puede encontrar una vista completa de las operaciones que se pueden completar con Almacenamiento de datos SQL y Factoría de datos de Azure [aquí](../data-factory/data-factory-azure-sql-data-warehouse-connector/).

En la sección 'Crear e implementar', haga clic en 'Más comandos' y, a continuación, en 'Nueva canalización'. Después de crear la canalización, puede usar el código siguiente para transferir los datos al almacenamiento de datos:

	{
	"name": "<Pipeline Name>",
	"properties": {
		"description": "<Description>",
		"activities": [
			{
				"type": "Copy",
				"typeProperties": {
					"source": {
						"type": "BlobSource",
						"skipHeaderLineCount": 1
					},
					"sink": {
						"type": "SqlDWSink",
						"writeBatchSize": 0,
						"writeBatchTimeout": "00:00:10"
					}
				},
				"inputs": [
					{
						"name": "<Storage Dataset>"
					}
				],
				"outputs": [
					{
						"name": "<Data Warehouse Dataset>"
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
				"name": "Sample Copy",
				"description": "Copy Activity"
			}
		],
		"start": "<Date YYYY-MM-DD>",
		"end": "<Date YYYY-MM-DD>",
		"isPaused": false
	}
	}
	

<!---HONumber=AcomDC_0114_2016-->