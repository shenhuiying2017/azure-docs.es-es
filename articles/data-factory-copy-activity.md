<properties title="Copy data with Azure Data Factory" pageTitle="Copia de datos con la Factoría de datos de Azure" description="Obtenga información acerca de cómo usar la actividad de copia en la factoría de datos de Azure para copiar datos desde un origen de datos a otro origen de datos." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Copia de datos con la Factoría de datos de Azure (actividad de copia)
Utilice la **actividad de copia** en una canalización para copiar datos de un origen a un receptor (destino) en un lote. La actividad de copia puede utilizarse en los escenarios siguientes:

- **Entrada en Azure.**. En este escenario, los datos se copian de un origen de datos local (por ejemplo: SQL Server) a un almacén de datos de Azure (por ejemplo: blob de Azure, tabla de Azure o base de datos de SQL Azure) para los subescenarios siguientes:
	- Recopilar datos en una ubicación centralizada en Azure para su posterior procesamiento.
	- Migrar a Azure datos de plataformas locales o en la nube que no sean Azure.
	- Archivar o crear copias de seguridad en Azure para un almacenamiento en capas rentable.
- **Salida de Azure**. En este escenario, los datos se copian de Azure (por ejemplo: blob de Azure, tabla de Azure o Base de datos SQL de Azure) a un almacén de datos y data marts locales (por ejemplo: SQL Server) para los subescenarios siguientes:
	- Transferir datos a la infraestructura local debido a la falta de espacio para almacenamiento de datos en la nube.
	- Transferir datos a la infraestructura local para aprovechar una solución o una infraestructura de informes locales.
	- Archivar o realizar copias de seguridad de datos en la infraestructura local para almacenamiento en capas.
- **Copia de Azure a Azure.**. En este escenario, se agregan los datos distribuidos en orígenes de datos de Azure a un almacén de datos centralizado de Azure. Ejemplos: de tabla de Azure a blob de Azure, de blob de Azure a tabla de Azure, de tabla de Azure a SQL Azure, de blob de Azure a SQL Azure.

Consulte [Introducción a la Factoría de datos de Azure][adfgetstarted] para obtener un tutorial que muestra cómo copiar datos de un almacenamiento de blobs a una base de datos de SQL Azure mediante la actividad de copia. Consulte [Habilitación de canalizaciones para trabajar con datos locales][use-onpremises-datasources] para obtener un tutorial que muestra cómo copiar datos de una base de datos de SQL Server local a un almacenamiento de blobs de Azure mediante la actividad de copia.


## Componentes de la actividad de copia
La actividad de copia contiene los siguientes componentes: 

- **Tabla de entrada**. Una tabla es un conjunto de datos que tiene un esquema y es rectangular. Componente tabla de entrada 
- describe los datos de entrada para la actividad, a saber: nombre de la tabla, tipo de tabla y servicio vinculado que hace referencia a un origen de datos que contiene los datos de entrada.
- **Tabla de salida**. La tabla de salida describe los datos de salida de la actividad, a saber: nombre de la tabla, tipo de tabla y servicio vinculado que hace referencia a un origen de datos que contiene los datos de salida.
- **Reglas de transformación**. Las reglas de transformación especifican el modo en que se extraen los datos de entrada del origen y cómo se cargan los datos de salida en el receptor, etc.
 
Una actividad de copia puede tener una **tabla de entrada** y una **tabla de salida**.

## JSON para la actividad de copia
Una canalización consta de una o varias actividades. Las actividades en las canalizaciones se definen en la sección **activities[]**. El código JSON de una canalización es como el siguiente:
         
	{
		"name": "PipelineName",
		"properties": 
    	{
        	"description" : "pipeline description",
        	"activities":
        	[
	
    		]
		}
	}

Cada actividad de la sección **activities** tiene la siguiente estructura de nivel superior. La propiedad **type** debe establecerse en **CopyActivity**. La actividad de copia puede tener solo una tabla de entrada y una tabla de salida.
         

	{
		"name": "ActivityName",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [],
		"outputs":  [],
		"transformation":
		{

		},
		"policy":
		{
		
		}
	}

