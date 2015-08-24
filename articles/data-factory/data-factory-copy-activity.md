<properties 
	pageTitle="Copia de datos con la Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo usar la actividad de copia en la Factoría de datos de Azure para copiar datos desde un origen de datos a otro origen de datos." 
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
	ms.date="07/21/2015" 
	ms.author="spelluru"/>

# Copia de datos con la Factoría de datos de Azure (actividad de copia)
## Información general
Puede usar la **actividad de copia** en una canalización para copiar datos de un origen a un receptor (destino) en un lote. La actividad de copia se puede usar en los escenarios siguientes:

- **Entrada en Azure**. En este escenario, los datos se copian desde un origen de datos local (p. ej., SQL Server) a un almacén de datos de Azure (p. ej., blob de Azure, tabla de Azure o Base de datos SQL de Azure) para los subescenarios siguientes:
	- Recopilar datos en una ubicación centralizada en Azure para su posterior procesamiento.
	- Migrar a Azure datos de plataformas locales o en la nube que no sean Azure.
	- Archivar o crear copias de seguridad en Azure para un almacenamiento en capas rentable.
- **Salida de Azure**. En este escenario, los datos se copian desde Azure (p. ej., blob de Azure, tabla de Azure o Base de datos SQL de Azure) a un almacén de datos o data marts locales (p. ej., SQL Server) para los subescenarios siguientes:
	- Transferir datos a la infraestructura local debido a la falta de espacio para almacenamiento de datos en la nube.
	- Transferir datos a la infraestructura local para aprovechar una solución o una infraestructura de informes locales.
	- Archivar o realizar copias de seguridad de datos en la infraestructura local para almacenamiento en capas.
- **Copia de Azure a Azure**. En este escenario, se agregan los datos distribuidos en orígenes de datos de Azure a un almacén de datos centralizado de Azure. Ejemplos: de tabla de Azure a blob de Azure, de blob de Azure a tabla de Azure, de tabla de Azure a SQL Azure, de blob de Azure a SQL Azure.

Para obtener más información:

- Vea el vídeo [Presentación de la actividad de copia de la Factoría de datos de Azure][copy-activity-video].
- Vea [Introducción a la Factoría de datos de Azure][adfgetstarted] para consultar un tutorial en el que se muestra cómo copiar datos de un almacenamiento de blobs de Azure a una base de datos SQL de Azure mediante la actividad de copia. 
- Vea [Habilitación de las canalizaciones para que funcionen con datos locales][use-onpremises-datasources] para consultar un tutorial en el que se muestra cómo copiar datos de una base de datos de SQL Server local a un almacenamiento de blobs de Azure mediante la actividad de copia.


## Orígenes y receptores compatibles
La actividad de copia admite los siguientes escenarios de movimiento de datos:

| *Origen/receptor* | Blob de Azure | Tabla de Azure | Base de datos SQL de Azure | DocumentDB de Azure | SQL Server en máquinas virtuales de Azure | SQL Server local |
| ------------- | ---------- | ----------- | ------------------ | ---------------- | ------------------ | ------------------- |
| Blob de Azure | X | X | X | X | X | X |
| Tabla de Azure | X | X | X | X | X | X |
| Base de datos SQL de Azure | X | X | X | X | X | X |
| DocumentDB de Azure | X | X | X | | | |  
| SQL Server local | X | X | X | | X | X |
| SQL Server en máquinas virtuales de Azure | X | X | X | | X | X |
| Sistema de archivos local | X | X | X | | X | X |
| Base de datos de Oracle local | X | X | X | | X | X |
| Base de datos MySQL local| X | X | X | | X | X |
| Base de datos DB2 local | X | X | X | | X | X |
| Base de datos Teradata local | X | X | X | | X | X |
| Base de datos Sybase local | X | X | X | | X | X |
| Base de datos PostgreSQL local | X | X | X | | X | X |


