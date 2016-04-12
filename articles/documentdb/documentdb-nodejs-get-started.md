<properties
	pageTitle="Tutorial de NoSQL para Node.js para DocumentDB | Microsoft Azure"
	description="Tutorial de NoSQL para Node.js que crea una aplicación de consola y la base de datos de nodos con el SDK de DocumentDB de Node.js. DocumentDB es una base de datos NoSQL para JSON."
    keywords="tutorial de Node.js, base de datos de nodos"
	services="documentdb"
	documentationCenter="node.js"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="hero-article" 
	ms.date="03/30/2016"
	ms.author="anhoh"/>

# Tutorial de NoSQL Node.js: aplicación de consola Node.js de DocumentDB  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Bienvenido al tutorial de Node.js para el SDK de Node.js de DocumentDB. Después de seguir este tutorial, tendrá una aplicación de consola que crea recursos de DocumentDB y realiza consultas en ellos, incluida una base de datos de nodos.

Describiremos:

- Creación y aprovisionamiento una cuenta de DocumentDB.
- Configuración de la aplicación
- Creación de una base de datos de nodos
- Creación de una colección
- Creación de documentos JSON
- Consulta de la colección
- Eliminación de la base de datos de nodos

¿No tiene tiempo? ¡No se preocupe! La solución completa está disponible en [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Consulte [Obtención de la solución completa](#GetSolution) para obtener instrucciones rápidas.

Después de haber completado el tutorial de Node.js, use los botones de votos en la parte superior e inferior de esta página para enviar comentarios. Si quiere que nos pongamos en contacto directamente con usted, puede incluir su dirección de correo electrónico en los comentarios.

Comencemos.

## Requisitos previos para el tutorial de Node.js

Asegúrese de que dispone de lo siguiente:

- Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Node.js](https://nodejs.org/) versión v0.10.29 o superior

## Paso 1: Creación de una cuenta de DocumentDB

Creemos una cuenta de DocumentDB. Si ya tiene una cuenta que desea usar, puede ir directamente a [Configuración de la aplicación de Node.js](#SetupNode).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> Paso 2: Configuración de la aplicación de Node.js

1. Abra su terminal favorito.
2. Busque la carpeta o el directorio donde desea guardar la aplicación de Node.js.
3. Cree dos archivos de JavaScript vacíos con los siguientes comandos:
	- Windows:    
	    * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
	- Linux/OS X: 
	    * ```touch app.js```
        * ```touch config.js```
4. Instale el módulo documentdb mediante npm. Use el comando siguiente:
    * ```npm install documentdb --save```

Estupendo. Ahora que terminamos la configuración, comencemos a escribir algo de código.

##<a id="Config"></a> Paso 3: Configuración de las opciones de la aplicación

Abra ```config.js``` en el editor de texto que prefiera.

Después, cree un objeto vacío llamado ```config``` y establezca las propiedades ```config.endpoint``` y ```config.authKey``` en el punto de conexión y en la clave de autorización de DocumentDB. Ambas opciones de configuración se encuentran en el [Portal de Azure](https://portal.azure.com).

![Tutorial de Node.js: captura de pantalla del Portal de Azure que muestra una cuenta de DocumentDB, con el concentrador ACTIVO resaltado, el botón CLAVES resaltado en la hoja de cuenta de DocumentDB y los valores URI, CLAVE PRINCIPAL y CLAVE SECUNDARIA resaltados en la hoja Claves (base de datos de nodos).][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

Ahora, agregue ```database id```, ```collection id``` y ```JSON documents``` al objeto ```config```. Debajo de donde estableció las propiedades ```config.endpoint``` y ```config.authKey```, agregue el código siguiente. Si ya dispone de datos que desea almacenar en la base de datos, puede usar la [herramienta de migración de datos](documentdb-import-data.md) de DocumentDB en lugar de agregar definiciones de documento.

    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl",
        "partitionKey": { "paths": ["/district"], "kind": "Hash" }
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "district": "WA5",
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
            "district": "NY23",
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

    module.exports = config;

##<a id="Connect"></a> Paso 4: Conexión a una cuenta de DocumentDB

Abra el archivo ```app.js``` vacío en el editor de texto. Importe el módulo ```documentdb``` y el módulo ```config``` recién creado.

    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

A continuación, use las propiedades ```config.endpoint``` y ```config.authKey``` que guardó anteriormente para crear un nuevo DocumentClient.

    var client = new documentClient(config.endpoint, { "masterKey": config.authKey });

Ahora que está conectado a una cuenta de DocumentDB, veamos cómo trabajar con los recursos de DocumentDB.

## Paso 5: Creación de una base de datos de nodos
Para crear una [base de datos](documentdb-resources.md#databases), se puede usar la función [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la clase **DocumentClient**. Una base de datos es un contenedor lógico de almacenamiento de documentos particionado en recopilaciones. Agregue una función para crear la nueva base de datos en el archivo app.js con ```id``` especificado en el objeto ```config```. En primer lugar, compruebe que no existe ninguna base de datos con el mismo identificador ```FamilyRegistry```. Si existe, usaremos esa base de datos en lugar de crear una nueva.

    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    /**
     * Get the database by ID, or create if it doesn't exist.
     * @param {string} database - The database to get or create
     */
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
##<a id="CreateColl"></a>Paso 6: Creación de una colección  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** creará una nueva colección, que tiene implicaciones de precios. Para obtener más información, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).

Para crear una [colección](documentdb-resources.md#collections), puede usar la función [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la clase **DocumentClient**. Una colección es un contenedor de documentos JSON y la lógica asociada de la aplicación JavaScript. Agregue una función para crear la nueva colección en el archivo app.js con ```id``` especificado en el objeto ```config```. Vuelva a comprobar que no existe ninguna colección con el mismo identificador ```FamilyCollection```. Si existe, usaremos esa colección en lugar de crear una nueva.

    /**
     * Get the collection by ID, or create if it doesn't exist.
     */
    function getCollection() {
        console.log(`Getting collection:\n${collection.id}\n`);

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

##<a id="CreateDoc"></a>Paso 7: Creación de un documento
Para crear un [documento](documentdb-resources.md#documents), se puede usar la función [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la clase **DocumentClient**. Los documentos son contenido JSON definido por el usuario (arbitrario). Ahora puede insertar un documento en DocumentDB.

A continuación, agregue una función a app.js para crear los documentos que contienen los datos en formato JSON guardados en el objeto ```config```. Comprobaremos de nuevo que no existe ya un documento con el mismo identificador.

    /**
     * Get the document by ID, or create if it doesn't exist.
     * @param {function} callback - The callback function on completion
     */
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

¡Enhorabuena! Ya tiene funciones para crear una base de datos, una colección y un documento en DocumentDB.

![Tutorial de Node.js: diagrama que muestra la relación jerárquica entre la cuenta, la base de datos, la colección y los documentos (base de datos de nodos)](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Paso 8: Consulta de recursos de DocumentDB

DocumentDB admite [consultas](documentdb-sql-query.md) enriquecidas contra los documentos JSON almacenados en cada colección. El código de ejemplo siguiente muestra una consulta que se puede ejecutar en los documentos de la colección. Agregue la función siguiente al archivo ```app.js```. DocumentDB admite consultas del tipo SQL tal y como se muestra a continuación. Para obtener más información sobre cómo crear consultas complejas, consulte [Query Playground](https://www.documentdb.com/sql/demo) y la [documentación sobre consultas](documentdb-sql-query.md).

    /**
     * Query the collection using SQL
     */
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.address.city FROM root r WHERE r.lastName = "Andersen"',
                { enableCrossPartitionQuery: true }
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        console.log(`Query returned ${queryResult}`);
                    }
                    resolve(results);
                }
            });
        });
    };


El siguiente diagrama muestra cómo se llama a la sintaxis de la consulta SQL de DocumentDB en la colección creada.

![Tutorial de Node.js: diagrama que ilustra el ámbito y el significado de la consulta (base de datos de nodo).](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

La palabra clave [FROM](documentdb-sql-query.md/#from-clause) es opcional en la consulta porque las consultas de DocumentDB ya tienen como ámbito una sola colección. Por lo tanto, «FROM Families f" se puede intercambiar por "FROM root r", o cualquier otra variable de nombre que elija. DocumentDB deducirá la familia, la raíz o el nombre de variable elegido y hará referencia a la colección actual de forma predeterminada.

##<a id="DeleteDatabase"></a>Paso 9: Eliminación de la base de datos de nodos

La eliminación de la base de datos creada quitará la base de datos y todos los recursos secundarios (colecciones, documentos, etc.). Para eliminar la base de datos, puede agregar el siguiente fragmento de código.

    /**
     * Cleanup the database and collection on completion
     */
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

##<a id="Build"></a>Paso 10: Integración

Ahora que ya están establecidas todas las funciones necesarias para su aplicación, vamos a llamarlas.

Agregue el siguiente fragmento de código al final del código en ```app.js```.

    /**
     * Exit the app with a prompt
     * @param {message} message - The message to display
     */
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
        .then(() => getCollection())
        .then(() => getFamilyDocument(config.documents.Andersen))
        .then(() => getFamilyDocument(config.documents.Wakefield))
        .then(() => queryCollection())
        .then(() => cleanup())
        .then(() => { exit(`Completed successfully`); })
        .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

##<a id="Run"></a> Paso 11: Configuración de la aplicación de Node.js

Ahora ya puede ejecutar la aplicación Node.js.

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
      Query returned Seattle

    Cleaning up by deleting database FamilyDB

    Completed successfully
    Press any key to exit

¡Enhorabuena! Ha completado el tutorial de Node.js y tiene su primera aplicación de consola de DocumentDB.

##<a id="GetSolution"></a> Obtener la solución completa del tutorial de Node.js
Para compilar la solución GetStarted que contiene todos los ejemplos de este artículo, necesitará lo siguiente:

-   [Cuenta de DocumentDB][documentdb-create-account].
-   La solución [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) está disponible en GitHub.

Instale el módulo **documentdb** mediante npm. Use el comando siguiente:
* ```npm install documentdb --save```

Después, en el archivo ```config.js```, actualice los valores de config.endpoint y config.authKey tal como se describe en el [paso 3: Configuración de las opciones de la aplicación](#Config).

## Pasos siguientes

-   ¿Desea un ejemplo más complejo de Node.js? Consulte [Compilación de una aplicación web Node.js mediante DocumentDB](documentdb-nodejs-application.md).
-	Aprenda a [supervisar una cuenta de DocumentDB](documentdb-monitor-accounts.md).
-	Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).
-	Obtenga más información sobre el modelo de programación en la sección sobre desarrollo de la [página de documentación de DocumentDB](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=AcomDC_0330_2016-->