---
title: Modelo de recursos y conceptos de Azure Cosmos DB | Microsoft Docs
description: "Obtenga información sobre el modelo jerárquico de Azure Cosmos DB de bases de datos, colecciones, funciones definidas por el usuario (UDF), documentos, permisos para administrar recursos, etc."
keywords: "Modelo jerárquico, cosmosdb, azure, Microsoft azure"
services: cosmos-db
documentationcenter: 
author: rafats
manager: jhubbard
ms.assetid: ef9d5c0c-0867-4317-bb1b-98e219799fd5
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a88f17a658987e1ff3ae0e0f38d6551c3acee1da
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-hierarchical-resource-model-and-core-concepts"></a>Conceptos básicos y modelo jerárquico de recursos de Azure Cosmos DB

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Las entidades de base de datos que administra Azure Cosmos DB se conocen como **recursos**. Cada recurso se identifica mediante un URI lógico. Se puede interactuar con los recursos mediante verbos HTTP estándar, encabezados de solicitud/respuesta y códigos de estado. 

En este artículo se responden a las siguientes preguntas:

* ¿Qué es el modelo de recursos de Azure Cosmos DB?
* ¿Qué diferencia existe entre los recursos que define el sistema y los que define el usuario?
* ¿Cómo se puede dirigir un recurso?
* ¿Cómo se trabaja con las colecciones?
* ¿Cómo se trabaja con procedimientos almacenados, desencadenadores y Funciones definidas por el usuario (UDF)?

## <a name="hierarchical-resource-model"></a>Modelo jerárquico de recursos
Tal y como muestra el siguiente diagrama, el **modelo de recursos** jerárquico de Azure Cosmos DB consta de conjuntos de recursos en una cuenta de base de datos, cada uno de ellos direccionable mediante un URI lógico y estable. En este artículo, los conjuntos de recursos se denominan **fuentes**. 

> [!NOTE]
> Azure Cosmos DB ofrece un protocolo de TCP sumamente eficaz que también es RESTful en su modelo de comunicación. Está disponible a través de [API de cliente de .NET de SQL](sql-api-sdk-dotnet.md).
> 
> 

![Modelo jerárquico de recursos de Azure Cosmos DB][1]  
**Modelo jerárquico de recursos**   

Para empezar a trabajar con recursos, debe [crear una cuenta de base de datos de base de datos](create-sql-api-dotnet.md) con su suscripción de Azure. Una cuenta de base de datos puede estar formada por un grupo de **bases de datos**, cada una con varias **colecciones**, que a su vez pueden contener **procedimientos almacenados, desencadenadores, UDF, documentos y **datos adjuntos** relacionados. Una base de datos también tiene **usuarios** asociados, cada uno con un conjunto de **permisos** para obtener acceso a las colecciones, procedimientos almacenados, desencadenadores, UDF, documentos o datos adjuntos. Si bien las bases de datos, los usuarios, los permisos y las colecciones son recursos que define el sistema con esquemas conocidos, los documentos y los elementos adjuntos tienen contenido JSON arbitrario que define el usuario.  

| Recurso | DESCRIPCIÓN |
| --- | --- |
| Cuenta de base de datos |Una cuenta de base de datos está asociada a un conjunto de bases de datos y una cantidad fija de almacenamiento de blobs para los datos adjuntos. Puede crear una o más cuentas de base de datos mediante su suscripción de Azure. Para obtener más información, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| Base de datos |Una base de datos es un contenedor lógico de almacenamiento de documentos particionado en recopilaciones. Es también un contenedor de usuarios. |
| Usuario |El espacio de nombres lógico para dirigir los permisos. |
| Permiso |Un token de autorización asociado con un usuario para el acceso a un recurso específico. |
| Colección |Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript. Una colección es una entidad facturable, donde el [costo](performance-levels.md) está determinado por el nivel de rendimiento asociado a la colección. Las colecciones pueden abarcar una o varias particiones o uno o varios servidores y se pueden escalar para administrar volúmenes prácticamente ilimitados de almacenamiento o rendimiento. |
| Procedimiento almacenado |Lógica de aplicaciones creada en JavaScript que se registra con una recopilación y se ejecuta de manera transaccional en el motor de la base de datos. |
| Desencadenador |Lógica de aplicaciones creada en JavaScript que se ejecuta antes o después de una operación de inserción, reemplazo o eliminación. |
| UDF |Lógica de aplicaciones creada en JavaScript. Las UDF le permiten modelar un operador de consultas personalizado y con ello ampliar el lenguaje de consultas central de SQL API. |
| Documento |Contenido JSON definido por el usuario (arbitrario). De manera predeterminada, no es necesario definir esquemas ni proporcionar índices secundarios para todos los documentos agregados a una recopilación. |
| Datos adjuntos |Los datos adjuntos son documentos especiales que contienen referencias y metadatos asociados con blobs/medios externos. El desarrollador puede decidir administrar los blobs a través de Cosmos DB o almacenarlos con un proveedor de servicios de blobs externos como OneDrive, Dropbox, etc. |

