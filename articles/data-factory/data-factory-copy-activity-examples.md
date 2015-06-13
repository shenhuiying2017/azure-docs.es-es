<properties 
	pageTitle="Ejemplos de uso de actividad de copia en la factoría de datos de Azure" 
	description="Proporciona ejemplos de usa una actividad de copia en un generador de datos de Azure." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Ejemplos de uso de actividad de copia en la factoría de datos de Azure
Puede usar la **actividad de copia** en una canalización para copiar datos de un origen a un receptor (destino) en un lote. Este tema proporciona algunos ejemplos de uso de la actividad de copia en una canalización del generador de datos. Para obtener información general detallada de la actividad de copia y los escenarios principales que admite, consulte [Copia de datos con la Factoría de datos de Azure][adf-copyactivity].

## Copiar datos desde una base de datos de SQL Server local a un blob de Azure
En este ejemplo, se definen una tabla de entrada y una tabla de salida, y se utilizan en una actividad de copia dentro de una canalización que copia datos de una base de datos de SQL Server local a un blob de Azure.

### Supuestos
En este ejemplo se supone que ya tiene los siguientes artefactos de la factoría de datos de Azure:

* Grupo de recursos denominado **ADF**.
* Una factoría de datos de Azure denominada **CopyFactory**.
* Una puerta de enlace de administración de datos denominado **mygateway** se crea y está en línea.  

### Crear un servicio vinculado para la base de datos de SQL Server de origen local
En este paso, creará un servicio vinculado denominado **MyOnPremisesSQLDB** que apunta a una base de datos de SQL Server local.

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

- **tipo** está establecido en **OnPremisesSqlLinkedService**.
- **connectionString** se establece en la cadena de conexión para una base de datos de SQL Server. 
- **gatewayName** se establece en el nombre de la puerta de enlace de datos de administración ha instalado en el equipo local y registrado con el portal de servicio de generador de datos de Azure. 

