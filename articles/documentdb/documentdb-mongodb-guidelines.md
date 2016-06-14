<properties 
	pageTitle="Obtención de una vista previa de las instrucciones de desarrollo para cuentas de DocumentDB con soporte de protocolo para MongoDB | Microsoft Azure." 
	description="Aprenda a obtener una vista previa de las instrucciones de desarrollo para cuentas de DocumentDB con soporte de protocolo para MongoDB, que ya están disponibles para su vista previa." 
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

# Obtenga una vista previa de las instrucciones de desarrollo para cuentas de DocumentDB con soporte de protocolo para MongoDB.

Puede comunicarse con DocumentDB a través de todos los [controladores](https://docs.mongodb.org/ecosystem/drivers/) de cliente de MongoDB de código abierto. El soporte de protocolo para MongoDB supone que los controladores de cliente de MongoDB se comunican con un punto de conexión del servidor de MongoDB 2.6 o posterior. DocumentDB admite esto mediante la adhesión al [protocolo de conexión](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol/) de MongoDB versión 2.6 (tenga en cuenta que el protocolo de conexión de la versión 3.2 es compatible casi en su totalidad, pero algunas experiencias de clientes como las sesiones shell de MongoDB versión 3.2 parecen indicar que se está "degradando al modo 'heredado'").

DocumentDB es compatible con las funciones principales de la API de MongoDB para realizar acciones de creación, lectura, actualización y eliminación (CRUD) de datos así como de consulta de la base de datos. Las funcionalidades implementadas se han priorizado según las necesidades de plataformas, marcos, herramientas y patrones de aplicación comunes.

## Colecciones

> [AZURE.IMPORTANT] DocumentDB utiliza rendimiento reservado en el nivel de colección para ofrecer un rendimiento garantizado y predecible. Las colecciones de DocumentDB son, por tanto, entidades facturables.

Las reservas de rendimiento se aplican en el nivel de colección para que las aplicaciones puedan ajustar el rendimiento al nivel más bajo de los contenedores de datos del sistema. Por consiguiente, el costo de una colección viene determinado por el rendimiento aprovisionado de la colección, que se mide en unidades de solicitud por segundo, junto con el almacenamiento total consumido en gigabytes. El rendimiento aprovisionado se puede ajustar durante la vida de una colección para adaptarse a las cambiantes necesidades de procesamiento y a los patrones de acceso de la aplicación. Para obtener más información, consulte [Niveles de rendimiento de DocumentDB](documentdb-performance-levels.md).

Las colecciones de DocumentDB con soporte de protocolo para MongoDB se crean, de forma predeterminada, con el plan de tarifa estándar de 1000 RU/s de rendimiento aprovisionado. Puede ajustar el rendimiento aprovisionado de cada una de las colecciones, tal como se describe en la sección [Cambio de los niveles de rendimiento mediante el Portal de Azure clásico](documentdb-performance-levels.md#changing-performance-levels-using-the-azure-portal).

## Operaciones CRUD

Las operaciones de MongoDB de inserción, lectura, actualización, reemplazo y eliminación son totalmente compatibles. Esto incluye compatibilidad con las actualizaciones de campos, matrices, las de bit a bit y las de aislamiento tal y como se especifica en [Update operator specification](https://docs.mongodb.org/manual/reference/operator/update/) (Especificaciones de los operadores de actualización) de MongoDB. Para los operadores de actualización que necesitan varias manipulaciones de documentos, DocumentDB proporciona la semántica ACID completa con aislamiento de instantánea.

## Operaciones de consulta

DocumentDB admite la gramática completa de consulta de MongoDB con algunas excepciones. Se admiten las consultas que operan en el conjunto compatible de JSON de [tipos BSON](https://docs.mongodb.org/manual/reference/bson-types/) y también se admite el formato de hora y fecha de MongoDB. Para las consultas que requieren operadores específicos de tipos distintos a JSON, DocumentDB admite los tipos de datos de GUID. La tabla siguiente proporciona los aspectos compatibles y no compatibles de la gramática de consulta de MongoDB.

## Agregación

DocumentDB no admite las operaciones de MongoDB de canalización de agregación ni las de Map-Reduce. La canalización de agregación se utiliza normalmente para procesar documentos a través de un conjunto de varias fases de filtros y transformaciones, como el emparejamiento y agrupación de los resultados. DocumentDB admite, de forma nativa, funciones definidas por el usuario y procedimientos almacenados de JavaScript. Además, DocumentDB puede actuar fácilmente como origen y receptor de trabajos de Hive, Pig y MapReduce mediante HDInsight de Azure a través del [conector de Hadoop](documentdb-run-hadoop-with-hdinsight.md) de DocumentDB.

## Experiencia del portal
La experiencia del Portal de Azure para las cuentas con protocolo habilitado de MongoDB es ligeramente diferente a la experiencia del Portal para las cuentas estándar de DocumentDB. Queremos ampliar la experiencia, pero necesitamos sus [comentarios](mailto:askdocdb@microsoft.com?subject=DocumentDB%20Protocol%20Support%20for%20MongoDB%20Preview%20Portal%20Experience) respecto a qué características del portal le serían de mayor utilidad.

## Matrices compatibles


### Operaciones de CRUD y de consulta

Característica|Compatible|Compatible en el futuro|No compatible 
---|---|---|---
Insertar|InsertOne| | 
 |InsertMany| | 
 |Insertar| | 
Actualizar|UpdateOne| | 
 |UpdateMany| | 
 |Actualizar| | 
Actualización de campo|$inc, $mul, $rename, $set, $unset, $min, $max|$currentDate| 
Actualización de matrices| |-todo-| 
Bit a bit| |-todo-| 
Aislamiento| |-todo-| 
Sustituya|ReplaceOne| |
Eliminar|DeleteOne | |
 |DeleteMany| | 
 |Remove| | 
BulkWrite| |bulkWrite()| 
De comparación|-todo-| | 
Lógicos|-todo-| | 
Consulta de elementos| |-todo-| 
Evaluación|$mod|$regex, $text, $where| 
Geoespaciales|2dsphere, 2d, polygon|Todo lo demás| 
Array|$all, $size|$elemMatch| 
Bit a bit| |-todo-| 
Comentario|-todo-| | 
Proyección| |-todo-| 


### Comandos de base de datos

Característica|Compatible|Compatible en el futuro|No compatible 
---|---|---|---
Agregación|Recuento| |aggregate, distinct, group, mapreduce
Geoespaciales| |-todo-| 
Consulta y escritura|find, insert, update, delete, getLastError, getMore, findAndModify| |Eval, parallelCollectionScan, getPrevError, resetError
Caché de QueryPlan| | |-todo-
Autenticación|getnonce, logout, authenticate| |Copydbgetnone, authschemaUpgrade
Administración de usuarios| | |-todo-
Administración de roles| | |-todo-
Replicación| | |-todo-
Administración|createIndex, listIndexes, dropIndexes, connectionStatus, reIndex| |Otros comandos. Para los índices, no hay compatibilidad para Unique, expireAfterSeconds, storageEngine, weights, default\_language, textIndexVersion, min, max, bucketSize
Diagnóstico|listDatabases, collStats, dbStats| |Todo lo demás

## Pasos siguientes

- Obtenga información acerca de cómo [usar MongoChef](documentdb-mongodb-mongochef.md) con una cuenta de DocumentDB con compatibilidad de protocolo con MongoDB.
- Explore DocumentDB con soporte de protocolo para buscar [ejemplos](documentdb-mongodb-samples.md) de MongoDB.

 

<!---HONumber=AcomDC_0601_2016-->