Para obtener información más detallada, consulte el tema [Orígenes y receptores compatibles](https://msdn.microsoft.com/library/dn894007.aspx) en MSDN Library.

### SQL en infraestructura como servicio (IaaS)
También se admite SQL Server en IaaS como origen y receptor. Data Management Gateway es necesario al crear un servicio vinculado a SQL Server en IaaS. Considere la posibilidad de instalar Data Management Gateway en una máquina virtual que no sea la que hospeda SQL Server para evitar la degradación del rendimiento debido a que tanto SQL Server como la puerta de enlace compiten por los recursos. Para obtener detalles acerca de Data Management Gateway, vea [Habilitación de las canalizaciones para tener acceso a datos locales][use-onpremises-datasources].

1.	Máquina virtual con nombre DNS público y puerto público estático: asignación de puerto privado
2.	Máquina virtual con el nombre DNS público sin exponer el extremo SQL.
3.	Red virtual
	<ol type='a'>
<li>VPN de nube de Azure con la topología siguiente al final de la lista. </li>	
<li>Máquina virtual con VPN de local a nube o de sitio a sitio usando Red Virtual de Azure.</li>	
</ol>![Factoría de datos con actividad de copia][image-data-factory-copy-actvity]

## Componentes de la actividad de copia
La actividad de copia contiene los siguientes componentes:

- **Tabla de entrada**. Una tabla es un conjunto de datos que tiene un esquema y es rectangular. El componente de tabla de entrada describe los datos de entrada para la actividad entre los que se incluyen los siguientes: el nombre de la tabla, el tipo de tabla y el servicio vinculado que hace referencia a un origen de datos que contiene los datos de entrada.
- **Tabla de salida**. La tabla de salida describe los datos de salida para la actividad entre los que se incluyen los siguientes: el nombre de la tabla, el tipo de la tabla y el servicio vinculado que hace referencia a un origen de datos que contiene los datos de salida.
- **Reglas de transformación**. Las reglas de transformación especifican el modo en que se extraen los datos de entrada del origen y cómo se cargan los datos de salida en el receptor, etc.
 
Una actividad de copia puede tener una **tabla de entrada** y otra **tabla de salida**.

## <a name="CopyActivityJSONSchema"></a>JSON para la actividad de copia
Una canalización consta de una o varias actividades. Las actividades de las canalizaciones se definen en la sección de **actividades**. El código JSON de una canalización es el siguiente:
         
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

Cada actividad de la sección **actividades** tiene la siguiente estructura de nivel superior. La propiedad **type** se debe establecer en **CopyActivity**. La actividad de copia solo puede tener una tabla de entrada y una tabla de salida.
         

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

| Etiqueta | Descripción | Obligatorio |
|-----|-------------|----------|
|name|Nombre de la actividad.|Y|
|description|Texto que describe para qué se usa la actividad.|Y|
|type|Especifica el tipo de actividad. El tipo debe establecerse en **Copy**. |Y|
|inputs|Tablas de entrada utilizadas por la actividad. Especifique solo una tabla de entrada para la actividad de copia. | Y
|outputs|Tablas de salida usadas por la actividad. Especifique solamente una tabla de salida para la actividad de copia. | Y
|transformation|Las propiedades de la transformación dependen del tipo. La actividad de copia requiere que se especifique una sección de origen y una sección de receptor dentro de la sección de transformación. Más adelante en este artículo se proporcionan más detalles.|Y
|policy| Directivas que afectan al comportamiento en tiempo de ejecución de la actividad. Si no se especifica, se utilizan los valores predeterminados. | N

Vea la [Referencia del scripting JSON][json-script-reference] para obtener información detallada acerca de las etiquetas o propiedades JSON.

### tipos de origen y receptor
Para obtener una lista de tipos de origen y de receptor, así como de las propiedades que admiten estos tipos, vea el tema [Orígenes y receptores compatibles][msdn-supported-sources-sinks] en la Biblioteca de MSDN.

## Ejemplo de actividad de copia
En este ejemplo, se definen una tabla de entrada y una tabla de salida, y se utilizan en una actividad de copia dentro de una canalización que copia datos de una base de datos de SQL Server local a un blob de Azure.

**Asunciones** En estos scripts JSON de ejemplo se hace referencia a los siguientes artefactos de la Factoría de datos de Azure:

* Grupo de recursos denominado **ADF**.
* Una factoría de datos de Azure denominada **CopyFactory**.
* Un servicio vinculado denominado **MyOnPremisesSQLDB** que apunta a una base de datos de SQL Server local.
* Un servicio vinculado denominado **MyAzureStorage** que apunta a un almacenamiento de blobs de Azure.

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

El siguiente comando de ejemplo de Azure PowerShell usa el cmdlet **New-AzureDataFactoryTable** que usa un archivo JSON que contiene el script anterior para crear una tabla (**MyOnPremTable**) en una factoría de datos de Azure: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF –Name MyOnPremTable –DataFactoryName CopyFactory –File <Filepath>\MyOnPremTable.json.

Vea [Referencia de cmdlet][cmdlet-reference] para obtener detalles acerca de los cmdlets de la Factoría de datos.

### Tabla de salida JSON
El siguiente script JSON define una tabla de salida: **MyDemoBlob**, que hace referencia a un blob de Azure: **MyBlob** en la carpeta de blobs: **MySubFolder** del contenedor de blobs: **MyContainer**.
         
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
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory –File <Filepath>


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


 El siguiente comando de ejemplo de Azure PowerShell **utiliza el cmdlet New-AzureDataFactoryPipeline** que usa un archivo JSON que contiene el script anterior para crear una canalización (**CopyActivityPipeline**) en una factoría de datos de Azure: **CopyFactory**.
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF –DataFactoryName CopyFactory –File <Filepath>


## Seguridad
Esta sección incluye instrucciones generales de seguridad y prácticas recomendadas para establecer el acceso seguro a almacenes de datos para la actividad de copia.

Para los almacenes de datos que ofrecen conexión HTTPS, elija la conexión HTTPS para la actividad de copia a fin de establecer una comunicación segura a través de la red. Por ejemplo, para **Almacenamiento de Azure**, use **DefaultEndpointsProtocol=https** en la cadena de conexión.

Para **Base de datos SQL de Azure**, solicite explícitamente una conexión cifrada y no confíe en los certificados de servidor para evitar el ataque de tipo "Man in the middle". Para ello, use **Encrypt=True** y **TrustServerCertificate=False** en la cadena de conexión. Para obtener más información, vea [Instrucciones y limitaciones de seguridad de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ff394108.aspx).

Para bases de datos tradicionales como **SQL Server**, especialmente cuando las instancias están en una Máquina Virtual de Azure, habilite la opción de conexión cifrada mediante la configuración de un certificado firmado, con **Encrypt=True** y **TrustServerCertificate=False** en la cadena de conexión. Para obtener más información, consulte [Habilitar conexiones cifradas en el motor de base de datos](https://msdn.microsoft.com/library/ms191192(v=sql.110)) y [Sintaxis de cadena de conexión.](https://msdn.microsoft.com/library/ms254500.aspx).

## Escenarios avanzados
- **Filtrado de columnas mediante la definición de estructuras**. Según el tipo de tabla, es posible especificar un subconjunto de las columnas del origen especificando menos columnas en la definición de **estructura** de la definición de la tabla que las que hay en el origen de datos subyacente.
- **Reglas de transformación: asignación de columnas**. La asignación de columnas puede utilizarse para especificar cómo se asignan las columnas de la tabla de origen a columnas de la tabla receptora.
- **Control del tipo de datos con la actividad de copia**. Explica en qué caso los tipos de datos especificados en la sección Estructura de la definición de la tabla se admiten/ignoran.
- **Invocar el procedimiento almacenado para el receptor de SQL**. Al copiar datos en SQL Server o en Base de datos SQL de Azure, se puede configurar e invocar un procedimiento almacenado especificado por el usuario.


## Tutoriales
Vea [Introducción a la Factoría de datos de Azure][adfgetstarted] para consultar un tutorial en el que se muestra cómo copiar datos de un almacenamiento de blobs de Azure a una base de datos SQL de Azure mediante la actividad de copia.
 
Vea [Habilitación de las canalizaciones para que funcionen con datos locales][use-onpremises-datasources] para consultar un tutorial en el que se muestra cómo copiar datos de una base de datos local de SQL Server a un almacenamiento de blobs de Azure mediante la actividad de copia.

## Otras referencias
- [Vídeo: Presentación de la actividad de copia de la Factoría de datos de Azure][copy-activity-video]
- [Tema Actividad de copia en la Biblioteca de MSDN][msdn-copy-activity]

[msdn-copy-activity]: https://msdn.microsoft.com/library/dn835035.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-tables-topic]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-supported-sources-sinks]: https://msdn.microsoft.com/library/dn894007.aspx
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx

[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md

[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png
 

<!---HONumber=August15_HO7-->