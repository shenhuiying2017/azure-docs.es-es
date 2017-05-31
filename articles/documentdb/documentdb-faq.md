---
title: "Preguntas de base de datos de Azure Cosmos DB: preguntas más frecuentes| Microsoft Docs"
description: "Obtenga respuestas a las preguntas más frecuentes sobre Azure Cosmos DB, un servicio de base de datos con varios modelos distribuido globalmente. Responda a preguntas de base de datos acerca de la capacidad, los niveles de rendimiento y escalado."
keywords: Database questions, frequently asked questions, documentdb, azure, Microsoft azure
services: cosmosdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 687690fc3b99d52d7c822b7b7ff71c282541be6d
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="frequently-asked-questions-about-azure-cosmos-db"></a>Preguntas más frecuentes sobre Azure Cosmos DB
## <a name="database-questions-about-microsoft-azure-cosmos-db-fundamentals"></a>Preguntas de bases de datos sobre conceptos básicos de Microsoft Azure Cosmos DB
### <a name="what-is-microsoft-azure-cosmos-db"></a>¿Qué es Microsoft Azure Cosmos DB?
Microsoft Azure Cosmos DB es un servicio de base de datos de varios modelos replicado globalmente que ofrece consultas enriquecidas a través de datos sin esquemas, ayuda a proporcionar un rendimiento configurable y de confianza, y posibilita un desarrollo rápido. Todo ello gracias a una plataforma administrada respaldada por la potencia y el alcance de Microsoft Azure. Azure Cosmos DB es la solución adecuada para aplicaciones web, móviles, de juego e IoT cuando un rendimiento predecible, una alta disponibilidad, una baja latencia y un modelo de datos sin esquemas son requisitos clave. Ofrece flexibilidad de esquemas e indexación enriquecida, e incluye compatibilidad transaccional de documentos múltiples con JavaScript integrado.  

Azure Cosmos DB se lanzó a finales de 2010 para abordar los problemas de desarrollo que aparecen en las aplicaciones a gran escala de Microsoft. Puesto que la creación de aplicaciones distribuidas globalmente no es un problema exclusivo de Microsoft, hemos puesto el servicio a disponibilidad de todos los desarrolladores de Azure como Azure DocumentDB. Azure Cosmos DB es el siguiente gran salto en la evolución de DocumentDB y ahora lo ponemos a su disposición. Como parte de esta versión de Azure Cosmos DB, los clientes de DocumentDB (con sus datos) se convierten automáticamente en clientes de Azure Cosmos DB. La transición es perfecta y ya tienen acceso a la gama más amplia de nuevas funcionalidades que ofrece Azure Cosmos DB. 

