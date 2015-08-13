<properties 
	pageTitle="Ejemplos para el uso de la actividad de copia en Factoría de datos de Azure" 
	description="Proporciona ejemplos para el uso de la actividad de copia en Factoría de datos de Azure" 
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

# Ejemplos para el uso de la actividad de copia en Factoría de datos de Azure
Puede usar la **actividad de copia** en una canalización para copiar datos de un origen a un receptor (destino) en un lote. Este tema proporciona algunos ejemplos de uso de la actividad de copia en una canalización de Factoría de datos. Para obtener información general detallada de la actividad de copia y los escenarios principales que admite, consulte [Copia de datos con la Factoría de datos de Azure][adf-copyactivity].

## Copia de datos de una base de datos SQL Server local en un blob de Azure
En este ejemplo, se definen una tabla de entrada y una tabla de salida, y se utilizan en una actividad de copia dentro de una canalización que copia datos de una base de datos de SQL Server local a un blob de Azure.

### Supuestos
En este ejemplo se supone que ya tiene los siguientes artefactos de Factoría de datos de Azure:

* Grupo de recursos denominado **ADF**.
* Una factoría de datos de Azure denominada **CopyFactory**.
* Se crea y aparece en línea una puerta de enlace de administración de datos denominada **mygateway**.  

### Creación de un servicio vinculado para la base de datos SQL Server local de origen
En este paso, creará un servicio vinculado con el nombre **MyOnPremisesSQLDB** que apunta a una base de datos SQL Server local.

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
	        "connectionString": "Data Source=<servername>;Initial Catalog=<database>;Integrated Security=False;User ID=<username>;Password=<password>;",
	        "gatewayName": "mygateway"
	    }
	}

Tenga en cuenta lo siguiente:

- **type** se establece en **OnPremisesSqlLinkedService**.
- **connectionString** se establece en la cadena de conexión para una base de datos de SQL Server. 
- **gatewayName** se establece en el nombre de Data Management Gateway que ha instalado en el equipo local y registrado con el portal de servicio de Factoría de datos de Azure. 