Consulte [local SQL vinculado servicio](https://msdn.microsoft.com/library/dn893523.aspx) para obtener más información sobre JSON elementos de definición de SQL local vinculan servicio.
 
### Crear un servicio vinculado para el destino de blob de Azure
En este paso, creará un servicio vinculado denominado **MyAzureStorage** que señala a un almacenamiento de blobs de Azure.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Tenga en cuenta lo siguiente:

- **tipo** está establecido en **AzureStorageLinkedService**.
- **connectionString** - especifique el nombre de cuenta y cuenta clave para el almacenamiento de Azure.

Consulte [servicios vinculados del almacenamiento de Azure](https://msdn.microsoft.com/library/dn893522.aspx) para obtener más información acerca de los elementos JSON para definir el almacenamiento de Azure vinculados servicio.

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

- **tipo** está establecido en **OnPremisesSqlServerTableLocation**.
- **tableName** está establecido en **MyTable**, que contiene los datos de origen. 
- **linkedServiceName** está establecido en **MyOnPremisesSQLDB**, vinculado servicio creado para la base de datos SQL de forma local.

Consulte [Propiedades de la ubicación local SQL](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL) para obtener más información acerca de los elementos JSON para definir una tabla de generador de datos que hace referencia a una tabla de SQL Server.

### Tabla de salida JSON
El siguiente script JSON define una tabla de resultados: **MyAzureBlob**, que hace referencia a un blob de Azure: **MyBlob** en la carpeta de blob: **MiSubcarpeta** en el contenedor de blobs: **MyContainer**.
         
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
 
- **tipo** está establecido en **AzureBlobLocation**.
- **folderPath** está establecido en **MyContainer/MiSubcarpeta**, que contiene los datos de blob que contiene los datos copiados. 
- **nombre de archivo** se establece en **MyBlob**, el blob que contiene los datos de salida.
- **linkedServiceName** está establecido en **MyAzureStorge**, vinculado servicio creado para el almacenamiento de Azure.    

Consulte [Propiedades de la ubicación de Azure blob](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) para obtener más información acerca de los elementos JSON para definir una tabla de generador de datos que hace referencia a un blob de Azure.

### Canalización (con la actividad de copia) JSON
En este ejemplo, se define una canalización **CopyActivityPipeline** con las propiedades siguientes:

- La propiedad **type** se establece en **CopyActivity**.
- **MyOnPremTable** se especifica como la entrada (etiqueta **inputs**).
- **MyAzureBlob** se especifica como la salida (etiqueta **outputs**)
- La sección **transformación** contiene dos subsecciones: **origen** y **receptor**. El tipo de origen se establece en **SqlSource** y el tipo de receptor se establece en **BlobSink**. **sqlReaderQuery** define la transformación (proyección) que se debe realizar en el origen. Para obtener información detallada de todas las propiedades, consulte [Referencia del scripting JSON][json-script-reference].

         
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

Consulte [referencia de JSON de canalización](https://msdn.microsoft.com/library/dn834988.aspx) para obtener más información acerca de los elementos JSON para definir una canalización del generador de datos y [admite orígenes y receptores](https://msdn.microsoft.com/library/dn894007.aspx) para las propiedades de SqlSource (por ejemplo: **sqlReaderQuery **en el ejemplo) y BlobSink. 


## Copiar datos desde un sistema de archivos local a un blob de Azure
Puede usar la actividad de copia para copiar archivos desde un sistema de archivos local (recursos compartidos de red de Windows/Linux o host local de Windows) a un Blob de Azure. El host puede ser Windows o Linux con Samba configurado. Data Management Gateway debe instalarse en un equipo de Windows que se puede conectar al host.

### Supuestos
En este ejemplo se supone lo siguiente:

- **Host** -nombre del servidor que hospeda el sistema de archivos es: **\contoso**.
- **Carpeta** -nombre de la carpeta que contiene los archivos de entrada es: **marketingcampaign\regionaldata\ {segmento}, donde los archivos se particionan en una carpeta denominada {segmento}, como 2014121112 (año de 2014, mes de 12, día de 11, hora de 12). 
### Crear un servicio de sistema vinculado del archivo local
El ejemplo siguiente JSON puede utilizarse para crear un servicio vinculado denominado **FolderDataStore** de tipo **OnPremisesFileSystemLinkedService**.

	{
	    "name": "FolderDataStore",
	    "properties": {
	        "type": "OnPremisesFileSystemLinkedService",
	        "host": "\\contoso",
	        "userId": "username",
	        "password": "password",
	        "gatewayName": "ContosoGateway"
	    }
	}

> [AZURE.NOTE]Recuerde que debe usar el carácter de escape '' para nombres de host y las carpetas de archivos de JSON. Para **\Contoso**, utilice **\\Contoso**.

Consulte [servicio vinculado del sistema de archivos local](https://msdn.microsoft.com/library/dn930836.aspx) para obtener más información acerca de los elementos JSON para definir un sistema de archivos local vinculado el servicio.

### Crear un servicio vinculado para el destino de blob de Azure
El ejemplo siguiente JSON puede utilizarse para crear un servicio vinculado denominado **MyAzureStorage** de tipo **AzureStorageLinkedSerivce**.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Consulte [servicios vinculados del almacenamiento de Azure](https://msdn.microsoft.com/library/dn893522.aspx) para obtener más información acerca de los elementos JSON para definir el almacenamiento de Azure vinculados servicio.

### Crear la tabla de entrada
El siguiente script JSON define una tabla de entrada que hace referencia a un servicio de sistema vinculado de archivos local que creó anteriormente.

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

Consulte [Propiedades de la ubicación de sistema de archivos local](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem) para obtener más información acerca de los elementos JSON para definir una tabla de generador de datos que hace referencia a un sistema de archivos local.

### Crear la salida puede
El siguiente script JSON define una tabla de resultados: **AzureBlobDest**, que hace referencia a un blob de Azure: **MyBlob** en la carpeta de blob: **MiSubcarpeta** en el contenedor de blobs: **MyContainer**.
         
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

Consulte [Propiedades de la ubicación de Azure blob](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) para obtener más información acerca de los elementos JSON para definir una tabla de generador de datos que hace referencia a un blob de Azure.

### Crear la canalización
La canalización siguiente JSON define una canalización con una actividad de copia que copia los datos del sistema de archivos local en el destino de blob de Azure.
 
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

La canalización en este ejemplo copia el contenido binario, sin cualquier análisis o realizar transformaciones. Tenga en cuenta que puede aprovechar **simultaneidad** para copiar los intervalos de archivos en paralelo. Esto es útil cuando desea mover que los sectores ya ha ocurrido en el pasado.

> [AZURE.NOTE]Las actividades de copia simultánea con el mismo host a través de la ruta de acceso UNC con distintas cuentas de usuario pueden dar lugar a errores como "no se permiten varias conexiones a un servidor o recurso compartido del mismo usuario, con más de un nombre de usuario". Se trata de la restricción del sistema operativo por motivos de seguridad. Programar las actividades de copia diferentes puertas de enlace, o instalar la puerta de enlace en el host y use "localhost" o "local" en lugar de la ruta de acceso UNC.

Consulte [referencia de JSON de canalización](https://msdn.microsoft.com/library/dn834988.aspx) para obtener más información acerca de los elementos JSON para definir una canalización del generador de datos y [admite orígenes y receptores](https://msdn.microsoft.com/library/dn894007.aspx) para las propiedades de FileSystemSource y BlobSink.

### Escenarios adicionales para el uso de tablas de sistema de archivos

#### Copie todos los archivos en una carpeta específica
Tenga en cuenta que solo **folderPath** se especifica en el ejemplo de JSON.

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
 
#### Copie todos los archivos CSV en la carpeta específica
Tenga en cuenta que el **fileFilter** está establecido en ***.csv**.

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

#### Copiar un archivo específico
Tenga en cuenta que el **fileFiter** está establecido en un archivo específico: **201501.csv**.

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

## Copiar datos desde una base de datos local a un blob de Azure
Puede usar la actividad de copia para copiar archivos de una base de datos de Oracle en entornos locales a un Blob de Azure.

### crear un servicio vinculado para una base de datos de Oracle local
El siguiente JSON puede utilizarse para crear un servicio vinculado que apunta a una base de datos de Oracle en local. Tenga en cuenta que el **tipo** está establecido en **OnPremisesOracleLinkedService**.

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

Consulte [servicio vinculado de Oracle local](https://msdn.microsoft.com/library/dn948537.aspx) para obtener más información acerca de los elementos JSON para definir local de Oracle vinculado el servicio.

### Crear un servicio vinculado para el destino de blob de Azure
El ejemplo siguiente JSON puede utilizarse para crear un servicio vinculado denominado **MyAzureStorage** de tipo **AzureStorageLinkedSerivce**.

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Consulte [servicios vinculados del almacenamiento de Azure](https://msdn.microsoft.com/library/dn893522.aspx) para obtener más información acerca de los elementos JSON para definir el almacenamiento de Azure vinculados servicio.

### Crear la tabla de entrada
En el ejemplo siguiente JSON puede utilizarse para crear una tabla de la factoría de datos de Azure que hace referencia a una tabla en una base de datos de Oracle en local. Tenga en cuenta que el **tipo de ubicación** está establecido en **OnPremisesOracleTableLocation**.

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

Consulte [Propiedades de la ubicación local Oracle](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) para obtener más información acerca de los elementos JSON para definir una tabla de generador de datos que hace referencia a una tabla en una base de datos de Oracle en local.

### Crear la tabla de salida
El siguiente script JSON define una tabla de resultados: **MyAzureBlob**, que hace referencia a un blob de Azure: **MyBlob** en la carpeta de blob: **MiSubcarpeta** en el contenedor de blobs: **MyContainer**.
         
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

Consulte [Propiedades de la ubicación de Azure blob](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) para obtener más información acerca de los elementos JSON para definir una tabla de generador de datos que hace referencia a un blob de Azure.

### Crear la canalización
La canalización de ejemplo siguiente tiene una actividad de copia que copia datos de una tabla de base de datos de Oracle en un blob de almacenamiento de Azure.

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
	                        "oracleReaderQuery": "$$Text.Format('select * from LOG where "Timestamp" >= to_date('{0:yyyy-MM-dd}', 'YYYY-MM-DD') AND "Timestamp" < to_date('{1:yyyy-MM-dd}', 'YYYY-MM-DD')', SliceStart, SliceEnd)"
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

Consulte [referencia de JSON de canalización](https://msdn.microsoft.com/library/dn834988.aspx) para obtener más información acerca de los elementos JSON para definir una canalización del generador de datos y [admite orígenes y receptores](https://msdn.microsoft.com/library/dn894007.aspx) para las propiedades de OracleSource y BlobSink.

## Otras referencias

- [Copia de datos con la Factoría de datos de Azure][adf-copyactivity]
- [Actividad de copia: referencia de scripting](https://msdn.microsoft.com/library/dn835035.aspx)
- [Vídeo: Presentación de la actividad de copia de la Factoría de datos de Azure][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!---HONumber=GIT-SubDir--> 