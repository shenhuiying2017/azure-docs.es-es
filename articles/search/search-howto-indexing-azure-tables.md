---
title: "Indexación de Almacenamiento de tablas de Azure con Búsqueda de Azure"
description: "Obtenga información sobre cómo indexar los datos almacenados en tablas de Azure con Búsqueda de Azure"
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
ms.date: 01/18/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 19a652f81beacefd4a51f594f045c1f3f7063b59
ms.openlocfilehash: b7f6c92867e3fabe07312539ec8dfd2d3525f02e

---

# <a name="indexing-azure-table-storage-with-azure-search"></a>Indexación de Almacenamiento de tablas de Azure con Búsqueda de Azure
Este artículo muestra cómo usar Búsqueda de Azure para indexar los datos almacenados en Almacenamiento de tablas de Azure. El nuevo indexador de tablas de Búsqueda de Azure agiliza y facilita este proceso.

## <a name="setting-up-azure-table-indexing"></a>Configuración de la indexación de tablas de Azure
Para instalar y configurar un indexador de tablas de Azure, puede usar la API de REST de Azure Search para crear y administrar **indexadores** y **orígenes de datos**, como se describe en [Operaciones de indexador](https://msdn.microsoft.com/library/azure/dn946891.aspx). También puede usar la [versión 2.0-preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) del SDK de .NET. En el futuro, se agregará la indexación de tablas al Portal de Azure.

Un origen de datos especifica los datos que se deben indexar, las credenciales necesarias para obtener acceso a los mismos y las directivas que posibilitan que Búsqueda de Azure identifique cambios en los datos de forma eficiente (filas nuevas, modificadas o eliminadas).

Un indexador lee datos de un origen de datos y los carga en un índice de búsqueda de destino.

Para configurar la indexación de tablas:

1. Creación de un origen de datos
   * Establezca el parámetro `type` en `azuretable`
   * Transfiera la cadena de conexión de la cuenta de almacenamiento como parámetro `credentials.connectionString`. Consulte [Especificación de credenciales](#Credentials) a continuación para más información.
   * Especifique el nombre de la tabla con el parámetro `container.name`
   * De manera opcional, especifique una consulta con el parámetro `container.query` . Siempre que sea posible, use un filtro en PartitionKey para obtener el mejor rendimiento posible; cualquier otra consulta dará como resultando un recorrido de tabla completo, el que puede generar un rendimiento deficiente en el caso de tablas de gran tamaño.
2. Cree un índice de búsqueda con el esquema que corresponde a las columnas de la tabla que desee indexar.
3. Cree el indexador mediante la conexión del origen de datos con el índice de búsqueda.

### <a name="create-data-source"></a>Creación de un origen de datos
    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Para más información sobre la API de creación de origen de datos, consulte [Crear origen de datos](https://msdn.microsoft.com/library/azure/dn946876.aspx).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Especificación de credenciales ####

Puede proporcionar las credenciales para la tabla de una de estas maneras: 

- **Cadena de conexión de la cuenta de almacenamiento de acceso completo**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Para obtener la cadena de conexión del portal de Azure, vaya a la hoja de la cuenta de almacenamiento > Configuración > Claves (para las cuentas de almacenamiento del modelo clásico) o Configuración > Claves de acceso (para las cuentas de almacenamiento de Azure Resource Manager).
- Cadena de conexión de la **firma de acceso compartido de la cuenta de almacenamiento** (SAS): `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl`. La firma de acceso compartido debe tener permisos de enumeración y lectura sobre los contenedores (en este caso, tablas) y objetos (filas de tabla).
-  **Firma de acceso compartido de tabla**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`. La firma de acceso compartido debe tener permisos de enumeración y lectura sobre la tabla.

Para más información sobre las firmas de acceso compartido, consulte [Uso de firmas de acceso compartido](../storage/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Si usa credenciales SAS, deberá actualizar las credenciales del origen de datos periódicamente con firmas renovadas para evitar que caduquen. Si las credenciales de SAS expiran, el indexador producirá un mensaje de error similar a `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="create-index"></a>Creación de índice
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

Para más información sobre la API de creación de índice, consulte [Create Index](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>Creación de un indexador
Por último, cree un indexador que haga referencia al origen de datos y un índice de destino. Por ejemplo:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Para más información sobre la API de creación de indexador, consulte [Crear indexador](https://msdn.microsoft.com/library/azure/dn946899.aspx).

Eso es todo. ¡Feliz indexación!

## <a name="dealing-with-different-field-names"></a>Manejo de distintos nombres de campos
Con frecuencia, los nombres de campos del índice existente serán distintos de los nombres de las propiedades de la tabla. Puede usar **asignaciones de campos** para asignar los nombres de propiedad provenientes de la tabla a los nombres de campo del índice de búsqueda. Para obtener más información sobre las asignaciones de campos, consulte [Las asignaciones de campos de indizador de Búsqueda de Azure salvan las diferencias entre los orígenes de datos y los índices de búsqueda](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>Control de claves de documentos
En Búsqueda de Azure, la clave del documento identifica de forma exclusiva a un documento. Cada índice de búsqueda debe tener exactamente un campo clave de tipo `Edm.String`. El campo clave es necesario para cada documento que se agrega al índice (de hecho, es el único campo obligatorio).

Como las filas de tablas tienen una clave compuesta, Búsqueda de Azure genera un campo sintético llamado `Key` , que es una concatenación de valores clave de partición y clave de fila. Por ejemplo, si el valor PartitionKey de una fila es `PK1` y el valor RowKey es `RK1`, el valor del campo `Key` será `PK1RK1`.

> [!NOTE]
> El valor `Key` puede contener caracteres no válidos en claves de documentos, como guiones. Para tratar con caracteres no válidos, use la [función de asignación de campos](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`. Si lo hace, recuerde utilizar también la codificación Base64 de seguridad de direcciones URL al pasar las claves de documento en las llamadas de la API como búsqueda.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Indexación incremental y detección de eliminación
Cuando configure un indizador de tablas para que se ejecute según una programación, vuelva a indexar solo las filas nuevas o actualizadas, según lo determine el valor `Timestamp` de una fila. No tiene que especificar una directiva de detección de cambios, la indexación incremental ya está habilitada automáticamente.

Para indicar que determinados documentos se deben quitar del índice, puede usar una estrategia de eliminación temporal: en lugar de eliminar una fila, agregue una propiedad para indicar que está eliminada y configure una directiva de detección de eliminación temporal en el origen de datos. Por ejemplo, la directiva que se muestra a continuación considerará que una fila está eliminada si tiene una propiedad `IsDeleted` con el valor `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Ayúdenos a mejorar Búsqueda de Azure
Si tiene solicitudes o ideas para mejorar las características, póngase en contacto con nosotros en nuestro [sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search/).



<!--HONumber=Jan17_HO3-->