## <a name="system-vs-user-defined-resources"></a>Recursos del sistema frente a recursos definidos por el usuario
Los recursos como cuentas de base de datos, bases de datos, colecciones, usuarios, permisos, procedimientos almacenados, desencadenadores y UDF, tienen todos un esquema fijo y se denominan recursos del sistema. En cambio, los recursos como documentos y los datos adjuntos no tienen restricciones de esquema y son ejemplos de recursos definidos por el usuario. En Cosmos DB, tanto los recursos del sistema como los del usuario se representan y controlan como JSON compatibles con el estándar. Todos los recursos, ya sean definidos por el sistema o el usuario, tienen las siguientes propiedades comunes:

> [!NOTE]
> Todas las propiedades que genera el sistema en un recurso tienen un prefijo con un guion bajo (_) en su representación de JSON.
> 
> 

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Propiedad</strong></p></td>
            <td valign="top"><p><strong>¿Configurable por el usuario o generada por el sistema?</strong></p></td>
            <td valign="top"><p><strong>Propósito</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Generado por el sistema</p></td>
            <td valign="top"><p>Identificador del recurso único y jerárquico que generó el sistema</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Generado por el sistema</p></td>
            <td valign="top"><p>etag del recurso necesario para un control optimista de concurrencia</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Generado por el sistema</p></td>
            <td valign="top"><p>Última actualización de la marca de tiempo del recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Generado por el sistema</p></td>
            <td valign="top"><p>URI del recurso única y direccionable</p></td>
        </tr>
        <tr>
            <td valign="top"><p>id</p></td>
            <td valign="top"><p>Es posible usar el</p></td>
            <td valign="top"><p>Nombre único del recurso (con el mismo valor de la clave de partición) que define el usuario. Si el usuario no especifica un identificador, el sistema generará uno.</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Representación de conexión de los recursos
Cosmos DB no exige ninguna extensión propietaria a la norma JSON ni codificaciones especiales; funciona con documentos JSON según la norma.  

### <a name="addressing-a-resource"></a>Direccionamiento de un recurso
Todos los recursos se pueden diseccionar mediante URI. El valor de la propiedad **self** de un recurso representa el URI relativo del recurso. El formato del URI consta de los segmentos de ruta /\<feed\>/{_rid}:  

| Valor de _self | DESCRIPCIÓN |
| --- | --- |
| /dbs |Fuente de bases de datos que hay en una cuenta de base de datos |
| /dbs/{dbName} |Base de datos con un identificador que coincide con el valor {dbName} |
| /dbs/{dbName}/colls/ |Fuente de colecciones que hay en una base de datos |
| /dbs/{dbName}/colls/{collName} |Colección con un identificador que coincide con el valor {collName} |
| /dbs/{dbName}/colls/{collName}/docs |Fuente de documentos que hay en una colección |
| /dbs/{dbName}/colls/{collName}/docs/{docId} |Documento con un identificador que coincide con el valor {doc} |
| /dbs/{dbName}/users/ |Fuente de usuarios que hay en una base de datos |
| /dbs/{dbName}/users/{userId} |Usuario con un identificador que coincide con el valor {user} |
| /dbs/{dbName}/users/{userId}/permissions |Fuente de permisos que hay en un usuario |
| /dbs/{dbName}/users/{userId}/permissions/{permissionId} |Permiso con un identificador que coincide con el valor {permission} |

Cada recurso tiene un nombre único que define el usuario y que se expone con la propiedad id. Nota: en el caso de los documentos, si el usuario no especifica un identificador, los SDK generarán automáticamente un identificador único para el documento. El identificador es una cadena que define el usuario y que está formada por 256 caracteres como máximo; asimismo, es única dentro del contexto de un recurso principal específico. 

Cada recurso tiene también un identificador de recursos jerárquico generado por el sistema (también denominado RID), que está disponible a través de la propiedad _rid. El RID codifica toda la jerarquía de un recurso dado y es una representación interna muy útil que se usa para imponer una integridad referencial de manera distribuida. El RID es único en una cuenta de base de datos y se usa internamente a través de la Cosmos DB para un enrutamiento eficaz, sin necesidad de búsquedas en las particiones. Los valores de las propiedades _self y _rid son representaciones alternativas y canónicas de un recurso. 

