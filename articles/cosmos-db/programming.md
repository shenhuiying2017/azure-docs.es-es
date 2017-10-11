---
title: "Programación de JavaScript en el lado del servidor de Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información sobre cómo usar Azure Cosmos DB para escribir procedimientos almacenados, desencadenadores de base de datos y funciones definidas por el usuario en JavaScript. Obtenga sugerencias de programación de base de datos y mucho más."
keywords: Desencadenadores de base de datos, procedimiento almacenado, procedimiento almacenado, programa de base de datos, sproc, documentdb, azure, Microsoft azure
services: cosmos-db
documentationcenter: 
author: aliuy
manager: jhubbard
editor: mimig
ms.assetid: 0fba7ebd-a4fc-4253-a786-97f1354fbf17
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: andrl
ms.openlocfilehash: 8cddc7a8c9aa677b9c93bee3a7e05c226cc1f655
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-cosmos-db-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Programación en el servidor de Azure Cosmos DB: procedimientos almacenados, desencadenadores de base de datos y funciones definidas por el usuario
Conozca cómo la ejecución transaccional integrada del lenguaje de Azure Cosmos DB de JavaScript permite a los desarrolladores escribir **procedimientos almacenados**, **desencadenadores** y **funciones definidas por el usuario (UDF)** de forma nativa en un elemento de JavaScript [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/). Esto permite escribir la lógica de aplicación del programa de base de datos que se puede enviar y ejecutar directamente en las particiones de almacenamiento de base de datos. 

Se recomienda comenzar con el vídeo siguiente, en el que Andrew Liu ofrece una breve introducción al modelo de programación de base de datos en el servidor de Cosmos DB. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Demo-A-Quick-Intro-to-Azure-DocumentDBs-Server-Side-Javascript/player]
> 
> 

A continuación, vuelva a este artículo, donde conocerá las respuestas a las preguntas siguientes:  

* ¿Cómo se escribe un procedimiento almacenado, un desencadenador o una UDF con JavaScript?
* ¿Qué garantías ACID ofrece Cosmos DB?
* ¿Cómo funcionan las transacciones en Cosmos DB?
* ¿Qué son los desencadenadores previos y posteriores y cómo se escriben?
* ¿Cómo se registran y se ejecutan un procedimiento almacenado, un desencadenador o una UDF de forma compatible con REST mediante HTTP?
* ¿Qué SDK de Cosmos DB está disponible para crear y ejecutar procedimientos almacenados, desencadenadores y funciones definidas por el usuario?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Introducción a la programación con UDF y procedimientos almacenados
Este enfoque de *"JavaScript como un T-SQL moderno"* libera a los desarrolladores de aplicaciones de las complejidades de los errores de coincidencia del sistema de tipo y de las tecnologías de asignación relacional de objetos. También cuenta con un número de ventajas intrínsecas que se pueden utilizar para generar sofisticadas aplicaciones:  

* **Lógica de procedimientos:** JavaScript como un lenguaje de programación de alto nivel, proporciona una interfaz completa y familiar para expresar la lógica empresarial. Puede realizar secuencias complejas de operaciones acercándose más a los datos.
* **Transacciones atómicas:** Cosmos DB garantiza que las operaciones de base de datos realizadas dentro de un único procedimiento almacenado o desencadenador sean atómicas. Esto permite a una aplicación combinar operaciones relacionadas en un único lote para que todas se realicen correctamente o no lo haga ninguna. 
* **Rendimiento**: el hecho de que JSON se asigne intrínsecamente al sistema de tipo de lenguaje de Javascript y que también sea la unidad básica de almacenamiento en Cosmos DB permite un número de optimizaciones como la materialización diferida de documentos JSON en el grupo de búferes y hacerlos disponibles bajo demanda para el código de ejecución. Hay más ventajas de rendimiento asociadas con el envío de la lógica empresarial a la base de datos:
  
  * Procesamiento por lotes: los desarrolladores pueden agrupar operaciones como inserciones y enviarlas en masa. El coste de la latencia de tráfico de red y la sobrecarga de almacenamiento para crear transacciones independientes se reducen de forma significativa. 
  * Precompilación: Cosmos DB precompila procedimientos almacenados, desencadenadores y funciones definidas por el usuario para evitar el coste de compilación de JavaScript en cada invocación. La sobrecarga de generar el código de byte para la lógica de procedimiento se amortiza en un valor mínimo.
  * Secuenciación: muchas operaciones necesitan un efecto secundario (“desencadenador”) que implica potencialmente realizar una o más operaciones de almacenamiento secundarias. Aparte de la atomicidad, tiene mayor rendimiento cuando se mueve al servidor. 
