---
title: "Indexación de Azure Table Storage con Azure Search"
description: "Obtenga información sobre cómo indexar los datos almacenados en tablas de Azure con Azure Search"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 1cc27411-d0cc-40ed-8aed-c7cb9ab402b9
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/10/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 9b45ab6b86ab0a336b2a4b90e702fa4ff098d41c
ms.lasthandoff: 04/10/2017

---

# <a name="indexing-azure-table-storage-with-azure-search"></a>Indexación de Azure Table Storage con Azure Search
Este artículo muestra cómo usar Azure Search para indexar los datos almacenados en Azure Table Storage.

## <a name="setting-up-azure-table-indexing"></a>Configuración de la indexación de tablas de Azure

Para configurar un indexador de tabla de Azure, utilice:

* [Azure Portal](https://ms.portal.azure.com)
* [API de REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) de Azure Search
* [SDK de .NET de Azure Search](https://aka.ms/search-sdk)

En este caso, demostramos el flujo mediante la API de REST. 

### <a name="step-1-create-a-data-source"></a>Paso 1: Creación de un origen de datos

Un origen de datos especifica los datos que se indexan, las credenciales necesarias para obtener acceso a los mismos y las directivas que posibilitan que Azure Search identifique cambios en los datos de forma eficiente.

Para realizar la indexación de tablas, el origen de datos debe tener las siguientes propiedades:

- **name** es el nombre único del origen de datos dentro del servicio de búsqueda.
- **type** debe ser `azuretable`.
- El parámetro **credentials** contiene la cadena de conexión de la cuenta de almacenamiento. Para más información, consulte [Especificación de credenciales](#Credentials).
- **contenedor** establece el nombre de tabla y una consulta opcional
    - Especifique el nombre de la tabla con el parámetro `name`
    - De manera opcional, especifique una consulta con el parámetro `query` . 

> [!IMPORTANT] 
> Siempre que sea posible, utilice un filtro en PartitionKey para mejorar el rendimiento. Cualquier otra consulta realiza un examen de toda la tabla, lo que produce un rendimiento deficiente de las tablas grandes. Consulte la sección [Consideraciones acerca del rendimiento](#Performance).


Pasos para crear un origen de datos:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Para más información sobre la API de creación de origen de datos, consulte [Crear origen de datos](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Especificación de credenciales ####

Puede proporcionar las credenciales para la tabla de una de estas maneras: 

- **Cadena de conexión de la cuenta de almacenamiento de acceso completo**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Para obtener la cadena de conexión de Azure Portal, vaya a la hoja de la cuenta de almacenamiento > Configuración > Claves (para las cuentas de almacenamiento del modelo clásico) o Configuración > Claves de acceso (para las cuentas de almacenamiento de Azure Resource Manager).
- Cadena de conexión de la **firma de acceso compartido de la cuenta de almacenamiento** (SAS): `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl`. La firma de acceso compartido debe tener permisos de enumeración y lectura sobre los contenedores (en este caso, tablas) y objetos (filas de tabla).
-  **Firma de acceso compartido de tabla**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r`. La SAS debería tener permisos de consulta (lectura) en la tabla.

Para más información sobre las firmas de acceso compartido, consulte [Uso de firmas de acceso compartido](../storage/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Si usa credenciales SAS, deberá actualizar las credenciales del origen de datos periódicamente con firmas renovadas para evitar que caduquen. Si las credenciales de SAS expiran, el indexador genera un mensaje de error similar a `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Paso 2: Creación de un índice
El índice especifica los campos de un documento, los atributos y otras construcciones que conforman la experiencia de búsqueda.

Aquí se muestra cómo crear un índice:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Para más información acerca de la creación de índices, consulte [Creación de un índice](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Paso 3: Creación de un indexador
Un indizador conecta un origen de datos con un índice de búsqueda de destino y proporciona una programación para automatizar la actualización de datos. 

Una vez creados el origen de datos y los índices, ya podrá crear el indizador:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexador se ejecuta cada dos horas (el intervalo de programación se establece en "PT2H"). Para ejecutar un indizador cada 30 minutos, establézcalo en PT30M. El intervalo más breve que se admite es de 5 minutos. La programación es opcional: si se omite, el indizador solo se ejecuta una vez cuando se crea. Sin embargo, puede ejecutarlo a petición en cualquier momento.   

Para más información sobre la API de creación de indexador, consulte [Crear indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="dealing-with-different-field-names"></a>Manejo de distintos nombres de campos
A veces, los nombres de los campos de un índice existente no coincidirán con los nombres de las propiedades de la tabla. Puede usar **asignaciones de campos** para asignar los nombres de propiedad provenientes de la tabla a los nombres de campo del índice de búsqueda. Para obtener más información sobre las asignaciones de campos, consulte [Las asignaciones de campos de indizador de Azure Search salvan las diferencias entre los orígenes de datos y los índices de búsqueda](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>Control de claves de documentos
En Azure Search, la clave del documento identifica de forma exclusiva a un documento. Cada índice de búsqueda debe tener exactamente un campo clave de tipo `Edm.String`. El campo clave es necesario para cada documento que se agrega al índice (de hecho, es el único campo obligatorio).

Como las filas de tablas tienen una clave compuesta, Azure Search genera un campo sintético llamado `Key`, que es una concatenación de valores clave de partición y clave de fila. Por ejemplo, si el valor PartitionKey de una fila es `PK1` y el valor RowKey es `RK1`, el valor del campo `Key` es `PK1RK1`.

> [!NOTE]
> El valor `Key` puede contener caracteres no válidos en claves de documentos, como guiones. Para tratar con caracteres no válidos, use la [función de asignación de campos](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`. Si lo hace, recuerde utilizar también la codificación Base64 de seguridad de direcciones URL al pasar las claves de documento en las llamadas de la API como búsqueda.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Indexación incremental y detección de eliminación
Cuando configure un indizador de tablas para que se ejecute según una programación, vuelva a indexar solo las filas nuevas o actualizadas, según lo determine el valor `Timestamp` de una fila. No tiene que especificar una directiva de detección de cambios, la indexación incremental ya está habilitada automáticamente.

Para indicar que determinados documentos se deben quitar del índice, puede usar una estrategia de eliminación temporal. En lugar de eliminar una fila, agregue una propiedad para indicar que se elimina y configure una directiva de detección de eliminaciones temporales en el origen de datos. Por ejemplo, la siguiente directiva considera que una fila se ha eliminado si tiene una propiedad `IsDeleted` con el valor `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>Consideraciones acerca del rendimiento

De forma predeterminada, Azure Search usa el siguiente filtro de consulta: `Timestamp >= HighWaterMarkValue`. Como las tablas de Azure no tienen un índice secundario en el campo `Timestamp`, este tipo de consulta requiere una exploración de toda la tabla y, por tanto, es lento para tablas grandes.


Aquí hay dos posibles enfoques para mejorar el rendimiento de la indexación de tablas. Ambos se basan en el uso de particiones de tabla: 

- Si los datos se pueden dividir de forma natural en varios intervalos de partición, cree un origen de datos y un indexador correspondiente para cada intervalo de partición. Cada indexador tiene que procesar solo un intervalo de partición concreto, lo que mejora el rendimiento de las consultas. Si los datos que hay que indexar tienen un número pequeño de particiones fijas, aún mejor (cada indexador solo realiza la exploración de una partición). Por ejemplo, para crear un origen de datos para procesar un intervalo de partición con las claves de `000` a `100`, utilice una consulta como esta: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Si los datos se dividen por tiempo (por ejemplo, se crea una nueva partición cada día o semana), considere el siguiente enfoque: 
    - Utilice una consulta del formulario: `(PartitionKey ge <TimeStamp>) and (other filters)` 
    - Supervise el progreso del indexador mediante la [API de Get Indexer Status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) y actualice periódicamente la condición `<TimeStamp>` de la consulta según el valor alto correcto de la marca de agua más reciente. 
    - Con este enfoque, si tiene que desencadenar un reindexado completo, tendrá que restablecer la consulta del origen de datos, además de restablecer el indexador. 


## <a name="help-us-make-azure-search-better"></a>Ayúdenos a mejorar Azure Search
Si tiene solicitudes o ideas para mejorar las características, envíelas en el [sitio de UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

