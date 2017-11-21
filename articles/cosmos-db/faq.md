---
title: "Preguntas más frecuentes sobre Azure Cosmos DB | Microsoft Docs"
description: "Obtenga respuestas a las preguntas más frecuentes sobre Azure Cosmos DB, un servicio de base de datos con varios modelos distribuido globalmente. Obtenga más información sobre capacidad, niveles de rendimiento y escalado."
keywords: Database questions, frequently asked questions, documentdb, azure, Microsoft azure
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: f7949fe4acbc4efa394bf8bfb34e912a6c861ba3
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmos-db-faq"></a>Preguntas más frecuentes sobre Azure Cosmos DB
## <a name="azure-cosmos-db-fundamentals"></a>Conceptos básicos de Azure Cosmos DB
### <a name="what-is-azure-cosmos-db"></a>¿Qué es Azure Cosmos DB?
Azure Cosmos DB es un servicio de base de datos de varios modelos distribuido de forma global que ofrece consultas enriquecidas de datos sin esquemas, ayuda a proporcionar un rendimiento configurable y de confianza, y posibilita un desarrollo rápido. Todo ello gracias a una plataforma administrada respaldada por la potencia y el alcance de Microsoft Azure. 

Azure Cosmos DB es la solución adecuada para aplicaciones web, móviles, de juego e IoT cuando un rendimiento predecible, una alta disponibilidad, una baja latencia y un modelo de datos sin esquemas son requisitos clave. Ofrece flexibilidad de esquemas e indexación enriquecida, e incluye compatibilidad transaccional de documentos múltiples con JavaScript integrado. 