Para obtener más preguntas, respuestas e instrucciones de bases de datos sobre la implementación y el uso de este servicio, consulte la [página de documentación de Azure Cosmos DB](https://azure.microsoft.com/documentation/services/documentdb/).

### <a name="what-happened-to-documentdb"></a>¿Qué ha ocurrido con DocumentDB?
La API de DocumentDB es uno de los modelos de datos y las API compatibles con Azure Cosmos DB. Además, Azure Cosmos DB le ofrece compatibilidad con API Graph (versión preliminar), Table API (versión preliminar) y MongoDB API. 

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>¿Cómo accedo a mi cuenta de DocumentDB en Azure Portal?
Basta con hacer clic en el icono de Azure Cosmos DB en el menú izquierdo de Azure Portal. Si ya tenía una cuenta de DocumentDB, ahora tiene una cuenta de Azure Cosmos DB, sin cambios en la facturación.

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>¿Cuáles son los casos de uso típicos de Azure Cosmos DB?
Azure Cosmos DB es una buena elección para las nuevas aplicaciones web, móviles, de juego e IoT donde es importante la escala automática, el rendimiento predecible, la orden rápida de tiempos de respuesta en milisegundos y la capacidad para consultar datos sin esquemas. Se presta a un desarrollo rápido y admite la iteración continua de modelos de datos de aplicaciones.  [Casos de uso comunes de Azure Cosmos DB](documentdb-use-cases.md) son las aplicaciones que administran contenido y datos generados por el usuario.  

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>¿Cómo ofrece Azure Cosmos DB un rendimiento predecible?
Una [unidad de solicitud](documentdb-request-units.md) es la medida de rendimiento de Azure Cosmos DB. 1 RU corresponde al rendimiento de una solicitud GET de un documento de 1 KB. Todas las operaciones de Azure Cosmos DB, incluidas las lecturas, escrituras, consultas SQL y ejecuciones de procedimiento almacenado, tienen un valor de unidad de solicitud determinista en función del rendimiento necesario para completar la operación. En vez de pensar en la CPU, la E/S, la memoria y en cómo afecta cada una al rendimiento de la aplicación, puede hacerlo en términos de una medida única de unidad de solicitud.

Cada contenedor de Azure Cosmos DB puede reservarse con rendimiento aprovisionado en términos de unidades de solicitud del rendimiento por segundo. Para las aplicaciones de cualquier escala, puede realizar pruebas comparativas de las solicitudes individuales para medir los valores de unidad de solicitud y aprovisionar contenedores para controlar la suma total de las unidades de solicitud en todas las solicitudes. También puede escalar o reducir verticalmente el rendimiento del contenedor a medida que evolucionen las necesidades de la aplicación. Para más información sobre las unidades de solicitud y ayuda para determinar las necesidades del contenedor, consulte [Estimación de necesidades de rendimiento](documentdb-request-units.md#estimating-throughput-needs) y pruebe la [calculadora de rendimiento](https://www.documentdb.com/capacityplanner). Los contenedores aquí mencionados hacen referencia a la colección de API de DocumentDB, los grafos de API Graph, la colección de API de MongoDB y las tablas de Table API.  


### <a name="is-azure-cosmos-db-hipaa-compliant"></a>¿Es compatible Azure Cosmos DB con HIPAA?
Sí, Azure Cosmos DB es compatible con HIPAA. HIPAA establece los requisitos para el uso, la divulgación y la protección de información de mantenimiento identificable individualmente. Para más información, consulte el [Centro de confianza de Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>¿Cuáles son los límites de almacenamiento de Azure Cosmos DB?
No existe ningún límite en la cantidad total de datos que puede almacenar un contenedor en Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>¿Cuáles son los límites de rendimiento de Azure Cosmos DB?
No hay ningún límite en la cantidad total de rendimiento que un contenedor puede admitir en Azure Cosmos DB, siempre que la carga de trabajo se tenga que distribuir aproximadamente de manera uniforme entre un número suficientemente elevado de claves de partición.

### <a name="how-much-does-microsoft-azure-cosmos-db-cost"></a>¿Cuánto cuesta Microsoft Azure Cosmos DB?
Para obtener más información, consulte la página sobre [detalles de precios de Azure Cosmos DB](https://azure.microsoft.com/pricing/details/documentdb/). Los cargos por uso de Azure Cosmos DB se determinan según la cantidad de contenedores aprovisionados, el número de horas que los contenedores estuvieron conectados y el rendimiento aprovisionado de cada contenedor. Los contenedores aquí mencionados hacen referencia a la colección de API de DocumentDB, los grafos de API Graph, la colección de API de MongoDB y las tablas de Table API. 

### <a name="is-there-a-free-account-available"></a>¿Existe una cuenta gratuita disponible?
Si es la primera vez que usa Azure, regístrese para obtener una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), que ofrece 30 días y 200 USD para que pruebe todos los servicios de Azure. Si tiene una suscripción a Visual Studio, puede recibir [150 USD en créditos gratis de Azure al mes](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para usarlos en cualquier servicio de Azure.  

También puede usar el [Emulador de Azure Cosmos DB](documentdb-nosql-local-emulator.md) para desarrollar y probar su aplicación localmente, de forma gratuita, sin necesidad de crear una suscripción de Azure. Cuando esté satisfecho con el funcionamiento de la aplicación en el Emulador, puede cambiar a una cuenta de Azure Cosmos DB en la nube.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>¿Cómo puedo obtener ayuda adicional con Azure Cosmos DB?
Si necesita ayuda, póngase en contacto con nosotros en [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb), el [foro de MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB) o programe un chat 1:1 con el equipo de ingeniería de Azure Cosmos DB, para lo que debe enviar un correo a [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com). 

## <a name="set-up-microsoft-azure-cosmos-db"></a>Configurar Microsoft Azure Cosmos DB
### <a name="how-do-i-sign-up-for-microsoft-azure-cosmos-db"></a>¿Cómo me suscribo a Microsoft Azure Cosmos DB?
Microsoft Azure Cosmos DB está disponible en Azure Portal. Primero debe registrarse para una suscripción a Microsoft Azure. Una vez suscrito a Microsoft Azure, puede agregar una cuenta de la API de DocumentDB, API Graph (versión preliminar), Table API (versión preliminar) o MongoDB API a su suscripción de Azure.

### <a name="what-is-a-master-key"></a>¿Qué es una clave maestra?
Una clave maestra es un token de seguridad para acceder a todos los recursos de una cuenta. Los individuos con esta clave tienen acceso de lectura y escritura a todos los recursos de la cuenta de base de datos. Tenga cuidado cuando distribuya claves maestras. Las claves maestras principal y secundaria están disponibles en la hoja **Claves** de [Azure Portal][azure-portal]. Para más información sobre las claves, vea [Visualización, copia y regeneración de las claves de acceso](documentdb-manage-account.md#keys).

### <a name="what-are-the-regions-to-which-preferredlocations-be-set-to"></a>¿Cuáles son las regiones en las que se establece PreferredLocations? 
Las regiones válidas presentes para PrefferredLocations son: "Oeste de EE. UU.", "Centro occidental de EE.UU.", "Oeste de EE. UU. 2", "Este de EE. UU.","Este de EE. UU. 2", " Centro de EE. UU.", "Centro-Sur de EE. UU.", "Centro-Norte de EE. UU.", "Europa Occidental", "Europa del Norte", "Asia Oriental", "Sudeste Asiático", "Japón Occidental", "Japón Oriental", "Sudeste de Australia", "Este de Australia", "Centro de la India", "India del Sur", "Oeste de la India", "Canadá Oriental", "Canadá Central", "Centro de Alemania", "Noreste de Alemania","Norte de China", "Este de China", "Corea del Sur", "Corea central","Oeste de Reino Unido", "Sur de Reino Unido", "Sur de Brasil", "USGov Arizona", "USGov Texas".

### <a name="is-there-something-i-should-be-aware-of-when-distributing-data-across-the-world-via-azures-data-centers"></a>¿Hay algo que deba tener en cuenta al distribuir datos en todo el mundo a través de centros de datos de Azure? 
Cosmos DB está presente en todas las regiones. Puesto que se trata del servicio principal, cada nuevo centro de datos estará presente en Cosmos DB. A partir de ahora, se aplica la lista documentada anteriormente. Al establecer estas opciones, debe recordar que Cosmos DB respeta las nubes independientes y de administración pública. Lo que implica que si crea una cuenta allí y quiere replicarla fuera no está autorizado a hacerlo, del mismo modo que no puede acceder a estas ubicaciones para habilitar la replicación allí desde una cuenta externa. 



### <a name="do-you-plan-to-provide-more-price-options-in-the-future"></a>¿Tienen previsto ofrecer otras opciones de precio en el futuro?
Sí, Cosmos DB proporciona en este momento un modelo de rendimiento optimizado. Tenemos previsto ofrecer modelos de precios más óptimos en un futuro próximo. 

 
# <a name="documentdb-api"></a>API de DocumentDB 
## <a name="database-questions-about-developing-against-documentdb-api"></a>Preguntas de bases de datos sobre el desarrollo con la API de DocumentDB

### <a name="how-to-do-i-start-developing-against-documentdb-api"></a>¿Cómo se empieza a desarrollar con la API de DocumentDB?
La API de Microsoft DocumentDB está disponible en [Azure Portal][azure-portal].  Primero debe registrarse para una suscripción a Microsoft Azure.  Una vez suscrito a Microsoft Azure, puede agregar un contenedor de la API de DocumentDB a su suscripción a Azure. Para obtener instrucciones sobre cómo agregar una cuenta de Azure Cosmos DB, vea [Creación de una cuenta de base de datos de Azure Cosmos DB](documentdb-create-account.md). Si tuvo una cuenta de DocumentDB en el pasado, ahora tiene una cuenta de Azure Cosmos DB.  

[SDK](documentdb-sdk-dotnet.md) están disponibles para NET, Python, Node.js, JavaScript y Java.  Los desarrolladores también pueden usar las [API de HTTP RESTful](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interactuar con recursos de Azure Cosmos DB en varias plataformas y lenguajes.

### <a name="can-i-access-some-ready-made-samples-for-getting-headstart"></a>¿Puedo acceder a algunos ejemplos ya preparados que pueda aprovechar?
En GitHub se encuentran disponibles ejemplos de los SDK de la API de DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) y [Python](documentdb-python-samples.md).


### <a name="does-documentdb-api--database-support-schema-free-data"></a>¿Admiten las bases de datos de la API de DocumentDB datos sin esquemas?
Sí, la API de DocumentDB permite que las aplicaciones almacenen documentos JSON arbitrarios sin definiciones ni sugerencias de esquemas. Los datos están disponibles inmediatamente para consulta a través de la interfaz de consulta SQL de Azure Cosmos DB.   

### <a name="does-documentdb-api-support-acid-transactions"></a>¿Admite la API de DocumentDB transacciones ACID?
Sí, la API de DocumentDB admite transacciones entre documentos expresadas como procedimientos almacenados y desencadenadores JavaScript. Las transacciones se limitan a una única partición dentro de cada colección y se ejecutan con semánticas ACID, como todo o nada, aisladas de otras que ejecutan a la vez solicitudes de usuario y código.  Si se producen excepciones por la ejecución en el servidor de código de aplicación JavaScript, la transacción entera se revierte. Para obtener más información sobre las transacciones, consulte [Transacciones del programa de base de datos](documentdb-programming.md#database-program-transactions).

### <a name="what-is-a-collection"></a>¿Qué es una colección?
Una colección es un grupo de documentos y su lógica de aplicación de JavaScript asociada. Una colección es una entidad facturable, en la que el [costo](documentdb-performance-levels.md) lo determinan el rendimiento y el almacenamiento que se usen. Las colecciones pueden abarcar una o varias particiones o uno o varios servidores y se pueden escalar para administrar volúmenes prácticamente ilimitados de almacenamiento o rendimiento.

Las colecciones también son las entidades de facturación de Azure Cosmos DB. Cada colección se factura por hora basándose en el rendimiento aprovisionado y en el espacio de almacenamiento usado. Para obtener más información, consulte [Precios de la API de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/).  

### <a name="how-do-i-create-a-database"></a>¿Cómo se crea una base de datos?
Puede crear bases de datos mediante [Azure Portal](), tal y como se describe en [Create an Azure Cosmos DB collection and database](documentdb-create-collection.md) (Creación de una colección y una base de datos Azure Cosmos DB), uno de los [SDK de Azure Cosmos DB](documentdb-sdk-dotnet.md) o a través de las [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### <a name="how-do-i-set-up-users-and-permissions"></a>¿Cómo se configuran los usuarios y los permisos?
Puede crear usuarios y permisos mediante uno de los [SDK de la API de DocumentDB](documentdb-sdk-dotnet.md) o de las [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

### <a name="does-documentdb-api-support-sql"></a>¿La API de DocumentDB admite SQL?
El lenguaje de consulta SQL es un subconjunto mejorado de la funcionalidad de consulta admitida por SQL. El lenguaje de consulta SQL de Azure Cosmos DB ofrece complejos operadores relacionales y jerárquicos y extensibilidad a través de funciones definidas por el usuario (UDF) basadas en JavaScript. La gramática de JSON permite el modelado de documentos JSON como árboles con etiquetas como los nodos del árbol, algo que usan las técnicas de indexación automática de Azure Cosmos DB y el dialecto de consulta SQL de Azure Cosmos DB.  Para obtener información detallada acerca del uso de la gramática de SQL, vea el artículo [Consulta SQL y sintaxis SQL en DocumentDB][query].

### <a name="does-documentdb-api--support-sql-aggregation-functions"></a>¿La API de DocumentDB admite funciones de agregación de SQL?
La API de DocumentDB admite la agregación de baja latencia a cualquier escala a través de las funciones de agregado `COUNT`, `MIN`, `MAX`, `AVG` y `SUM` a través de la gramática SQL. Para más información, consulte [Funciones de agregado](documentdb-sql-query.md#Aggregates).

### <a name="how-does-documentdb-api--provide-concurrency"></a>¿De qué modo proporciona simultaneidad la API de DocumentDB?
La API de DocumentDB admite control de simultaneidad optimista (OCC) a través de etiquetas de entidad HTTP o ETag. Cada recurso de la API de DocumentDB tiene una ETag, que se establece en el servidor cada vez que se actualiza un documento. El encabezado de la ETag y el valor actual se incluyen en todos los mensajes de respuesta. Las ETag pueden utilizarse con el encabezado If-Match para permitir que el servidor decida si debe actualizarse un recurso. El valor de If-Match es el valor de ETag que somete al proceso de comprobación. Si el valor de ETag coincide con el valor de ETag del servidor, se actualizará el recurso. Si el valor de ETag ya no es el actual, el servidor rechaza la operación con un código de respuesta HTTP 412 Precondition failure (HTTP 412: error de condición previa). Después, el cliente tendrá que volver a recuperar el recurso para obtener el valor de ETag actual del recurso. Además, las ETag pueden emplearse con el encabezado If-None-Match para determinar si hay que volver a recuperar un recurso.

Para usar la simultaneidad optimista en .NET, utilice la clase [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Para ver un ejemplo de .NET, consulte la sección [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) en el ejemplo del artículo DocumentManagement de GitHub.

### <a name="how-do-i-perform-transactions-in-documentdb-api"></a>¿Cómo se realizan transacciones en la API de DocumentDB?
La API de DocumentDB admite transacciones integradas en el lenguaje a través de procedimientos almacenados y desencadenadores JavaScript. Todas las operaciones de base de datos dentro de scripts se ejecutan en un aislamiento de instantánea limitado a la colección si es una colección de partición única, o en documentos con el mismo valor de la clave de partición dentro de una colección, si la colección está particionada. Una instantánea de las versiones de documento (etiquetas ETag) se toma al comienzo de la transacción y solo se confirma si el script se ejecuta correctamente. Si el JavaScript produce un error, la transacción se revierte. Consulte [Programación de servidor de la API de DocumentDB](documentdb-programming.md) para más detalles.

### <a name="how-can-i-bulk-insert-documents-into-documentdb-api"></a>¿Cómo se pueden insertar documentos de forma masiva en la API de DocumentDB?
Existen tres maneras de insertar documentos de forma masiva en Azure Cosmos DB:

* La herramienta de migración de datos, tal y como se describe en [Importación de datos en la API de DocumentDB](documentdb-import-data.md).
* El Explorador de documentos en Azure Portal, como se describe en [Adición en masa de documentos con el Explorador de documentos](documentdb-view-json-document-explorer.md#bulk-add-documents).
* Procedimientos almacenados, tal y como se describe en [Programación de servidor de la API de DocumentDB](documentdb-programming.md).

### <a name="does-documentdb-api-support-resource-link-caching"></a>¿Admite la API de DocumentDB el almacenamiento en caché de vínculos de recursos?
Sí. Como Cosmos DB es un servicio RESTful, los vínculos de recursos son inmutables y se pueden almacenar en caché. Los clientes de la API de DocumentDB pueden especificar un encabezado "If-None-Match" para las lecturas con relación a cualquier recurso como documento o colección y actualizar sus copias locales solo cuando la versión del servidor cambie.

### <a name="is-a-local-instance-of-documentdb-api-available"></a>¿Hay disponible una instancia local de la API de DocumentDB?
Sí. El [Emulador de Azure Cosmos DB](documentdb-nosql-local-emulator.md) ofrece una emulación de alta fidelidad del servicio de la API de DocumentDB. Admite una funcionalidad idéntica a la de Azure Cosmos DB, incluida la compatibilidad con la creación y la consulta de documentos JSON, el aprovisionamiento y el escalado de colecciones y la ejecución de procedimientos almacenados y desencadenadores. Puede desarrollar y probar aplicaciones mediante el Emulador de Azure Cosmos DB e implementarlas en Azure a escala global realizando simplemente un solo cambio de configuración en el punto de conexión de la conexión de Azure Cosmos DB.

## <a name="database-questions-about-developing-against-api-for-mongodb"></a>Preguntas de bases de datos sobre el desarrollo con la API de MongoDB
### <a name="what-is-azure-cosmos-dbs-api-for-mongodb"></a>¿Qué es la API de Azure Cosmos DB para MongoDB?
La API de Microsoft Azure Cosmos DB para MongoDB es una capa de compatibilidad que permite a las aplicaciones comunicarse de manera sencilla y transparente con el motor de base de datos de Azure Cosmos DB mediante los controladores y las API de Apache MongoDB compatibles con la comunidad. Los desarrolladores ahora pueden aprovechar las técnicas y las cadenas de herramientas de MongoDB para crear aplicaciones que emplean Azure Cosmos DB, con lo que se benefician de las funcionalidades exclusivas de esta solución, entre las que se encuentran la indexación automática, el mantenimiento de copias de seguridad, los acuerdos de nivel de servicio con respaldo financiero, etc.

### <a name="how-to-do-i-connect-to-my-api-for-mongodb-database"></a>¿Cómo puedo conectarme a la base de datos de mi API de MongoDB?
La forma más rápida de conectarse la API de MongoDB de Azure Cosmos DB es a través de [Azure Portal](https://portal.azure.com). Acceda a su cuenta. En el panel de *navegación izquierda* de la cuenta, haga clic en *Inicio rápido*. *Inicio rápido* es la mejor manera de obtener fragmentos de código para conectarse a su base de datos. 

Azure Cosmos DB aplica estándares y requisitos de seguridad estrictos. Las cuentas de Azure Cosmos DB requieren autenticación y comunicación segura a través de *SSL* para garantizar el uso de la versión 1.2 del protocolo TLS.

Para obtener más información, consulte el artículo sobre [conectarse a la base de datos de la API de MongoDB](documentdb-connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>¿Existen otros códigos de error para una base de datos de la API de MongoDB?
La API de MongoDB tiene sus propios códigos de error, además de los comunes de MongoDB.


| Error               | Código  | Descripción  | Solución  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | El número total de unidades de solicitud consumido superó la tasa de unidad de solicitud de aprovisionamiento de la colección y se ha limitado. | Considere la posibilidad de escalar el rendimiento de la colección desde Azure Portal o vuelva a intentarlo. |
| ExceededMemoryLimit | 16501 | Como se trata de un servicio de varios inquilinos, la operación ha superado la asignación de memoria del cliente. | Reduzca el ámbito de la operación a través de un criterio de consulta más restrictivo o póngase en contacto con el equipo de asistencia técnica desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>*Ejemplo: &nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="database-questions-about-developing-with-azure-cosmos-db-table-api-preview"></a>Preguntas de bases de datos sobre el desarrollo con Azure Cosmos DB: Table API (versión preliminar)

### <a name="terms"></a>Términos 
Azure Cosmos DB: Table API (versión preliminar) se refiere a ofertas premium por Azure Cosmos DB para compatibilidad con tablas anunciadas en la compilación de 2017. 

El SDK de tabla estándar es el SDK de tabla de almacenamiento de Azure existente. 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>¿Cómo puedo usar la nueva oferta de Table API (versión preliminar)? 
Microsoft Table API está disponible en [Azure Portal][azure-portal].  Primero debe registrarse para una suscripción a Microsoft Azure.  Una vez suscrito a Microsoft Azure, puede agregar un contenedor de Table API a su suscripción a Azure.  
En el período de versión preliminar, hay [SDK](../cosmos-db/table-sdk-dotnet.md) disponibles para .NET. Puede completar el inicio rápido de [Table API](../cosmos-db/create-table-dotnet.md) para empezar a trabajar.

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>¿Necesito un nuevo SDK para usar Table API (versión preliminar)? 
Sí, hay un nuevo SDK en forma de paquete Nuget disponible aquí [Table API](../cosmos-db/table-sdk-dotnet.md). Se denomina SDK para Microsoft Azure Storage 8.1.2 con Premium Table API (versión preliminar).  

### <a name="how-do-i-provide-the-feedback-about-the-sdk-bugs"></a>¿Cómo se puede proporcionar información sobre el SDK y los errores?
Comparta sus comentarios mediante uno de estos métodos:

* [En UserVoice](https://feedback.azure.com/forums/263030-documentdb)
* [Foro de MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-documentdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>¿Cuál es la cadena de conexión que tengo que usar para conectarme a Table API (versión preliminar)?
La cadena de conexión es `DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com`. Puede obtener la cadena de conexión en la página de claves de Azure Portal. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-apipreview"></a>¿Cómo se invalidan los valores de configuración de las opciones de solicitud de la nueva Table API (versión preliminar)?
Estas opciones de configuración se documentan en [Funcionalidades de Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Puede cambiar la configuración si la agrega a app.config en la sección appsettings de la aplicación cliente.
<appSettings>
    <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
    <add key="TableThroughput" value="<PositiveIntegerValue"/>
    <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
    <add key="TableUseGatewayMode" value="True|False"/>
    <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
</appSettings>


### <a name="is-there-any-change-to-existing-customers-that-use-the-existing-standard-table-sdk"></a>¿Hay algún cambio a los clientes existentes que usan el SDK de tabla estándar existente?
Ninguno. No hay ningún cambio para los clientes nuevos o existentes que usen el SDK de tabla estándar existente. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>¿Cómo se ven los datos de tablas que se almacenan en Azure Cosmos DB para su uso con Table API (versión preliminar)? 
Puede usar Azure Portal para examinar los datos. También puede usar el código de Table API (versión preliminar) o las herramientas que se mencionan a continuación. 

### <a name="which-tools-will-work-with-table-api-preview"></a>¿Qué herramientas funcionarán con Table API (versión preliminar)? 
Versión anterior de Azure Explorer (0.8.9).

Las herramientas que tengan suficiente flexibilidad como para aceptar una cadena de conexión en el formato especificado antes son compatibles con la nueva Table API (versión preliminar). En la página [Herramientas de cliente de Azure Storage](../storage/storage-explorers.md) se ofrece una lista de herramientas de tabla. 

### <a name="does-powershellcli-work-with-the-new-table-api-preview-"></a>¿Funcionan PowerShell y la CLI con la nueva Table API (versión preliminar)?
Tenemos previsto agregar compatibilidad de PowerShell y la CLI con Table API (versión preliminar). 

### <a name="is-the-concurrency-on-operations-controlled"></a>¿Se controla la simultaneidad en las operaciones?
Sí, se ofrece simultaneidad optimista a través del mecanismo de ETag según lo previsto en Standard API Table. 

### <a name="is-odata-query-model-supported-for-entities"></a>¿Es el modelo de consulta de OData compatible con entidades? 
Sí, Table API (versión preliminar) admite consulta de OData y consulta Linq. 

### <a name="can-i-connect-to-standard-azure-table-and-the-new-premium-table-api-preview-at-side-by-side-in-same-application-"></a>¿Puedo conectarme en paralelo a la tabla estándar de Azure y a la nueva Premium Table API (versión preliminar) en la misma aplicación? 
Sí, esto puede conseguirse creando dos instancias distintas de CloudTableClient que apunten a sus propios URI a través de la cadena de conexión.

### <a name="how-do-i-migrate-existing-table-storage-application-to-this-new-offering"></a>¿Cómo se puede migrar la aplicación Table Storage existente a esta nueva oferta?
Póngase en contacto con askdocdb@microsoft.com si quiere aprovechar las ventajas de la nueva oferta de Table API en los datos de Table Storage existentes. 

### <a name="what-is-the-roadmap-for-this-service-when-will-other-functionality-of-standard-table-api-be-offered"></a>¿Cuál es el mapa de ruta para este servicio y cuando se va a ofrecer otra funcionalidad de Standard API Table?
Tenemos previsto agregar compatibilidad con tokens de SAS, ServiceContext, estadísticas, cifrado, análisis y otras características conforme avancemos hacia GA.  Háganos sus comentarios en [UserVoice](https://feedback.azure.com/forums/263030-documentdb). 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-say-i-start-with-n-gbs-of-data-and-my-data-will-grow-to-1-tb-overtime"></a>¿Cómo se realiza la expansión del tamaño de almacenamiento para este servicio, digamos que se empieza por N gigabytes de datos y con el tiempo los datos crecerán a 1 TB?  
Cosmos DB está diseñado para proporcionar un almacenamiento ilimitado a través del uso de escalado horizontal. Nuestro servicio supervisará y aumentará el almacenamiento de forma eficaz. 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>¿Cómo se supervisa la oferta de Table API (versión preliminar)?
Puede usar el panel de métricas de Table API (versión preliminar) para supervisar las solicitudes y el uso de almacenamiento. 

### <a name="how-do-i-calculate-throughput-i-require"></a>¿Cómo se puede calcular el rendimiento que se necesita?
Puede usar la calculadora de capacidad para calcular el valor de TableThroughput necesario para las operaciones, tal y como se documenta en [Estimate Request Units and Data Storage](https://www.documentdb.com/capacityplanner) (Cálculo de unidades de solicitud y almacenamiento de datos). Por lo general puede representar la entidad como JSON e indicar las cantidades para las operaciones. 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>¿Puedo usar el nuevo SDK de Table API (versión preliminar) localmente con el emulador?
Sí, puede usar Table API (versión preliminar) en el emulador local al usar el nuevo SDK. Descargue [aquí](documentdb-nosql-local-emulator.md) el nuevo emulador. El valor de StorageConnectionString en el archivo app.config tiene que ser "DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081". 

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>¿Puede una aplicación existente funcionar con Table API (versión preliminar)? 
El área expuesta de la nueva Table API (versión preliminar) es compatible con el SDK de tabla estándar de Azure existente en las operaciones de creación, eliminación, actualización y consulta con la API de .NET. Debe asegurarse de que tiene la clave de fila, ya que Table API (versión preliminar) requiere las claves de partición y de fila. También tenemos previsto agregar otra compatibilidad de SDK conforme avancemos hacia GA de esta oferta de servicio.

### <a name="do-i-need-to-migrate-existing-azure-table-based-application-to-new-sdk-if-i-do-not-want-to-use-table-api-preview-features"></a>¿Es necesario migrar la aplicación basada en la tabla de Azure existente al nuevo SDK si no se quieren usar las características de Table API (versión preliminar)?
No, los clientes existentes pueden crear y usar recursos de tabla estándar presentes sin interrupciones de ningún tipo. Pero, si no usa la nueva Table API (versión preliminar), no puede aprovechar el índice automático, la opción de coherencia adicional ni la distribución global. 

### <a name="how-do-i-add-replication-for-the-data-in-premium-table-api-preview-across-multiple-regions-of-azure"></a>¿Cómo se agrega la replicación de los datos de Premium Table API (versión preliminar) en varias regiones de Azure?
Puede usar la [configuración de replicación global](documentdb-portal-global-replication.md) del portal de Cosmos DB para agregar regiones que sean adecuadas para la aplicación. Para desarrollar una aplicación distribuida globalmente, debe agregar también la aplicación con la información de PreferredLocation establecida en la región local para proporcionar baja latencia de lectura. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in--premium-table-apipreview"></a>¿Cómo se puede cambiar la región principal de escritura para la cuenta en Premium Table API (versión preliminar)?
Puede usar el panel del portal de replicación global de Cosmos DB para agregar una región y luego conmutar por error a la región necesaria. Para obtener instrucciones, vea [Developing with multi-region Azure Cosmos DB accounts](documentdb-regional-failovers.md) (Desarrollo con cuentas de Azure Cosmos DB de varias regiones). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>¿Cómo se configuran las regiones de lectura preferidas con una baja latencia al distribuir los datos? 
Tiene que aprovechar la clave de PreferredLocation del archivo app.config para que le ayude a realizar las lecturas en la ubicación local. En el caso de una aplicación existente, Table API (versión preliminar) iniciará un error si se establece LocationMode. Quite ese código ya que Premium Table API(versión preliminar) seleccionará esta información del archivo app.config.  Estas opciones de configuración se documentan en [Funcionalidades de Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).


### <a name="how-do-i-think-of-consistency-in-table-api-preview"></a>¿Cómo he de considerar la coherencia en Table API (versión preliminar)? 
Cosmos DB ofrece contrapartidas bien fundamentadas entre coherencia, disponibilidad y latencia. A partir de esta versión ofrece cinco niveles de coherencia para elegir. Estos niveles de coherencia están disponibles para el desarrollador de Table API (versión preliminar). Esto permite a los desarrolladores elegir el modelo de coherencia adecuado en el nivel de tabla y solicitud individual al consultar los datos.  Cuando se conecta un cliente, puede especificar un nivel de coherencia, que puede cambiarse a través de la configuración de app.config para el valor de la clave TableConsistencyLevel. Table API (versión preliminar) ofrece lecturas de baja latencia con la opción de lectura de sus propias escrituras con coherencia de sesión como valor predeterminado. Para más información, consulte [Niveles de coherencia](documentdb-consistency-levels.md). De forma predeterminada, la tabla estándar ofrece actualmente una coherencia fuerte dentro de una región y posible en las ubicaciones secundarias.  

### <a name="does-this-mean-compared-to-eventual--strong-consistency-that-is-possible-with-standard-table---we-now-have-more-choices"></a>En comparación con la coherencia fuerte y posible que se puede obtener con una tabla estándar, ¿significa esto que ahora tenemos más opciones?
Sí, estas opciones se documentan en el artículo [Niveles de coherencia de datos optimizables en DocumentDB](documentdb-consistency-levels.md) para ayudar a los desarrolladores de aplicaciones a aprovechar la naturaleza distribuida de Cosmos DB. Puesto que también se ofrecen garantías de coherencia, actualmente se pueden aprovechar con toda confianza. Estas opciones de configuración se documentan en [Funcionalidades de Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled--how-long-does-it-take-to-replicate-the-data"></a>Cuando se habilita la distribución, ¿cuánto se tarda en replicar los datos?
Confirmamos la duración de los datos en una región local e inmediatamente insertamos los datos en otras regiones en cuestión de milisegundos. Esta replicación depende únicamente del tiempo de ida y vuelta (RTT) del centro de datos. Infórmese de las posibilidades de distribución global de Cosmos DB en [DocumentDB, un servicio de bases de datos distribuidas globalmente de Azure](documentdb-distribute-data-globally.md).

### <a name="can-the-read-request-consistency-be-changed"></a>¿Se puede cambiar la coherencia de la solicitud de lectura?
Cosmos DB permite establecer la coherencia en el nivel de contenedor, es decir, una tabla. El SDK permite también cambiar el nivel especificando el valor de la clave TableConsistencyLevel en el archivo app.config. Estos son los valores posibles: Fuerte|Obsolescencia limitada|Sesión|ConsistentPrefix|Posible. Esto se documenta en el artículo de [niveles de coherencia](documentdb-consistency-levels.md). Es importante aquí recordar que la coherencia de la solicitud no puede ser superior a la que se establece para la tabla. Por ejemplo, si ha configurado la coherencia de la tabla en posible y establece el nivel de coherencia de fuerte en la solicitud, no funcionará. 

### <a name="how-does-premium-table-api-preview-account-take-care-of-failover-in-case-a-region-goes-down"></a>¿Cómo se encarga la cuenta de Premium Table API (versión preliminar) de la conmutación por error en caso de que una región deje de funcionar? 
Premium Table API (versión preliminar) aprovecha la plataforma distribuida globalmente de Cosmos DB. Para garantizar que la aplicación puede tolerar un tiempo de inactividad del centro de datos, tiene que habilitar como mínimo otra región adicional para la cuenta en [Developing with multi-region Azure Cosmos DB accounts](documentdb-regional-failovers.md) (Desarrollo con cuentas de Azure Cosmos DB de varias regiones) del portal de Cosmos DB. Puede establecer la prioridad de la región mediante [Developing with multi-region Azure Cosmos DB accounts](documentdb-regional-failovers.md) (Desarrollo con cuentas de Azure Cosmos DB de varias regiones) del portal. Puede agregar todas las regiones que quiera para la cuenta y controlar dónde puede conmutar por error especificando una prioridad. Huelga decir que también tiene que proporcionar una aplicación allí para aprovechar la base de datos. Así, los clientes no notarán el tiempo de inactividad. El SDK de cliente se hospeda automáticamente. Puede detectar la región que no funciona y conmutar por error automáticamente a la nueva región.

### <a name="is-premium-table-api-preview-enabled-for-backups"></a>¿Está habilitada Premium Table API (versión preliminar) para copias de seguridad?
Sí, Premium Table API (versión preliminar) aprovecha la plataforma de Cosmos DB para copias de seguridad. Se realizan copias de seguridad automáticamente. La copia de seguridad de Cosmos DB se documenta en [Copias de seguridad y restauración automáticas en línea con Azure Cosmos DB](C:\Users\govindk\azure-docs-pr\articles\documentdb\documentdb-online-backup-and-restore.md)

 
### <a name="does-the-table-api-preview-index-all-attributes-of-entities-by-default"></a>¿Indexa Table API (versión preliminar) todos los atributos de entidades de manera predeterminada?
Sí, todos los atributos de la entidad se indexan de manera predeterminada. Los detalles de indexación se documentan en el artículo [Directivas de indexación de Azure Cosmos DB](documentdb-indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-different-indexes-to-satisfy-the-queries"></a>¿Significa esto que no hay que crear distintos índices para responder a las consultas? 
Sí, Cosmos DB ofrece la indexación automática de todos los atributos sin ninguna definición de esquema. Esto libera al desarrollador para que se centre en la aplicación en lugar de preocuparse por la creación y administración de índices. Los detalles de indexación se documentan en el artículo [Directivas de indexación de Azure Cosmos DB](documentdb-indexing-policies.md).

### <a name="can-the-indexing-policy-be-changed"></a>¿Se puede cambiar la directiva de indexación?
Sí, puede cambiar el índice especificando la definición del índice. El significado de estas opciones de configuración se documenta en el artículo [Funcionalidades de Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Esta configuración se tiene que codificar adecuadamente e incluir secuencias de escape.  

En formato JSON de cadena en el archivo app.config.
{ "indexingMode": "consistent", "automatic": true, "includedPaths": [ { "path": "/somepath", "indexes": [ { "kind": "Range", "dataType": "Number", "precision": -1 }, { "kind": "Range", "dataType": "String", "precision": -1 } ] } ], "excludedPaths": [ { "path": "/anotherpath" } ] }

### <a name="cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-like-sorting-aggregates-hierarchy-and-other-functionality---is-this-planned-to-be-added-to-the-table-api-"></a>Cosmos DB como plataforma parece tener gran cantidad de funcionalidades, como ordenación, agregados, jerarquía y otras funciones, ¿está previsto agregarlas a Table API? 
En la versión preliminar, Azure Cosmos DB admite la misma funcionalidad de consulta que Azure Table Storage para Table API. Azure Cosmos DB también admite ordenación, agregados, consulta geoespacial, jerarquía y una amplia variedad de funciones integradas. Una futura actualización de servicio de Table API ofrecerá funcionalidades adicionales. En [Consulta de Azure Cosmos DB](../documentdb/documentdb-sql-query.md) encontrará información general sobre esas funcionalidades.
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>Cuándo se debe cambiar TableThroughput para Table API (versión preliminar)?
El valor de TableThroughput se debe cambiar cuando se realiza la extracción, transformación y carga de datos (ETL) o se quieren cargar muchos datos en un período breve de tiempo. 

OR

Cuando vea que el rendimiento usado es mayor que el rendimiento aprovisionado en las métricas y tiene limitaciones, necesita más rendimiento del contenedor en el back-end. Esto se documenta en el artículo [Establecimiento del rendimiento para las colecciones de Azure Cosmos DB](documentdb-set-throughput.md).

### <a name="can-i-scale-up-or-down-the-throughput-of-my-table-api-preview-table"></a>¿Se puede escalar o reducir verticalmente el rendimiento de mi tabla de Table API (versión preliminar)? 
Sí, puede usar el panel de escala del portal de Cosmos DB para ello. Esto se documenta en el tema [Establecimiento del rendimiento](documentdb-set-throughput.md).

### <a name="can-premium-table-api-preview-leverage-ru-per-minute-offering"></a>¿Puede Premium Table API (versión preliminar) aprovechar la oferta de unidades de solicitud por minuto? 
Si, Premium Table API (versión preliminar) aprovecha las funcionalidades de Cosmos DB para ofrecer acuerdos de nivel de servicio para rendimiento, latencia, disponibilidad y coherencia. Así se asegura que puede aprovecharse la oferta de unidades de solicitud por minuto, tal y como se documenta en [Unidades de solicitud](documentdb-request-units.md). Esta capacidad permite al cliente no tener que aprovisionar en función del máximo y suavizar los picos de la carga de trabajo.

### <a name="is-there-a-default-tablethroughput-which-is-set-for-newly-provisioned-tables"></a>¿Existe un TableThroughput predeterminado que se establezca para tablas recién aprovisionadas?
Sí, si no invalida el TableThroughput a través de app.config y no usa un contenedor creado previamente en Cosmos DB, el servicio crea una tabla con un rendimiento de 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-standard-table-api"></a>¿Hay algún cambio de precio para los clientes existentes de Standard API Table?
Ninguno. No hay ningún cambio de precio para los clientes existentes de Standard API Table. 

### <a name="how-is-the-price-calculated-for-the-table-apipreview"></a>¿Cómo se calcula el precio de Table API (versión preliminar)? 
Depende del TableThroughput asignado. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>¿Cómo se controlan las limitaciones en las tablas de la oferta de Table API (versión preliminar)? 
Si la tasa de solicitudes supera la capacidad del rendimiento aprovisionado para el contenedor subyacente, obtendrá un error y el SDK reintentará la llamada con la directiva de reintentos.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-leverage-premium-table-preview-offering-of-cosmos-db"></a>¿Por qué hay que elegir un rendimiento además de PartitionKey y RowKey para aprovechar la oferta de Premium Table API (versión preliminar) de Cosmos DB?
Si no se especifica uno en el archivo app.config, Cosmos DB establecerá un rendimiento predeterminado para el contenedor. 

Cosmos DB ofrece garantías de rendimiento, latencia con límites superiores en la operación. Esto es posible cuando un motor puede aplicar la regulación de las operaciones del inquilino. La configuración de TableThroughput básicamente tiene por objeto garantizar que se obtiene el rendimiento y la latencia garantizados, ya que ahora la plataforma reservará esta capacidad y garantiza el éxito de las operaciones.  
La especificación de rendimiento también le permite cambiarlo de forma elástica para aprovechar la estacionalidad de la aplicación y satisfacer las necesidades de rendimiento y ahorrar costos.

### <a name="azure-storage-sdk-has-been-very-cheap-for-me-as-i-only-pay-to-store-the-data-and-i-rarely-query-cosmos-dbs-new-offering-seems-to-be-charging-me-even-though-i-have-not-performed-single-transaction-or-stored-anything-whats-going-on-here"></a>El SDK de Azure Storage me ha resultado muy económico ya que solo pago por almacenar los datos y rara vez hago consultas. La nueva oferta de Cosmos DB parece que me cobra aunque no haya realizado ni una sola transacción ni haya almacenado nada. ¿Qué ocurre aquí?

Cosmos DB se ha diseñado para ser un sistema basado en acuerdos de nivel de servicio distribuido globalmente con garantías de disponibilidad, latencia y rendimiento. A diferencia de otros sistemas, cuando se reserva rendimiento en Cosmos DB está garantizado. Cosmos DB ofrece también otras funcionalidades que los clientes llevan pidiendo desde hace mucho, como, por ejemplo, índices secundarios, distribución global, etc. Durante el período de versión preliminar ofrecemos el modelo optimizado de rendimiento y, a largo plazo, tenemos previsto ofrecer un modelo optimizado de almacenamiento para satisfacer las necesidades de nuestros clientes.  

### <a name="i-never-get-quota-full-indicating-a-partition-is-full-when-i-keep-ingesting-data-into-azure-table-storage-with-the-table-apipreview--i-can-get-this-error-is-this-offering-limiting-me-and-forcing-me-to-change-my-present-application"></a>Nunca aparece “cuota” completa (lo que indica que una partición está completa) cuando sigo introduciendo datos en Azure Table Storage. Con Table API (versión preliminar), puedo obtener este error. ¿Esta oferta me limita y me obliga a cambiar mi aplicación actual?

Cosmos DB es un sistema basado en acuerdos de nivel de servicio que ofrece escalado ilimitado con garantías de latencia, rendimiento, disponibilidad y coherencia. Para obtener el rendimiento premium garantizado, tiene que asegurarse de que el tamaño de los datos y el índice son administrables y escalables. El límite de 10 GB en el número de entidades o elementos por clave de partición tiene por objeto asegurar un excelente rendimiento de consultas y búsquedas. Para asegurar que la aplicación se escala correctamente incluso con Azure Storage le pedimos que no cree una partición activa almacenando toda la información en una partición y realizando consultas en ella.  

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>Entonces, ¿PartitionKey y RowKey siguen siendo necesarios con la nueva Table API (versión preliminar)? 
Sí. Dado que el área expuesta de Table API (versión preliminar) es similar al SDK de Azure Table Storage, la clave de partición supone una forma excelente de distribuir los datos. La clave de fila es única en esa partición. Sí, la clave de fila tiene que estar presente y no puede ser nula como en el SDK estándar. A partir de la versión preliminar de la compilación, la longitud de RowKey es 255 bytes y la de PartitionKey es 100 bytes (pronto se va a aumentar a 1 KB). 

### <a name="what-will-be-the-error-messages-of-table-api-preview-"></a>¿Cuáles serán los mensajes de error de Table API (versión preliminar)?
Puesto que esta versión preliminar es compatible con la tabla estándar, la mayoría de los errores se asignarán a los errores de tabla estándar. 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another--in-tables-api-preview"></a>¿Por qué me veo limitado cuando trato de crear muchas tablas seguidas en instancias de Table API (versión preliminar)?
Cosmos DB es un sistema basado en acuerdos de nivel de servicio que ofrece garantía de latencia, rendimiento, disponibilidad y coherencia. Puesto que es un sistema aprovisionado, reserva recursos para garantizar estos requisitos. Se detecta y limita la frecuencia de creación de tablas de manera rápida. Se recomienda observar la frecuencia de creación de tablas y moderarla a menos de cinco por minuto. Recuerde que Table API (versión preliminar) en un sistema aprovisionado, por lo que tiene que pagar por él desde el momento en que lo aprovisione.  

# <a name="graph-apipreview"></a>API Graph (versión preliminar)
## <a name="database-questions-about-developing-against-graph-apipreview"></a>Preguntas de bases de datos sobre el desarrollo con API Graph (versión preliminar)
### <a name="how-can-i-leverage-graph-api-preview-with-cosmos-db"></a>¿Cómo puedo usar API Graph (versión preliminar) con Cosmos DB?
La funcionalidad de API Graph (versión preliminar) puede aprovecharse en una biblioteca de extensiones. Se denomina Microsoft Azure Graphs y está disponible en NuGet. 

### <a name="it-looks-like-you-support-gremlin-as-traversal-language-do-you-plan-to-add-some-more-forms-of-query-"></a>Parece que aceptan Gremlin como lenguaje traversal, ¿tienen previsto agregar algunas otras formas de consulta?
Sí, tenemos planes de agregar otros mecanismos de consulta en el futuro. 

### <a name="how-can-i-use-the-new-graph-apipreview-offering"></a>¿Cómo puedo usar la nueva oferta de API Graph (versión preliminar)? 
Complete el inicio rápido de [API Graph](../cosmos-db/create-graph-dotnet.md) para empezar a trabajar.




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md