Las API de REST admiten el direccionamiento de recursos y el enrutamiento de solicitudes por el identificador y por las propiedades de _rid.

## <a name="database-accounts"></a>Cuentas de la base de datos
Puede aprovisionar una o más cuentas de base de datos de Cosmos DB mediante su suscripción a Azure.

Puede crear y administrar cuentas de bases de datos de Cosmos DB desde Azure Portal en [http://portal.azure.com/](https://portal.azure.com/). Crear y administrar una cuenta de base de datos requiere acceso administrativo y solo se puede realizar con su suscripción de Azure. 

### <a name="database-account-properties"></a>Propiedades de la cuenta de base de datos
Como parte del aprovisionamiento y control de una cuenta de base de datos, puede configurar y leer las siguientes propiedades:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Nombre de la propiedad</strong></p></td>
            <td valign="top"><p><strong>Descripción</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Directiva de coherencia</p></td>
            <td valign="top"><p>Establezca esta propiedad para configurar el nivel de coherencia predeterminado para todas las colecciones con su cuenta de base de datos. Puede reemplazar el nivel de coherencia en función de la solicitud mediante el encabezado de solicitud [x-ms-consistency-level]. <p><p>Tenga en cuenta que esta propiedad solo se aplica a los <i>recursos que define el usuario</i>. Todos los recursos definidos por el sistema se configuran para que sean compatibles con lecturas/consultas con alta coherencia.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Claves de autorización</p></td>
            <td valign="top"><p>Estas son las claves maestras y de solo lectura principales y secundarias que proporcionan acceso administrativo a todos los recursos que hay en la cuenta de base de datos.</p></td>
        </tr>
    </tbody>
</table>

Además de aprovisionar, configurar y administrar la cuenta de base de datos desde Azure Portal, también puede crear y administrar mediante programación cuentas de bases de datos de Cosmos DB mediante las [API de REST de Azure Cosmos DB](/rest/api/documentdb/) y los [SDK de cliente](sql-api-sdk-dotnet.md).  

## <a name="databases"></a>Bases de datos
Una base de datos de Cosmos DB es un contenedor lógico de una o varias colecciones o usuarios, como se muestra en el diagrama siguiente. Puede crear cualquier número de bases de datos en la cuenta de base de datos de Cosmos DB en función de los límites de la oferta.  

![Modelo jerárquico de colecciones y cuenta de base de datos][2]  
**Una base de datos es un contenedor lógico de usuarios y colecciones**

Una base de datos puede contener almacenamiento de documentos prácticamente ilimitado y particionado en colecciones.

### <a name="elastic-scale-of-an-azure-cosmos-db-database"></a>Escala elástica de una base de datos de Azure Cosmos DB
Una base de datos de Cosmos DB es elástica de forma predeterminada, desde unos pocos GB hasta petabytes de almacenamiento de documentos respaldado y capacidad de rendimiento aprovisionada de SSD. 

A diferencia de una base de datos en RDBMS tradicional, una base de datos en Cosmos DB no está dirigida a un único equipo. Con Cosmos DB, a medida que aumenten las necesidades de escalado de la aplicación, podrá crear más colecciones, bases de datos o ambas. De hecho, varias aplicaciones propias dentro de Microsoft han usado Azure Cosmos DB a nivel de consumidor creando bases de datos de Azure Cosmos DB extremadamente grandes, que contienen cada una miles de colecciones con terabytes de almacenamiento de documentos. Puede ampliar o reducir una base de datos agregando o eliminando colecciones para cumplir con los requisitos de escala de su aplicación. 

Puede crear cualquier número de colecciones dentro de una base de datos en función de la oferta. Cada colección tiene almacenamiento con copia de seguridad en SSD y el rendimiento aprovisionado para usted según el nivel de rendimiento seleccionado.

Una base de datos de Aure Cosmos DB también es un contenedor de usuarios. Un usuario, a su vez, es un espacio de nombres lógico para un conjunto de permisos que proporciona autorización y acceso detallado a colecciones, documentos y datos adjuntos.  

Como sucede con otros recursos del modelo de recursos de Azure Cosmos DB, las bases de datos se pueden crear, reemplazar, eliminar, leer o enumerar fácilmente mediante las [API de REST](/rest/api/documentdb/) o cualquier [SDK de cliente](sql-api-sdk-dotnet.md). Azure Cosmos DB garantiza una gran coherencia para leer o consultar los metadatos de un recurso de base de datos. Eliminar una base de datos automáticamente garantiza que no puede obtener acceso a ninguna colección o usuario contenidos en la misma.   

## <a name="collections"></a>Colecciones
Una colección de Cosmos DB es un contenedor de sus documentos JSON. 

### <a name="elastic-ssd-backed-document-storage"></a>Almacenamiento de documentos respaldado con SSD elástico
Una colección es elástica de forma intrínseca; crece y se reduce automáticamente a medida que agrega o elimina documentos. Las colecciones son recursos lógicos y pueden abarcar una o varios servidores o particiones físicos. El número de particiones que hay en una colección lo determina Cosmos DB basándose en el tamaño de almacenamiento y el rendimiento aprovisionado de la colección. Todas las particiones de Cosmos DB tienen una cantidad fija de almacenamiento con respaldo SSD asociado y se replican para ofrecer una alta disponibilidad. Azure Cosmos DB se encarga de administrar las particiones de principio a fin, por lo que no tendrá que escribir ningún código complejo ni administrar ninguna partición. Las colecciones de Cosmos DB ofrecen funcionalidades de almacenamiento y rendimiento **prácticamente ilimitadas** . 

### <a name="automatic-indexing-of-collections"></a>Indexación automática de las colecciones
Azure Cosmos DB es un verdadero sistema de bases de datos libre de esquemas. No asume ni requiere ningún esquema para los documentos JSON. A medida que agrega documentos a una colección, Azure Cosmos DB los indexa automáticamente y están disponibles para que los consulte. La indexación automática de documentos sin requerir un esquema ni índices secundarios es una capacidad clave de Azure Cosmos DB y se habilita mediante técnicas de mantenimiento de índices de escritura optimizada, sin bloqueos y estructuradas por registros. Azure Cosmos DB admite un volumen constante de operaciones de escritura muy rápidas mientras sigue dando servicio a consultas coherentes. Tanto el almacenamiento de documentos como de índices se utilizan para calcular el almacenamiento consumido por cada colección. Puede controlar el equilibrio entre almacenamiento y rendimiento asociado con la indexación configurando la directiva de indexación de una colección. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Configuración de la directiva de indexación de una colección
La directiva de indexación de cada colección le permite realizar equilibrios entre rendimiento y almacenamiento relacionados con la indexación. Tiene las siguientes opciones disponibles como parte de la configuración de indexación:  

* Seleccione si la colección indexa o no todos los documentos automáticamente. De forma predeterminada, se indexan todos los documentos automáticamente. Puede seleccionar desactivar la indexación automática y agregar únicamente documentos de forma selectiva al índice. Al contrario, puede seleccionar de forma selectiva excluir únicamente documentos específicos. Para ello, establezca la propiedad automática en verdadera o falsa en el elemento indexingPolicy de una colección y utilice el encabezado de solicitud [x-ms-indexingdirective] al insertar, reemplazar o eliminar un documento.  
* Seleccione si incluir o excluir rutas o patrones específicos del índice en sus documentos. Puede lograr esto configurando includedPaths y excludedPaths en el indexingPolicy de una colección, respectivamente. También puede configurar los equilibrios de almacenamiento y rendimiento para un intervalo y hash de consultas de patrones de ruta específicos. 
* Seleccione entre actualizaciones de índice sincrónica (consistente) y asincrónica (diferida). De forma predeterminada, el índice se actualiza de forma sincrónica con cada inserción, reemplazo o eliminación de un documento de la colección. Esto permite a las consultas respetar el mismo nivel de coherencia que el de las lecturas de documentos. Aunque Azure Cosmos DB está optimizada para escrituras y admite volúmenes sostenidos de escrituras de documentos junto con un mantenimiento sincrónico de índices y un servicio de consultas coherentes, se pueden configurar determinadas colecciones para actualizar el índice de manera diferida. La indexación diferida aumenta más el rendimiento de escritura y es ideal para casos de ingesta en bloque de colecciones principales con mucha carga de lectura.

La directiva de indexación puede cambiarse ejecutando un comando PUT en la colección. Esto se puede hacer mediante el [SDK de cliente](sql-api-sdk-dotnet.md), [Azure Portal](https://portal.azure.com) o las [API de REST](/rest/api/documentdb/).

### <a name="querying-a-collection"></a>Consulta de una colección
Los documentos de una colección pueden tener esquemas arbitrarios y puede consultar documentos de una colección sin proporcionar por adelantando ningún esquema o índice secundario. Puede consultar la colección mediante la [referencia de sintaxis de SQL de Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn782250.aspx) que proporciona numerosos operadores jerárquicos, relacionales y espaciales, así como una gran extensibilidad a través de las UDF basadas en JavaScript. La gramática de JSON permite el modelado de documentos JSON como árboles con etiquetas como los nodos del árbol. Esto lo aprovechan tanto las técnicas de indexación automática de SQL API como el dialecto de SQL de Azure Cosmos DB. El lenguaje de consulta de SQL consta de tres aspectos principales:   

1. Un pequeño conjunto de operaciones de consulta que se asignan de forma natural a la estructura de árbol, incluiidas las consultas jerárquicas y las proyecciones. 
2. Un subconjunto de operaciones relacionales que incluye la composición, el filtro, las proyecciones, los agregados y las autocombinaciones. 
3. UDF basadas puramente en JavaScript que se funcionan con (1) y (2)  

El modelo de consulta de Azure Cosmos DB intenta lograr un equilibrio entre funcionalidad, eficacia y simplicidad. El motor de la base de datos de Azure Cosmos DB compila y ejecuta instrucciones de consultas SQL de forma nativa. Puede consultar una colección mediante las [API de REST](/rest/api/documentdb/) o cualquier [SDK de cliente](sql-api-sdk-dotnet.md). El SDK de .NET viene con un proveedor LINQ.

> [!TIP]
> Puede probar la SQL API y ejecutar consultas de SQL en nuestro conjunto de datos en el [Área de consultas](https://www.documentdb.com/sql/demo).
> 
> 

## <a name="multi-document-transactions"></a>Transacciones de documentos múltiples
Las transacciones de la base de datos proporcionan un modelo de programación seguro y predecible para tratar cambios concurrentes en los datos. En RDBMS, la forma tradicional de escribir lógica empresarial es escribir **procedimientos almacenados** o **desencadenadores** y enviarlos al servidor de la base de datos para la ejecución transaccional. En RDBMS, es necesario el programador de aplicaciones para tratar con dos lenguajes de programación dispares: 

* El lenguaje de programación de aplicaciones (no transaccional) como, por ejemplo, JavaScript, Python, C#, Java, etc.
* T-SQL, el lenguaje de programación transaccional que ejecuta la base de datos de forma nativa.

Debido a su profundo compromiso con JavaScript y JSON directamente dentro del motor de la base de datos, Azure Cosmos DB proporciona un modelo de programación intuitivo para ejecutar una lógica de aplicaciones basada en JavaScript directamente en las colecciones según procedimientos almacenados y desencadenadores. Esto permite realizar las dos acciones siguientes:

* Implementación eficaz del control simultáneo, la recuperación, la indexación automática de los gráficos de objeto JSON directamente en el motor de la base de datos.
* Expresión de forma natural del flujo de control, el ámbito de las variables, la asignación y la integración de primitivos de control de excepciones con transacciones de bases de datos directamente en términos del lenguaje de programación de JavaScript.

La lógica de JavaScript registrada a nivel de colección puede entonces emitir operaciones de base de datos en los documentos de la colección dada. Azure Cosmos DB ajusta los procedimientos almacenados y desencadenadores basados en JavaScript dentro de transacciones ACID ambientales con un aislamiento de instantáneas en los documentos de una colección. Durante el transcurso de esta ejecución, si JavaScript lanza una excepción, entonces se cancela toda la transacción. El modelo de programación resultante es muy sencillo, pero eficaz. Los desarrolladores de JavaScript obtienen un modelo de programación “duradero” mientras siguen utilizando sus construcciones de lenguaje y primitivos de biblioteca familiares.   

La capacidad de ejecutar JavaScript directamente en el motor de la base de datos en el mismo espacio de dirección que el grupo de búferes, permite la ejecución transaccional y con mayor rendimiento de las operaciones de base de datos en los documentos de una colección. Además, debido a que el motor de base de datos de Cosmos DB tiene un fuerte compromiso con JSON y JavaScript, elimina cualquier error de coincidencia de impedancia entre los sistemas tipo de la aplicación y la base de datos.   

Tras crear una colección, puede registrar procedimientos almacenados, desencadenadores y UDF con una colección mediante las [API de REST](/rest/api/documentdb/) o cualquier [SDK de cliente](sql-api-sdk-dotnet.md). Tras el registro, puede hacer referencia a los mismos y ejecutarlos. Tenga en cuenta el siguiente procedimiento almacenado escrito completamente en JavaScript, el código siguiente toma dos argumentos (nombre del libro y nombre del autor) y crea un nuevo documento, consulta un documento y lo actualiza; todo en una transacción ACID implícita. En cualquier punto de la ejecución, si se lanza una excepción de JavaScript, se cancelará toda la transacción.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);

                        context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

El cliente puede "enviar" la lógica de JavaScript anterior a la base de datos para la ejecución transaccional mediante POST HTTP. Para obtener más información acerca del uso de los métodos HTTP, consulte [Interacciones RESTful con recursos de Azure Cosmos DB](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Tenga en cuenta que debido a que la base de datos comprende JSON y JavaScript de forma nativa, no existe ningún de error de coincidencia del sistema de tipo, no es necesaria ninguna "asignación OR" ni magia de generación de códigos.   

Los procedimientos almacenados y desencadenadores interactúan con una colección y los documentos de la misma a través de un modelo de objetos bien definido que se expone al contexto de colección actual.  

Las colecciones de SQL API se pueden crear, eliminar, leer o enumerar fácilmente con las [API de REST](/rest/api/documentdb/) o con cualquier [SDK de cliente](sql-api-sdk-dotnet.md). SQL API siempre proporciona una alta coherencia para leer o consultar los metadatos de una colección. Eliminar una colección automáticamente garantiza que no puede obtener acceso a ningún documento, dato adjunto, procedimiento almacenado, desencadenador o UDF contenido en la misma.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF)
Como se describe en la sección anterior, puede escribir lógica de aplicación para ejecutarla directamente en una transacción dentro del motor de base de datos. La lógica de la aplicación se puede escribir totalmente en JavaScript y se puede modelar como un procedimiento almacenado, desencadenador o UDF. El código de JavaScript de un procedimiento almacenado o desencadenador puede insertar, reemplazar, eliminar, leer o consultar documentos de una colección. Por otro lado, el código JavaScript de una UDF no puede insertar, reemplazar o eliminar documentos. Las UDF enumeran los documentos del conjunto de resultados de una consulta y producen otro conjunto de resultados. Para los servicios multiinquilino, Azure Cosmos DB impone una estricta gobernanza de los recursos basada en las reservas. Cada procedimiento almacenado, desencadenador o UDF obtiene un cuanto de recursos del sistema operativo para realizar su trabajo. Además, los procedimientos almacenados, los desencadenadores o las UDF no se pueden vincular con bibliotecas externas de JavaScript y están en la lista negra si superan el presupuesto de recursos que se les asignó. Puede registrar y anular el registro de los procedimientos almacenados, los desencadenadores o las UDF de una colección mediante las API de REST.  Tras el registro de un procedimiento almacenado, desencadenador o UDF, se compila de forma previa y almacena como código de bytes que se ejecutará más tarde. La siguiente sección ilustra cómo puede utilizar el SDK de JavaScript de Azure Cosmos DB para registrar, ejecutar o anular el registro de un procedimiento almacenado, un desencadenador o una UDF. El SDK de JavaScript es un contenedor sencillo de las [API de REST](/rest/api/documentdb/). 

### <a name="registering-a-stored-procedure"></a>Registro de un procedimiento almacenado
El registro de un procedimiento almacenado crea un nuevo recurso de procedimiento almacenado en una colección mediante POST HTTP.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();

            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };

    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>Ejecución de un procedimiento almacenado
La ejecución de un procedimiento almacenado se realiza emitiendo un POST HTTP en un recurso de procedimiento almacenado existente pasando parámetros al procedimiento en el cuerpo de la solicitud.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Anulación del registro de un procedimiento almacenado
Anular el registro de un procedimiento almacenado es sencillo mediante la emisión de una solicitud HTTP DELETE en un recurso de procedimiento almacenado existente.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Registro de un desencadenador previo
El registro de un desencadenador se realiza creando un nuevo recurso de desencadenador en una colección mediante POST HTTP. Puede especificar si el desencadenador es previo o posterior y el tipo de operación con la que se puede asociar; por ejemplo, Crear, Reemplazar, Eliminar o Todas.   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }

    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>Ejecución de un desencadenador previo