* **Encapsulación:** los procedimientos almacenados se pueden utilizar para agrupar la lógica empresarial en un lugar. Esto tiene dos ventajas:
  * Agrega una capa de abstracción en la parte superior de los datos sin procesar, lo cual permite a los arquitectos de datos desarrollar sus aplicaciones de forma independiente de los datos. Esto supone una especial ventaja cuando los datos no tienen esquema, debido a débiles suposiciones que se deben integrar en la aplicación si tienen que tratar directamente con los datos.  
  * Esta abstracción permite a las empresas mantener seguros sus datos simplificando el acceso desde los scripts.  

Se admite la creación y ejecución de operadores de consulta personalizados, procedimientos almacenados y desencadenadores de base de datos mediante la [API de REST](/rest/api/documentdb/), [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases) y los [SDK de cliente](documentdb-sdk-dotnet.md) de muchas plataformas, como .NET, Node.js y JavaScript.

En este tutorial se usa el [SDK de Node.js con objetos Q promise](http://azure.github.io/azure-documentdb-node-q/) para mostrar la sintaxis y el uso de procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF).   

## <a name="stored-procedures"></a>Procedimientos almacenados
### <a name="example-write-a-simple-stored-procedure"></a>Ejemplo: creación de un procedimiento almacenado sencillo
Comencemos con un sencillo procedimiento almacenado que devuelve una respuesta “Hello World”.

    var helloWorldStoredProc = {
        id: "helloWorld",
        serverScript: function () {
            var context = getContext();
            var response = context.getResponse();

            response.setBody("Hello, World");
        }
    }


Los procedimientos almacenados se registran por colección y pueden funcionar en cualquier documento y dato adjunto presente en esa colección. En el siguiente fragmento se muestra cómo registrar el procedimiento almacenado Hola mundo con una colección. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


Una vez que se registre el procedimiento almacenado, podemos ejecutarlo con la colección y leer los resultados en el cliente. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


El objeto de contexto proporciona acceso a todas las operaciones que se pueden realizar en el almacenamiento de Cosmos DB, así como acceso a los objetos de solicitud y respuesta. En este caso, hemos utilizado el objeto de respuesta para establecer el cuerpo de la respuesta que se ha devuelto al cliente. Para más información, consulte la [documentación del SDK del lado del servidor JavaScript de Azure Cosmos DB](http://azure.github.io/azure-documentdb-js-server/).  

Permítanos ampliar este ejemplo y agregar más funcionalidad relacionada con la base de datos al procedimiento almacenado. Los procedimientos almacenados pueden crear, actualizar, leer, consultar y eliminar documentos y datos adjuntos de la colección.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Ejemplo: escritura de un procedimiento almacenado para crear un documento
En el siguiente fragmento, se muestra cómo utilizar el objeto de contexto para que interactúe con los recursos de Cosmos DB.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        serverScript: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


Este procedimiento almacenado toma como entrada documentToCreate, el cuerpo de un documento que se va a crear en la colección actual. Todas estas operaciones son asíncronas y dependen de las devoluciones de llamadas de función de JavaScript. La función de devolución de llamada tiene dos parámetros, uno para el objeto de error en caso de que falle la operación y otro para el objeto creado. Dentro de la devolución de llamada, los usuarios pueden controlar la excepción o lanzar un error. En caso de que no se proporcione una devolución de llamada y haya un error, el sistema en tiempo de ejecución de Azure Cosmos DB produce un error.   

En el ejemplo anterior, la devolución de llamada lanza un error si falló la operación. De lo contrario, establece el identificador del documento creado como el cuerpo de la respuesta al cliente. A continuación se explica cómo se ejecuta este procedimiento almacenado con parámetros de entrada.

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;

            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };

            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });


Tenga en cuenta que este procedimiento almacenado se puede modificar para tomar una matriz de cuerpos de documentos como la entrada y crearlos todos en la misma ejecución del procedimiento almacenado en lugar de en varias solicitudes de red para crear cada uno individualmente. Esto se puede utilizar para implementar un importador masivo eficiente para Cosmos DB, algo que se tratará más adelante en este tutorial.   

