---
title: "Compatibilidad de la característica Cosmos DB con MongoDB | Microsoft Docs"
description: "Obtenga información acerca de la compatibilidad de la característica que proporciona la API de Azure Cosmos DB MongoDB con MongoDB 3.4."
services: cosmos-db
author: alekseys
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 29b6547c-3201-44b6-9e0b-e6f56e473e24
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: 007b530cd7a14f063ae4f86d18daa9742c6655c2
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>Compatibilidad de la API de MongoDB con la sintaxis y las características de MongoDB

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede comunicarse con la API de MongoDB de la base de datos través de todos los [controladores](https://docs.mongodb.org/ecosystem/drivers) de cliente de MongoDB de código abierto. La API de MongoDB permite el uso de los controladores de cliente existentes mediante la adhesión al [protocolo de conexión](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) de MongoDB.

Mediante la API de Azure Cosmos DB MongoDB, puede disfrutar de las ventajas de las API de MongoDB a las que está acostumbrado, con todas las funcionalidades empresariales de Azure Cosmos DB: [distribución global](distribute-data-globally.md), [particionamiento automático](partition-data.md), garantías de disponibilidad y latencia, indexación automática de cada campo, el cifrado en reposo, copias de seguridad y mucho más.

## <a name="mongodb-query-language-support"></a>Compatibilidad con lenguajes de consulta de MongoDB

La API de Azure Cosmos DB MongoDB proporciona una compatibilidad completa con las construcciones del lenguaje de consulta de MongoDB. A continuación, encontrará una lista detallada de las opciones, comandos, fases, operadores y operaciones compatibles actualmente.


## <a name="database-commands"></a>Comandos de base de datos

Azure Cosmos DB admite los siguientes comandos de base de datos en todas las cuentas de la API de MongoDB. 

### <a name="query-and-write-operation-commands"></a>Comandos de operación de consulta y escritura
- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>Comandos de autenticación
- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Comandos de administración
- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Comandos de diagnóstico
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Canalización de agregación</a>

Azure DB Cosmos es compatible con la canalización de agregación en la versión preliminar pública. Vea el [blog de Azure](https://aka.ms/mongodb-aggregation) para obtener instrucciones sobre cómo incorporarse a la versión preliminar pública.

### <a name="aggregation-commands"></a>Comandos de agregación
- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Fases de agregación
- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count

### <a name="aggregation-expressions"></a>Expresiones de agregación

#### <a name="boolean-expressions"></a>Expresiones booleanas
- $and
- $or
- $not

#### <a name="set-expressions"></a>Expresiones de conjunto
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Expresiones de comparación
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Expresiones aritméticas
- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Expresiones de cadena
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Expresiones de matriz
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Expresiones de fecha
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Expresiones condicionales
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Acumuladores de agregación
- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operadores

Los siguientes operadores son compatibles con los correspondientes ejemplos de su uso. Tenga en cuenta este documento de muestra utilizada en las consultas siguientes:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

operador | Ejemplo |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$in | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$or | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$and | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$nor | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$exists | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>Notas

En las consultas de $regex, las expresiones ancladas a la izquierda permiten la búsqueda de índice. Sin embargo, si utiliza el modificador 'i' (no distingue mayúsculas y minúsculas) y el modificador 'm' modificador (multilínea), se realiza el examen de colección de todas las expresiones.
Cuando es necesario incluir '$' o '|', es mejor crear dos (o más) consultas regex. Por ejemplo, dada la siguiente consulta original: ```find({x:{$regex: /^abc$/})```, tiene que modificarse de la siguiente forma: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
La primera parte utilizará el índice para restringir la búsqueda a esos documentos que empiezan por ^ abc y la segunda parte buscará coincidencias con los datos exactos. El operador de barra '|' actúa como una función "or": la consulta ```find({x:{$regex: /^abc|^def/})``` coincide con los documentos con los que el campo 'x' tiene un valor que comienza por "abc" o "def". Para utilizar el índice, se recomienda dividir la consulta en dos consultas distintas combinadas mediante el operador $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.


### <a name="geospatial-operators"></a>Operadores de geoespaciales

operador | Ejemplo 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Sí
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sí
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sí
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Sí
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sí
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Sí
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Sí
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Sí
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Sí
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Sí
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sí

## <a name="additional-operators"></a>Operadores adicionales

operador | Ejemplo | Notas 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | No compatible. Utilice $regex en su lugar. 

### <a name="methods"></a>Métodos

Los siguientes métodos son compatibles:

#### <a name="cursor-methods"></a>Métodos de cursor

Método | Ejemplo | Notas 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | No se devuelven los documentos sin criterio de ordenación.

## <a name="unique-indexes"></a>Índices únicos

Azure Cosmos DB indexa cada campo de los documentos que se escriben en la base de datos de forma predeterminada. Los índices únicos garantizan que un campo concreto no tiene valores duplicados en todos los documentos de una colección, de forma similar en que se conserva la singularidad en la clave "_id" predeterminada. Ahora puede crear índices personalizados en Azure Cosmos DB mediante el comando createIndex, incluida la restricción 'unique'.

Los índices únicos están disponibles para todas las cuentas de la API de MongoDB.

## <a name="time-to-live-ttl"></a>Período de vida (TTL)

Azure Cosmos DB admite un período de vida (TTL) relativo en función de la marca de tiempo del documento. El período de vida (TTL) puede habilitarse para las colecciones de la API de MongoDB a través de [Azure Portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Administración de usuarios y roles

Azure Cosmos DB no admite aún usuarios y roles. Azure Cosmos DB admite el control de acceso basado en roles (RBAC) y claves/contraseñas de solo lectura y escritura que pueden obtenerse a través de [Azure Portal](https://portal.azure.com) (página de cadena de conexión).

## <a name="replication"></a>Replicación

Azure Cosmos DB admite la replicación automática y nativa en las capas más inferiores. Esta lógica se amplía para lograr también una replicación global de baja latencia. Azure Cosmos DB no es compatible con comandos de replicación manuales.

## <a name="sharding"></a>Clave de particionamiento

Azure Cosmos DB admite el particionamiento de servidor automático. no es compatible con comandos de particionamiento manual.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de cómo [usar Studio 3T](mongodb-mongochef.md) con una API para la base de datos de MongoDB.
- Obtenga información acerca de cómo [usar Robo 3T](mongodb-robomongo.md) con una API para la base de datos de MongoDB.
- Explore Azure Cosmos DB con compatibilidad de protocolo para buscar [ejemplos](mongodb-samples.md)de MongoDB.