La tabla siguiente describe las etiquetas utilizadas con una sección de actividades. 

<table border="1">	
	<tr>
		<th align="left">Etiqueta</th>
		<th align="left">Descripción</th>
		<th align="left">Requerido</th>
	</tr>	

	<tr>
		<td>name</td>
		<td>Nombre de la actividad.</td>
		<td>Y</td>
	</tr>	

	<tr>
		<td>description</td>
		<td>Texto que describe para qué se usa la actividad.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>type</td>
		<td>Especifica el tipo de actividad. <br/><br/><b>type</b> debe establecerse en <b>CopyActivity</b>.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>Tablas de entrada que utiliza la actividad.  Especifique una única tabla de entrada para la actividad de copia.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>Tablas de salida que utiliza la actividad.  Especifique una única tabla de salida para la actividad de copia.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>Las propiedades de la transformación dependen del tipo.  La <b>actividad de copia</b> requiere que se especifique una sección <b>source</b> (origen) y una sección <b>sink</b> (receptor) dentro de la sección <b>transformation</b>. Más adelante en este artículo se proporcionan más detalles. </td>
		<td>Y</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>Directivas que afectan al comportamiento en tiempo de ejecución de la actividad. Si no se especifica, se utilizan los valores predeterminados.</td>
		<td>N</td>
	</tr>


</table>

Consulte la [documentación de referencia de scripting con JSON][json-script-reference] para obtener información detallada acerca de las etiquetas o propiedades de JSON.

## Ejemplo de actividad de copia
En este ejemplo, se definen una tabla de entrada y una tabla de salida, y se utilizan en una actividad de copia dentro de una canalización que copia datos de una base de datos de SQL Server local a un blob de Azure.

**Supuestos**
En estos sripts JSON de ejemplo se hace referencia a los siguientes artefactos de la Factoría de datos de Azure:

* Grupo de recursos denominado **ADF**.
* Una factoría de datos de Azure denominada **CopyFactory**.
* Un servicio vinculado denominado **MyOnPremisesSQLDB** que apunta a una base de datos de SQL Server local.
* Un servicio vinculado denominado **MyAzureStorage** que apunta a un almacenamiento de blobs de Azure.

### Tabla de entrada JSON
El siguiente script JSON define una tabla de entrada que hace referencia a una tabla SQL: **MyTable** en una base de datos de SQL Server local que el servicio vinculado **MyOnPremisesSQLDB** define. Tenga en cuenta que **name** es el nombre de la tabla de Factoría de datos de Azure y **tableName** es el nombre de la tabla de SQL en la base de datos de SQL Server.

         
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

El siguiente comando de ejemplo de Azure PowerShell utiliza el cmdlet **New-AzureDataFactoryTable** que usa un archivo JSON que contiene el script anterior para crear una tabla (**MyOnPremTable**) en una factoría de datos de Azure: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -Name MyOnPremTable -DataFactoryName CopyFactory -File <Filepath>\MyOnPremTable.json.

Consulte la [documentación de referencia de cmdlets][cmdlet-reference] para obtener información detallada sobre los cmdlets de Factoría de datos. 

