---
title: "Indexación en la API de MongoDB de Azure Cosmos DB | Microsoft Docs"
description: "Presenta información general sobre las funcionalidades de indexación de la API de MongoDB de Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: orestis-ms
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703047
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 03/01/2018
ms.author: orkostak
ms.openlocfilehash: 090f8a664409d9cde1e4440ca9e2390a7c9def7a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="indexing-in-the-azure-cosmos-db-mongodb-api"></a>Indexación en Azure Cosmos DB: API de MongoDB

La API de MongoDB de Azure Cosmos DB aprovecha las funcionalidades de administración automática de índices de Azure Cosmos DB. Como resultado, los usuarios tienen acceso a las directivas de indexación predeterminadas de Azure Cosmos DB. Por lo tanto, si el usuario no ha definido índices o no se ha eliminado ningún índice, todos los campos se indexarán automáticamente de forma predeterminada cuando se insertan en la colección. Para la mayoría de los escenarios, se recomienda utilizar la directivas de indexación predeterminada establecida en la cuenta.

## <a name="dropping-the-default-indexes"></a>Eliminación de los índices predeterminados

El comando siguiente se puede utilizar para eliminar los índices predeterminados de una colección ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>Creación de índices compuestos

Los índices compuestos contienen referencias a varios campos de un documento. Lógicamente, son equivalentes a la creación de varios índices individuales por campo. Para aprovechar las ventajas de las optimizaciones de las técnicas de indexación de Cosmos DB, se recomienda crear varios índices individuales en lugar de un solo (no único) índice compuesto.

## <a name="creating-unique-indexes"></a>Creación de índices únicos

Los [índices únicos](unique-keys.md) son útiles para exigir que dos o más documentos no contengan el mismo valor para los campos indexados. 
>[!important] 
> Actualmente, se pueden crear índices únicos solo cuando la colección está vacía (no contiene documentos). 

El comando siguiente crea un índice único en el campo "student_id":

```JavaScript
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} ) 
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Para colecciones particionadas, según el comportamiento de MongoDB, la creación de un índice único requiere proporcionar la clave de partición. En otras palabras, todos los índices únicos de una colección particionada son índices compuestos donde uno de los campos es la clave de partición.

Los siguientes comandos crean una colección particionada ```coll``` (la clave de partición es ```university```) con un índice único en los campos student_id y university:

```JavaScript
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

En el ejemplo anterior, si se omite la cláusula ```"university":1```, se devolvería un error con el mensaje siguiente:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>Índices TTL

Para habilitar la caducidad de documentos en una colección determinada, se debe crear un ["Índice TTL" (índice de período de vida)](../cosmos-db/time-to-live.md). Un índice TTL es un índice en el campo _ts con un valor de "expireAfterSeconds".
 
Ejemplo:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

El comando anterior provocará la eliminación de todos los documentos de la colección ```db.coll``` que no se han modificado en los últimos 10 segundos. 
 
> [!NOTE]
> **_ts** es un campo específico de Cosmos DB y no es accesible desde los clientes de MongoDB. Es una propiedad reservada (sistema) que contiene la marca de tiempo de la última modificación del documento.
>

## <a name="migrating-collections-with-indexes"></a>Migración de colecciones con índices

Actualmente, solo se pueden crear índices únicos cuando la colección no contiene documentos. Las herramientas de migración de MongoDB más populares intentan crear los índices únicos después de importar los datos. Para prevenir este problema, se recomienda que los usuarios creen manualmente las colecciones y los índices únicos correspondientes en lugar de permitir que lo haga la herramienta de migración (con ```mongorestore```, este comportamiento se logra mediante la marca --noIndexRestore en la línea de comandos).

## <a name="next-steps"></a>Pasos siguientes
* [¿Cómo indexa los datos Azure Cosmos DB?](../cosmos-db/indexing-policies.md)
* [Expiración automática de los datos de las colecciones de Azure Cosmos DB con período de vida](../cosmos-db/time-to-live.md)