El ejemplo descrito ha demostrado cómo utilizar procedimientos almacenados. Más tarde trataremos los desencadenadores y las funciones definidas por el usuario (UDF) en el tutorial.

## <a name="database-program-transactions"></a>Transacciones del programa de base de datos
Una transacción en una base de datos típica se puede definir como una secuencia de operaciones realizadas como una única unidad lógica de trabajo. Cada transacción proporciona **garantías ACID**. ACID es un acrónimo conocido que, por sus siglas en inglés, hace referencia a cuatro propiedades: Atomicidad, Coherencia, Aislamiento y Durabilidad.  

Brevemente, la atomicidad garantiza que todo el trabajo realizado dentro de una transacción se lea como una única unidad donde se confirma todo o nada. La Coherencia asegura que los datos se encuentran siempre en buen estado interno en todas las transacciones. El Aislamiento garantiza que dos transacciones no pueden interferir entre ellas; generalmente, la mayoría de los sistemas comerciales proporcionan varios niveles de aislamiento que se pueden utilizar según las necesidades de aplicación. La Durabilidad asegura que cualquier cambio que esté confirmado en la base de datos estará siempre presente.   

En Cosmos DB, JavaScript está hospedado en el mismo espacio de memoria que la base de datos. Por lo tanto, las solicitudes realizadas dentro de los procedimientos almacenados y desencadenadores se ejecutan en el mismo ámbito de una sesión de base de datos. Esto permite a Cosmos DB garantizar ACID para todas las operaciones que formen parte de un único procedimiento almacenado/desencadenador. Considere la siguiente definición de procedimiento almacenado:

    // JavaScript source code
    var exchangeItemsSproc = {
        id: "exchangeItems",
        serverScript: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();

            var player1Document, player2Document;

            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);

                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];

                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });

            if (!accept) throw "Unable to read player details, abort ";

            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;

                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";

                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });

                        if (!accept2) throw "Unable to update player 2, abort";
                    });

                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }

    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

Este procedimiento almacenado utiliza transacciones con una aplicación de juegos para intercambiar elementos entre dos jugadores en una única operación. El procedimiento almacenado intenta leer dos documentos, cada uno de ellos corresponde al identificador del jugador que se ha pasado como un argumento. Si se encuentran ambos documentos de jugador, entonces el procedimiento almacenado actualiza los documentos intercambiando sus elementos. Si se produce cualquier error durante el proceso, lanza una excepción de JavaScript que de forma implícita cancela la transacción.

Si la colección en la que se encuentra registrado el procedimiento almacenado es de partición única, la transacción estará en el ámbito de todos los documentos de la colección. Si la colección tiene particiones, los procedimientos almacenados se ejecutan en el ámbito de transacción de una clave de partición única. Por tanto, cada ejecución de procedimientos almacenados debe incluir un valor de clave de partición que se corresponda con el ámbito en que debe ejecutarse la transacción. Para más información, consulte [Creación de particiones con Azure Cosmos DB](partition-data.md).

### <a name="commit-and-rollback"></a>Confirmación y reversión
Las transacciones están integradas de forma profunda y nativa en el modelo de programación de JavaScript de Cosmos DB. Dentro de una función de JavaScript, todas las operaciones se ajustan automáticamente en una única transacción. Si el JavaScript se completa sin excepciones, se confirman las operaciones en la base de datos. De hecho, las instrucciones “BEGIN TRANSACTION” y “COMMIT TRANSACTION” en las bases de datos relacionales están implícitas en Cosmos DB.  

Si existe cualquier excepción que se propague desde el script, el sistema en tiempo de ejecución de JavaScript de Cosmos DB revertirá toda la transacción. Como se muestra en el ejemplo anterior, iniciar una excepción es un equivalente efectivo de “ROLLBACK TRANSACTION” en Cosmos DB.

### <a name="data-consistency"></a>Coherencia de datos
Los procedimientos almacenados y los desencadenadores se ejecutan siempre en la réplica principal del contenedor de Azure Cosmos DB. Esto garantiza que las lecturas desde dentro de los procedimientos almacenados ofrecen una fuerte coherencia. Las consultas que utilizan funciones definidas por el usuario se pueden ejecutar en la réplica principal o en cualquier réplica secundaria, pero nos aseguramos de cumplir con el nivel de coherencia solicitado seleccionando la réplica adecuada.

