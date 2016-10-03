<properties 
	pageTitle="Obtención de una vista previa de las instrucciones de desarrollo para cuentas de DocumentDB con soporte de protocolo para MongoDB | Microsoft Azure." 
	description="Aprenda a obtener una vista previa de las instrucciones de desarrollo para cuentas de DocumentDB con soporte de protocolo para MongoDB, que ya están disponibles para su vista previa." 
	services="documentdb" 
	authors="andrewhoh" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="anhoh"/>

# Instrucciones de desarrollo para cuentas de DocumentDB con compatibilidad con el protocolo MongoDB

Puede comunicarse con Azure DocumentDB a través de todos los [controladores](https://docs.mongodb.org/ecosystem/drivers/) de cliente de MongoDB de código abierto. El soporte de protocolo para MongoDB supone que los controladores de cliente de MongoDB se comunican con un punto de conexión del servidor de MongoDB 2.6 o posterior. Esta compatibilidad es gracias a que DocumentDB sigue el protocolo [MongoDB Wire Protocol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol/), versión 2.6.

DocumentDB es compatible con las funciones principales de la API de MongoDB para realizar acciones de creación, lectura, actualización y eliminación (CRUD) de datos así como de consulta de la base de datos. Las funcionalidades implementadas se han priorizado según las necesidades de plataformas, marcos, herramientas y patrones de aplicación comunes.

## Colecciones

> [AZURE.IMPORTANT] DocumentDB utiliza rendimiento reservado en el nivel de colección para ofrecer un rendimiento garantizado y predecible. Las colecciones de DocumentDB son, por tanto, entidades facturables.

Las reservas de rendimiento se aplican en el nivel de colección para que las aplicaciones puedan ajustar el rendimiento al nivel más bajo de los contenedores de datos del sistema. El precio de una colección viene determinado por el rendimiento aprovisionado de la colección, que se mide en unidades de solicitud por segundo, junto con el almacenamiento total consumido en gigabytes. El rendimiento aprovisionado se puede ajustar durante la vida de una colección para adaptarse a las cambiantes necesidades de procesamiento y a los patrones de acceso de la aplicación. Para obtener más información, consulte [Niveles de rendimiento de DocumentDB](documentdb-performance-levels.md).

Las colecciones de DocumentDB con soporte de protocolo para MongoDB se crean, de forma predeterminada, con el plan de tarifa estándar de 1000 RU/s de rendimiento aprovisionado. Puede ajustar el rendimiento aprovisionado de cada una de las colecciones, tal como se describe en la sección [Cambio de los niveles de rendimiento mediante el Portal de Azure clásico](documentdb-performance-levels.md#changing-performance-levels-using-the-azure-portal).

## Operaciones CRUD

Las operaciones de MongoDB de inserción, lectura, actualización, reemplazo y eliminación son totalmente compatibles. Esto incluye compatibilidad con las actualizaciones de campos, matrices, las de bit a bit y las de aislamiento tal y como se especifica en [Update operator specification](https://docs.mongodb.org/manual/reference/operator/update/) (Especificaciones de los operadores de actualización) de MongoDB. Para los operadores de actualización que necesitan varias manipulaciones de documentos, DocumentDB proporciona la semántica ACID completa con aislamiento de instantánea.

## Operaciones de consulta

DocumentDB admite la gramática completa de consulta de MongoDB con algunas excepciones. Se admiten las consultas que operan en el conjunto compatible de JSON de [tipos BSON](https://docs.mongodb.org/manual/reference/bson-types/) y también se admite el formato de hora y fecha de MongoDB. Para las consultas que requieren operadores específicos de tipos distintos a JSON, DocumentDB admite los tipos de datos de GUID.

## Experiencia del portal
La experiencia de Azure Portal en las cuentas habilitadas para el protocolo MongoDB se proporciona para dichas cuentas. Queremos ampliar la experiencia, pero necesitamos sus [comentarios](mailto:askdocdb@microsoft.com?subject=DocumentDB%20Protocol%20Support%20for%20MongoDB%20Preview%20Portal%20Experience) respecto a qué características del portal le serían de mayor utilidad.

## Matrices compatibles


### Operaciones de CRUD y de consulta

Característica|Compatible|Compatible en el futuro
---|---|---
Insertar|InsertOne| 
 |InsertMany| 
 |Insertar| 
Actualizar|UpdateOne| 
 |UpdateMany| 
 |Actualizar| 
Actualización de campo|$inc, $mul, $rename, $set, $unset, $min, $max|$currentDate| 
Actualización de matrices| |-todo-
Bit a bit| |-todo-
Aislamiento| |-todo-
Sustituya|ReplaceOne| 
Eliminar|DeleteOne | 
 |DeleteMany| 
 |Remove| 
BulkWrite| |bulkWrite()
De comparación|-todo-| 
Lógicos|-todo-| 
Consulta de elementos| |-todo-
Evaluación|$mod, $regex |$text, $where
Geoespaciales|2dsphere, 2d, polygon|Todo lo demás
Array|$all, $size, $elemMatch|
Bit a bit| |-todo-
Comentario|-todo-| 
Proyección| |-todo-


### Comandos de base de datos

Característica|Compatible|Compatible en el futuro
---|---|---
Agregación|Recuento| 
Geoespaciales| |-todo-
Consulta y escritura|find, insert, update, delete, getLastError, getMore, findAndModify| 
Autenticación|getnonce, logout, authenticate| 
Administración|createIndex, listIndexes, dropIndexes, connectionStatus, reIndex| 
Diagnóstico|listDatabases, collStats, dbStats| 

## Pasos siguientes

- Obtenga información acerca de cómo [usar MongoChef](documentdb-mongodb-mongochef.md) con una cuenta de DocumentDB con compatibilidad de protocolo con MongoDB.
- Explore DocumentDB con soporte de protocolo para buscar [ejemplos](documentdb-mongodb-samples.md) de MongoDB.

 

<!---HONumber=AcomDC_0921_2016-->