Para otras preguntas, respuestas e instrucciones sobre bases de datos para implementar y usar este servicio, vea la página de documentación de Azure Cosmos DB].(https://docs.microsoft.com/azure/cosmos-db/).

### <a name="what-happened-to-documentdb"></a>¿Qué ha ocurrido con DocumentDB?
La API de DocumentDB es uno de los modelos de datos y las API compatibles con Azure Cosmos DB. Además, Azure Cosmos DB ofrece compatibilidad con API Graph (versión preliminar), Table API y MongoDB API. Para más información, vea [Preguntas de clientes de DocumentDB](#moving-to-cosmos-db).

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>¿Cómo accedo a mi cuenta de DocumentDB en Azure Portal?
En Azure Portal, haga clic en el icono de Azure Cosmos DB del panel izquierdo. Si ya tenía una cuenta de DocumentDB, ahora tiene una cuenta de Azure Cosmos DB, sin cambios en la facturación.

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>¿Cuáles son los casos de uso típicos de Azure Cosmos DB?
Azure Cosmos DB es una buena elección para las nuevas aplicaciones web, móviles, de juegos y de IoT donde la escala automática, el rendimiento predecible, la orden rápida de tiempos de respuesta en milisegundos y la capacidad para consultar datos sin esquemas son importantes. Se presta a un desarrollo rápido y admite la iteración continua de modelos de datos de aplicaciones. Las aplicaciones que administran contenido y datos generados por el usuario son [casos de uso comunes de Azure Cosmos DB](use-cases.md). 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>¿Cómo ofrece Azure Cosmos DB un rendimiento predecible?
Una [unidad de solicitud](request-units.md) (RU) es la medida de procesamiento de Azure Cosmos DB. 1 RU corresponde al rendimiento de una solicitud GET de un documento de 1 KB. Todas las operaciones de Azure Cosmos DB, incluidas las lecturas, escrituras, consultas SQL y ejecuciones de procedimiento almacenado, tienen un valor de unidad de solicitud determinista en función del rendimiento necesario para completar la operación. En lugar de pensar en la CPU, las E/S y la memoria, y en cómo cada uno de estos aspectos afecta al rendimiento de la aplicación, es mejor que se centre en una unidad única de unidad de solicitud.

Cada contenedor de Azure Cosmos DB puede reservarse con rendimiento aprovisionado en términos de unidades de solicitud del rendimiento por segundo. Para las aplicaciones de cualquier escala, puede realizar pruebas comparativas de las solicitudes individuales para medir los valores de unidad de solicitud y aprovisionar un contenedor para administrar el total de unidades de solicitud en todas las solicitudes. También puede escalar o reducir verticalmente el rendimiento del contenedor a medida que evolucionen las necesidades de la aplicación. Para más información sobre las unidades de solicitud y ayuda para determinar las necesidades del contenedor, vea [Estimación de necesidades de rendimiento](request-units.md#estimating-throughput-needs) y pruebe la [calculadora de rendimiento](https://www.documentdb.com/capacityplanner). En este contexto, el término *contenedor* se refiere a una colección de la API de DocumentDB, un gráfico de API Graph, una colección de la API de MongoDB y una tabla de Table API. 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>¿Por qué Azure Cosmos DB admite varios modelos de datos, como pares clave-valor, columnas, documentos y gráficos?

Los modelos de datos de pares clave-valor (tabla), columnas, documentos y gráficos se admiten de forma nativa porque Azure Cosmos DB se basa en el diseño de ARS (átomos, registros y secuencias). Los átomos, los registros y las secuencias se pueden asignar y proyectar con facilidad en varios modelos de datos. Ya se encuentran disponibles las API para un subconjunto de modelos (API de DocumentDB, MongoDB, Table y Graph), mientras que otras que son específicas para modelos de datos adicionales estarán disponibles en el futuro.

Azure Cosmos DB tiene un motor de indexación independiente del esquema capaz de indexar automáticamente todos los datos ingeridos sin necesidad de que el desarrollador proporcione índices de esquema o secundarios. El motor se basa en un conjunto de diseños de índices lógicos (invertidos, en columnas y en árbol) que desacopla el diseño de almacenamiento del índice y los subsistemas de procesamiento de consultas. Cosmos DB también tiene la capacidad de admitir un conjunto de API y protocolos de conexión de forma extensible y convertirlos de forma eficaz en el modelo de datos principal (1) y los diseños de índices lógicos (2), lo que le permite admitir con exclusividad varios modelos de datos de forma nativa.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>¿Es compatible Azure Cosmos DB con HIPAA?
Sí, Azure Cosmos DB es compatible con HIPAA. HIPAA establece los requisitos para el uso, la divulgación y la protección de información de mantenimiento identificable individualmente. Para más información, consulte el [Centro de confianza de Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>¿Cuáles son los límites de almacenamiento de Azure Cosmos DB?
No existe ningún límite en la cantidad total de datos que puede almacenar un contenedor en Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>¿Cuáles son los límites de rendimiento de Azure Cosmos DB?
No existe ningún límite en la cantidad total de rendimiento que admite un contenedor en Azure Cosmos DB. La idea clave es distribuir la carga de trabajo de manera más o menos uniforme entre una cantidad suficientemente grande de claves partición.

### <a name="how-much-does-azure-cosmos-db-cost"></a>¿Cuánto cuesta Azure Cosmos DB?
Para más información, vea la página de [precios de Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Los cargos por uso de Azure Cosmos DB se determinan según la cantidad de contenedores aprovisionados, el número de horas que los contenedores estuvieron conectados y el rendimiento aprovisionado de cada contenedor. En este contexto, el término *contenedor* se refiere a una colección de la API de DocumentDB, un gráfico de API Graph, una colección de la API de MongoDB y una tabla de Table API. 

### <a name="is-a-free-account-available"></a>¿Existe una cuenta gratuita disponible?
Sí, puede registrarse para una cuenta de tiempo limitado sin cargo, con ningún compromiso. Para registrarse, visite [Pruebe gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) o consulte la sección de [preguntas más frecuentes sobre Azure Cosmos DB](#try-cosmos-db).

Si es la primera vez que usa Azure, regístrese para obtener una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), que ofrece 30 días y un crédito para que pruebe todos los servicios de Azure. Si tiene una suscripción a Visual Studio, puede recibir [créditos gratis de Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para usarlos en cualquier servicio de Azure. 

También puede usar el [Emulador de Azure Cosmos DB](local-emulator.md) para desarrollar y probar su aplicación localmente, de forma gratuita, sin necesidad de crear una suscripción de Azure. Cuando esté satisfecho con el funcionamiento de la aplicación en el Emulador, puede cambiar a una cuenta de Azure Cosmos DB en la nube.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>¿Cómo puedo obtener ayuda adicional con Azure Cosmos DB?
Si necesita ayuda, póngase en contacto con nosotros en [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb), el [foro de MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB) o programe un chat individual con el equipo de ingeniería de Azure Cosmos DB, para lo que debe enviar un correo a [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com). 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>Prueba de las suscripciones de Azure Cosmos DB

Ya puede disfrutar de Azure Cosmos DB durante un tiempo limitado sin una suscripción, y sin que le suponga ningún gasto ni compromiso. Para registrarse en una suscripción de prueba de Azure Cosmos DB, vaya a [Pruebe gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Esta suscripción es independiente de la [evaluación gratuita de Azure](https://azure.microsoft.com/free/) y puede utilizarse junto con una evaluación gratuita o una suscripción de pago de Azure. 

Las suscripciones de prueba de Azure Cosmos DB aparecen en Azure Portal junto con otras suscripciones asociadas con el identificador de usuario. 

Las condiciones siguientes se aplican a las suscripciones de prueba de Azure Cosmos DB:

* Un contenedor por cada suscripción de SQL (API de DocumentDB), Gremlin (API Graph) y cuentas de Table.
* Hasta 3 colecciones por suscripción para las cuentas de MongoDB.
* 10 GB de capacidad de almacenamiento.
* La replicación global está disponible en las siguientes [regiones de Azure](https://azure.microsoft.com/regions/): Centro de EE. UU., Europa del Norte y Sudeste Asiático
* Rendimiento máximo de 5000 unidades de solicitud por segundo.
* Las suscripciones expiran después de 24 horas y se pueden extender hasta un máximo de 48 horas.
* No se pueden crear vales de soporte técnico de Azure para las cuentas de prueba de Azure Cosmos DB; sin embargo, se proporciona soporte técnico para aquellos suscriptores con planes de soporte técnico ya existentes. 

## <a name="set-up-azure-cosmos-db"></a>Configuración de Azure Cosmos DB
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>¿Cómo me registro en Azure Cosmos DB?
Azure Cosmos DB está disponible en Azure Portal. En primer lugar, regístrese para obtener una suscripción de Azure. Una vez registrado, puede agregar una cuenta de API de DocumentDB, API Graph (versión preliminar), Table API o API de MongoDB a su suscripción de Azure.

### <a name="what-is-a-master-key"></a>¿Qué es una clave maestra?
Una clave maestra es un token de seguridad para acceder a todos los recursos de una cuenta. Los usuarios con esta clave tienen acceso de lectura y escritura a todos los recursos de la cuenta de base de datos. Tenga cuidado cuando distribuya claves maestras. Las claves maestras principal y secundaria están disponibles en la hoja **Claves** de [Azure Portal][azure-portal]. Para más información sobre las claves, vea [Visualización, copia y regeneración de las claves de acceso](manage-account.md#keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>¿En qué regiones se puede establecer PreferredLocations? 
El valor PreferredLocations se puede establecer en cualquiera de las regiones de Azure en las que Cosmos DB está disponible. Para obtener una lista de las regiones disponibles, vea [Regiones de Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>¿Hay algo que deba tener en cuenta al distribuir datos en todo el mundo a través de centros de datos de Azure? 
Azure Cosmos DB se ofrece en todas las regiones de Azure, tal y como se especifica en la página [Regiones de Azure](https://azure.microsoft.com/regions/). Puesto que se trata del servicio principal, cada nuevo centro de datos está presente en Azure Cosmos DB. 

Al establecer una región, debe recordar que Cosmos DB respeta las nubes independientes y de administración pública. Es decir, si crea una cuenta en una región independiente, no puede replicarla fuera de dicha región. De manera similar, tampoco puede habilitar la replicación en otras ubicaciones independientes desde una cuenta externa. 

## <a name="develop-against-the-documentdb-api"></a>Desarrollo con la API de DocumentDB

### <a name="how-do-i-start-developing-against-the-documentdb-api"></a>¿Cómo se empieza a desarrollar con la API de DocumentDB?
La API de Microsoft DocumentDB está disponible en [Azure Portal][azure-portal]. En primer lugar, debe registrarse para obtener una suscripción de Azure. Una vez registrado para obtener una suscripción de Azure, puede agregar un contenedor de la API de DocumentDB a la suscripción a Azure. Para obtener instrucciones sobre cómo agregar una cuenta de Azure Cosmos DB, vea [Creación de una cuenta de base de datos de Azure Cosmos DB](create-documentdb-dotnet.md#create-account). Si tuvo una cuenta de DocumentDB en el pasado, ahora tiene una cuenta de Azure Cosmos DB. 

[SDK](documentdb-sdk-dotnet.md) están disponibles para NET, Python, Node.js, JavaScript y Java. Los desarrolladores también pueden usar las [API de HTTP RESTful](/rest/api/documentdb/) para interactuar con recursos de Azure Cosmos DB en varias plataformas y lenguajes.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>¿Se puede acceder a algunos ejemplos disponibles que me puedan ayudar a empezar?
En GitHub se encuentran disponibles ejemplos de los SDK de la API de DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) y [Python](documentdb-python-samples.md).


### <a name="does-the-documentdb-api-database-support-schema-free-data"></a>¿Las bases de datos de la API de DocumentDB admiten datos sin esquemas?
Sí, la API de DocumentDB permite que las aplicaciones almacenen documentos JSON arbitrarios sin definiciones ni sugerencias de esquemas. Los datos están disponibles inmediatamente para consulta a través de la interfaz de consulta SQL de Azure Cosmos DB.  

### <a name="does-the-documentdb-api-support-acid-transactions"></a>¿La API de DocumentDB admite transacciones ACID?
Sí, la API de DocumentDB admite transacciones entre documentos expresadas como desencadenadores y procedimientos almacenados con JavaScript. Las transacciones se limitan a una única partición dentro de cada colección y se ejecutan con semántica ACID, como "todo o nada", aisladas de otras solicitudes de usuario y código que se ejecutan a la vez. Si se producen excepciones por la ejecución en el servidor de código de aplicación JavaScript, la transacción entera se revierte. Para obtener más información sobre las transacciones, consulte [Transacciones del programa de base de datos](programming.md#database-program-transactions).

### <a name="what-is-a-collection"></a>¿Qué es una colección?
Una colección es un grupo de documentos y su lógica de aplicación de JavaScript asociada. Una colección es una entidad facturable, en la que el [costo](performance-levels.md) se determina en función del procesamiento y del almacenamiento utilizado. Las colecciones pueden abarcar una o varias particiones o uno o varios servidores y se pueden escalar para administrar volúmenes prácticamente ilimitados de almacenamiento o rendimiento.

Las colecciones también son las entidades de facturación de Azure Cosmos DB. Cada colección se factura por hora según el rendimiento aprovisionado y el espacio de almacenamiento usado. Para más información, vea los [precios de Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). 

### <a name="how-do-i-create-a-database"></a>¿Cómo se crea una base de datos?
Puede crear bases de datos mediante [Azure Portal](https://portal.azure.com), tal y como se describe en [Agregar una colección](create-documentdb-dotnet.md#create-collection), uno de los [SDK de Azure Cosmos DB](documentdb-sdk-dotnet.md) o las [API de REST](/rest/api/documentdb/). 

### <a name="how-do-i-set-up-users-and-permissions"></a>¿Cómo se configuran los usuarios y los permisos?
Puede crear usuarios y permisos mediante uno de los [SDK de la API de Cosmos DB](documentdb-sdk-dotnet.md) o de las [API de REST](/rest/api/documentdb/).  

### <a name="does-the-documentdb-api-support-sql"></a>¿La API de DocumentDB admite SQL?
El lenguaje de consulta SQL es un subconjunto mejorado de la funcionalidad de consulta admitida por SQL. El lenguaje de consulta SQL de Azure Cosmos DB ofrece complejos operadores relacionales y jerárquicos y extensibilidad a través de funciones definidas por el usuario (UDF) basadas en JavaScript. La sintaxis JSON permite el modelado de documentos JSON como árboles con nodos etiquetados, algo que usan las técnicas de indexación automática de Azure Cosmos DB y el dialecto de consulta SQL de Azure Cosmos DB. Para información detallada sobre el uso de la gramática de SQL, vea el artículo [QueryDocumentDB][query].

### <a name="does-the-documentdb-api-support-sql-aggregation-functions"></a>¿La API de DocumentDB admite las funciones de agregación de SQL?
La API de DocumentDB admite la agregación de baja latencia a cualquier escala a través de las funciones de agregado `COUNT`, `MIN`, `MAX`, `AVG` y `SUM` mediante la gramática de SQL. Para más información, consulte [Funciones de agregado](documentdb-sql-query.md#Aggregates).

### <a name="how-does-the-documentdb-api-provide-concurrency"></a>¿De qué modo proporciona simultaneidad la API de DocumentDB?
La API de DocumentDB admite control de simultaneidad optimista (OCC) a través de etiquetas de entidad HTTP, o ETag. Cada recurso de la API de DocumentDB tiene una ETag, que se establece en el servidor cada vez que se actualiza un documento. El encabezado de la ETag y el valor actual se incluyen en todos los mensajes de respuesta. Las etiquetas ETag pueden utilizarse con el encabezado If-Match para permitir que el servidor decida si debe actualizarse un recurso. El valor de If-Match es el valor de ETag con el que se va a cotejar. Si el valor de ETag coincide con el valor de ETag del servidor, el recurso se actualiza. Si el valor de ETag ya no es el actual, el servidor rechaza la operación con un código de respuesta "HTTP 412 Precondition failure" (HTTP 412: error de condición previa). Después, el cliente recupera el recurso para obtener el valor de Etag actual del recurso. Además, las etiquetas ETag pueden emplearse con el encabezado If-None-Match para determinar si hay que volver a recuperar un recurso.

Para usar la simultaneidad optimista en .NET, utilice la clase [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Para ver un ejemplo de .NET, vea la sección [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) en el ejemplo del artículo DocumentManagement de GitHub.

### <a name="how-do-i-perform-transactions-in-the-documentdb-api"></a>¿Cómo se realizan transacciones en la API de DocumentDB?
La API de DocumentDB admite transacciones integradas en el lenguaje mediante desencadenadores y procedimientos almacenados JavaScript. Todas las operaciones de base de datos contenidas en scripts se ejecutan con aislamiento de instantánea. Si se trata de una colección de partición única, el ámbito de la ejecución es la colección. Si la colección se particiona, el ámbito de la ejecución son documentos con el mismo valor de clave de partición que hay dentro de la colección. Una instantánea de las versiones de documento (etiquetas ETag) se toma al comienzo de la transacción y solo se confirma si el script se ejecuta correctamente. Si el JavaScript produce un error, la transacción se revierte. Para más información, vea [Programación de JavaScript en el lado del servidor de Azure Cosmos DB](programming.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>¿Cómo se pueden insertar documentos de forma masiva en Cosmos DB?
Hay dos maneras de insertar documentos de forma masiva en Azure Cosmos DB:

* La herramienta de migración de datos, como se describe en [Herramienta de migración de bases de datos de Azure Cosmos DB](import-data.md).
* Procedimientos almacenados, como se describe en [Programación de JavaScript en el lado del servidor de Azure Cosmos DB](programming.md).

### <a name="does-the-documentdb-api-support-resource-link-caching"></a>¿La API de DocumentDB admite el almacenamiento en caché de vínculos de recursos?
Sí. Como Azure Cosmos DB es un servicio RESTful, los vínculos de recursos son inmutables y se pueden almacenar en caché. Los clientes de la API de DocumentDB pueden especificar un encabezado "If-None-Match" para lecturas en cualquier documento o colección de tipo recurso y actualizar sus copias locales cuando la versión del servidor cambie.

### <a name="is-a-local-instance-of-documentdb-api-available"></a>¿Hay disponible una instancia local de la API de DocumentDB?
Sí. El [Emulador de Azure Cosmos DB](local-emulator.md) proporciona una emulación de gran fidelidad del servicio Cosmos DB. Admite una funcionalidad que es idéntica a la de Azure Cosmos DB, incluida la compatibilidad con la creación y la consulta de documentos JSON, el aprovisionamiento y el escalado de colecciones y la ejecución de desencadenadores y procedimientos almacenados. Puede desarrollar y probar aplicaciones mediante el Emulador de Azure Cosmos DB e implementarlas en Azure a escala global con tan solo un cambio de configuración en el punto de conexión de la conexión de Azure Cosmos DB.

## <a name="develop-against-the-api-for-mongodb"></a>Desarrollo con la API de MongoDB
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>¿Qué es API de MongoDB de Azure Cosmos DB?
La API de MongoDB de Azure Cosmos DB es una capa de compatibilidad que permite a las aplicaciones comunicarse de manera sencilla y transparente con el motor de base de datos nativo de Azure Cosmos DB mediante los controladores y las API de Apache MongoDB admitidas por la comunidad. Los desarrolladores ahora pueden usar cadenas de herramienta de MongoDB existentes y las aptitudes para crear aplicaciones que aprovechan las ventajas de Azure Cosmos DB. Los desarrolladores pueden beneficiarse de la funcionalidad única de Azure Cosmos DB, que incluye indexación automática, mantenimiento de copia de seguridad, contratos de nivel de servicio con respaldo financiero, etc.

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>¿Cómo puedo conectarme a la base de datos de mi API de MongoDB?
La forma más rápida de conectarse a la API de MongoDB de Azure Cosmos DB es a través de [Azure Portal](https://portal.azure.com). Vaya a la cuenta y luego, en el menú de navegación izquierdo, haga clic en **Inicio rápido**. Inicio rápido es la mejor manera de obtener fragmentos de código para conectarse a la base de datos. 

Azure Cosmos DB aplica estándares y requisitos de seguridad estrictos. Las cuentas de Azure Cosmos DB requieren autenticación y comunicación segura a través de SSL, por lo que debe usar TLS v1.2.

Para más información, vea [Conectarse a la base de datos de la API de MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>¿Existen otros códigos de error para una base de datos de la API de MongoDB?
Además de los códigos de error comunes de MongoDB, la API de MongoDB tiene sus propios códigos de error:


| Error               | Código  | Descripción  | Solución  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | El número total de unidades de solicitud consumido ha superado la tasa de unidades de solicitud aprovisionadas de la colección y se ha limitado. | Considere la posibilidad de escalar el rendimiento de la colección desde Azure Portal o vuelva a intentarlo. |
| ExceededMemoryLimit | 16501 | Como se trata de un servicio de varios inquilinos, la operación ha superado la asignación de memoria del cliente. | Reduzca el ámbito de la operación a través de criterios de consulta más restrictivos o póngase en contacto con soporte técnico desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Ejemplo: *&nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api"></a>Desarrollo con Table API

### <a name="how-can-i-use-the-table-api-offering"></a>¿Cómo se puede usar la oferta de Table API? 
Table API de Azure Cosmos DB está disponible en [Azure Portal][azure-portal]. En primer lugar, debe registrarse para obtener una suscripción de Azure. Una vez registrado, puede agregar una cuenta de Table API de Azure Cosmos DB a la suscripción de Azure y luego agregar tablas a la cuenta. 

Puede encontrar los lenguajes admitidos y las guías de inicio rápido asociadas en la [Introducción a Table API de Azure Cosmos](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>¿Se necesita un nuevo SDK para usar Table API? 
No, los SDK de almacenamiento existentes deberían seguir funcionando. Sin embargo, se recomienda obtener siempre los SDK más recientes para lograr la mejor compatibilidad y, en muchos casos, un rendimiento superior. Vea la lista de lenguajes disponibles en la [Introducción a Table API de Azure Cosmos DB](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>¿En qué se diferencia Table API del comportamiento de Azure Table Storage?
Hay algunas diferencias de comportamiento que los usuarios acostumbrados a Azure Table Storage que deseen crear tablas con Table API de Azure Cosmos DB deben tener en cuenta:

* Table API de Azure Cosmos DB utiliza un modelo de capacidad reservada con el fin de asegurar un rendimiento garantizado, pero esto significa que se paga por la capacidad en cuanto se crea la tabla, incluso aunque no se esté usando. Con Azure Table Storage, solo se paga por la capacidad que realmente se utilice. Esto ayuda a explicar por qué Table API puede ofrecer un Acuerdo de Nivel de Servicio con lecturas de 10 ms y escrituras de 15 ms en el percentil 99 mientras que Azure Table Storage ofrece un Acuerdo de Nivel de Servicio de 10 segundos. Pero, como consecuencia, con las tablas de Table API, incluso las tablas vacías sin ninguna solicitud cuestan dinero, con el fin de garantizar que la capacidad está disponible para atender las solicitudes que les llegan en el Acuerdo de Nivel de Servicio ofrecido por Azure Cosmos DB.
* Los resultados de las consultas devueltos por Table API no se ordenan por la clave de fila o por la clave de partición, como aparecen en Azure Table Storage.
* Las claves de fila solo pueden ser de hasta 255 bytes
* Las llamadas a CreateIfNotExists están limitadas por una limitación de administración fija e independiente de otras operaciones de tabla que están cubiertas por RU. Esto significa que los que hacen grandes cantidades de CreateIfNotExists son limitados y no podrán hacer nada porque el límite no procede de sus RU.
* CORS no se admite actualmente
* En los nombres de tabla de Azure Table Storage no se distinguen mayúsculas de minúsculas, a diferencia de lo que ocurre en Table API de Azure Cosmos DB

En cuanto a la API de REST, hay una serie de opciones de puntos de conexión o consulta que no son compatibles con Table API de Azure Cosmos DB:
| Métodos de REST | Opción de punto de conexión o consulta de REST | Direcciones URL de documento | Explicación |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /?restype=service@comp=properties| [Establecer propiedades de Table service](https://docs.microsoft.com/en-us/rest/api/storageservices/set-table-service-properties) y [Obtener propiedades de Table service](https://docs.microsoft.com/en-us/rest/api/storageservices/get-table-service-properties) | Este punto de conexión se usa para establecer reglas de CORS, de configuración del análisis de almacenamiento y de configuración del registro. CORS no se admite actualmente y el análisis y el registro se tratan de forma diferente en Azure Cosmos DB y en Azure Storage Tables |
| OPCIONES | /<nombre-recurso-tabla> | [Solicitud de tabla preparatoria CORS](https://docs.microsoft.com/en-us/rest/api/storageservices/preflight-table-request) | Esto forma parte de CORS que Azure Cosmos DB no admite actualmente. |
| GET | /?restype=service@comp=stats | [Obtener estadísticas de Table service](https://docs.microsoft.com/en-us/rest/api/storageservices/get-table-service-stats) | Proporciona información sobre la rapidez con que los datos se están replicando datos entre los elementos principales y los secundarios. Esto no es necesario en Cosmos DB ya que la replicación es parte de las escrituras. |
| GET, PUT | /mytable?comp=acl | [Obtener tabla ACL](https://docs.microsoft.com/en-us/rest/api/storageservices/get-table-acl) y [Definir ACL de tabla](https://docs.microsoft.com/en-us/rest/api/storageservices/set-table-acl) | Así se obtienen y establecen las directivas de acceso almacenadas que se usan para administrar las firmas de acceso compartido (SAS). Aunque SAS se admite, las firmas se establecen y administran de manera diferente. |

Además, Table API de Azure Cosmos DB solo es compatible con el formato JSON, no con ATOM.

Aunque Azure Cosmos DB admite firmas de acceso compartido (SAS), hay determinadas directivas que no admite, en concreto las relacionadas con las operaciones de administración como el derecho para crear nuevas tablas.

Para el SDK para .NET en concreto, hay algunas clases y métodos que Azure Cosmos DB no admite actualmente.

| Clase | Método no admitido |
|-------|-------- |
| CloudTableClient | \*ServiceProperties* |
|                  | \*ServiceStats* |
| CloudTable | SetPermissions* |
|            | GetPermissions* |
| TableServiceContext | * (esta clase está en desuso en realidad) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Si alguna de estas diferencias supone un problema para el proyecto, póngase en contacto con [ askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) y háganoslo saber.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>¿Cómo se pueden hacer comentarios sobre el SDK o los errores?
Puede proporcionar comentarios de alguna de las siguientes formas:

* [UserVoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [Foro de MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>¿Qué cadena de conexión hay que usar para conectarse a Table API?
La cadena de conexión es:
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.table.cosmosdb.azure.com
```
Puede obtener la cadena de conexión en la página de cadenas de conexión de Azure Portal. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>¿Cómo se invalidan los valores de configuración de las opciones de solicitud del SDK para .NET para Table API?
Para información sobre las opciones de configuración, vea [Funcionalidades de Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Algunas opciones de configuración se tratan en el método CreateCloudTableClient y otros a través del archivo app.config en la sección appSettings en la aplicación cliente.

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>¿Existen cambios para los clientes que usan los SDK existentes de Azure Table Storage?
Ninguno. No hay ningún cambio para los clientes nuevos o existentes que usan los SDK existentes de Azure Table Storage. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>¿Cómo se ven los datos de tablas que se almacenan en Azure Cosmos DB para usarlos con Table API? 
Puede usar Azure Portal para examinar los datos. También puede usar el código de Table API o las herramientas que se mencionan en la respuesta siguiente. 

### <a name="which-tools-work-with-the-table-api"></a>¿Qué herramientas funcionarán con Table API? 
Puede usar el [Explorador de Azure Storage](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Las herramientas que tengan suficiente flexibilidad como para aceptar una cadena de conexión en el formato especificado antes son compatibles con la nueva Table API. En la página [Herramientas de cliente de Azure Storage](../storage/common/storage-explorers.md) se ofrece una lista de herramientas de tabla. 

### <a name="do-powershell-or-azure-cli-work-with-the-table-api"></a>¿Funcionan PowerShell y la CLI de Azure con la nueva Table API?
Es compatible con [PowerShell](table-powershell.md). La CLI de Azure no está disponible actualmente.

### <a name="is-the-concurrency-on-operations-controlled"></a>¿Se controla la simultaneidad en las operaciones?
Sí, se ofrece simultaneidad optimista a través del mecanismo de ETag. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>¿Es el modelo de consulta de OData compatible con entidades? 
Sí. Table API admite la consulta OData y la consulta LINQ. 

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>¿Puedo conectarme en paralelo a Azure Table Storage y a Table API de Azure Cosmos DB en la misma aplicación? 
Sí, puede conectarse mediante la creación de dos instancias independientes de CloudTableClient, donde cada una apunte a su propio URI a través de la cadena de conexión.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>¿Cómo se puede migrar la aplicación de Azure Table Storage existente a esta nueva oferta?
Tanto [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy) como la [Herramienta de migración de datos de Azure Cosmos DB](import-data.md) se admiten.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>¿Cómo se realiza la expansión del tamaño de almacenamiento para este servicio si, por ejemplo, se empieza por *n* gigabytes de datos y con el tiempo los datos crecen a 1 TB? 
Azure Cosmos DB está diseñado para ofrecer almacenamiento ilimitado mediante el uso de escalado horizontal. Nuestro servicio puede supervisar y aumentar el almacenamiento de forma eficaz. 

### <a name="how-do-i-monitor-the-table-api-offering"></a>¿Cómo se supervisa la oferta de Table API?
Puede usar el panel **Métricas** de Table API para supervisar las solicitudes y el uso del almacenamiento. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>¿Cómo se puede calcular el rendimiento que se necesita?
Puede usar la calculadora de capacidad para calcular el valor de TableThroughput necesario para las operaciones. Para más información, vea [Estimate Request Units and Data Storage](https://www.documentdb.com/capacityplanner) (Cálculo de unidades de solicitud y almacenamiento de datos). Normalmente, puede representar la entidad como JSON e indicar las cantidades para las operaciones. 

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>¿Se puede usar el nuevo SDK de Table API localmente con el emulador?
De momento, no.

### <a name="can-my-existing-application-work-with-the-table-api"></a>¿Puede una aplicación existente funcionar con Table API? 
Sí, se admiten las mismas API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-do-not-want-to-use-the-table-api-features"></a>¿Es necesario migrar las aplicaciones existentes de Azure Table Storage al nuevo SDK si no se quieren usar las características de Table API?
No, se pueden crear y usar recursos existentes de Azure Table Storage sin interrupciones de ningún tipo. Pero, si no usa la nueva Table API, no puede aprovechar el índice automático, la opción de coherencia adicional ni la distribución global. 

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-multiple-regions-of-azure"></a>¿Cómo se agrega la replicación de los datos de Table API en varias regiones de Azure?
Puede usar la [configuración de replicación global](tutorial-global-distribution-documentdb.md#portal) del portal de Azure Cosmos DB para agregar regiones que sean adecuadas para la aplicación. Para desarrollar una aplicación distribuida globalmente, debe agregar también la aplicación con la información de PreferredLocation establecida en la región local para proporcionar baja latencia de lectura. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>¿Cómo se puede cambiar la región principal de escritura para la cuenta de Table API?
Puede usar el panel del portal de replicación global de Azure Cosmos DB para agregar una región y luego conmutar por error a la región necesaria. Para obtener instrucciones, vea [Developing with multi-region Azure Cosmos DB accounts](regional-failover.md) (Desarrollo con cuentas de Azure Cosmos DB de varias regiones). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>¿Cómo se configuran las regiones de lectura preferidas con una baja latencia al distribuir los datos? 
Con el fin de leer desde la ubicación local, use la clave PreferredLocation en el archivo app.config. En aplicaciones existentes, Table API produce un error si se establece LocationMode. Quite el código, ya que Table API recoge esta información del archivo app.config. Para más información, vea [Funcionalidades de Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>¿Cómo debo plantearme los niveles de coherencia en Table API? 
Azure Cosmos DB ofrece contrapartidas bien fundamentadas entre coherencia, disponibilidad y latencia. Azure Cosmos DB ofrece cinco niveles de coherencia para desarrolladores de Table API, por lo que puede elegir el modelo adecuado en el nivel de tabla y realizar solicitudes individuales al tiempo que se consultan datos. Al conectarse, el cliente puede especificar un nivel de coherencia. Puede cambiar el nivel a través del argumento consistencyLevel de CreateCloudTableClient. 

Table API ofrece lecturas de baja latencia con la opción de lectura de sus propias escrituras con coherencia de obsolescencia limitada como valor predeterminado. Para más información, consulte [Niveles de coherencia](consistency-levels.md). 

De forma predeterminada, Azure Table Storage ofrece una coherencia alta dentro de una región y una posible coherencia en las ubicaciones secundarias. 

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>¿Table API de Azure Cosmos DB ofrece más niveles de coherencia que Azure Table Storage?
Sí, para obtener información sobre cómo beneficiarse de la naturaleza distribuida de Azure Cosmos DB, vea [Niveles de coherencia](consistency-levels.md). Dado que se ofrecen garantías para los niveles de coherencia, puede usarlos con toda confianza. Para más información, vea [Funcionalidades de Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Cuando se habilita la distribución global, ¿cuánto se tarda en replicar los datos?
En cuestión de milisegundos, Azure Cosmos DB confirma los datos de manera duradera en la región local y los inserta inmediatamente en otras regiones. Esta replicación solo depende del tiempo de ida y vuelta (RTT) del centro de datos. Para más información sobre la funcionalidad de distribución global de Azure Cosmos DB, vea [Azure Cosmos DB: A globally distributed database service on Azure](distribute-data-globally.md) (Cómo distribuir datos globalmente con Azure Cosmos DB).

### <a name="can-the-read-request-consistency-level-be-changed"></a>¿Se puede cambiar el nivel de coherencia de la solicitud de lectura?
Con Azure Cosmos DB, puede establecer el nivel de coherencia en el nivel de contenedor (en la tabla). Mediante el SDK para .NET, puede cambiar el nivel especificando el valor de la clave TableConsistencyLevel en el archivo app.config. Los valores son: Alta, Obsolescencia limitada, Sesión, Prefijo coherente y Posible. Para más información, vea [Niveles de coherencia de datos optimizables en Azure Cosmos DB](consistency-levels.md). La idea clave es que no se puede establecer el nivel de coherencia de la solicitud por encima del establecido para la tabla. Por ejemplo, no puede establecer el nivel de coherencia de la tabla en Posible y el nivel de coherencia de la solicitud en Alta. 

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>¿Cómo trata Table API la conmutación por error en caso de que una región deje de funcionar? 
Table API aprovecha la plataforma distribuida globalmente de Azure Cosmos DB. Para garantizar que la aplicación puede tolerar un tiempo de inactividad del centro de datos, habilite otra región adicional para la cuenta como mínimo en [Developing with multi-region Azure Cosmos DB accounts](regional-failover.md) (Desarrollo con cuentas de Azure Cosmos DB de varias regiones) del portal de Cosmos DB. Puede establecer la prioridad de la región mediante [Developing with multi-region Azure Cosmos DB accounts](regional-failover.md) (Desarrollo con cuentas de Azure Cosmos DB de varias regiones) del portal. 

Puede agregar todas las regiones que quiera para la cuenta y controlar adónde puede conmutar por error especificando una prioridad. Por supuesto, para usar la base de datos, debe especificar también una aplicación allí. Al hacerlo, los clientes no experimentarán tiempo de inactividad. El [SDK cliente para .NET más reciente](table-sdk-dotnet.md) tiene hospedaje automático, a diferencia de los otros SDK. Es decir, puede detectar la región que no funciona y conmutar por error automáticamente a la nueva región.

### <a name="is-the-table-api-enabled-for-backups"></a>¿Está habilitada Table API para copias de seguridad?
Sí, Table API aprovecha la plataforma de Azure Cosmos DB para las copias de seguridad. Se realizan copias de seguridad automáticamente. Para más información, vea [Copias de seguridad y restauración automáticas en línea con Azure Cosmos DB](online-backup-and-restore.md).

 
### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>¿Table API indexa de forma predeterminada todos los atributos de una entidad?
Sí, todos los atributos de una entidad se indexan de forma predeterminada. Para más información, vea [Directivas de indexación de Azure Cosmos DB](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>¿Significa esto que no hay que crear varios índices para responder a las consultas? 
Sí, Table API de Azure Cosmos DB ofrece indexación automática de todos los atributos sin ninguna definición de esquema. Esta automatización libera al desarrollador para que se centre en la aplicación y no en la creación y administración de índices. Para más información, vea [Directivas de indexación de Azure Cosmos DB](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>¿Se puede cambiar la directiva de indexación?
Sí, puede cambiar la directiva de indexación especificando la definición del índice. Para más información, vea [Funcionalidades de Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Esta configuración se tiene que codificar adecuadamente e incluir secuencias de escape. 

para los SDK que no son para .NET, la directiva de indexación solo se puede establecer en el portal en el **Explorador de datos**; vaya a la tabla concreta que desee cambiar y, a continuación, vaya a **Escala y configuración**-> Directiva de indexación, realice el cambio deseado y, a continuación, haga clic en **Guardar**.

Desde el SDK para .NET pueden enviarse en el archivo app.config:
```
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        } 
      ]
    }
  ],
  "excludedPaths": 
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB como plataforma parece tener gran cantidad de funcionalidades, como ordenación, agregados, jerarquía y otras funciones. ¿Se van a agregar estas funcionalidades a Table API? 
Table API proporciona la misma funcionalidad de consulta que Azure Table Storage. Azure Cosmos DB también admite ordenación, agregados, consulta geoespacial, jerarquía y una amplia variedad de funciones integradas. Una futura actualización de servicio de Table API ofrecerá funcionalidades adicionales. Para más información, vea, [Consultas SQL para la API de DocumentDB de Azure Cosmos DB](../documentdb/documentdb-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>¿Cuándo se debe cambiar TableThroughput por Table API?
Debe cambiar el valor de TableThroughput cuando se cumpla alguna de las condiciones siguientes:
* Va a realizar una extracción, transformación y carga (ETL) de datos o quiere cargar una gran cantidad de datos en un período corto de tiempo. 
* Necesita más rendimiento desde el contenedor en el back-end. Por ejemplo, verá que el rendimiento de uso es mayor que el rendimiento aprovisionado, lo que se traduce en limitaciones. Para más información, vea [Configuración del rendimiento para contenedores de Azure Cosmos DB](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>¿Se puede escalar o reducir verticalmente el rendimiento de mi tabla de Table API? 
Sí, puede usar el panel de escala del portal de Azure Cosmos DB para ello. Para más información, vea [Establecimiento del rendimiento](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>¿Existe un TableThroughput predeterminado que se establezca para tablas recién aprovisionadas?
Sí, si no invalida el TableThroughput a través de app.config y no usa un contenedor creado previamente en Azure Cosmos DB, el servicio crea una tabla con un rendimiento de 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>¿Hay algún cambio de precio para los clientes actuales del servicio Azure Table Storage?
Ninguno. No hay ningún cambio de precio para los clientes existentes de Azure Table Storage. 

### <a name="how-is-the-price-calculated-for-the-table-api"></a>¿Cómo se calcula el precio de Table API? 
El precio depende del TableThroughput asignado. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-offering"></a>¿Cómo se tratan las limitaciones en las tablas de la oferta de Table API? 
Si la tasa de solicitudes supera la capacidad del rendimiento aprovisionado para el contenedor subyacente, obtendrá un error y el SDK reintentará la llamada aplicando la directiva de reintentos.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>¿Por qué hay que elegir un rendimiento además de PartitionKey y RowKey para aprovechar las ventajas de la oferta de Table API de Azure Cosmos DB?
Si no se especifica un rendimiento predeterminado para el contenedor en el archivo app.config o a través del portal, Azure Cosmos DB establecerá uno. 

Azure Cosmos DB ofrece garantías de rendimiento y latencia con límites superiores por operación. Esta garantía es posible cuando un motor puede aplicar la regulación de las operaciones del inquilino. Al establecer TableThroughput se asegura de obtener el rendimiento y la latencia garantizados, ya que la plataforma reserva esta capacidad y garantiza el éxito operativo. 

Si usa la especificación de rendimiento, puede cambiarla de forma elástica para aprovechar la estacionalidad de la aplicación, satisfacer las necesidades de rendimiento y ahorrar costos.

### <a name="azure-table-storage-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Azure Table Storage me ha resultado muy económico ya que solo pago por almacenar los datos y rara vez hago consultas. Parece que la oferta de Table API de Azure Cosmos DB me cobra aunque no haya realizado ni una sola transacción ni haya almacenado nada. ¿Puede explicarse?

Azure Cosmos DB se ha diseñado para ser un sistema basado en contratos de nivel de servicio distribuido globalmente con garantías de disponibilidad, latencia y rendimiento. A diferencia de otros sistemas, cuando se reserva rendimiento en Azure Cosmos DB está garantizado. Azure Cosmos DB ofrece funcionalidades adicionales que los clientes han solicitado, como índices secundarios y distribución global.  

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nunca aparece una notificación de “cuota completa" (que indica que una partición está completa) cuando se ingieren datos en Azure Table Storage. Con Table API, recibo este mensaje. ¿Esta oferta me limita y me obliga a cambiar mi aplicación actual?

Azure Cosmos DB es un sistema basado en contratos de nivel de servicio que ofrece escalado ilimitado con garantías de latencia, rendimiento, disponibilidad y coherencia. Para obtener el rendimiento premium garantizado, asegúrese de que el tamaño de los datos y el índice son administrables y escalables. El límite de 10 GB en el número de entidades o elementos por clave de partición tiene por objeto asegurar un excelente rendimiento de consultas y búsquedas. Para asegurarse de que la aplicación se escala correctamente incluso con Azure Storage le pedimos que *no* cree una partición muy activa almacenando toda la información en una partición y realizando consultas en ella. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Entonces, ¿PartitionKey y RowKey siguen siendo necesarios con Table API? 
Sí. Dado que el área expuesta de Table API es similar al del SDK de Azure Table Storage, la clave de partición supone una forma excelente de distribuir los datos. La clave de fila es única en esa partición. La clave de fila tiene que estar presente y no puede ser nula como en el SDK estándar. La longitud de RowKey es 255 bytes y la de PartitionKey es 1 KB. 

### <a name="what-are-the-error-messages-for-the-table-api"></a>¿Cuáles son los mensajes de error de Table API?
Azure Table Storage y Table API de Azure Cosmos DB usan el mismo SDK, por lo que la mayoría de los errores coincidirán.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>¿Por qué me veo limitado cuando trato de crear muchas tablas seguidas en Table API?
Azure Cosmos DB es un sistema basado en Acuerdos de Nivel de Servicio que ofrece garantías de latencia, rendimiento, disponibilidad y coherencia. Puesto que es un sistema aprovisionado, reserva recursos para garantizar estos requisitos. Se detecta y limita la frecuencia rápida de creación de tablas. Se recomienda que examine la tasa de creación de tablas y la reduzca a menos de cinco por minuto. Recuerde que Table API es un sistema aprovisionado. Se empieza a pagar por él desde el momento en que se aprovisiona. 

## <a name="develop-against-the-graph-api-preview"></a>Desarrollo con API Graph (versión preliminar)
### <a name="how-can-i-apply-the-functionality-of-graph-api-preview-to-azure-cosmos-db"></a>¿Cómo se puede aplicar la funcionalidad de API Graph (versión preliminar) a Azure Cosmos DB?
Puede usar una biblioteca de extensiones para aplicar la funcionalidad de API Graph (versión preliminar). Esta biblioteca se denomina Microsoft Azure Graphs y está disponible en NuGet. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>Parece que se admite el lenguaje de recorrido de gráficos Gremlin. ¿Tienen previsto agregar otras formas de consulta?
Sí, tenemos previsto agregar otros mecanismos de consulta en el futuro. 

### <a name="how-can-i-use-the-new-graph-api-preview-offering"></a>¿Cómo se puede usar la nueva oferta de API Graph (versión preliminar)? 
Para empezar a trabajar, complete el artículo sobre inicio rápido de [API Graph](../cosmos-db/create-graph-dotnet.md).

<a id="cassandra"></a> 
## <a name="develop-with-the-apache-cassandra-api-preview"></a>Desarrollo con la API de Apache Cassandra (versión preliminar)

### <a name="what-is-the-protocol-version-supported-in-the-preview-do-you-plan-to-support-other-protocols"></a>¿Cuál es la versión de protocolo admitida en la versión preliminar? ¿Tiene pensado admitir otros protocolos?
La API de Apache Cassandra de Azure Cosmos DB actualmente admite CQL versión 4. Para proporcionar comentarios sobre la compatibilidad con modelos adicionales, cree solicitudes que utilizan [comentarios de uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db) o envíe un correo electrónico a [askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="is-it-possible-to-create-multiple-tables-with-apache-cassandra-api-of-azure-cosmos-db"></a>¿Es posible crear varias tablas con la API de Apache Cassandra de Azure Cosmos DB?
Azure Cosmos DB es un sistema regulado por recursos tanto para las actividades de planeamiento de control como de datos. Los contenedores como las colecciones y las tablas son entidades del entorno de tiempo de ejecución que se han aprovisionado para una capacidad de rendimiento determinado. La creación de estos contenedores en sucesión rápida no es una actividad esperada y puede estar limitada. Si tiene pruebas que quitan o crean tablas inmediatamente, intente separarlas o volver a usar las tablas existentes después de quitar los datos.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>¿Cuál es el número máximo de tablas que se pueden crear?
No hay ningún límite físico en el número de tablas que se pueden crear. Envíe un correo electrónico a [askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) si tiene un gran número de tablas (el tamaño constante total superior a 10 TB) que deban crearse por encima de lo habitual de decenas o cientos. 

### <a name="what-is-the-maximum-number-of-keyspaces-that-can-be-created"></a>¿Cuál es el número máximo de espacios de claves que se pueden crear? 
No hay ningún límite físico en el número de espacios de claves que pueden crearse, ya que son contenedores de metadatos. Envíe un correo electrónico a [askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) si tiene que crear un número muy elevado de espacios de claves. 

### <a name="is-it-possible-to-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>¿Es posible tener una gran cantidad de datos tras comenzar con una tabla normal? 
La capacidad de almacenamiento se administra automáticamente y aumenta a medida que inserta más datos en ella. Así se garantiza que tiene suficiente capacidad para admitir esta cantidad de datos, por lo que puede importar con confianza tantos datos como sea necesario.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>¿Es posible proporcionar la configuración del archivo yaml para configurar el comportamiento de Apache Casssandra API de Azure Cosmos DB?
La API de Apache Cassandra de Azure Cosmos DB es un servicio de plataforma. Proporciona compatibilidad de nivel de protocolo para la ejecución de operaciones. Oculta la complejidad de la administración, supervisión y configuración. Como desarrollador y usuario, no es necesario preocuparse sobre la disponibilidad, los marcadores de exclusión, la memoria caché de claves, la memoria caché de filas, los filtros de Bloom y muchas otras opciones. La API de Apache Cassandra de Azure Cosmos DB se centra en proporcionar el rendimiento de lectura y escritura que necesite sin sobrecarga.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>¿Admitirá la API de Apache Cassandra de Azure Cosmos DB comandos de estado de nodo, estado de clústeres o adición de nodos?
La API de Apache Cassandra es un servicio de plataforma que facilita el planeamiento de la capacidad y la respuesta a las exigencias de elasticidad en el rendimiento y el almacenamiento. Con Azure Cosmos DB, se aprovisiona el rendimiento que se necesita. A continuación, puede escalar verticalmente aumentando o disminuyendo lo que necesite a lo largo del día sin preocuparse de agregar o eliminar nodos ni de administrarlos. Esto también significa que no es necesario utilizar una herramienta de administración de clústeres o nodos. 

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation"></a>¿Qué ocurre con respecto a los distintos valores de configuración para la creación de espacios de claves?
Azure Cosmos DB proporciona una distribución global directamente, debido a la baja latencia y la disponibilidad. No es necesario configurar réplicas. Todas las escrituras se confirman siempre mediante cuórum duradero en cualquier región donde escriba o distribuya los datos mientras proporciona garantías de rendimiento. Por este motivo, los valores de configuración como `Simple Strategy` y `Network Topology Strategy` se pasan por alto. 

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filters-caching-read-repair-changes-gcgrace-and-compression-memtableflushperiod"></a>¿Qué ocurre con respecto a los diversos valores de configuración para los metadatos de tablas como los filtros de Bloom, el almacenamiento en caché, los cambios de reparación de las lecturas, gc_grace y el valor memtable_flush_period de la compresión?
Azure Cosmos DB proporciona el rendimiento para las lecturas y escrituras, y la capacidad de proceso sin necesidad de variar ninguno de los valores de configuración y correr el riesgo de manipularlos accidentalmente. Así se elimina todo un conjunto de valores de configuración que son difíciles de administrar y mantener.

### <a name="what-is-the-default-consistency-of-an-apache-cassandra-api-account"></a>¿Qué es la coherencia predeterminada de una cuenta de la API de Apache Cassandra?
Azure Cosmos DB admite cinco niveles de coherencia de fábrica, como se describe en [Niveles de coherencia de datos optimizables en Azure Cosmos DB](consistency-levels.md). La API de Apache Cassandra actualmente solo admite la coherencia de la sesión. 

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>¿Se admite el período de vida (TTL) en las tablas de Cassandra? 
Sí, se admite para las tablas. 

### <a name="what-is-the-default-throughput-of-a-table-when-created-through-cql-what-if-i-need-to-change-it"></a>¿Cuál es el rendimiento predeterminado de una tabla cuando se crea mediante CQL? ¿Qué ocurre si deseo cambiarlo?
Azure Cosmos DB utiliza unidades de solicitud por segundo (RU/s) como la moneda para proporcionar el rendimiento. Las tablas creadas a través de CQL tienen 400 RU/s. aprovisionados de forma predeterminada. Puede cambiar el rendimiento de una tabla en Azure Portal y escalar hasta 10 000 RU. Si tiene más de 10 000 RU/s durante la versión preliminar, cree la tabla con Azure Portal. O bien, si necesita esta funcionalidad para CQL, conecte con nosotros en [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

###  <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-what-should-i-monitor"></a>¿Es posible supervisar los parámetros del sistema operativo, gc, el estado de las réplicas y el estado de los nodos? ¿Qué debo supervisar?
Azure Cosmos DB es un servicio de plataforma que le ayuda a aumentar la productividad y no preocuparse de la administración y supervisión de la infraestructura. La métrica que desea supervisar es el rendimiento, que está disponible en la pestaña Rendimiento de la página Métricas de Azure Cosmos DB en Azure Portal. Estos gráficos indican si va a sufrir limitaciones y debe aumentar o disminuir el rendimiento. Para obtener más información, consulte [Supervisión de Azure Cosmos DB](monitor-accounts.md) y [Supervisión y depuración con métricas de Azure Cosmos DB](use-metrics.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>¿Los SDK de cliente pueden funcionar con la API de Apache Cassandra de Azure Cosmos DB?
La API de Apache Cassandra se ha probado con los controladores de C#, Java, Python y Node Apache Cassandra. Si tiene otros controladores que utiliza y necesita asistencia, envíenos un correo electrónico a [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="how-do-i-get-ru-capacity-planning-for-the-apache-cassandra-api"></a>¿Cómo consigo planear la capacidad de RU de la API de Apache Cassandra?
Azure Cosmos DB proporciona una [herramienta de planeamiento de capacidad](https://www.documentdb.com/capacityplanner). Proporcione una representación JSON de la entidad y la herramienta obtendrá las RU necesarias para las operaciones de lectura/escritura. Para las consultas y otras operaciones, los controladores existentes devuelven los metadatos que tiene la información de consumo de RU. Puede utilizar esa información para llevar a cabo un plan preciso. 

### <a name="is-composite-primary-key-supported"></a>¿Se admite la clave principal compuesta?
Sí, puede usar la sintaxis normal para crear una clave de partición compuesta. 

### <a name="can-i-use-sstable-loader-for-data-loading"></a>¿Puedo usar el cargador sstable para cargar los datos?
No, actualmente no se admite. 

### <a name="can-an-on-premise-cassandra-cluster-be-paired-with-the-azure-cosmos-db-apache-cassandra-api"></a>¿Un clúster Cassandra local se puede emparejar con la API de Apache Cassandra de Azure Cosmos DB?
Por favor, envíenos un correo electrónico en [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) y describa sus escenarios. El servicio tiene una experiencia optimizada para el entorno en la nube sin la sobrecarga de operaciones.

### <a name="do-i-need-to-take-snapshots-and-incremental-backups"></a>¿Es necesario tomar instantáneas y copias de seguridad incrementales? 
Azure Cosmos DB proporciona dos copias de seguridad completas gratis realizadas cada cuatro horas, entre todas las API. Así se garantiza que no es necesario crear una programación de copia de seguridad. Si desea modificar la retención y la frecuencia de las copias de seguridad, envíenos un correo electrónico a [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="where-can-i-provide-feedback-if-a-feature-in-the-regular-cassandra-api-does-not-work"></a>¿Dónde puedo proporcionar comentarios si una característica de la API de Cassandra normal no funciona?
Envíe un correo electrónico a [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="where-can-i-suggest-new-features-for-the-apache-cassandra-api"></a>¿Dónde puedo sugerir nuevas características para la API de Apache Cassandra?
Envíe un correo electrónico a [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com). Si lo desea, puede proporcionar un comentario en el sitio de [comentarios por voz](https://feedback.azure.com/forums/263030-azure-cosmos-db). 

### <a name="why-do-i-need-to-choose-a-throughput-level"></a>¿Por qué necesito elegir un nivel de rendimiento?
Azure Cosmos DB establece el rendimiento predeterminado para el contenedor en función de dónde crear la tabla, desde el portal o desde CQL. 

Azure Cosmos DB ofrece garantías de rendimiento y latencia con límites superiores en las operaciones. Esta garantía es posible porque un motor puede aplicar la regulación de las operaciones del inquilino. Al establecer el rendimiento, se asegura de obtener el rendimiento y la latencia garantizados, ya que la plataforma reserva esta capacidad y garantiza el éxito operativo. Para más información, vea [Unidades de solicitud en Azure Cosmos DB](request-units.md).

Si usa la especificación de rendimiento, puede cambiarla de forma elástica para aprovechar la estacionalidad de la aplicación, satisfacer las necesidades de rendimiento y ahorrar costos.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-regular-cassandra-with-the-azure-cosmos-db-cassandra-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nunca aparece una notificación de “cuota completa" (que indica que una partición está completa) cuando se ingieren datos en Cassandra en modo normal. Con la API de Cassandra de Azure Cosmos DB, aparece este mensaje. ¿Esta oferta me limita y me obliga a cambiar mi aplicación actual?

Azure Cosmos DB es un sistema basado en contratos de nivel de servicio que ofrece escalado ilimitado con garantías de latencia, rendimiento, disponibilidad y coherencia. Para obtener el rendimiento premium garantizado, asegúrese de que el tamaño de los datos y el índice son administrables y escalables. El límite de 10 GB en el número de entidades o elementos por clave de partición tiene por objeto asegurar que Azure Cosmos DB proporciona un excelente rendimiento de consultas y búsquedas. Para asegurarse de que la aplicación se escala correctamente incluso con Azure Storage le pedimos que *no* cree una partición muy activa almacenando toda la información en una partición y realizando consultas en ella. 

### <a name="how-does-the-cassandra-api-handle-failover-if-a-region-goes-down"></a>¿Cómo trata la API de Cassandra la conmutación por error en caso de que una región deje de funcionar? 
La API de Cassandra de Azure Cosmos DB adopta la plataforma distribuida globalmente de Azure Cosmos DB. Para garantizar que la aplicación puede tolerar un tiempo de inactividad del centro de datos, habilite al menos una región adicional para la cuenta en el portal de Azure Cosmos DB. Puede establecer la prioridad de la región mediante el portal, según se describe en [Developing with multi-region Azure Cosmos DB accounts](regional-failover.md) (Desarrollo con cuentas de Azure Cosmos DB de varias regiones). 

Puede agregar todas las regiones que quiera para la cuenta y controlar adónde puede esta conmutar por error especificando una prioridad. Por supuesto, para usar la base de datos, también debe especificar también una aplicación allí. Al hacerlo, los clientes no experimentarán tiempo de inactividad.  

### <a name="is-the-apache-cassandra-api-enabled-for-backups"></a>¿Está habilitada la API de Apache Cassandra para las copias de seguridad?
Sí, la API de Cassandra de Azure Cosmos DB tiene las mismas copias de seguridad que Azure Cosmos DB. Se realizan copias de seguridad automáticamente. Para más información, vea [Copias de seguridad y restauración automáticas en línea con Azure Cosmos DB](online-backup-and-restore.md).
 
### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>¿Indexa la API de Apache Cassandra todos los atributos de una entidad de forma predeterminada?
Sí, Azure Cosmos DB indexa todos los atributos de una entidad de forma predeterminada. Para más información, vea [Directivas de indexación de Azure Cosmos DB](indexing-policies.md). Se obtienen ventajas de rendimiento garantizado con indización coherente y escrituras confirmadas con cuórum perdurable. 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>¿Significa esto que no hay que crear varios índices para responder a las consultas? 
Sí, Azure Cosmos DB ofrece indexación automática de todos los atributos sin ninguna definición de esquema. Esta automatización libera al desarrollador para que se centre en la aplicación y no en la creación y administración de índices. Para más información, vea [Directivas de indexación de Azure Cosmos DB](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>¿Se puede cambiar la directiva de indexación?
Sí, puede cambiar la directiva de indexación especificando la definición del índice. Para más información, vea [Funcionalidades de Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Esta configuración se tiene que codificar adecuadamente e incluir secuencias de escape. Para más información, envíe un correo electrónico a [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com). 

A continuación tiene un ejemplo de formato JSON de cadena en el archivo app.config:

```csharp
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        } 
      ]
    }
  ],
  "excludedPaths": 
[
 {
      "path": "/anotherpath"
 }
]
}
```
### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>¿Se puede usar el nuevo SDK de la API de Cassandra localmente con el emulador?
El [emulador local](local-emulator.md) no admite actualmente la API de Cassandra. 

<a id="moving-to-cosmos-db"></a>
## <a name="questions-from-documentdb-customers"></a>Preguntas de clientes de DocumentDB
### <a name="why-are-you-moving-to-azure-cosmos-db"></a>¿Por qué cambiar a Azure Cosmos DB? 

Azure Cosmos DB supone el próximo gran salto en cuanto a bases de datos en la nube distribuidas globalmente y a escala. Como cliente de DocumentDB, ahora tiene acceso al sistema y a las funcionalidades novedosas y excepcionales ofrecidas por Azure Cosmos DB.

Azure Cosmos DB se lanzó en 2010 como "Proyecto Florencia" para abordar los problemas de desarrollo que aparecen al generar aplicaciones a gran escala en Microsoft. Los desafíos en la compilación de aplicaciones distribuidas globalmente no son exclusivos de Microsoft, por lo que en 2015 creamos la primera generación de esta tecnología disponible para los desarrolladores de Azure. Este servicio se llamó Azure DocumentDB. 

Desde ese momento, hemos agregado nuevas características e incorporado nuevas funcionalidades significativas. Azure Cosmos DB es el resultado. Como parte de esta versión de Azure Cosmos DB, los clientes de DocumentDB (con sus datos) se convierten automáticamente en clientes de Azure Cosmos DB. Estas funcionalidades pertenecen a las áreas del motor de base de datos principal así como a la distribución global, escalabilidad elástica y a unos contratos de nivel de servicio completos y líderes del sector. En concreto, hemos desarrollado el motor de base de datos de Azure Cosmos DB para asignar eficazmente todos los modelos de datos, sistemas de tipos y API más conocidos al modelo de datos subyacente de Azure Cosmos DB. 

La manifestación actual de desarrollo más conocida de este trabajo es la nueva compatibilidad con las API de [Table Storage](../cosmos-db/graph-introduction.md) y [Gremlin](../cosmos-db/table-introduction.md). Y esto es solo el principio. Se tiene previsto agregar otras API conocidas y modelos de datos más recientes con el tiempo y se incorporarán nuevos avances hacia el rendimiento y el almacenamiento a escala global. 

Es importante señalar que el [dialecto de SQL](../documentdb/documentdb-sql-query.md) de DocumentDB siempre ha sido una de las distintas API que la instancia subyacente de Azure Cosmos DB podía admitir. Para los desarrolladores que usan un servicio completamente administrado como Azure Cosmos DB, la única interfaz del servicio son las API expuestas por este. Nada cambia realmente para los clientes existentes de DocumentDB. En Azure Cosmos DB, obtiene exactamente la misma API de SQL que ofrece DocumentDB. Y ahora (y en el futuro), puede acceder a otras funcionalidades anteriormente inaccesibles. 

Otra prueba de nuestro trabajo continuo es la base extendida para la escalabilidad global y flexible del rendimiento y almacenamiento. Hemos realizado varias mejoras fundamentales en el subsistema de distribución global. Una de las múltiples manifestaciones de desarrollo de este trabajo es el modelo de coherencia Prefijo coherente (con lo que se alcanza un total de cinco modelos de coherencia bien definidos). Publicaremos muchas más funcionalidades interesantes a medida que vayan tomando forma. 

### <a name="what-do-i-need-to-do-to-ensure-that-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>¿Qué hay que hacer para asegurarse de que los recursos de DocumentDB continúan ejecutándose en Azure Cosmos DB?

No es necesario realizar ningún cambio. Los recursos de DocumentDB son ahora recursos de Azure Cosmos DB y no se produjo ninguna interrupción del servicio al realizar este cambio.

### <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>¿Qué cambios hay que hacer para que mi aplicación funcione con Azure Cosmos DB?

No es necesario realizar ningún cambio. Las clases, los espacios de nombres y los paquetes NuGet no han cambiado. Como siempre, es recomendable mantener el SDK actualizado para aprovechar las características y mejoras más recientes. 

### <a name="whats-changed-in-the-azure-portal"></a>¿Qué ha cambiado en Azure Portal?

DocumentDB ya no aparece en el portal como servicio de Azure. En su lugar, se encuentra un nuevo icono de Azure Cosmos DB, tal y como se muestra en la imagen siguiente. Todas las colecciones están disponibles tal y como se encontraban antes y aún puede escalar el rendimiento, cambiar la coherencia y supervisar los contratos de nivel de servicio. Se han mejorado las funcionalidades del Explorador de datos (versión preliminar). Ya puede ver y editar documentos, crear y ejecutar consultas y trabajar con procedimientos, desencadenadores y UDF almacenados desde una sola página, tal y como se muestra en la imagen siguiente: 

![Página Colecciones de Azure Cosmos DB](./media/faq/cosmos-db-data-explorer.png)

### <a name="are-there-changes-to-pricing"></a>¿Hay algún cambio en el precio?

No, el costo de ejecutar la aplicación en Azure Cosmos DB es el mismo que antes.

### <a name="are-there-changes-to-the-slas"></a>¿Hay algún cambio en el precio de los contratos de nivel de servicio?

No, los Acuerdos de Nivel de Servicio relacionados con la disponibilidad, coherencia, latencia y rendimiento permanecen igual y aún aparecen en el portal. Para más información, vea [Contrato de nivel de servicio para Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/).
   
![Aplicación de tareas pendientes con datos de ejemplo](./media/faq/azure-cosmosdb-portal-metrics-slas.png)


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