### Tabla de salida JSON
El siguiente script JSON define una tabla de salida: **MyDemoBlob**, que hace referencia a un blob de Azure: **MyBlob** en la carpeta de blobs: **MySubFolder** en el contenedor de blobs: **MyContainer**.
         
	{
   		"name": "MyDemoBlob",
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

El siguiente comando de ejemplo de Azure PowerShell utiliza el cmdlet **New-AzureDataFactoryTable** que usa un archivo JSON que contiene el script anterior para crear una tabla (**MyDemoBlob**) en una factoría de datos de Azure: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>


### Canalización (con la actividad de copia) JSON
En este ejemplo, se define la canalización **CopyActivityPipeline** con las siguientes propiedades: 

- La propiedad **type** se establece en **CopyActivity**.
- **MyOnPremTable** se especifica como entrada (etiqueta **inputs**).
- **MyAzureBlob** se especifica como salida (etiqueta **outputs**)
La sección- ** Transformation** contiene dos subsecciones: **source** (origen) y **sink** (receptor). El tipo de origen se establece en **SqlSource** y el tipo de receptor se establece en **BlobSink**. **sqlReaderQuery** define la transformación (proyección) que se debe realizar en el origen. Para obtener información detallada de todas las propiedades, consulte la [documentación de referencia del scripting JSON][json-script-reference].

         
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


El siguiente comando de ejemplo de Azure PowerShell utiliza el cmdlet **New-AzureDataFactoryPipeline** que usa un archivo JSON que contiene el script anterior para crear una canalización (**CopyActivityPipeline**) en una factoría de datos de Azure: **CopyFactory**.
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>

## Entradas y salidas admitidas
El ejemplo anterior usa SqlSource como origen y BlobSink como receptor en la sección de transformación. En la tabla siguiente se enumeran los orígenes y receptores que admite la actividad de copia. 

<table border="1">	
	<tr>
		<th><i>Receptor/origen<i></th>
		<th>Blob de Azure</th>
		<th>Tabla de Azure</th>
		<th>Base de datos SQL de Azure</th>
		<th>SQL Server local</th>
		<th>SQL Server en IaaS</th>
	</tr>	

	<tr>
		<td><b>Blob de Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>

	<tr>
		<td><b>Tabla de Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>	

	<tr>
		<td><b>Base de datos SQL de Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>


	<tr>
		<td><b>SQL Server local</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>SQL Server en IaaS</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

</table>


En la tabla siguiente se enumeran los tipos de origen y receptor que pueden utilizarse en un archivo JSON de una canalización que contiene una actividad de copia.


<table border="1">	
	<tr>
		<th></th>
		<th align="left">Tipo de origen</th>
		<th align="left">Tipo de receptor</th>
	</tr>	

	<tr>
		<td><b>Blob de Azure</b></td>
		<td>BlobSource</td>
		<td>BlobSink</td>
	</tr>

	<tr>
		<td><b>Tabla de Azure</b></td>
		<td>AzureTableSource</td>
		<td>AzureTableSink</td>
	</tr>

	<tr>
		<td><b>Base de datos SQL de Azure</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>SQL Server local</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>SQL en IaaS</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>
</table>

En la siguiente tabla se enumeran las propiedades que admiten estos orígenes y receptores.

<table border="1">

	<tr>
	<th align="left">Origen/receptor</th>
	<th align="left">Propiedad admitida</th>
	<th align="left">Descripción</th>
	<th align="left">Valores permitidos</th>
	<th align="left">Requerido</th>
	</tr>

	<tr>
		<tr>
		<td><b>BlobSource</b></td>
		<td>BlobSourceTreatEmptyAsNull</td>
		<td>Especifica si se debe tratar una cadena nula o vacía como un valor nulo.</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
		</tr>
	</tr>

	<tr>
		<td></td>
		<td>BlobSourceSkipHeaderLineCount</td>
		<td>Indica cuántas líneas deben omitirse.</td>
		<td>Entero de 0 a Máx.</td>
		<td>N</td>
	</tr>

	<tr>
		<td><b>AzureTableSource</b></td>
		<td>AzureTableSourceQuery</td>
		<td>Utilice la consulta personalizada para leer los datos.</td>
		<td>Cadena de consulta de tabla de Azure.<br/>Ejemplo: "ColumnA eq ValueA"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>AzureTableSourceIgnoreTableNotFound</td>
		<td>Indica si debe omitirse la excepción: "la tabla no existe".</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>SqlSource</b></td>
		<td>sqlReaderQuery</td>
		<td>Utilice la consulta personalizada para leer los datos.</td>
		<td>Cadena de consulta SQL.<br/><br/> Por ejemplo: "Select * from MyTable".<br/><br/> Si no se especifica, seleccione <columnas definidas en la estructura> en la consulta MyTable.</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>AzureTableSink</b></td>
		<td>azureTableDefaultPartitionKeyValue</td>
		<td>Valor predeterminado de la clave de la partición que puede usar el receptor.</td>
		<td>Valor de cadena.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTablePartitionKeyName</td>
		<td>Nombre de columna especificado por el usuario, cuyos valores de columna se utilizan como clave de la partición.<br/><br/> Si no se especifica, se utiliza AzureTableDefaultPartitionKeyValue como clave de la partición.</td>
		<td>Un nombre de columna.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableRowKeyName</td>
		<td>Valores de la columna especificada que deben usarse como clave de fila.<br/><br/>Si no se especifica, se usa un GUID para cada fila.</td>
		<td>Un nombre de columna.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableInsertType</td>
		<td>Modo de insertar datos en la tabla de Azure.</td>
		<td>"merge"<br/><br/>"replace"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchSize</td>
		<td>Inserta datos en la tabla de Azure cuando se alcanza el valor de writeBatchSize o writeBatchTimeout.</td>
		<td>Entero de 1 a 100 (unidad = recuento de filas)</td>
		<td>N<br/><br/>(predet. = 100)</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchTimeout</td>
		<td>Inserta datos en la tabla de Azure cuando se alcanza el valor de writeBatchSize o writeBatchTimeout.</td>
		<td>(Unidad = marca de tiempo)<br/><br/>Ejemplo: "00:20:00" (20 minutos)</td>
		<td>N<br/><br/>(el valor predeterminado de marca de tiempo del cliente de almacenamiento es 90 segundos)</td>
	</tr>

	<tr>
		<td><b>SqlSink</b></td>
		<td>SqlWriterStoredProcedureName</td>
		<td>Especifica el nombre del procedimiento almacenado para actualizar o insertar datos en la tabla de destino.</td>
		<td>Un nombre de procedimiento almacenado.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>SqlWriterTableType</td>
		<td>Especifica el tipo de tabla que se usará en el procedimiento anterior.</td>
		<td>Un nombre de tipo de tabla.</td>
		<td>N</td>
	</tr>
</table>

### SQL en infraestructura como servicio (IaaS)
Para SQL en IaaS, se admite Azure como proveedor de IaaS. Se admiten las siguientes topologías VPN y de red. Tenga en cuenta que Data Management Gateway es necesario para los casos 2 y 3, mientras que no es necesario para el caso 1. Para obtener más información acerca de Data Management Gateway, consulte [Habilitación de canalizaciones para obtener acceso a datos locales][use-onpremises-datasources].

1.	Máquina virtual con el nombre DNS público y puerto público estático: asignación de puerto privado.
2.	Máquina virtual con el nombre DNS público sin exponer el extremo SQL.
3.	Red virtual
	<ol type='a'>
	<li>VPN de nube de Azure con la topología siguiente al final de la lista. </li>	
	<li>Máquina virtual con VPN de local a nube o de sitio a sitio usando Red Virtual de Azure.</li>	
	</ol>  
	![Data Factory with Copy Activity][image-data-factory-copy-actvity]

## Filtrado de columnas mediante la definición de estructuras
Según el tipo de tabla, es posible especificar un subconjunto de las columnas del origen especificando menos columnas en la definición de **Structure** de la definición de la tabla que las que hay en el origen de datos subyacente. La tabla siguiente proporciona información acerca de la lógica de filtrado de columnas para diferentes tipos de tabla. 

<table>

	<tr>
		<th align="left">Tipo de tabla</th>
		<th align="left">Lógica de filtrado de columnas</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>La definición de <b>Structure</b> en la tabla JSON debe coincidir con la estructura del blob.  Para seleccionar un subconjunto de columnas, utilice la característica de asignación de columnas que se describe en la sección siguiente: Reglas de transformación: asignación de columnas.</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation y OnPremisesSqlServerTableLocation</td>
		<td align="left">
			<p>Si la propiedad <b>SqlReaderQuery</b> está especificada como parte de la definición de la actividad de copia, la definición de <b>Structure</b> de la tabla debe alinearse con las columnas seleccionadas en la consulta.</p>
			<p>Si no se especifica la propiedad <b>SqlReaderQuery</b>, la actividad de copia construirá automáticamente una consulta SELECT basada en las columnas especificadas en la definición de <b>Structure</b> de la definición de la tabla.</p>
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			La sección <b>Structure</b> de la definición de la tabla puede contener el conjunto completo o un subconjunto de las columnas de la tabla de Azure subyacente.
		</td>
	<tr>

</table> 

## Reglas de transformación: asignación de columnas
La asignación de columnas puede utilizarse para especificar cómo se asignan las columnas de la tabla de origen a columnas de la tabla receptora. Admite los siguientes escenarios:

- Asignación de todas las columnas de la tabla de origen "structure" a la tabla de destino "structure".
- Asignación de un subconjunto de las columnas de la tabla de origen "structure" a toda la tabla de destino "structure".

No se admite lo siguiente y se produce una excepción: 

- Menos columnas o más columnas en el destino.
- Asignación duplicada.
- Resultado de la consulta SQL sin un nombre de columna.

#### Ejemplo 1: Asignación de columnas de SQL Server a un blob de Azure
En este ejemplo, la **tabla de entrada** se define del siguiente modo. La tabla de entrada tiene una estructura y apunta a una tabla SQL en una base de datos de SQL Server.
         
		{
		    "name": "MyOnPremTable",
    		"properties":
    		{
				"structure": 
				[
            		{ "name": "userid", "type": "String"},
            		{ "name": "name", "type": "String"},
            		{ "name": "group", "type": "Decimal"}
				],
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

En este ejemplo, la **tabla de salida** se define del siguiente modo. La tabla de salida tiene una estructura y apunta a un blob en un almacenamiento de blobs de Azure.
         
		
	{
		"name": "MyDemoBlob",
		"properties":
		{
    		"structure":
			[
        	    { "name": "myuserid", "type": "String"},
        	    { "name": "mygroup", "type": "String"},
        	    { "name": "myname", "type": "Decimal"}
			],
			"location":
    		{
    	    	"type": "AzureBlobLocation",
		        "folderPath": "MyContainer/MySubFolder",
				"fileName": "MyBlobName",
    	    	"linkedServiceName": "MyLinkedService",
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

Si no especifica un **fileName** para una **tabla de entrada**, todos los archivos o blobs de la carpeta de entrada (**folderPath**) se consideran entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada. Puede ver algunos archivos de ejemplo en el [tutorial][adf-tutorial].

Si no especifica un **fileName** para una **tabla de salida**, los archivos generados de **folderPath** se nombran con el siguiente formato: Data.<Guid>.txt (por ejemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

Para establecer **folderPath** y **fileName** de forma dinámica en función de la hora de **SliceStart**, use la propiedad **partitionedBy**. En el ejemplo siguiente, **folderPath** utiliza los valores Year, Month y Day de SliceStart (hora de inicio del segmento que se procesa) y fileName utiliza el valor Hour de SliceStart. Por ejemplo, si se está produciendo una división de 2014-10-20T08:00:00, el nombre de carpeta se establece en wikidatagateway/wikisampledataout/2014/10/20 y el nombre de archivo se establece en 08.csv. 

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

En este ejemplo, una actividad en una canalización se define como se indica a continuación. Las columnas del origen se asignan a columnas del receptor (**columnMappings**) usando la propiedad **Translator**.

##### Ejemplo: Definición de asignación de columnas

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "MyOnPremTable"  } ],
		"outputs":  [ { "name": "MyDemoBlob" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource"
    		},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
    		}
		}
	}

![Column Mapping][image-data-factory-column-mapping-1]

##### Ejemplo 2: Asignación de columnas con una consulta SQL de SQL Server a un blob de Azure
En este ejemplo, se utiliza una consulta SQL (en lugar de una tabla como en el ejemplo anterior) para extraer datos de un servidor SQL Server local, y se asignan las columnas de los resultados de la consulta al artefacto de origen y después al artefacto de destino. Para este ejemplo, la consulta devuelve 5 columnas.

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource",
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', Time.AddHours(SliceStart, 0))"
			},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
    		}
		}
	}