Consulte [Servicio vinculado SQL local](https://msdn.microsoft.com/library/dn893523.aspx) para obtener información sobre elementos JSON para la definición en un servicio vinculado SQL local.
 
### Creación de un servicio vinculado para el blob de Azure de destino
En este paso, creará un servicio vinculado con el nombre **MyAzureStorage** que señala a un almacenamiento de blobs de Azure.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Tenga en cuenta lo siguiente:

- **type** se establece en **AzureStorageLinkedService**.
- **connectionString**: especifique el nombre de cuenta y la clave de cuenta para el almacenamiento de Azure.

Consulte [Servicio vinculado de Almacenamiento de Azure](https://msdn.microsoft.com/library/dn893522.aspx) para obtener información sobre los elementos JSON para definir un servicio vinculado de Almacenamiento de Azure.

### Tabla de entrada JSON
El siguiente script JSON define una tabla de entrada que hace referencia a una tabla SQL: **MyTable** en una base de datos local de SQL Server que define el servicio vinculado **MyOnPremisesSQLDB**. Tenga en cuenta que **name** es el nombre de la tabla de Factoría de datos de Azure y **tableName** es el nombre de la tabla de SQL en una base de datos de SQL Server.

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

Tenga en cuenta lo siguiente:

- **type** se establece en **OnPremisesSqlServerTableLocation**.
- **tableName** se establece en **MyTable**, que contiene los datos de origen. 
- **linkedServiceName** se establece en **MyOnPremisesSQLDB**, el servicio vinculado que creó para la base de datos SQL local.

Consulte [Propiedades de la ubicación SQL local](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL) para obtener más información acerca de los elementos JSON para definir una tabla de Factoría de datos que hace referencia a una tabla de SQL Server.

### Tabla de salida JSON
El siguiente script JSON define una tabla de salida: **MyAzureBlob**, que hace referencia a un blob de Azure: **MyBlob** en la carpeta de blobs: **MySubFolder** del contenedor de blobs: **MyContainer**.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Tenga en cuenta lo siguiente:
 
- **type** se establece en **AzureBlobLocation**.
- **folderPath** se establece en **MyContainer/MySubFolder**, que contiene el blob que cuenta con los datos copiados. 
- **fileName** se establece en **MyBlob**, el blob que contendrá los datos de salida.
- **linkedServiceName** se establece en **MyAzureStorge**, el servicio vinculado que creó para Almacenamiento de Azure.    

Consulte [Propiedades de la ubicación de blob de Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) para obtener más información acerca de los elementos JSON para definir una tabla de Factoría de datos que hace referencia a un blob de Azure.

### Canalización (con la actividad de copia) JSON
En este ejemplo, se define una canalización **CopyActivityPipeline** con las propiedades siguientes:

- La propiedad **type** se establece en **CopyActivity**.
- **MyOnPremTable** se especifica como la entrada (etiqueta **inputs**).
- **MyAzureBlob** se especifica como la salida (etiqueta **outputs**)
- La sección **transformación** contiene dos subsecciones: **origen** y **receptor**. El tipo de origen se establece en **SqlSource** y el tipo de receptor se establece en **BlobSink**. **sqlReaderQuery** define la transformación (proyección) que se debe realizar en el origen. Para obtener información detallada de todas las propiedades, consulte [Referencia del scripting JSON](https://msdn.microsoft.com/library/dn835050.aspx).

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}

Consulte [Referencia JSON de canalizaciones](https://msdn.microsoft.com/library/dn834988.aspx) para obtener información sobre los elementos JSON para definir una canalización de Factoría de datos y [Receptores y orígenes compatibles](https://msdn.microsoft.com/library/dn894007.aspx) para propiedades de SqlSource (por ejemplo: **sqlReaderQuery **en el ejemplo) y BlobSink. 


## Copia de datos de un sistema de archivos local a un blob de Azure
Puede usar la actividad de copia para copiar archivos desde un sistema de archivos local (recursos compartidos de red de Windows/Linux o host local de Windows) a un Blob de Azure. El host puede ser Windows o Linux con Samba configurado. Data Management Gateway debe instalarse en un equipo de Windows que se pueda conectar al host.

### Supuestos
En este ejemplo se da por hecho lo siguiente:

- **Host**: el nombre del servidor que hospeda el sistema de archivos es: **\contoso**.
- **Carpeta**: el nombre de la carpeta que contiene los archivos de entrada es: **marketingcampaign\regionaldata\{slice}, donde los archivos se particionan en una carpeta denominada {slice}, como 2014121112 (año 2014, mes 12, día 11, hora 12). 
### Creación de un servicio vinculado del sistema de archivos local
El siguiente ejemplo JSON puede usarse para crear un servicio vinculado con el nombre **FolderDataStore** de tipo **OnPremisesFileSystemLinkedService**.

		{
		    "name": "FolderDataStore",
	    	"properties": {
		        "type": "OnPremisesFileSystemLinkedService",
	        	"host": "\\\\contoso",
	        	"userId": "username",
	        	"password": "password",
	        	"gatewayName": "ContosoGateway"
	    	}
		}

> [AZURE.NOTE]Recuerde que debe usar el carácter de escape '' para nombres de host y carpetas en archivos JSON. Para **\Contoso**, utilice **\\Contoso**.

Consulte [Servicio vinculado del sistema de archivos local](https://msdn.microsoft.com/library/dn930836.aspx) para obtener información sobre elementos JSON para la definición de un servicio vinculado del sistema de archivos local.

### Creación de un servicio vinculado para el blob de Azure de destino
El siguiente JSON de ejemplo puede usarse para crear un servicio vinculado con el nombre **MyAzureStorage** de tipo **AzureStorageLinkedSerivce**.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Consulte [Servicio vinculado de Almacenamiento de Azure](https://msdn.microsoft.com/library/dn893522.aspx) para obtener información sobre los elementos JSON para definir un servicio vinculado de Almacenamiento de Azure.

### Creación de la tabla de entrada
El siguiente script JSON define una tabla de entrada que hace referencia a un servicio vinculado del sistema de archivos local que creó anteriormente.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\{Slice}",
	            "partitionedBy": [
	                { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }
	            ],
	            "linkedServiceName": "FolderDataStore"
	        },
	        "availability": {
	            "waitOnExternal": { },
	            "frequency": "Hour",
	            "interval": 24
	        }
	    }
	}

Consulte[Propiedades de ubicación del sistema de archivos local](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem) para obtener más información acerca de los elementos JSON para definir una tabla de Factoría de datos que hace referencia a un sistema de archivos local.

### Creación de una tabla de salida
El siguiente script JSON define una tabla de salida: **AzureBlobDest**, que hace referencia a un blob de Azure: **MyBlob** en la carpeta de blobs: **MySubFolder** del contenedor de blobs: **MyContainer**.
         
	{
   		"name": "AzureBlobDest",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Consulte [Propiedades de la ubicación de blob de Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) para obtener más información acerca de los elementos JSON para definir una tabla de Factoría de datos que hace referencia a un blob de Azure.

### Creación de la canalización
El siguiente JSON de canalización define una canalización con una actividad de copia que copia los datos del sistema de archivos local en el blob de Azure de destino.
 
	{
	    "name": "CopyFileToBlobPipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "Ingress",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "OnPremFileSource" } ],
	                "outputs": [ { "name": "AzureBlobDest" } ],
	                "transformation": {
	                    "source": {
	                        "type": "FileSystemSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 4,
	                    "timeout": "00:05:00"
	                }
	            }
	        ]
	    }
	}

La canalización en este ejemplo copia el contenido binario, sin análisis ni realización de transformaciones. Tenga en cuenta que puede aprovechar la **simultaneidad** para copiar los segmentos de archivos en paralelo. Esto es útil cuando desea mover los segmentos que ya se han realizado en el pasado.

> [AZURE.NOTE]Las actividades de copia simultánea con el mismo host a través de la ruta de acceso UNC con distintas cuentas de usuario pueden dar lugar a errores como "Las conexiones múltiples para un servidor o recurso compartido compatible por el mismo usuario, usando más de un nombre de usuario, no están permitidas". Se trata de la restricción del sistema operativo por motivos de seguridad. Programe las actividades de copia con distintas puertas de enlace o instale la puerta de enlace dentro del host y use "localhost" o "local" en lugar de la ruta de acceso UNC.

Consulte [Referencia JSON de canalizaciones](https://msdn.microsoft.com/library/dn834988.aspx) para obtener información sobre los elementos JSON para definir una canalización de Factoría de datos y [Receptores y orígenes compatibles](https://msdn.microsoft.com/library/dn894007.aspx) para propiedades de FileSystemSource y BlobSink.

### Escenarios adicionales para el uso de tablas de sistema de archivos

#### Copia de todos los archivos en una carpeta específica
Tenga en cuenta que solo **folderPath** se especifica en el JSON de ejemplo.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}
 
#### Copia de todos los archivos CSV en la carpeta específica
Tenga en cuenta que **fileFilter** está establecido en ***.csv**.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "*.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

#### Copia de un archivo específico
Tenga en cuenta que **fileFiter** está establecido en un archivo específico: **201501.csv**.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "201501.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

## Copia de datos de una base de datos de Oracle local a un blob de Azure
Puede usar la actividad de copia para copiar archivos de una base de datos de Oracle local a un blob de Azure.

### Creación de un servicio vinculado para una base de datos de Oracle local
El siguiente JSON puede utilizarse para crear un servicio vinculado que apunta a una base de datos de Oracle local. Tenga en cuenta que **type** se establece en **OnPremisesOracleLinkedService**.

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

Consulte [Servicio vinculado de Oracle local](https://msdn.microsoft.com/library/dn948537.aspx) para obtener información sobre elementos JSON para la definición de un servicio vinculado de Oracle local.

### Creación de un servicio vinculado para el blob de Azure de destino
El siguiente JSON de ejemplo puede usarse para crear un servicio vinculado con el nombre **MyAzureStorage** de tipo **AzureStorageLinkedSerivce**.

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Consulte [Servicio vinculado de Almacenamiento de Azure](https://msdn.microsoft.com/library/dn893522.aspx) para obtener información sobre los elementos JSON para definir un servicio vinculado de Almacenamiento de Azure.

### Creación de la tabla de entrada
El siguiente JSON de ejemplo puede utilizarse para crear una tabla de Factoría de datos de Azure que hace referencia a una tabla en una base de datos de Oracle local. Tenga en cuenta que el **tipo de ubicación** se establece en **OnPremisesOracleTableLocation**.

	{
	    "name": "TableOracle",
	    "properties": {
	        "location": {
	            "type": "OnPremisesOracleTableLocation",
	            "tableName": "LOG",
	            "linkedServiceName": "OnPremOracleSource"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": "1",
	            "waitOnExternal": {}
	        },
	        "policy": {}
	    }
	} 

Consulte[Propiedades de ubicación de Oracle local](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) para obtener más información acerca de los elementos JSON para definir una tabla de Factoría de datos que hace referencia en una base de datos de Oracle local.

### Creación de una tabla de salida
El siguiente script JSON define una tabla de salida: **MyAzureBlob**, que hace referencia a un blob de Azure: **MyBlob** en la carpeta de blobs: **MySubFolder** del contenedor de blobs: **MyContainer**.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "AzureBlobDest",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Consulte [Propiedades de la ubicación de blob de Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) para obtener más información acerca de los elementos JSON para definir una tabla de Factoría de datos que hace referencia a un blob de Azure.

### Creación de la canalización
La canalización de ejemplo siguiente tiene una actividad de copia que copia datos de una tabla de base de datos de Oracle en un blob de Almacenamiento de Azure.

	{
	    "name": "PipelineCopyOracleToBlob",
	    "properties": {
	        "activities": [
	            {
	                "name": "CopyActivity",
	                "description": "copy slices of oracle records to azure blob",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "TableOracle" } ],
	                "outputs": [ { "name": "TableAzureBlob" } ],
	                "transformation": {
	                    "source": {
	                        "type": "OracleSource",
	                        "oracleReaderQuery": "$$Text.Format('select * from LOG where "Timestamp" >= to_date(\'{0:yyyy-MM-dd}\', \'YYYY-MM-DD\') AND "Timestamp" < to_date(\'{1:yyyy-MM-dd}\', \'YYYY-MM-DD\')', SliceStart, SliceEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 3,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2015-03-01T00:00:00Z",
	        "end": "2015-03-15T00:00:00Z",
	        "isPaused": false
	    }
	}

Consulte [Referencia JSON de canalizaciones](https://msdn.microsoft.com/library/dn834988.aspx) para obtener información sobre los elementos JSON para definir una canalización de Factoría de datos y [Receptores y orígenes compatibles](https://msdn.microsoft.com/library/dn894007.aspx) para propiedades de OracleSource y BlobSink.

## Otras referencias

- [Copia de datos con la Factoría de datos de Azure][adf-copyactivity]
- [Actividad de copia: referencia de scripting](https://msdn.microsoft.com/library/dn835035.aspx)
- [Vídeo: Presentación de la actividad de copia de la Factoría de datos de Azure][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!--------HONumber=July15_HO4-->