## <a name="bounded-execution"></a>Ejecución vinculada
Todas las operaciones de Cosmos DB se deben completar dentro de la duración del tiempo de espera de la solicitud especificada. Esta restricción también se aplica a las funciones de JavaScript (procedimientos almacenados, desencadenadores y funciones definidas por el usuario). Si una operación no se completa dentro de ese límite de tiempo, la transacción se revierte. Las funciones de JavaScript deben finalizar dentro del límite de tiempo o implementar un modelo basado en la continuación en el lote o reanudar la ejecución.  

Con el fin de simplificar el desarrollo de los procedimientos almacenados y los desencadenadores para controlar los límites de tiempo, todas las funciones del objeto de colección (para crear, leer, reemplazar y eliminar documentos y datos adjuntos) devuelven un valor booleano que representa si se completará la operación. Si este valor es falso, es un indicador de que el límite de tiempo está a punto de cumplirse y de que el procedimiento debe concluir la ejecución.  Se garantiza la finalización de las operaciones en cola anteriores a la primera operación de almacenamiento no aceptada si el procedimiento almacenado se completa a tiempo y no pone en cola más solicitudes.  

Las funciones de JavaScript también se vinculan al consumo de recursos. Cosmos DB reserva la capacidad de proceso por colección en función del tamaño aprovisionado de una cuenta de base de datos. La capacidad de proceso se expresa en términos de una unidad de CPU normalizada, consumo de memoria y E/S llamadas unidades de solicitud o RU. Las funciones de JavaScript pueden utilizar potencialmente un gran número de RU en poco tiempo y podrían obtener una limitación de frecuencia si se alcanza el límite de la colección. Los procedimientos almacenados que utilizan muchos recursos también podrían ponerse en cuarentena para garantizar la disponibilidad de operaciones de base de datos primitivas.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Ejemplo: importación masiva de datos a un programa de base de datos
A continuación se muestra un ejemplo de un procedimiento almacenado que se escribe en documentos de importación masiva de una colección. Observe cómo controla el procedimiento almacenado la ejecución vinculada comprobando el valor de devolución booleano de createDocument y, a continuación, utiliza el recuento de documentos insertados en cada invocación del procedimiento almacenado para realizar un seguimiento y reanudar el progreso en todos los lotes.

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();

        // The count of imported docs, also used as current doc index.
        var count = 0;

        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");

        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }

        // Call the create API to create a document.
        tryCreate(docs[count], callback);

        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);

            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }

        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;

            // One more document has been inserted, increment the count.
            count++;

            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a> Desencadenadores de base de datos
### <a name="database-pre-triggers"></a>Desencadenadores previos de base de datos
Cosmos DB proporciona desencadenadores que se ejecutan o desencadenan por una operación en un documento. Por ejemplo, puede especificar un desencadenador previo al crear un documento; este desencadenador previo se ejecutará antes de crear el documento. A continuación se muestra un ejemplo de cómo se pueden utilizar desencadenadores previos para validar las propiedades de un documento que se está creando:

    var validateDocumentContentsTrigger = {
        id: "validateDocumentContents",
        serverScript: function validate() {
            var context = getContext();
            var request = context.getRequest();

            // document to be created in the current operation
            var documentToCreate = request.getBody();

            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }

            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


Y el código de registro del cliente de Node.js correspondiente para el desencadenador:

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };

            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };

            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


Los desencadenadores previos no pueden tener parámetros de entrada. El objeto solicitado se puede utilizar para manipular el mensaje de solicitud asociado con la operación. Aquí, el desencadenador previo se está ejecutando con la creación de un documento y el cuerpo del mensaje de solicitud contiene el documento que se va a crear en formato JSON.   

Cuando se registran los desencadenadores, los usuarios pueden especificar las operaciones que se pueden ejecutar con ellos. Este desencadenador se ha creado con TriggerOperation.Create, lo que significa que no se permite lo siguiente.

    var options = { preTriggerInclude: "validateDocumentContents" };

    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });

    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Desencadenadores anteriores de base de datos
