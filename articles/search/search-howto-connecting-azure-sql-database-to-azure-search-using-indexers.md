---
title: "Conexión de Azure SQL Database a Azure Search con indexadores | Microsoft Docs"
description: "Obtenga información sobre cómo extraer datos de la base de datos SQL de Azure en un índice de Búsqueda de Azure mediante el uso de indizadores."
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: e9bbf352-dfff-4872-9b17-b1351aae519f
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 0841744b806f3dba38dddee21fb7fe881e07134f
ms.openlocfilehash: 51c9d9afb6c2ed460abd4c47a6afbc404b97a85e
ms.lasthandoff: 02/16/2017

---

# <a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Conexión de Base de datos SQL de Azure a Búsqueda de Azure con indexadores
El servicio Búsqueda de Azure es un servicio de búsqueda hospedado en la nube que facilita ofrecer una excelente experiencia de búsqueda. Antes de buscar, deberá rellenar un índice de Búsqueda de Azure con los datos. Si los datos residen en una SQL Database de Azure, el nuevo **indexador de Azure Search para Azure SQL Database** (o **indexador Azure SQL**, para abreviar) puede automatizar el proceso de indexación. Esto significa que tendrá que escribir menos código y tendrá menos infraestructura para mantener.

Este artículo abordará la forma de usar los indexadores, pero también describe las características que solo están disponibles con bases de datos SQL de Azure (por ejemplo, seguimiento de cambios integrado). Azure Search también admite otros orígenes de datos, como Azure DocumentDB, Blob Storage y Table Storage. Si desea consultar soporte técnico para orígenes de datos adicionales, envíe sus comentarios a través del [foro de comentarios de Azure Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexadores y orígenes de datos
Puede instalar y configurar un indexador de SQL de Azure mediante:

* El asistente para importar datos en [Azure Portal](https://portal.azure.com)
* [SDK de .NET de Azure Search](https://msdn.microsoft.com/library/azure/dn951165.aspx)
* [API de REST](http://go.microsoft.com/fwlink/p/?LinkID=528173) de Azure Search

En este artículo, usaremos la API de REST para mostrar cómo crear y administrar **indexadores** y **orígenes de datos**.

Un **origen de datos** especifica los datos que se deben indexar, las credenciales necesarias para obtener acceso a estos y las directivas que identifican cambios en los datos de forma eficaz (filas nuevas, modificadas o eliminadas). Se define como un recurso independiente para que puedan usarlo múltiples indizadores.

Un **indizador** es un recurso que conecta los orígenes de datos con los índices de búsqueda de destino. Un indexador se usa de las maneras siguientes:

* Realizar una copia única de los datos para rellenar un índice.
* Actualizar un índice con los cambios del origen de datos en una programación.
* Ejecutar a petición para actualizar un índice según sea necesario.

## <a name="when-to-use-azure-sql-indexer"></a>Cuándo usar un indizador de SQL Azure
Según varios factores relacionados con los datos, el uso del indizador de SQL Azure puede ser o no ser adecuado. Si los datos cumplen los requisitos siguientes, puede usar el indizador de SQL Azure:

* Todos los datos proceden de una sola tabla o vista.
  * Si los datos están distribuidos en varias tablas, puede crear una vista y usarla con el indizador. Sin embargo, si usa una vista, no podrá usar la detección de cambios integrada de SQL Server. Para más información, consulte [esta sección](#CaptureChangedRows).
* El indizador admite los tipos de datos usados en el origen de datos. Se admite la mayoría de los tipos de SQL, aunque no todos. Para obtener más información, consulte [Asignar tipos de datos en Búsqueda de Azure](http://go.microsoft.com/fwlink/p/?LinkID=528105).
* No necesita actualizaciones del índice casi en tiempo real cuando una fila cambia.
  * El indizador puede volver a indizar la tabla cada 5 minutos como máximo. Si los datos cambian con frecuencia y los cambios deben reflejarse en el índice en cuestión de segundos o minutos, se recomienda usar directamente la [API de índice de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798930.aspx) .
* Si tiene un conjunto de datos grande y prevé ejecutar el indexador en una programación, su esquema nos permite identificar de forma eficaz las filas que se cambiaron (y eliminaron, si corresponde). Para obtener más información, vea a continuación «Capturar filas cambiadas y eliminadas».
* El tamaño de los campos indizados en una fila no supera el tamaño máximo de una solicitud de indización de Búsqueda de Azure, que es de 16 MB.

## <a name="create-and-use-an-azure-sql-indexer"></a>Crear y usar un indizador de SQL Azure
En primer lugar, cree el origen de datos:

    POST https://myservice.search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


Puede obtener la cadena de conexión del [Portal de Azure clásico](https://portal.azure.com): use la opción `ADO.NET connection string`.

Si aún no tiene un índice de Búsqueda de Azure de destino, créelo. Puede crear un índice mediante la [interfaz de usuario del portal](https://portal.azure.com) o la [API de creación de índices](https://msdn.microsoft.com/library/azure/dn798941.aspx). Asegúrese de que el esquema del índice de destino sea compatible con el de la tabla de origen; consulte la [asignación entre tipos de datos de SQL y tipos de datos de Azure Search](#TypeMapping) para más información.

Por último, cree el indizador asignándole un nombre y haciendo referencia al índice de origen y destino de datos:

    POST https://myservice.search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

Un indizador creado de esta forma no tiene una programación. Se ejecuta automáticamente una vez en cuanto se crea. Puede volver a ejecutarlo en cualquier momento mediante una solicitud **ejecutar indizador** :

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2016-09-01
    api-key: admin-key

Puede personalizar varios aspectos del comportamiento del indexador, como el tamaño de lote y el número de documentos que se puede omitir antes de que la ejecución de un indexador produzca un error. Para más información, consulte [Create Indexer API](https://msdn.microsoft.com/library/azure/dn946899.aspx)(API para crear índices).

Puede que necesite permitir que los servicios de Azure se conecten a la base de datos. Consulte [Conectarse desde Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para obtener instrucciones sobre cómo hacerlo.

Para supervisar el estado del indizador y el historial de ejecución (número de elementos indizados, errores, etc.), use una solicitud **estado del indizador** :

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2016-09-01
    api-key: admin-key

La respuesta debe ser similar a la siguiente:

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

El historial de ejecución contiene como máximo las 50 ejecuciones completadas más recientemente en orden cronológico inverso (la ejecución más reciente aparece en primer lugar).
Puede encontrar información adicional sobre la respuesta en [Obtener el estado del indizador](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Ejecutar indexadores según una programación
También puede disponer que el indizador se ejecute periódicamente según una programación. Para ello, agregue la propiedad **schedule** al crear o actualizar el indexador. El ejemplo siguiente muestra una solicitud PUT para actualizar el indizador:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

El parámetro **interval** es obligatorio. El intervalo se refiere al tiempo entre el inicio de dos ejecuciones consecutivas de indizador. El intervalo mínimo permitido es de 5 minutos y el máximo de un día. Debe tener el formato de un valor "dayTimeDuration" XSD (subconjunto restringido de un valor de [duración ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). El patrón de este es: `P(nD)(T(nH)(nM))`. Ejemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

El valor **startTime** opcional indica cuándo deben comenzar las ejecuciones programadas. Si se omite, se usará la hora UTC actual. Este tiempo puede estar en el pasado, en cuyo caso la primera ejecución se programa como si el indexador se hubiera ejecutado continuamente desde la hora de inicio.  

Solo se puede ejecutar a la vez una ejecución de un indexador. Si está ejecutando un indexador cuando está programada su ejecución, dicha ejecución se pospone hasta la próxima hora programada.

Veamos un ejemplo entenderlo concretamente. Supongamos que está configurada la siguiente programación por hora:

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Esto es lo que sucede:

1. La primera ejecución del indizador se inicia exactamente o en torno al 1 de marzo de 2015, 12:00 a. m. hora UTC.
2. Suponga que esta ejecución tarda 20 minutos (o un período inferior a 1 hora).
3. La segunda ejecución se inicia exactamente o en torno al 1 de marzo de 2015, 1:00 a. m.
4. Ahora suponga que esta ejecución tarda más de una hora (por ejemplo, 70 minutos), de forma que finalice aproximadamente 2:10 a.m.
5. Ahora son las 2:00 a. m., momento en que debe comenzar la tercera ejecución. Ahora bien, dado que la segunda ejecución de la 1 a. m. aún está en ejecución, la tercera se omite. La tercera ejecución empieza a las 3 a. m.

Puede agregar, cambiar o eliminar la programación de un indizador existente mediante una solicitud **PUT de indizador** .

<a name="CaptureChangedRows"></a>

## <a name="capturing-new-changed-and-deleted-rows"></a>Captura de filas nuevas, cambiadas y eliminadas
Si la tabla tiene muchas filas, debe utilizar una directiva de detección de cambio de datos. La detección de cambios permite una recuperación eficaz solo de las filas nuevas o cambiadas , sin tener que volver a indexar toda la tabla.

### <a name="sql-integrated-change-tracking-policy"></a>Directiva de seguimiento de cambios integrada de SQL
Si la base de datos SQL admite el [seguimiento de cambios](https://msdn.microsoft.com/library/bb933875.aspx), se recomienda usar la **directiva de seguimiento de cambios integrada de SQL**. Esta es la directiva más eficiente. Además, permite que Azure Search identifique las filas eliminadas sin tener que agregar a la tabla una columna de "eliminación temporal" explícita.

El seguimiento de cambios integrado se admite a partir de las siguientes versiones de la base de datos de SQL Server:

* SQL Server 2008 R2 y versiones posteriores, si usa SQL Server en máquinas virtuales de Azure.
* Base de datos SQL de Azure V12, si está usando la Base de datos SQL de Azure.

Al usar la directiva de seguimiento de cambios integrada de SQL, no especifique una directiva de detección de eliminación de datos independiente, ya que esta directiva tiene compatibilidad integrada para identificar las filas eliminadas.

Esta directiva solo se puede usar con tablas; no se puede usar con vistas. Deberá habilitar el seguimiento de cambios para la tabla que está usando para poder emplear esta directiva. Consulte [Habilitar y deshabilitar el seguimiento de cambios](https://msdn.microsoft.com/library/bb964713.aspx) para obtener instrucciones.

Para usar esta directiva, cree o actualice el origen de datos de la siguiente manera:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Directiva de detección de cambios de límite superior
Aunque se recomienda la directiva de seguimiento de cambios integrado de SQL, solo puede utilizarse con tablas, no con vistas. Si usa una vista, considere el uso de la directiva de marca de límite superior. Puede usar esta directiva si la tabla o vista contiene una columna que cumple los criterios siguientes:

* Todas las inserciones especifican un valor para la columna.
* Todas las actualizaciones de un elemento también cambian el valor de la columna.
* El valor de esta columna aumenta con cada inserción o actualización.
* Consultas con las cláusulas WHERE y ORDER BY siguientes se pueden ejecutar eficientemente: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`.

> [!IMPORTANT] 
> Se recomienda encarecidamente usar una columna **rowversion** para el seguimiento de cambios. Si se utiliza cualquier otro tipo de datos, no se garantiza que el seguimiento de cambios capture todos los cambios en el caso de transacciones que se ejecutan simultáneamente con una consulta de indexador.

Para usar una directiva de marca de límite superior, cree o actualice el origen de datos de la siguiente manera:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> Si la tabla de origen no tiene un índice en la columna de límite superior, las consultas utilizadas por el indexador SQL pueden agotar el tiempo de espera. En concreto, la cláusula `ORDER BY [High Water Mark Column]` requiere un índice para ejecutarse de forma eficaz cuando la tabla contiene muchas filas.
>
>

Si se producen errores de tiempo de espera, puede utilizar la configuración de indexador `queryTimeout` para establecer el tiempo de espera de consulta en un valor mayor que el tiempo de espera predeterminado de 5 minutos. Por ejemplo, para establecer el tiempo de espera en 10 minutos, cree o actualice el indexador con la siguiente configuración:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

También puede deshabilitar la cláusula `ORDER BY [High Water Mark Column]`. Sin embargo, esto no se recomienda porque si se interrumpe la ejecución del indexador por un error, dicho indexador tiene que volver a procesar todas las filas si se ejecuta más tarde, incluso si ya ha procesado casi todas las filas en el momento en el que se interrumpió. Para deshabilitar la cláusula `ORDER BY`, utilice la configuración `disableOrderByHighWaterMarkColumn` en la definición del indexador:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Directiva de detección de eliminación de columna de eliminación temporal
Cuando se eliminan filas de la tabla de origen, probablemente le interesará eliminar dichas filas del índice de búsqueda. Si usa la directiva de seguimiento de cambios integrada de SQL, esto se lleva a cabo automáticamente. Sin embargo, la directiva el seguimiento de cambios de marca de límite superior no le ayuda con las filas eliminadas. ¿Qué debe hacer?

Si las filas se quitaron físicamente de la tabla, Azure Search no tiene forma de deducir la presencia de registros que ya no existen.  Sin embargo, puede utilizar la técnica de "eliminación temporal" para eliminar filas lógicamente sin quitarlas de la tabla. Agregue una columna a la tabla o vista y marque filas como eliminadas mediante esa columna.

Cuando use la técnica de la eliminación temporal, puede especificar la directiva de eliminación temporal de la manera que se indica a continuación al crear o actualizar el origen de datos:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**softDeleteMarkerValue** debe ser una cadena. Use la representación de cadena del valor real. Por ejemplo, si tiene una columna de enteros donde las filas eliminadas se marcan con el valor 1, use `"1"`. Si tiene una columna BIT donde las filas eliminadas se marcan con el valor booleano true, use `"True"`.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Asignación entre tipos de datos de SQL y tipos de datos de Búsqueda de Azure
| Tipo de datos de SQL | Tipos de campos de índice de destino permitidos | Notas |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, numérico decimal de dinero |Edm.String |Búsqueda de Azure no admite la conversión de tipos decimales en Edm.Double porque esto podría provocar la pérdida de precisión |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Una cadena SQL se puede usar para rellenar un campo Collection (EDM.String) si la cadena representa una matriz JSON de cadenas: `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| geography |Edm.GeographyPoint |Solo se admiten instancias de geography de tipo POINT con SRID 4326 (que es el valor predeterminado) |
| rowversion |N/D |Las columnas de versión de la fila no se pueden almacenar en el índice de búsqueda, pero pueden usarse para el seguimiento de cambios |
| time, timespan, binary, varbinary, image, xml, geometry, tipos CLR |N/D |No compatible |

## <a name="configuration-settings"></a>Valores de configuración
El indexador de SQL expone varios valores de configuración:

| Configuración | Tipo de datos | Propósito | Valor predeterminado |
| --- | --- | --- | --- |
| queryTimeout |string |Establece el tiempo de espera para la ejecución de consultas SQL |5 minutos ("00:05:00") |
| disableOrderByHighWaterMarkColumn |booleano |Hace que la consulta SQL utilizada por la directiva de límite superior omita la cláusula ORDER BY. Consulte [Directiva de detección de cambios de límite superior](#HighWaterMarkPolicy). |false |

Esta configuración se utiliza en el objeto `parameters.configuration` en la definición del indexador. Por ejemplo, para establecer el tiempo de espera de consulta en 10 minutos, cree o actualice el indexador con la siguiente configuración:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes
**P:** ¿Puedo usar un indizador de SQL Azure con Bases de datos SQL que se ejecutan en máquinas virtuales de IaaS en Azure?

R: Sí. Sin embargo, debe permitir que el servicio de búsqueda se conecte a su base de datos. Consulte el artículo [Configuración de una conexión desde un indexador de Azure Search a SQL Server en una máquina virtual de Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) para más información.

**P:** ¿Puedo usar un indizador de SQL Azure con Bases de datos SQL que se ejecutan localmente?

R: No se recomienda ni se admite esta posibilidad, ya que requeriría abrir las bases de datos al tráfico de Internet.

**P:** ¿Puedo usar un indizador de SQL Azure con bases de datos que no son de SQL Server que se ejecutan en IaaS en Azure?

R: No admitimos esta posibilidad, ya que no hemos probado el indizador con bases de datos que no son de SQL Server.  

**P:** ¿Puedo crear varios indizadores que se ejecuten según una programación?

R: Sí. Sin embargo, solo puede ejecutarse un indizador en un nodo de cada vez. Si necesita varios indizadores que se ejecuten simultáneamente, considere la posibilidad de escalar verticalmente el servicio de búsqueda a más de una unidad de búsqueda.

**P:** ¿Afecta la ejecución de un indizador a la carga de trabajo de consulta?

R: Sí. El indizador se ejecuta en uno de los nodos del servicio de búsqueda, y los recursos de dicho nodo se reparten entre la indización y la prestación de servicios al tráfico de consultas y otras solicitudes de API. Si al ejecutar cargas de trabajo intensivas de indización y consulta detecta la alta tasa de 503 errores o el aumento de los tiempos de respuesta, considere la posibilidad de escalar verticalmente el servicio de búsqueda.

