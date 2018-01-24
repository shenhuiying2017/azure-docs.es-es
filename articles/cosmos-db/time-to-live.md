---
title: "Hacer que caduquen datos en Azure Cosmos DB con período de vida | Microsoft Docs"
description: "Con TTL, Microsoft Azure Cosmos DB ofrece la posibilidad de que los documentos se purguen automáticamente del sistema tras un período de tiempo."
services: cosmos-db
documentationcenter: 
keywords: "período de vida"
author: arramac
manager: jhubbard
editor: 
ms.assetid: 25fcbbda-71f7-414a-bf57-d8671358ca3f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: arramac
ms.openlocfilehash: 3737a240d92d9420bac7d42475622182fb425a2b
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>Hacer que caduquen automáticamente los datos de colecciones de Azure Cosmos DB con período de vida
Las aplicaciones pueden generar y almacenar enormes cantidades de datos. Algunos de estos datos, como los datos de eventos, los registros y la información de sesión de usuario que se generan automáticamente, solo son útiles durante un tiempo finito. Una vez que los datos se convierten en un excedente para las necesidades de la aplicación, es seguro purgarlos para así reducir sus necesidades de almacenamiento.

Con "período de vida", o TTL, Microsoft Azure Cosmos DB ofrece la posibilidad de purgar los documentos automáticamente de la base de datos tras un período de tiempo. El período de vida predeterminado se puede establecer en el nivel de colección, y se puede invalidar de forma individual (por documento). Una vez que se establece el TTL, como un valor predeterminado de la colección o en un nivel de documento, Cosmos DB quita automáticamente los documentos que existen después de ese período de tiempo, en segundos, desde que se modificaron por última vez.

El período de vida en Cosmos DB usa un desplazamiento con el que se mide cuándo se modificó por última vez el documento. Para ello, usa el campo `_ts` que existe en todos los documentos. El campo _ts es una marca de tiempo de estilo unix que representa la fecha y la hora. El campo `_ts` se actualiza cada vez que se modifica un documento. 

## <a name="ttl-behavior"></a>Comportamiento de TTL
La característica TTL se controla mediante las propiedades de TTL en dos niveles: el nivel de colección y el nivel de documento. Los valores se establecen en segundos y se tratan como una diferencia de `_ts` en que el documento se modificó por última vez.

1. DefaultTTL para la colección
   
   * Si no existe (o está establecido en null), los documentos no se eliminan automáticamente.
   * Si existe y el valor es "-1" = infinito, los documentos no caducan de forma predeterminada.
   * Si existe y el valor es un número ("n"), los documentos caducan "n" segundos después de la última modificación.
2. TTL para los documentos: 
   
   * La propiedad solo es aplicable si DefaultTTL existe en la colección primaria.
   * Invalida el valor DefaultTTL para la colección primaria.

En cuanto el documento ha expirado (`ttl` + `_ts` >=hora actual del servidor), el documento se marca como "expirado". Transcurrido este tiempo, no se permite ninguna operación en estos documentos y quedarán excluidos de los resultados de todas las consultas realizadas. Los documentos se eliminan físicamente del sistema y se eliminan en segundo plano de manera oportuna en un momento posterior. Esta proceso no consume ninguna [unidad de solicitud (RU)](request-units.md) del presupuesto de la colección.

La lógica anterior se puede mostrar en la siguiente matriz:

|  | DefaultTTL falta o no establecido en la colección | DefaultTTL = -1 en la colección | DefaultTTL = "n" en la colección |
| --- |:--- |:--- |:--- |
| Falta TTL en el documento |No hay nada que invalidar en el nivel de documento dado que tanto el documento como la colección no tienen concepto de TTL. |Ningún documento de esta colección caducará. |Los documentos de esta colección caducarán cuando transcurra el intervalo n. |
| TTL = -1 en el documento |No hay nada que invalidar en el nivel de documento dado que la colección no define la propiedad DefaultTTL que puede invalidar un documento. El sistema no interpreta el TTL de un documento. |Ningún documento de esta colección caducará. |El documento con TTL =-1 en esta colección nunca caducará. Todos los demás documentos caducarán después del intervalo "n". |
| TTL = n en documentos |No hay nada que invalidar en el nivel de documento. El sistema no interpreta el TTL de un documento. |El documento con TTL = n caducará después del intervalo n, en segundos. Otros documentos heredarán el intervalo de -1 y no caducarán nunca. |El documento con TTL = n caducará después del intervalo n, en segundos. Otros documentos heredarán el intervalo "n" de la colección. |

## <a name="configuring-ttl"></a>Configuración de TTL
De forma predeterminada, el período de vida está deshabilitado en todas las colecciones de Cosmos DB y en todos los documentos. TTL puede establecerse mediante programación o en Azure Portal, en la sección **Configuración** de la colección. 

## <a name="enabling-ttl"></a>Habilitación del TTL
Para habilitar el TTL en una colección, o en los documentos de una colección, debe establecer la propiedad DefaultTTL de una colección en -1 o un número positivo distinto de cero. Establecer el valor de DefaultTTL en -1 significa que todos los documentos de la colección no caducarán nunca de forma predeterminada, pero el servicio Cosmos DB debe supervisar esta colección para ver los documentos que tienen invalidado este valor predeterminado.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>Configuración del TTL predeterminado en una colección
Es posible configurar un período de vida predeterminado en un nivel de colección. Para establecer el TTL de una colección, debe proporcionar un número positivo distinto de cero que indica el período en segundos, para hacer que caduquen todos los documentos de la colección después de la marca de tiempo de última modificación del documento (`_ts`). O bien, puede establecer el valor predeterminado en -1, lo que implica que todos los documentos insertados en la colección no caducarán nunca de forma predeterminada.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>Configuración del TTL en un documento
Además de establecer un TTL predeterminado en una colección, puede establecer un TTL específico en un nivel de documento. Con ello se invalida el valor predeterminado de la colección.