![Column Mapping 2][image-data-factory-column-mapping-2]

#### Control del tipo de datos con la actividad de copia

Los tipos de datos especificados en la sección Structure de la definición de la tabla solo se admiten para **BlobSource**.  La siguiente tabla describe cómo se controlan los tipos de datos para otros tipos de origen y receptor.

<table>	
	<tr>
		<th align="left">Origen/receptor</th>
		<th align="left">Lógica de control de tipos de datos</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>Los tipos de datos definidos en la sección <b>Structure</b> de la definición de la tabla se pasan por alto.  Los tipos de datos definidos en la base de datos SQL se utilizan para la extracción de datos durante la actividad de copia.</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>Los tipos de datos definidos en la sección <b>Structure</b> de la definición de la tabla se pasan por alto.  Se comparan los tipos de datos de origen y de destino subyacente, y se realiza la conversión de tipos implícita si hay discrepancias de tipo.</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td><p>Cuando se transfiere de <b>BlobSource</b> a <b>BlobSink</b>, no hay ninguna transformación de tipo. Los tipos definidos en la sección <b>Structure</b> de la definición de la tabla se pasan por alto.  Para destinos distintos de <b>BlobSink</b>, se usan los tipos de datos definidos en la sección <b>Structure</b> de la definición de la tabla.</p>
		<p>
		Si no se especifica la sección <b>Structure</b> en la definición de la tabla, el control de tipos depende de la propiedad <b>format</b> de <b>BlobSink</b>:
		</p>
		<ul>
			<li> <b>TextFormat:</b> todos los tipos de columna se tratan como cadena y todos los nombres de columna se establecen como "Prop_<0-N>"</li> 
			<li><b>AvroFormat:</b> utilice los nombres y tipos de columna integrados en el archivo Avro.</li> 
			<li><b>JsonFormat:</b> todos los tipos de columna se tratan como cadena y se usan los nombres de columna integrados en el archivo Json.</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>Los tipos de datos definidos en la sección <b>Structure</b> de la definición de la tabla de entrada se pasan por alto.  Se usan los tipos de datos definidos en el almacén de datos de entrada subyacente.  Las columnas se especifican como que admiten valores null para la serialización Avro.</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>Los tipos de datos definidos en la sección <b>Structure</b> de la definición de la tabla se pasan por alto.  Se usan los tipos de datos definidos en la tabla de Azure subyacente.</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>Los tipos de datos definidos en la sección <b>Structure</b> de la definición de la tabla se pasan por alto.  Se usan los tipos de datos definidos en el almacén de datos de entrada subyacente.</td>
	</tr>

</table>

## Limitaciones

> [WACOM.NOTE] Cuando se suspende o se elimina una canalización, o cuando se elimina una factoría de datos, no se suspende la operación de copia en curso. Seguirá ejecutándose hasta que termine. Sin embargo, la operación de copia que implica un origen de datos local puede detenerse con el reinicio del servicio Data Management Gateway usando el administrador de configuración de Data Management Gateway o el applet Servicios. 



## Tutoriales
Consulte [Introducción a la Factoría de datos de Azure][adfgetstarted] para obtener un tutorial que muestra cómo copiar datos de un almacenamiento de blobs a una base de datos de SQL Azure mediante la actividad de copia.
 
Consulte [Habilitación de canalizaciones para trabajar con datos locales][use-onpremises-datasources] para obtener un tutorial que muestra cómo copiar datos de una base de datos de SQL Server local a un almacenamiento de blobs de Azure mediante la actividad de copia.



[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png