Los desencadenadores posteriores, del mismo modo que los previos, se asocian con una operación de un documento y no aceptan parámetros de entrada. Se ejecutan **después** de que se haya completado la operación y tienen acceso al mensaje de respuesta que se envía al cliente.   

El siguiente ejemplo muestra desencadenadores posteriores en acción:

    var updateMetadataTrigger = {
        id: "updateMetadata",
        serverScript: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();

            // document that was created
            var createdDocument = response.getBody();

            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";

            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';

                         var metadataDocument = documents[0];

                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                            
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


El desencadenador se puede registrar como se muestra en el siguiente ejemplo.

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };

            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };

            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


Este desencadenador consulta el documento de metadatos y lo actualiza con detalles del documento recién creado.  

Es importante tener en cuenta la ejecución **transaccional** de los desencadenadores en Cosmos DB. Este desencadenador posterior se ejecuta como parte de la misma transacción cuando se crea el documento original. Por lo tanto, si lanzamos una excepción desde el desencadenador posterior (en caso de que no podamos actualizar el documento de metadatos), fallará y se revertirá toda la transacción. No se creará ningún documento y se devolverá una excepción.  

## <a id="udf"></a>Funciones definidas por el usuario
Las funciones definidas por el usuario se utilizan para ampliar la gramática del lenguaje de consultas SQL de la API de DocumentDB e implementar la lógica empresarial personalizada. Solo se las puede llamar desde consultas internas. No tienen acceso al objeto de contexto y se supone que se deben utilizar como un JavaScript únicamente de cálculo. Por lo tanto, las funciones definidas por el usuario se pueden ejecutar en réplicas secundarias del servicio Cosmos DB.  

