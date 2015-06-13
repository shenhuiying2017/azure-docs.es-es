<properties 
	pageTitle="Escenarios avanzados para usar la actividad de copia en la Factoría de datos de Azure" 
	description="Escenarios avanzados para usar la actividad de copia en la Factoría de datos de Azure." 
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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Escenarios avanzados para usar la actividad de copia en la Factoría de datos de Azure 
## Información general
Puede usar la **actividad de copia** en una canalización para copiar datos de un origen a un receptor (destino) en un lote. En este tema se describe los escenarios avanzados que admite la actividad de copia. Para obtener información general detallada de la actividad de copia y los escenarios principales que admite, consulte [Copia de datos con la Factoría de datos de Azure][adf-copyactivity].


## Filtrado de columnas mediante la definición de estructuras
Según el tipo de tabla, es posible especificar un subconjunto de  columnas del origen especificando menos columnas en la definición de **Structure** de la definición de la tabla que las que hay en el origen de datos subyacente. La tabla siguiente proporciona información acerca de la lógica de filtrado de columnas para diferentes tipos de tabla.

<table>

	<tr>
		<th align="left">Tipo de tabla</th>
		<th align="left">Lógica de filtrado de columnas</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>La definición de <b>Structure</b> en la tabla JSON debe coincidir con la estructura del blob. Para seleccionar un subconjunto de columnas, use la característica de asignación de columnas descrita en la siguiente sección: Normas de transformación: asignación de columnas.</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation y OnPremisesSqlServerTableLocation</td>
		<td align="left">
			Si la propiedad <b>SqlReaderQuery</b> está especificada como parte de la definición de la actividad de copia, la definición de <b>Structure</b> de la tabla debe ser coherente con las columnas seleccionadas en la consulta.<br/><br/>
			Si no se especifica la propiedad <b>SqlReaderQuery</b>, la actividad de copia construirá automáticamente una consulta SELECT basada en las columnas especificadas en la definición de <b>Structure</b> de la definición de la tabla.
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

En especial, al copiar datos entre dos blobs de Azure, la actividad de copia podría tratarla mayormente como una copia directa de datos binarios, a menos que se cumplan los tres escenarios siguientes:


1. Si las tablas de entrada y salida tienen un formato diferente, la actividad de copia realizará la conversión de formato;
2. Si la tabla de entrada se especifica como una carpeta que puede contener varios archivos y la tabla de salida se especifica como un archivo, la actividad de copia combinará los archivos de la carpeta de origen en un solo archivo de receptor;
3. Si se especifica "columnMapping", la actividad de copia realizará la transformación de datos correspondiente.


### Ejemplo 1: asignación de columnas de SQL Server a un blob de Azure
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

