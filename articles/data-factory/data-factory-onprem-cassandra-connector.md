<properties 
	pageTitle="Movimiento de datos de Cassandra mediante Data Factory de Azure | Microsoft Azure" 
	description="Aprenda a mover los datos de una base de datos de Cassandra local con Data Factory de Azure." 
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

# Movimiento de datos desde una base de datos de Cassandra local con Data Factory de Azure
En este artículo se describe cómo puede usar la actividad de copia de Data Factory de Azure para copiar datos de una base de datos de Cassandra local en cualquier almacén de datos que aparezca en la columna Receptores de la sección [Almacenes de datos que se admiten](data-factory-data-movement-activities.md#supported-data-stores). Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

En la actualidad, Data Factory solo permite mover datos de una base de datos de Cassandra a [almacenes de datos receptores admitidos](data-factory-data-movement-activities.md#supported-data-stores), pero no permite mover datos de otros almacenes de datos a una base de datos de Cassandra.

## Requisitos previos
Para que el servicio Data Factory de Azure pueda conectarse a la base de datos de Cassandra local, debe instalar lo siguiente:

- Data Management Gateway 2.0 o una versión posterior en la misma máquina que hospeda la base de datos o en una máquina distinta, para evitar que se dispute los recursos con la base de datos. Data Management Gateway es un software que conecta orígenes de datos locales a servicios en la nube de forma segura y administrada. Consulte el artículo [Mover datos entre orígenes locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información acerca de Data Management Gateway.
  
	Cuando instale la puerta de enlace, se instalará automáticamente el controlador ODBC de Microsoft Cassandra que se utiliza para establecer la conexión con la base de datos de Cassandra.

> [AZURE.NOTE] Vea [Solución de problemas de puerta de enlace](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace.

## Asistente para copia de datos
La manera más fácil de crear una canalización que copie datos de una base de datos de Cassandra en cualquiera de los almacenes de datos receptores admitidos es usar el Asistente para copiar datos. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

En el siguiente ejemplo, se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización mediante el [Portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

## Ejemplo: Copiar datos de Cassandra en un blob
El ejemplo copia los datos de una base de datos de Cassandra en un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos. Los datos se pueden copiar directamente en cualquiera de los receptores que se indican en el artículo [Movimiento de datos y actividad de copia](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia de Data Factory de Azure.

- Un servicio vinculado de tipo [OnPremisesCassandra](#onpremisescassandra-linked-service-properties).
- Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [CassandraTable](#cassandratable-properties).
- Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que use [CassandraSource](#cassandrasource-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Servicio vinculado de Cassandra**

En este ejemplo, se utiliza el servicio vinculado de **Cassandra**. Consulte la sección [Servicio vinculado de Cassandra](#onpremisescassandra-linked-service-properties) para ver las propiedades admitidas por este servicio vinculado.

	{
    	"name": "CassandraLinkedService",
    	"properties":
    	{
			"type": "OnPremisesCassandra",
			"typeProperties":
			{
				"authenticationType": "Basic",
				"host": "mycassandraserver",
				"port": 9042,
				"username": "user",
				"password": "password",
				"gatewayName": "mygateway"
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

**Conjunto de datos de entrada de Cassandra**

	{
		"name": "CassandraInput",
		"properties": {
			"linkedServiceName": "CassandraLinkedService",
			"type": "CassandraTable",
			"typeProperties": {
				"tableName": "mytable",
				"keySpace": "mykeyspace" 
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

Si se establece **external** en **true**, se informa al servicio Data Factory que el conjunto de datos es externo a la factoría de datos y que no lo genera ninguna actividad de la factoría de datos.

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
				"folderPath": "adfgetstarted/fromcassandra"
			},
			"availability":
			{
				"frequency": "Hour",
				"interval": 1
			}
		}
	}


**Canalización con actividad de copia**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de la canalización, el tipo de **origen** está establecido en **CassandraSource**, mientras que el tipo de **receptor** está establecido en **BlobSink**.

Consulte [Propiedades de tipo RelationalSource](#cassandrasource-type-properties) para obtener la lista de propiedades admitidas por RelationalSource.
	
	{  
		"name":"SamplePipeline",
		"properties":{  
			"start":"2016-06-01T18:00:00",
			"end":"2016-06-01T19:00:00",
			"description":"pipeline with copy activity",
			"activities":[  
			{
				"name": "CassandraToAzureBlob",
				"description": "Copy from Cassandra to an Azure blob",
				"type": "Copy",
				"inputs": [
				{
					"name": "CassandraInput"
				}
				],
				"outputs": [
				{
					"name": "AzureBlobOutput"
				}
				],
				"typeProperties": {
					"source": {
						"type": "CassandraSource",
						"query": "select id, firstname, lastname from mykeyspace.mytable"
		
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
## Propiedades del servicio vinculado OnPremisesCassandra

La tabla siguiente incluye una descripción de los elementos JSON específicos para el servicio vinculado de Cassandra.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| type | La propiedad type debe estar establecida en: **OnPremisesCassandra** | Sí |
| host | Una o varias direcciones IP o nombres de host de los servidores de Cassandra.<br/><br/>Especifica una lista de direcciones IP o nombres de host separada por comas para conectar con todos los servidores a la vez. | Sí | 
| puerto | Puerto TCP que el servidor de Cassandra utiliza para escuchar las conexiones del cliente. | No. El valor predeterminado es 9042. |
| authenticationType | Básica o anónima | Sí |
| nombre de usuario |Especifique el nombre de usuario de la cuenta de usuario. | Sí, si el valor de authenticationType es Basic. |
| contraseña | Especifique la contraseña para la cuenta de usuario. | Sí, si el valor de authenticationType es Basic. |
| gatewayName | Nombre de la puerta de enlace que se va a utilizar en la conexión con la base de datos de Cassandra local. | Sí |
| encryptedCredential | Credencial cifrada por la puerta de enlace. | No | 

## Propiedades de CassandraTable

Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties de los conjuntos de datos de tipo **CassandraTable** tiene las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| keyspace | Nombre del espacio de claves o esquema de la base de datos de Cassandra. | Sí (si no hay definida ninguna **consulta** para **CassandraSource**). |
| tableName | Nombre de la tabla de la base de datos de Cassandra. | Sí (si no hay definida ninguna **consulta** para **CassandraSource**). |


## Propiedades de tipo CassandraSource
Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

En el caso de la actividad de copia, cuando el origen es de tipo **CassandraSource**, las propiedades que se indican a continuación están disponibles en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Utilice la consulta personalizada para leer los datos. | Consulta SQL-92 o consulta CQL. Vea la [referencia de CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Cuando utilice una consulta SQL, especifique **nombre de espacio de claves.nombre de tabla** para representar la tabla que quiere consultar. | No (si tableName y el espacio de claves del conjunto de datos están definidos). |
| consistencyLevel | El nivel de coherencia establece el número de réplicas que deben responder a una solicitud de lectura antes de que se devuelvan datos a la aplicación cliente. Cassandra comprueba el número de réplicas especificado para que los datos satisfagan la solicitud de lectura. | ONE, TWO, THREE, QUORUM, ALL, LOCAL\_QUORUM, EACH\_QUORUM, LOCAL\_ONE. Para más información, consulte [Configuring data consistency](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) (Configuración de la coherencia de datos). | No. El valor predeterminado es ONE. |  


### Asignación de tipos de Cassandra
Tipo de Cassandra | Tipo basado en .NET
--------------- | ---------------
ASCII |	String 
BIGINT | Int64
BLOB | Byte
BOOLEAN | Booleano
DECIMAL | Decimal
DOUBLE | Doble
FLOAT | Single
INET | String
INT | Int32
TEXT | String
TIMESTAMP | DateTime
TIMEUUID | Guid
UUID | Guid
VARCHAR | String
VARINT | Decimal

> [AZURE.NOTE]  
Par más información sobre tipos de colecciones (map, set, list, etc.), consulte la sección [Uso de tipos de colección de Cassandra con tablas virtuales](#work-with-collections-using-virtual-table).
> 
> No se admiten tipos definidos por el usuario.
> 
> La longitud de la columna binaria y la columna de cadena no puede ser superior a 4000.

## Uso de colecciones con tablas virtuales
Data Factory de Azure utiliza un controlador ODBC integrado para conectarse a una base de datos de Cassandra y copiar datos de dicha base de datos. En el caso de los tipos de colección (como map, set y list), el controlador volverá a normalizar los datos en las tablas virtuales correspondientes. En concreto, si una tabla contiene columnas de colecciones, el controlador generará las siguientes tablas virtuales:

-	Una **tabla base**, que contiene los mismos datos que la tabla real, salvo las columnas de colecciones. La tabla base utiliza el mismo nombre que la tabla real a la que representa.
-	Una **tabla virtual** para cada columna de la colección, que ampliará los datos anidados. Para asignar un nombre a las tablas virtuales que representan colecciones, se utiliza el nombre de la tabla real, un separador “_vt_” y el nombre de la columna.

Las tablas virtuales hacen referencia a los datos de la tabla real, lo que permite al controlador obtener acceso a los datos no normalizados. Consulte el ejemplo de la siguiente sección para más información. Para acceder al contenido de las colecciones de Cassandra, puede crear consultas y combinar las tablas virtuales.

Si utiliza el [Asistente para copia](data-factory-data-movement-activities.md#data-factory-copy-wizard), podrá consultar una vista intuitiva de la lista de tablas de la base de datos de Cassandra (incluidas las tablas virtuales) y una vista previa de los datos incluidos. También puede crear una consulta en el Asistente para copia y validarla para ver el resultado.

### Ejemplo
Por ejemplo, el objeto “ExampleTable” que aparece a continuación es una tabla de una base de datos de Cassandra que contiene una columna de clave principal de enteros denominada “pk\_int”, una columna de texto denominada “value“, una columna List, una columna Map y una columna Set (denominada “StringSet”).

pk\_int | Valor | Enumerar | Map |	StringSet
------ | ----- | ---- | --- | --------
1 | "valor de ejemplo 1" | ["1", "2", "3"] | {"S1": "a", "S2": "b"} | {"A", "B", "C"}
3 | "valor de ejemplo 3" | ["100", "101", "102", "105"] | {"S1": "t"} | {"A", "E"}

El controlador generará varias tablas virtuales que representan a esta tabla. Las columnas de clave externa de las tablas virtuales hacen referencia a las columnas de clave principal de la tabla real e indican qué fila de la tabla real se corresponde con la fila de la tabla virtual.

La primera tabla virtual es la tabla base y se denomina “ExampleTable”, tal y como se muestra a continuación. La tabla base contiene los mismos datos que la tabla de base de datos original a excepción de las colecciones, que no aparecen en esta tabla, sino que se amplían en otras tablas virtuales.

pk\_int | Valor
------ | -----
1 | "valor de ejemplo 1"
3 | "valor de ejemplo 3"

Las tablas siguientes representan las tablas virtuales que normalizan de nuevo los datos de las columnas List, Map y StringSet. Las columnas cuyos nombres terminan en “\_index” o “\_key” indican la posición de los datos en la columna List o Map original. Las columnas cuyos nombres terminan en “\_value” contienen los datos ampliados de la colección.

#### Tabla “ExampleTable\_vt\_List”:
pk\_int | List\_index | List\_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### Tabla “ExampleTable\_vt\_Map”:
pk\_int | Map\_key | Map\_value
------ | ------- | ---------
1 | S1 | Encontrará
1 | S2 | b
3 | S1 | t

#### Tabla “ExampleTable\_vt\_StringSet”:
pk\_int | StringSet\_value
------ | ---------------
1 | Encontrará
1 | B
1 | C
3 | Encontrará
3 | E





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Data Factory de Azure y las diversas formas de optimizarlo.

<!---HONumber=AcomDC_0720_2016-->