En el siguiente ejemplo, se crea una UDF para calcular la base imponible basada en tipos para varios niveles de renta y, a continuación, se usa dentro de una consulta para buscar a todas las personas que pagan más de 20.000 $ en impuestos.

    var taxUdf = {
        id: "tax",
        serverScript: function tax(income) {

            if(income == undefined) 
                throw 'no input';

            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


La UDF se puede utilizar de forma consecuente en consultas como en el ejemplo siguiente:

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);

            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>API de consulta integradas en lenguajes JavaScript
Además de emitir consultas mediante la gramática de SQL del DocumentDB, el SDK del lado servidor permite realizar consultas optimizadas a través de una interfaz fluida de JavaScript sin necesitar conocimientos de SQL. La API de consulta de JavaScript permite crear mediante programación las consultas al pasar las funciones de predicado a función encadenada, con una sintaxis familiar para los elementos integrados de matriz de ECMAScript5 y las bibliotecas populares de JavaScript, como lodash. Las consultas se analizan con el tiempo de ejecución de JavaScript para que se ejecuten eficazmente mediante índices de Azure Cosmos DB.

> [!NOTE]
> `__` (subrayado doble) es un alias para `getContext().getCollection()`.
> <br/>
> En otras palabras, puede utilizar `__` o `getContext().getCollection()` para obtener acceso a la API de consulta de JavaScript.
> 
> 

Estas son algunas de las funciones compatibles:

<ul>
<li>
<b>chain() ... .value([callback] [, options])</b>
<ul>
<li>
Inicia una llamada encadenada que debe terminarse con value().
</li>
</ul>
</li>
<li>
<b>filter(predicateFunction [, options] [, callback])</b>
<ul>
<li>
Filtra la entrada usando una función de predicado que devuelve True o False para filtrar los documentos de entrada y salida en el conjunto resultante. Este comportamiento es similar al de una cláusula WHERE de SQL.
</li>
</ul>
</li>
<li>
<b>map(transformationFunction [, options] [, callback])</b>
<ul>
<li>
Se aplica una proyección dado que se trata de una función de transformación que asigna cada elemento de entrada a un valor u objeto de JavaScript. Este comportamiento es similar al de una cláusula SELECT de SQL.
</li>
</ul>
</li>
<li>
<b>pluck([propertyName] [, options] [, callback])</b>
<ul>
<li>
Se trata de un acceso directo a una asignación que extrae el valor de una propiedad única de cada elemento de entrada.
</li>
</ul>
</li>
<li>
<b>flatten([isShallow] [, options] [, callback])</b>
<ul>
<li>
Combina y reduce las matrices de cada elemento de entrada en una sola matriz. Este comportamiento es similar a SelectMany de LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy([predicate] [, options] [, callback])</b>
<ul>
<li>
Genera un nuevo conjunto de documentos al clasificarlos en orden ascendente en la secuencia de documentos de entrada según el predicado especificado. Este comportamiento es similar al de una cláusula ORDER BY de SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending([predicate] [, options] [, callback])</b>
<ul>
<li>
Genera un nuevo conjunto de documentos al clasificarlos en orden descendente en la secuencia de documentos de entrada según el predicado especificado. Este comportamiento es similar al de una cláusula ORDER BY x DESC de SQL.
</li>
</ul>
</li>
</ul>


Cuando se incluye dentro del predicado o las funciones selectoras, las siguientes construcciones de JavaScript se optimizan automáticamente para ejecutarse directamente en índices de Azure Cosmos DB:

* Operadores simples: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Literales, incluido el literal de objeto: {}
* var, return

Las siguientes construcciones de JavaScript no se optimizan para índices de Azure Cosmos DB:

* Control de flujo (por ejemplo: if, for, while)
* Llamadas a funciones

Para obtener más información, consulte [Server-Side JSDocs](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Ejemplo: escribir un procedimiento almacenado mediante la API de consulta de JavaScript
El ejemplo de código siguiente es un ejemplo de cómo se puede usar la API de consulta de JavaScript en el contexto de un procedimiento almacenado. El procedimiento almacenado inserta un documento, proporcionado por un parámetro de entrada y actualiza un documento de metadatos, mediante el método `__.filter()` , con los valores minSize, maxSize y totalSize basados en la propiedad de tamaño del documento de entrada.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>Hoja de referencia de SQL a Javascript
En la tabla siguiente se muestran varias consultas SQL con las consultas de JavaScript correspondientes.

Como sucede con las consultas SQL, las claves de propiedad del documento (por ejemplo, `doc.id`) distinguen mayúsculas de minúsculas.

|SQL| API de consulta de JavaScript|Descripción siguiente|
|---|---|---|
|SELECT *<br>FROM docs| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;return doc;<br>});|1|
|SELECT docs.id, docs.message AS msg, docs.actions <br>FROM docs|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|2|
|SELECT *<br>FROM docs<br>WHERE docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|3|
|SELECT *<br>FROM docs<br>WHERE ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|4|
|SELECT docs.id, docs.message AS msg<br>FROM docs<br>WHERE docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|5|
|SELECT VALUE tag<br>FROM docs<br>JOIN tag IN docs.Tags<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.Tags && Array.isArray(doc.Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|6|

En las descripciones siguientes se explican las consultas de la tabla anterior.
1. Devuelve resultados de todos los documentos (paginados con el token de continuación) tal y como están.
2. Proyecta el id., el mensaje (con el alias msg) y la acción de todos los documentos.
3. Realiza consultas de los documentos con el predicado: id = "X998_Y998".
4. Realiza consultas de los documentos que tengan una propiedad Tags que sea una matriz que contiene el valor 123.
5. Realiza consultas de los documentos con un predicado, id = "X998_Y998", y, después, proyecta el id. y el mensaje (con el alias msg).
6. Filtra los documentos que tienen una propiedad de matriz, Tags, y ordena los documentos resultantes por la propiedad del sistema _ts timestamp; después, proyecta + flattens en la matriz Tags.


## <a name="runtime-support"></a>Compatibilidad con el tiempo de ejecución
La [API del lado servidor de JavaScript de DocumentDB](http://azure.github.io/azure-documentdb-js-server/) ofrece compatibilidad con la mayoría de las características del lenguaje JavaScript habituales, según el estándar [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Seguridad
Los procedimientos almacenados y desencadenadores de JavaScript se encuentran en un espacio aislado para que los efectos de un script no se filtren al otro sin pasar por el aislamiento de la transacción de instantánea en el nivel de la base de datos. Los entornos de tiempo de ejecución se agrupan pero se borran del contexto tras cada ejecución. Por lo tanto se garantiza su seguridad de cualquier efecto secundario no intencionado entre ellos.

### <a name="pre-compilation"></a>Precompilación
Los procedimientos almacenados, desencadenadores y UDF se precompilan implícitamente en formato de código byte para evitar los costes de compilación en el momento de cada invocación de script. Esto garantiza que las invocaciones de los procedimientos almacenados son rápidos y tienen poca superficie.

## <a name="client-sdk-support"></a>Compatibilidad con SDK de cliente
Además de la API de DocumentDB para el cliente de [Node.js](documentdb-sdk-node.md), Azure Cosmos DB tiene SDK de [.NET](documentdb-sdk-dotnet.md), [.NET Core](documentdb-sdk-dotnet-core.md), [Java](documentdb-sdk-java.md), [JavaScript](http://azure.github.io/azure-documentdb-js/) y [Python](documentdb-sdk-python.md) para la API de DocumentDB. Los procedimientos almacenados, desencadenadores y UDF también se pueden crear y ejecutar mediante cualquiera de estos SDK. En el siguiente ejemplo se muestra cómo crear y ejecutar un procedimiento almacenado mediante el cliente.NET. Observe cómo los tipos de .NET se pasan al procedimiento almacenado como JSON y se vuelven a leer.

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    

                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }

                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
             }"
    };

    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;

    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "sproc"), document, 1920);


