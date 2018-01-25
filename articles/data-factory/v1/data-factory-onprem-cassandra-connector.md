---
title: Movimiento de datos de Cassandra mediante Data Factory | Microsoft Docs
description: Aprenda a mover los datos de una base de datos de Cassandra local con Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c8f61cb165b0bfffe2f42b060cdbd666fff3a8b3
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Movimiento de datos desde una base de datos de Cassandra local con Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](data-factory-onprem-cassandra-connector.md)
> * [Versión 2: versión preliminar](../connector-cassandra.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory, que está disponible con carácter general. Si usa la versión 2 del servicio Data Factory, que se encuentra en versión preliminar, vea [Cassandra connector in V2](../connector-cassandra.md) (Conector de Cassandra en V2).

En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos de una base de datos de Cassandra local. Se basa en la información general que ofrece el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md).

Puede copiar datos de un almacén de datos de Cassandra local a cualquier almacén de datos receptor admitido. Consulte la tabla de [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como receptores. Data Factory solo admite actualmente el movimiento de datos desde un almacén de datos de Cassandra hasta otros almacenes de datos, pero no al contrario. 

## <a name="supported-versions"></a>Versiones compatibles
El conector de Cassandra admite la versión 2.X de Cassandra.

## <a name="prerequisites"></a>requisitos previos
Para que el servicio de Azure Data Factory pueda conectarse a la base de datos de Cassandra local, se debe instalar Data Management Gateway en la misma máquina que hospeda la base de datos o en una máquina independiente, con el fin de evitar la competencia por los recursos con la base de datos. Data Management Gateway es un componente que conecta orígenes de datos locales a servicios en la nube de forma segura y administrada. Consulte el artículo [Data Management Gateway](data-factory-data-management-gateway.md) para más detalles sobre Data Management Gateway. Consulte el artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para instrucciones paso a paso sobre cómo configurar la puerta de enlace como canalización de datos para mover datos.

Debe usar la puerta de enlace para conectarse a una base de datos de Cassandra incluso si la base de datos está hospedada en la nube, por ejemplo, en una máquina virtual de IaaS de Azure. Puede tener la puerta de enlace en la misma máquina virtual que hospeda la base de datos o en una máquina virtual independiente, siempre que la puerta de enlace se pueda conectar a la base de datos.  

Cuando instale la puerta de enlace, se instalará automáticamente el controlador ODBC de Microsoft Cassandra que se utiliza para establecer la conexión con la base de datos de Cassandra. Por lo tanto, no es necesario instalar manualmente ningún controlador en la máquina de puerta de enlace cuando se copian datos desde la base de datos de Cassandra. 

> [!NOTE]
> Consulte [Solución de problemas de la puerta de enlace](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obtener sugerencias para solucionar problemas de conexión o puerta de enlace.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con una actividad de copia que mueva los datos desde un almacén de datos Cassandra local mediante el uso de diferentes herramientas o API. 

- La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos. 
- También puede usar las herramientas siguientes para crear una canalización: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **plantilla de Azure Resource Manager**, **API de .NET** y **API de REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia. 

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia. 
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida. 

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para ver un ejemplo con definiciones JSON para entidades de Data Factory que se usan para copiar datos de un almacén de datos de Cassandra local, consulte la sección [Ejemplo: Copia de datos de Cassandra en un blob de Azure](#json-example-copy-data-from-cassandra-to-azure-blob) de este artículo. 

Las secciones siguientes proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de un almacén de datos de Cassandra:

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
La tabla siguiente incluye una descripción de los elementos JSON específicos para el servicio vinculado de Cassandra.

| Propiedad | DESCRIPCIÓN | Obligatorio |
| --- | --- | --- |
| Tipo |La propiedad type debe estar establecida en: **OnPremisesCassandra** |Sí |
| host |Una o varias direcciones IP o nombres de host de los servidores de Cassandra.<br/><br/>Especifica una lista de direcciones IP o nombres de host separada por comas para conectar con todos los servidores a la vez. |Sí |
| puerto |Puerto TCP que el servidor de Cassandra utiliza para escuchar las conexiones del cliente. |No. El valor predeterminado es 9042. |
| authenticationType |Básica o anónima |Sí |
| Nombre de usuario |Especifique el nombre de usuario de la cuenta de usuario. |Sí, si el valor de authenticationType es Basic. |
| contraseña |Especifique la contraseña para la cuenta de usuario. |Sí, si el valor de authenticationType es Basic. |
| gatewayName |Nombre de la puerta de enlace que se va a utilizar en la conexión con la base de datos de Cassandra local. |Sí |
| encryptedCredential |Credencial cifrada por la puerta de enlace. |Sin  |

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties de los conjuntos de datos de tipo **CassandraTable** tiene las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Obligatorio |
| --- | --- | --- |
| keyspace |Nombre del espacio de claves o esquema de la base de datos de Cassandra. |Sí (si no hay definida ninguna **consulta** para **CassandraSource**). |
| tableName |Nombre de la tabla de la base de datos de Cassandra. |Sí (si no hay definida ninguna **consulta** para **CassandraSource**). |

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

Si el origen es de tipo **CassandraSource**, estarán disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | DESCRIPCIÓN | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| query |Utilice la consulta personalizada para leer los datos. |Consulta SQL-92 o consulta CQL. Vea la [CQL reference](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)(referencia de CQL). <br/><br/>Cuando utilice una consulta SQL, especifique **nombre de espacio de claves.nombre de tabla** para representar la tabla que quiere consultar. |No (si tableName y el espacio de claves del conjunto de datos están definidos). |
| consistencyLevel |El nivel de coherencia establece el número de réplicas que deben responder a una solicitud de lectura antes de que se devuelvan datos a la aplicación cliente. Cassandra comprueba el número de réplicas especificado para que los datos satisfagan la solicitud de lectura. |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Para más información, consulte [Configuring data consistency](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) (Configuración de la coherencia de datos). |Nº El valor predeterminado es ONE. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Ejemplo con definiciones de JSON: copia de datos de Cassandra a un blob de Azure
Este ejemplo proporciona definiciones JSON de ejemplo que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Muestra cómo copiar datos de una base de datos de Cassandra local a Azure Blob Storage. Sin embargo, los datos se pueden copiar en cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Azure Data Factory.

> [!IMPORTANT]
> Este ejemplo proporciona fragmentos JSON. No incluye instrucciones paso a paso para crear la factoría de datos. Las instrucciones paso a paso se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

El ejemplo consta de las siguientes entidades de factoría de datos:

* Un servicio vinculado de tipo [OnPremisesCassandra](#linked-service-properties).
* Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [CassandraTable](#dataset-properties).
* Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que use [CassandraSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Servicio vinculado de Cassandra**

En este ejemplo, se utiliza el servicio vinculado de **Cassandra** . Consulte la sección [Propiedades del servicio vinculado OnPremisesCassandra](#linked-service-properties) para ver las propiedades admitidas por el mismo.  

```json
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
```

**Servicio vinculado de Azure Storage:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Conjunto de datos de entrada de Cassandra**

```json
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
```

Si se establece **external** en **true**, se informa al servicio Data Factory que el conjunto de datos es externo a Data Factory y que no lo genera ninguna actividad de la factoría de datos.

**Conjunto de datos de salida de blob de Azure:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1).

```json
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
```

**Actividad de copia en una canalización con el origen de Cassandra y el receptor de blob:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de la canalización, el tipo de **origen** está establecido en **CassandraSource**, mientras que el tipo de **receptor** está establecido en **BlobSink**.

Consulte las [propiedades de tipo RelationalSource](#copy-activity-properties) para obtener la lista de propiedades admitidas por RelationalSource.

```json
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
```

### <a name="type-mapping-for-cassandra"></a>Asignación de tipos de Cassandra
| Tipo de Cassandra | Tipo basado en .NET |
| --- | --- |
| ASCII |string |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEAN |BOOLEAN |
| DECIMAL |DECIMAL |
| DOUBLE |DOUBLE |
| FLOAT |Single |
| INET |string |
| INT |Int32 |
| TEXT |string |
| TIMESTAMP |Datetime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |string |
| VARINT |DECIMAL |

> [!NOTE]
> Par más información sobre tipos de colecciones (map, set, list, etc.), consulte la sección [Uso de colecciones con tablas virtuales](#work-with-collections-using-virtual-table) .
>
> No se admiten tipos definidos por el usuario.
>
> La longitud de la columna binaria y la columna de cadena no puede ser superior a 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Uso de colecciones con tablas virtuales
Azure Data Factory utiliza un controlador ODBC integrado para conectarse a una base de datos de Cassandra y copiar datos de dicha base de datos. En el caso de los tipos de colección (como map, set y list), el controlador volverá a normalizar los datos en las tablas virtuales correspondientes. En concreto, si una tabla contiene columnas de colecciones, el controlador generará las siguientes tablas virtuales:

* Una **tabla base**, que contiene los mismos datos que la tabla real, salvo las columnas de colecciones. La tabla base utiliza el mismo nombre que la tabla real a la que representa.
* Una **tabla virtual** para cada columna de la colección, que ampliará los datos anidados. Para asignar un nombre a las tablas virtuales que representan colecciones, se utiliza el nombre de la tabla real, un separador “*vt*” y el nombre de la columna.

Las tablas virtuales hacen referencia a los datos de la tabla real, lo que permite al controlador obtener acceso a los datos no normalizados. Consulte la sección de ejemplo para más información. Para acceder al contenido de las colecciones de Cassandra, puede crear consultas y combinar las tablas virtuales.

Si usa el [Asistente para copia](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity), podrá consultar una vista intuitiva de la lista de tablas de la base de datos de Cassandra (incluidas las tablas virtuales) y una vista previa de los datos incluidos. También puede crear una consulta en el Asistente para copia y validarla para ver el resultado.

### <a name="example"></a>Ejemplo
Por ejemplo, el siguiente “ExampleTable” es una tabla de una base de datos de Cassandra que contiene una columna de clave principal de enteros denominada “pk_int”, una columna de texto denominada “value”, una columna List, una columna Map y una columna Set (denominada “StringSet”).

| pk_int | Valor | Enumerar | Map | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"valor de ejemplo 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"valor de ejemplo 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

El controlador generará varias tablas virtuales que representan a esta tabla. Las columnas de clave externa de las tablas virtuales hacen referencia a las columnas de clave principal de la tabla real e indican qué fila de la tabla real se corresponde con la fila de la tabla virtual.

La primera tabla virtual es la tabla base y se denomina “ExampleTable”, tal y como se muestra en la siguiente tabla. La tabla base contiene los mismos datos que la tabla de base de datos original a excepción de las colecciones, que no aparecen en esta tabla, sino que se amplían en otras tablas virtuales.

| pk_int | Valor |
| --- | --- |
| 1 |"valor de ejemplo 1" |
| 3 |"valor de ejemplo 3" |

Las tablas siguientes representan las tablas virtuales que normalizan de nuevo los datos de las columnas List, Map y StringSet. Las columnas cuyos nombres terminan en “_index” o “_key” indican la posición de los datos en la columna List o Map original. Las columnas cuyos nombres terminan en “_value” contienen los datos ampliados de la colección.

#### <a name="table-exampletablevtlist"></a>Tabla “ExampleTable_vt_List”:
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletablevtmap"></a>Tabla “ExampleTable_vt_Map”:
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |Una  |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletablevtstringset"></a>Tabla “ExampleTable_vt_StringSet”:
| pk_int | StringSet_value |
| --- | --- |
| 1 |Una  |
| 1 |b |
| 1 |C |
| 3 |Una  |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Asignación de columnas de origen a columnas de receptor
Para obtener más información sobre la asignación de columnas del conjunto de datos de origen a las del conjunto de datos receptor, consulte [Asignación de columnas de conjunto de datos de Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lectura repetible de orígenes relacionales
Cuando se copian datos desde almacenes de datos relacionales, hay que tener presente la repetibilidad para evitar resultados imprevistos. En Azure Data Factory, puede volver a ejecutar un segmento manualmente. También puede configurar la directiva de reintentos para un conjunto de datos con el fin de que un segmento se vuelva a ejecutar cuando se produce un error. Cuando se vuelve a ejecutar un segmento, debe asegurarse de que los mismos datos se lean sin importar el número de ejecuciones. Consulte [Lectura repetible de orígenes relacionales](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.