Si no especifica un valor **fileName** para una tabla de salida, los archivos generados en la **ruta de la carpeta** se denominan con el siguiente formato: Data.<Guid>.txt (por ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

Para establecer **folderPath** y **fileName** de forma dinámica según la hora de **SliceStart**, use la propiedad **partitionedBy**. En el ejemplo siguiente, **folderPath** usa Year, Month y Day de SliceStart (hora de inicio del segmento que se va a procesar) y fileName usa Hour de SliceStart. Por ejemplo, si se está produciendo una división de 2014-10-20T08:00:00, el nombre de carpeta se establece en wikidatagateway/wikisampledataout/2014/10/20 y el nombre de archivo se establece en 08.csv.

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

#### Ejemplo: definición de asignación de columnas
En este ejemplo, una actividad en una canalización se define como se indica a continuación. Las columnas del origen se asignan a columnas del receptor (**columnMappings**) usando la propiedad **Translator**.

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

![Asignación de columnas][image-data-factory-column-mapping-1]

### Ejemplo 2: asignación de columnas con una consulta SQL de SQL Server a un blob de Azure
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
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = '{0:yyyyMMdd-HH}'', SliceStart)"
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

![Asignación de columnas 2][image-data-factory-column-mapping-2]

## Control del tipo de datos con la actividad de copia

Los tipos de datos especificados en la sección Structure de la definición de la tabla solo se admiten para **BlobSource**. La siguiente tabla describe cómo se controlan los tipos de datos para otros tipos de origen y receptor.

<table>	
	<tr>
		<th align="left">Origen/receptor</th>
		<th align="left">Lógica de control de tipos de datos</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>Los tipos de datos definidos en la sección <b>Structure</b> de la definición de tabla se omiten. Los tipos de datos definidos en la base de datos SQL subyacente se usarán para la extracción de datos durante la actividad de copia.</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>Los tipos de datos definidos en la sección <b>Structure</b> de la definición de tabla se omiten. Se compararán los tipos de datos del origen y destino subyacentes y se realizará la conversión de tipo implícito si hay coincidencias de tipo.</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td>Durante la transferencia de <b>BlobSource</b> a <b>BlobSink</b>, no hay ninguna transformación de tipos; los tipos de datos definidos en la sección <b>Structure</b> de la definición de tabla se omiten. Para destinos distintos de <b>BlobSink</b>, se usan los tipos de datos definidos en la sección <b>Structure</b> de la definición de la tabla.<br/><br/>
		Si no se especifica la sección <b>Structure</b> en la definición de la tabla, el control de tipos depende de la propiedad <b>format</b> de la tabla <b>BlobSource</b>:
		<ul>
			<li> <b>TextFormat:</b>: todos los tipos de columna se tratan como cadena y todos los nombres de columna se establecen como "Prop_&lt;0-N>"</li> 
			<li><b>AvroFormat:</b> usa los nombres y tipos de columna integrados en el archivo Avro.</li> 
			<li><b>JsonFormat:</b>: todos los tipos de columna se tratan como una cadena y se usan los nombres de columna integrados en el archivo Json.</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>Los tipos de datos definidos en la sección <b>Structure</b> de la definición de tabla se omiten. Se usarán los tipos de datos definidos en el almacén de datos de entrada subyacente. Las columnas se especificarám como que aceptan valores NULL para la serialización de Avro.</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>Los tipos de datos definidos en la sección <b>Structure</b> de la definición de tabla se omiten. Se usarán los tipos de datos definidos en la tabla de Azure subyacente.</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>Los tipos de datos definidos en la sección <b>Structure</b> de la definición de tabla se omiten. Se usarán los tipos de datos definidos en el almacén de datos de entrada subyacente.</td>
	</tr>

</table>

**Nota:** Tabla de Azure solo admite un conjunto limitado de tipos de datos, consulte [Introducción al modelo de datos del servicio Tabla][azure-table-data-type].

## Invocación del procedimiento almacenado para el receptor de SQL
Al copiar datos en SQL Server o en Base de datos SQL de Azure, se puede configurar e invocar un procedimiento almacenado especificado por el usuario con parámetros adicionales.
### Ejemplo
1. Defina el JSON de la tabla de salida de la manera siguiente (tome la tabla Base de datos SQL de Azure como ejemplo):

    	{
    		"name": "MyAzureSQLTable",
    		"properties":
    		{
    			"location":
    			{
    				"type": "AzureSqlTableLocation",
    				"tableName": "Marketing",
    				"linkedServiceName": "AzureSqlLinkedService"
    			},
    			"availability":
    			{
    				"frequency": "Hour",
    				"interval": 1
    			}
    		}
    	}

2. Defina la sección **SqlSink** en el JSON de actividad de copia como se indica a continuación. Llame a un procedimiento almacenado al insertar datos, las propiedades **SqlWriterStoredProcedureName** y **SqlWriterTableType** son necesarias.

		"sink":
	    {
			"type": "SqlSink",
	        "SqlWriterTableType": "MarketingType",
		    "SqlWriterStoredProcedureName": "spOverwriteMarketing",	
			"storedProcedureParameters":
					{
                    	"stringData": 
						{
                        	"value": "str1"		
						}
                    }
	    }

3. En la base de datos, defina el procedimiento almacenado con el mismo nombre que **SqlWriterStoredProcedureName**. De este modo, se administran los datos de entrada desde el origen especificado y se insertan en la tabla de salida. Tenga en cuenta que el nombre del parámetro del procedimiento almacenado debe ser el mismo que el de **TableName** definido en el archivo JSON de la tabla.

		CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
		AS
		BEGIN
			DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    		INSERT [dbo].[Marketing](ProfileID, State)
    		SELECT * FROM @Marketing
		END


4. En la base de datos, defina el tipo de tabla con el mismo nombre que **SqlWriterTableType**. Tenga en cuenta que el esquema del tipo de tabla debe ser el mismo que el esquema devuelto por los datos de entrada.

		CREATE TYPE [dbo].[MarketingType] AS TABLE(
    	    [ProfileID] [varchar](256) NOT NULL,
    	    [State] [varchar](256) NOT NULL,
    	)

La característica de procedimiento almacenado aprovecha los [parámetros con valores de tabla][table-valued-parameters].

## Especificación de la codificación para archivos de texto
Aunque la codificación UTF-8 es bastante popular, con frecuencia los archivos de texto de hora de Blob de Azure siguen otras codificaciones por motivos históricos. La propiedad **encodingName** le permite especificar la codificación por nombre de la página de código para las tablas de tipo TextFormat. Para obtener la lista de nombres de codificación válidos, vea: Propiedad Encoding.EncodingName. Por ejemplo: windows-1250 o shift_jis. El valor predeterminado es: UTF-8. Consulte [Clase de codificación](https://msdn.microsoft.com/library/system.text.encoding(v=vs.110).aspx) para obtener nombres de codificación válidos.

## Otras referencias

- [Ejemplos de uso de la actividad de copia][copy-activity-examples]
- [Copia de datos con la Factoría de datos de Azure][adf-copyactivity]
- [Actividad de copia: referencia de scripting](https://msdn.microsoft.com/library/dn835035.aspx)
- [Vídeo: Presentación de la actividad de copia de la Factoría de datos de Azure][copy-activity-video]


[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx


[adfgetstarted]: data-factory-get-started.md
[adf-copyactivity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[azure-table-data-type]: https://msdn.microsoft.com/en-us/library/azure/dd179338.aspx

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample2.png

<!---HONumber=GIT-SubDir--> 