En este ejemplo se muestra cómo usar la [API de .NET para DocumentDB](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) para crear un desencadenador previo y generar un documento con el desencadenador habilitado. 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };

    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


Y en el siguiente ejemplo se muestra cómo crear una función definida por el usuario para usarla en una [consulta de SQL de la API de DocumentDB](documentdb-sql-query.md).

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };

    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>API de REST
Todas las operaciones de Azure Cosmos DB se pueden realizar mediante RESTful. Los procedimientos almacenados, desencadenadores y funciones definidas por el usuario se pueden registrar en una colección mediante POST HTTP. El siguiente es un ejemplo de cómo registrar un procedimiento almacenado:

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT


    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


El procedimiento almacenado se registra ejecutando una solicitud POST con el URI dbs/testdb/colls/testColl/sprocs conteniendo en el cuerpo el procedimiento almacenado que se va a crear. Los desencadenadores y las UDF se pueden registrar de forma similar mediante la emisión de una solicitud POST con respecto a /triggers y /udfs, respectivamente.
Este procedimiento almacenado se puede ejecutar mediante la emisión de una solicitud POST en su vínculo de recursos:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Aquí, la entrada del procedimiento almacenado se pasa al cuerpo de la solicitud. Tenga en cuenta que la entrada se pasa como una matriz JSON de parámetros de entrada. El procedimiento almacenado toma la primera entrada como un documento que es un cuerpo de respuesta. La respuesta que recibimos es como la siguiente:

    HTTP/1.1 200 OK

    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Los desencadenadores, a diferencia de los procedimientos almacenados, no se pueden ejecutar directamente. En su lugar, se ejecutan como parte de una operación en un documento. Podemos especificar los desencadenadores que se van a ejecutar con una solicitud mediante los encabezados de HTTP. La siguiente es una solicitud para crear un documento.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger


    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


Aquí el desencadenador previo que se debe ejecutar con la solicitud se especifica en el encabezado x-ms-documentdb-pre-trigger-include. Del mismo modo, cualquier desencadenador posterior se da en el encabezado x-ms-documentdb-post-trigger-include. Tenga en cuenta que tanto los desencadenadores previos como los posteriores se pueden especificar para una solicitud determinada.

## <a name="sample-code"></a>Código de ejemplo
Puede encontrar más ejemplos de código del lado servidor (entre los que se incluyen [bulk-delete](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js) y [update](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) en nuestro [repositorio de GitHub](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

¿Desea compartir el impresionante procedimiento almacenado? Envíenos una solicitud de extracción. 

## <a name="next-steps"></a>Pasos siguientes
Una vez que haya almacenado uno o varios procedimientos, y creado desencadenadores y funciones definidas por el usuario, puede cargarlos y verlos en Azure Portal mediante el Explorador de datos.

También puede encontrar útiles las siguientes referencias y recursos en su camino hacia el aprendizaje de la programación del servidor de Azure Cosmos DB:

* [SDK de Azure Cosmos DB](documentdb-sdk-dotnet.md)
* [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
* [JSON](http://www.json.org/) 
* [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Extensibilidad de la base de datos segura y portátil](http://dl.acm.org/citation.cfm?id=276339) 
* [Arquitectura de base de datos orientada a servicios](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
* [Hospedaje de runtime de .NET en Microsoft SQL Server](http://dl.acm.org/citation.cfm?id=1007669)

