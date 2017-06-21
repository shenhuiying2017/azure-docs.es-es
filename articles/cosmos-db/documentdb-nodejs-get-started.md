---
title: Tutorial de Node.js para la API de DocumentDB de Azure Cosmos DB | Microsoft Docs
description: Tutorial de Node.js en el que se crea una instancia de Cosmos DB con la API de DocumentDB.
keywords: tutorial de Node.js, base de datos de nodos
services: cosmos-db
documentationcenter: node.js
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: 14d52110-1dce-4ac0-9dd9-f936afccd550
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: hero-article
ms.date: 05/23/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: fef21c512aa8a6af32cec20e2e44cf4e20ac24ae
ms.contentlocale: es-es
ms.lasthandoff: 06/07/2017


---
# <a name="nodejs-tutorial-documentdb-nodejs-console-application"></a>Tutorial de Node.js: aplicación de consola de Node.js para DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js para MongoDB](mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Le damos la bienvenida al tutorial de Node.js para el SDK de Node.js de Azure Cosmos DB. Después de seguir este tutorial, tendrá una aplicación de consola que crea recursos de Azure Cosmos DB y los consulta.

Describiremos:

* Creación de una cuenta de Azure Cosmos DB y conexión a ella
* Configuración de la aplicación
* Creación de una base de datos de nodos
* Creación de una colección
* Creación de documentos JSON
* Consulta de la colección
* Sustitución de un documento
* Eliminación de un documento
* Eliminación de la base de datos de nodos