La ejecución de un desencadenador se realiza especificando el nombre de un desencadenador existente en el momento de emitir la solicitud POST/PUT/DELETE de un recurso de documento mediante el encabezado de solicitud.  

    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Anulación del registro de un desencadenador previo
Anular el registro de un desencadenador es sencillo mediante la emisión de una solicitud HTTP DELETE en un recurso de desencadenador existente.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Registro de una UDF
El registro de una UDF se realiza creando un nuevo recurso de UDF en una colección mediante POST HTTP.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>Ejecución de una UDF como parte de la consulta
Una UDF se puede especificar como parte de la consulta SQL y se utiliza como una forma de ampliar el núcleo del [lenguaje de consultas SQL](sql-api-sql-query-reference.md).

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Anulación del registro de una UDF
Anular el registro de una UDF es sencillo mediante la emisión de una solicitud HTTP DELETE en un recurso de UDF existente.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Aunque los fragmentos de código anteriores mostraban el registro (POST), la anulación del registro (PUT), la lectura/enumeración (GET) y la ejecución (POST) mediante el [SDK de JavaScript](https://github.com/Azure/azure-documentdb-js), también puede usar las [API de REST](/rest/api/documentdb/) u otro [SDK de cliente](sql-api-sdk-dotnet.md). 

## <a name="documents"></a>Documentos
Puede insertar, reemplazar, eliminar, leer, enumerar y consultar documentos JSON arbitrarios de una colección. Azure Cosmos DB no impone ningún esquema y no requiere índices secundarios para ser compatible con las consultas de documentos en una colección. El tamaño máximo de un documento es de 2 MB.   

Al ser un servicio de base de datos totalmente abierto, Azure Cosmos DB no crea ningún tipo de datos especializados como, por ejemplo, la fecha y la hora, o codificaciones específicas para documentos JSON. Tenga en cuenta que Azure Cosmos DB no necesita ninguna convención JSON especial para codificar las relaciones entre varios documentos; la sintaxis SQL de Azure Cosmos DB proporciona operadores de consulta jerárquica y relacional eficaces para consultar y proyectar documentos sin ninguna anotación especial o necesidad de codificar relaciones entre documentos utilizando propiedades importantes.  

Tal como sucede con el resto de recursos, se pueden crear, reemplazar, eliminar, leer, enumerar y consultar documentos fácilmente mediante las API de REST o con cualquier [SDK de cliente](sql-api-sdk-dotnet.md). Al eliminar un documento se libera inmediatamente la cuota correspondiente a todos los datos adjuntos anidados. El nivel de coherencia de lectura de documentos sigue la Directiva de coherencia de la cuenta de base de datos. Esta directiva se puede reemplazar en función de la solicitud dependiendo de los requisitos de coherencia de datos de su aplicación. Al consultar documentos, la coherencia de lectura sigue el modo de indexación establecido en la colección. Para ser "coherente", sigue la Directiva de coherencia de la cuenta. 

## <a name="attachments-and-media"></a>Datos adjuntos y multimedia
Azure Cosmos DB le permite almacenar blobs binarios/multimedia en Azure Cosmos DB (2 GB por cuenta, como máximo) o en su propio almacén multimedia remoto. También le permite representar los metadatos de un archivo multimedia en términos de un documento especial llamado dato adjunto. Un dato adjunto en Azure Cosmos DB es un documento especial (JSON) que hace referencia al archivo multimedia/blob almacenado en cualquier parte. Un dato adjunto es sencillamente un documento especial que captura los metadatos (por ejemplo, ubicación, autor, etc.) de un archivo multimedia almacenado en un almacenamiento multimedia remoto. 

Considere una aplicación de lectura social que use Azure Cosmos DB para almacenar anotaciones manuscritas y metadatos como comentarios, texto resaltado, marcadores, clasificaciones, gustos y preferencias, etc., asociados a un libro electrónico o usuario determinado.   

* El contenido del propio libro se almacena en el almacenamiento multimedia disponible como parte de la cuenta de Azure Cosmos DB o de un almacén multimedia remoto. 
* Una aplicación puede almacenar los metadatos de cada usuario como un documento distinto; por ejemplo, los metadatos de Joe para book1 se almacenan en un documento con la referencia /colls/joe/docs/book1. 
* Los datos adjuntos que señalan las páginas de contenido del libro de un usuario se almacenan en el documento correspondiente; por ejemplo, /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2, etc. 

Tenga en cuenta que los ejemplos anteriores usan identificadores sencillos para transmitir la jerarquía de recursos. Se obtiene acceso a los recursos mediante las API REST a través de los identificadores de recurso únicos. 

En cuanto a los archivos multimedia gestionados por Azure Cosmos DB, la propiedad _media de los datos adjuntos hará referencia al archivo multimedia mediante su URI. Azure Cosmos DB garantizará la recolección de archivos multimedia no utilizados cuando se quiten todas las referencias pendientes. Azure Cosmos DB genera automáticamente el dato adjunto cuando carga nuevos archivos multimedia y rellena la propiedad _media para señalar al nuevo archivo multimedia agregado. Si almacena el archivo multimedia en un almacén de blobs remoto que gestiona usted mismo (por ejemplo, OneDrive, Azure Storage, DropBox etc), puede seguir utilizando los datos adjuntos para hacer referencia a los archivos multimedia. En este caso, creará los datos adjuntos usted mismo y rellenará la propiedad _media.   

Tal como sucede con el resto de recursos, se pueden crear, reemplazar, eliminar, leer o enumerar datos adjuntos fácilmente mediante las API de REST o con cualquier SDK de cliente. Como con los documentos, el nivel de coherencia de lectura de datos adjuntos sigue la Directiva de coherencia en la cuenta de base de datos. Esta directiva se puede reemplazar en función de la solicitud dependiendo de los requisitos de coherencia de datos de su aplicación. Al consultar datos adjuntos, la coherencia de lectura sigue el modo de indexación establecido en la colección. Para ser "coherente", sigue la Directiva de coherencia de la cuenta. 
 

## <a name="users"></a>Usuarios
Un usuario de Azure Cosmos DB representa un espacio de nombres lógico para agrupar permisos. Un usuario de Azure Cosmos DB puede corresponder a un usuario en un sistema de administración de identidades o a un rol de aplicaciones predefinido. En Azure Cosmos DB, un usuario simplemente representa una abstracción para agrupar un conjunto de permisos de una base de datos.   

Para implementar servicios multiinquilino en una aplicación, puede crear usuarios en Azure Cosmos DB que correspondan a sus usuarios o inquilinos de la aplicación. Entonces puede crear permisos para un usuario determinado que se corresponda con el control de acceso de varias colecciones, documentos, datos adjuntos, etc.   

A medida que sus aplicaciones necesiten escalar con el crecimiento de su usuario, puede adoptar varias formas de particionar sus datos. Puede modelar cada usuario de la siguiente forma:   

* Cada usuario se asigna a una base de datos.
* Cada usuario se asigna a una colección. 
* Los documentos que corresponden a varios usuarios van a una colección dedicada. 
* Los documentos que corresponden a varios usuarios van a un conjunto de colecciones.   

Independientemente de la estrategia de partición específica que seleccione, puede modelar a los usuarios reales como usuarios de base de datos de Azure Cosmos DB y asociar permisos detallados a cada usuario.  

![Colecciones de usuario][3]  
**Estrategias de partición y modelado de usuarios**

Tal como sucede con el resto de recursos, los usuarios de Azure Cosmos DB se pueden crear, reemplazar, eliminar, leer o enumerar fácilmente mediante las API de REST o con cualquier SDK de cliente. Azure Cosmos DB siempre proporciona una alta coherencia para leer o consultar los metadatos de un recurso de usuario. Es importante señalar que eliminar un usuario garantiza automáticamente que no podrá obtener acceso a ningún permiso contenido en el mismo. Incluso aunque Azure Cosmos DB reclame la cuota de permisos como parte del usuario eliminado en segundo plano, los permisos eliminados estarán de nuevo disponibles al instante para que los use.  

## <a name="permissions"></a>Permisos
Desde la perspectiva del control de acceso, los recursos como las cuentas de base de datos, las bases de datos, los usuarios y los permisos se consideran recursos *administrativos*, puesto que requieren permisos administrativos. Por otro lado, los recursos que incluyen colecciones, documentos, datos adjuntos, procedimientos almacenados, desencadenadores y UDF se dirigen a una base de datos dada y se consideran *recursos de aplicación*. Conforme a los dos tipos de recursos y a los roles a los que obtienen acceso (a saber, el administrador y usuario), el modelo de autorización define dos tipos de *claves de acceso*: *clave maestra* y *clave de recursos*. La clave maestra forma parte de la cuenta de base de datos y se proporciona al desarrollador (o administrador) que está aprovisionando la cuenta de base de datos. Esta clave maestra tiene semánticas de administrador y se pueden utilizar para autorizar acceso a recursos tanto administrativos como de aplicación. Al contrario, una clave de recurso es una clave de acceso granular que permite el acceso a recursos de aplicación *específicos* . Por lo tanto, se captura la relación entre el usuario de una base de datos y los permisos que tiene el usuario para un recurso específico (por ejemplo, colección, documento, elemento adjunto, procedimiento almacenado, desencadenador o UDF).   

La única forma de obtener una clave de recurso es creando un recurso de permiso para un usuario determinado. Tenga en cuenta que para poder crear o recuperar un permiso, se debe presentar una clave maestra en el encabezado de autorización. Un recurso de permiso está relacionado con el recurso, su acceso y el usuario. Tras crear un recurso de permiso, el usuario solo necesita presentar la clave de recurso asociada para obtener acceso al recurso relevante. Así, una clave de recurso se puede visualizar como una representación lógica y compacta del recurso de permiso.  

Tal como sucede con el resto de recursos, los permisos de Azure Cosmos DB se pueden crear, reemplazar, eliminar, leer o enumerar fácilmente mediante las API de REST o con cualquier SDK de cliente. Azure Cosmos DB siempre proporciona una coherencia para leer o consultar los metadatos de un permiso. 

## <a name="next-steps"></a>pasos siguientes
Más información sobre cómo trabajar con recursos usando comandos HTTP en [Interacciones RESTful con recursos de Azure Cosmos DB](https://msdn.microsoft.com/library/azure/mt622086.aspx).

[1]: media/sql-api-resources/resources1.png
[2]: media/sql-api-resources/resources2.png
[3]: media/sql-api-resources/resources3.png