* Para establecer el TTL en un documento, debe proporcionar un número positivo distinto de cero que indica el período, en segundos, para hacer que caduque el documento después de la marca de tiempo de última modificación del documento (`_ts`).
* Si un documento no tiene ningún campo TTL, se aplicará el valor predeterminado de la colección.
* Si el TTL se deshabilita en el nivel de colección, se omitirá el campo TTL en el documento hasta que se vuelva a habilitar el TTL en la colección.

Este es un fragmento de código que muestra cómo establecer la expiración del período de vida de un documento:

    // Include a property that serializes to "ttl" in JSON
    public class SalesOrder
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        
        [JsonProperty(PropertyName="cid")]
        public string CustomerId { get; set; }
        
        // used to set expiration policy
        [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
        public int? TimeToLive { get; set; }
        
        //...
    }
    
    // Set the value to the expiration in seconds
    SalesOrder salesOrder = new SalesOrder
    {
        Id = "SO05",
        CustomerId = "CO18009186470",
        TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days 
    };


## <a name="extending-ttl-on-an-existing-document"></a>Extensión del TTL en un documento existente
Puede restablecer el TTL en un documento mediante cualquier operación de escritura en él realizada. Con ello, se establecerá el campo `_ts` en la hora actual, y la cuenta atrás hasta la caducidad del documento, según establece el `ttl`, comenzará de nuevo. Si desea cambiar el `ttl` en un documento, puede actualizar el campo como haría con cualquier otro campo configurable.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="removing-ttl-from-a-document"></a>Eliminación del TTL de un documento
Si se ha establecido un TTL en un documento y ya no desea que el documento caduque, puede recuperarlo, quitar el campo TTL y volver a colocar el documento en el servidor. Cuando se quita el campo TTL del documento, se aplica el valor predeterminado de la colección. Para impedir que un documento caduque y que no herede de la colección, debe establecer el valor de TTL en -1.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="disabling-ttl"></a>Deshabilitación de TTL
Para deshabilitar TTL completamente en una colección y detener el proceso en segundo plano de búsqueda de documentos caducados, se debe eliminar la propiedad DefaultTTL en la colección. Eliminar esta propiedad es diferente de establecerla en -1. Establecerla en -1 significa que los nuevos documentos agregados a la colección no caducarán nunca; sin embargo, puede invalidar esto en determinados documentos de la colección. Quitar esta propiedad completamente de la colección significa que ningún documento caducará, incluso si hay documentos que hayan invalidado explícitamente un valor predeterminado anterior.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);

<a id="ttl-and-index-interaction"></a> 
## <a name="ttl-and-index-interaction"></a>Interacción del período de vida (TTL) y el índice
Agregar o cambiar la configuración del TTL en una colección cambia el índice subyacente. Al cambiar TTL de desactivado a activado, la colección se vuelve a indexar. Al realizar cambios en la directiva de indexación cuando el modo de indexación es coherente, los usuarios no observarán cambios en el índice. Cuando el modo de indexación se establece en diferido, el índice siempre se pone al día y, si se cambia el valor de TTL, el índice se vuelve a crear desde cero. Al cambiar el valor de TTL con el modo del índice establecido en diferido, las consultas realizadas durante la regeneración del índice no devuelvan resultados correctos ni completados.

Si necesita la devolución de datos exactos, no cambie el valor de TTL con el modo de indexación establecido en diferido. Lo ideal es elegir un índice coherente para garantizar coherencia en los resultados de consulta. 

## <a name="faq"></a>Preguntas más frecuentes
**¿Cuánto me costará el TTL?**

Establecer un TTL en un documento no tiene ningún costo adicional.

**¿Cuánto tiempo se tarda en eliminar un documento una vez que el TTL está activado?**

Los documentos expiran inmediatamente una vez que TTL esté activado, y no será accesible mediante CRUD o las API de consulta. 

**¿Afectará el TTL en un documento a los cargos de RU?**

No, no se producirá ningún impacto en los cargos de RU por las eliminaciones de documentos expirados mediante TTL en Cosmos DB.

**¿La función TTL solo se aplica a documentos enteros o puedo hacer que caduquen valores de propiedad de documento individuales?**

TTL se aplica a todo el documento. Si quiere que solo una parte de un documento caduque, se recomienda que extraiga la parte del documento principal en un documento "vinculado" aparte y que use TTL en ese documento extraído.

**¿La función TTL tiene requisitos de indexación específicos?**

Sí. La colección necesita tener el [conjunto de directiva de indexación](indexing-policies.md) establecido en diferido o coherente. Si se intenta establecer DefaultTTL en una colección con la indexación definida como ninguna, se producirá un error, puesto que está intentando desactivar la indexación en una colección que tiene un valor de DefaultTTL ya establecido.

## <a name="next-steps"></a>pasos siguientes
Para más información sobre Azure Cosmos DB, consulte la página de [*documentación*](https://azure.microsoft.com/documentation/services/cosmos-db/) del servicio.