¿No tiene tiempo? ¡No se preocupe! La solución completa está disponible en [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Consulte [Obtención de la solución completa](#GetSolution) para obtener instrucciones rápidas.

Después de haber completado el tutorial de Node.js, use los botones de votos en la parte superior e inferior de esta página para enviar comentarios. Si quiere que nos pongamos en contacto directamente con usted, puede incluir su dirección de correo electrónico en los comentarios.

Comencemos.

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Requisitos previos para el tutorial de Node.js
Asegúrese de que dispone de lo siguiente:

* Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
    * Como alternativa, en este tutorial puede usar el [Emulador de Azure Cosmos DB](local-emulator.md).
* [Node.js](https://nodejs.org/) versión v0.10.29 o superior

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Paso 1: Creación de una cuenta de Azure Cosmos DB
Vamos a crear una cuenta de Azure Cosmos DB. Si ya tiene una cuenta que desea usar, puede ir directamente a [Configuración de la aplicación de Node.js](#SetupNode). Si usa el Emulador de Azure Cosmos DB, siga los pasos que se indican en [Emulador de Azure Cosmos DB](local-emulator.md) para configurar el emulador y vaya directamente a [Configuración de la aplicación de Node.js](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Paso 2: Configuración de la aplicación de Node.js
1. Abra su terminal favorito.
2. Busque la carpeta o el directorio donde desea guardar la aplicación de Node.js.
3. Cree dos archivos de JavaScript vacíos con los siguientes comandos:
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```
4. Instale el módulo documentdb mediante npm. Use el comando siguiente:
   * ```npm install documentdb --save```

Estupendo. Ahora que terminamos la configuración, comencemos a escribir algo de código.

## <a id="Config"></a>Paso 3: Configuración de las opciones de la aplicación
Abra ```config.js``` en el editor de texto que prefiera.

Después, copie y pegue el siguiente fragmento de código y defina las propiedades ```config.endpoint``` y ```config.primaryKey``` para el identificador URI del punto de conexión de DocumentDB y la clave principal. Ambas opciones de configuración se encuentran en el [Portal de Azure](https://portal.azure.com).

![Tutorial de Node.js: captura de pantalla de Azure Portal que muestra una cuenta de Azure Cosmos DB, con el centro ACTIVO resaltado, el botón CLAVES resaltado en la hoja de la cuenta de Azure Cosmos DB y los valores de URI, CLAVE PRINCIPAL y CLAVE SECUNDARIA resaltados en la hoja Claves (base de datos de nodos)][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Copie y pegue ```database id```, ```collection id``` y ```JSON documents``` en el objeto ```config``` siguiente, donde se definen las propiedades de ```config.endpoint``` y ```config.authKey```. Si ya dispone de los datos que desea almacenar en la base de datos, puede usar la [herramienta de migración de datos](import-data.md) de Azure Cosmos DB en lugar de agregar definiciones de documento.

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


Las definiciones de base de datos, colección y documento actuarán como ```database id```, ```collection id``` y datos de documentos de DocumentDB.

Por último, exporte el objeto ```config```, con el fin de que pueda hacer referencia a él en el archivo ```app.js```.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a>Paso 4: Conexión a una cuenta de Azure Cosmos DB
Abra el archivo ```app.js``` vacío en el editor de texto. Copie y pegue el código siguiente para importar el módulo ```documentdb``` y el módulo ```config``` que acaba de crear.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Copie y pegue el código para usar las propiedades ```config.endpoint``` y ```config.primaryKey``` que guardó anteriormente para crear un nuevo DocumentClient.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Ahora que tiene el código para inicializar el cliente de DocumentDB, veamos cómo trabajar con los recursos de DocumentDB.

## <a name="step-5-create-a-node-database"></a>Paso 5: Creación de una base de datos de nodos
Copie y pegue el código siguiente para definir el estado HTTP para No encontrado, la dirección URL de la base de datos y la dirección URL de la colección. A través de estas direcciones URL el cliente de DocumentDB encontrará la base de datos y la colección adecuadas.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

Para crear una [base de datos](documentdb-resources.md#databases), se puede usar la función [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la clase **DocumentClient**. Una base de datos es un contenedor lógico de almacenamiento de documentos particionado en recopilaciones.

Copie y pegue la función **getDatabase** para crear la base de datos nueva en el archivo app.js con el ```id``` especificado en el objeto ```config```. La función comprobará que no existe ninguna base de datos con el mismo identificador ```FamilyRegistry``` . Si existe, usaremos esa base de datos en lugar de crear una nueva.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copie y pegue el siguiente código donde defina la función **getDatabase** para agregar la función auxiliar **Salir** que imprimirá el mensaje de salida y la llamada a la función **getDatabase**.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque el archivo ```app.js``` y ejecute el comando: ```node app.js```

¡Enhorabuena! Ha creado correctamente una base de datos de Azure Cosmos DB.

## <a id="CreateColl"></a>Paso 6: Creación de una colección
> [!WARNING]
> **CreateDocumentCollectionAsync** creará una nueva colección, que tiene implicaciones de precios. Para obtener más información, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Para crear una [colección](documentdb-resources.md#collections), puede usar la función [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la clase **DocumentClient**. Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript.

Copie y pegue la función **getCollection** debajo de la función **getDatabase** del archivo app.js para crear la colección nueva con el ```id``` especificado en el objeto ```config```. Vuelva a comprobar que no existe ninguna colección con el mismo identificador ```FamilyCollection``` . Si existe, usaremos esa colección en lugar de crear una nueva.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copie y pegue el código de debajo de la llamada a **getDatabase** para ejecutar la función **getCollection**.

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque el archivo ```app.js``` y ejecute el comando: ```node app.js```

¡Enhorabuena! Ha creado correctamente una colección de DocumentDB.

## <a id="CreateDoc"></a>Paso 7: Creación de un documento
Para crear un [documento](documentdb-resources.md#documents), se puede usar la función [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la clase **DocumentClient**. Los documentos son contenido JSON definido por el usuario (arbitrario). Ahora puede insertar un documento en DocumentDB.

Copie y pegue la función **getFamilyDocument** debajo de la función **getCollection** para crear los documentos que contengan los datos JSON guardados en el objeto ```config```. Comprobaremos de nuevo que no existe ya un documento con el mismo identificador.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Copie y pegue el código de debajo de la llamada a **getCollection** para ejecutar la función **getFamilyDocument**.

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque el archivo ```app.js``` y ejecute el comando: ```node app.js```

¡Enhorabuena! Ha creado documentos de DocumentDB correctamente.

![Tutorial de Node.js: diagrama que muestra la relación jerárquica entre la cuenta, la base de datos, la colección y los documentos (base de datos de nodos)](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

## <a id="Query"></a>Paso 8: Consulta de los recursos de Azure Cosmos DB
Azure Cosmos DB admite [consultas](documentdb-sql-query.md) enriquecidas en los documentos JSON que se almacenan en las colecciones. El código de ejemplo siguiente muestra una consulta que se puede ejecutar en los documentos de la colección.

Copie y pegue la función **queryCollection** debajo de la función **getFamilyDocument** del archivo app.js. DocumentDB admite consultas del tipo SQL tal y como se muestra a continuación. Para más información sobre cómo crear consultas complejas, consulte [Query Playground](https://www.documentdb.com/sql/demo) y la [documentación sobre consultas](documentdb-sql-query.md).

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


El siguiente diagrama muestra cómo se llama a la sintaxis de la consulta SQL de DocumentDB en la colección creada.

![Tutorial de Node.js: diagrama que ilustra el ámbito y el significado de la consulta (base de datos de nodo).](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

La palabra clave [FROM](documentdb-sql-query.md#FromClause) es opcional en la consulta porque las consultas de DocumentDB ya tienen como ámbito una sola colección. Por lo tanto, «FROM Families f" se puede intercambiar por  "FROM root r", o cualquier otra variable de nombre que elija. DocumentDB deducirá la familia, la raíz o el nombre de variable elegido y hará referencia a la colección actual de forma predeterminada.

Copie y pegue el código de debajo de la llamada a **getFamilyDocument** para ejecutar la función **queryCollection**.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque el archivo ```app.js``` y ejecute el comando: ```node app.js```

¡Enhorabuena! Ha consultado correctamente documentos de Azure Cosmos DB.

## <a id="ReplaceDocument"></a>Paso 9: Reemplazo de un documento
Azure Cosmos DB admite la sustitución de documentos JSON.

Copie y pegue la función **replaceFamilyDocument** debajo de la función **queryCollection** del archivo app.js.

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copie y pegue el código de debajo de la llamada a **queryCollection** para ejecutar la función **replaceDocument**. Además, agregue el código para llamar a **queryCollection** de nuevo y comprobar que el documento ha cambiado correctamente.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque el archivo ```app.js``` y ejecute el comando: ```node app.js```

¡Enhorabuena! Ha sustituido correctamente un documento de Azure Cosmos DB.

## <a id="DeleteDocument"></a>Paso 10: Eliminación de un documento
Azure Cosmos DB admite la eliminación de documentos JSON.

Copie y pegue la función **deleteFamilyDocument** debajo de la función **replaceFamilyDocument**.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copie y pegue el código de debajo de la llamada a la segunda **queryCollection** para ejecutar la función **deleteDocument**.

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque el archivo ```app.js``` y ejecute el comando: ```node app.js```

¡Enhorabuena! Ha eliminado correctamente un documento de Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Paso 11: Eliminación de la base de datos de nodos
La eliminación de la base de datos creada quitará la base de datos y todos los recursos secundarios (colecciones, documentos, etc.).

Copie y pegue la función **cleanup** debajo de la función **deleteFamilyDocument** para quitar la base de datos y todos los recursos secundarios.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

Copie y pegue el código de debajo de la llamada a **deleteFamilyDocument** para ejecutar la función **cleanup**.

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>Paso 12: Ejecute íntegramente la aplicación Node.js
En conjunto, la secuencia para llamar a las funciones debe tener este aspecto:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque el archivo ```app.js``` y ejecute el comando: ```node app.js```

Ahora debería ver la salida de la aplicación GetStarted. La salida debe coincidir con el texto del ejemplo siguiente.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

¡Enhorabuena! Ha completado el tutorial de Node.js y tiene su primera aplicación de consola de Azure Cosmos DB.

## <a id="GetSolution"></a>Obtención de la solución completa del tutorial de Node.js
Si no dispuso de tiempo para completar los pasos de este tutorial, o simplemente desea descargar el código, puede obtenerlo de [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started).

Para ejecutar la solución GetStarted que contiene todos los ejemplos de este artículo, necesitará lo siguiente:

* [Cuenta de Azure Cosmos DB][create-account].
* La solución [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) está disponible en GitHub.

Instale el módulo **documentdb** mediante npm. Use el comando siguiente:

* ```npm install documentdb --save```

Después, en el archivo ```config.js``` , actualice los valores de config.endpoint y config.authKey tal como se describe en el [paso 3: Configuración de las opciones de la aplicación](#Config). 

Posteriormente, en el terminal, busque el archivo ```app.js``` y ejecute el comando: ```node app.js```.

Y, eso es todo, genérela y habrá terminado. 

## <a name="next-steps"></a>Pasos siguientes
* ¿Desea un ejemplo más complejo de Node.js? Consulte [Creación de una aplicación web de Node.js con Azure Cosmos DB](documentdb-nodejs-application.md).
* Información acerca de cómo [supervisar una cuenta de Azure Cosmos DB](monitor-accounts.md).
* Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).
* Obtenga más información sobre el modelo de programación en la sección sobre desarrollo de la [página de documentación de Azure Cosmos DB](https://azure.microsoft.com/documentation/services/documentdb/).

[create-account]: create-documentdb-dotnet.md#create-account
[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

