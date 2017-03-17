---
title: "Usar las API de MongoDB para compilar una aplicación de DocumentDB | Microsoft Docs"
description: "Tutorial de NoSQL que crea una base de datos en línea mediante las API de DocumentDB para MongoDB."
keywords: ejemplos de mongodb
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 867ec5d0b27e790f3b00c94a4e5d14e4b2b17f73
ms.lasthandoff: 03/08/2017


---
# <a name="build-a-documentdb-api-for-mongodb-app-using-nodejs"></a>Crear una aplicación de DocumentDB: API para MongoDB con Node.js
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [Node.js para MongoDB](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
>

En este ejemplo se muestra cómo crear una aplicación de consola de DocumentDB: API de para MongoDB con Node.js.

Para usar este ejemplo, tendrá que:

* [Crear](documentdb-create-mongodb-account.md) una cuenta de DocumentDB: API para MongoDB de Azure.
* Recuperar información de la [cadena de conexión](documentdb-connect-mongodb-account.md) de MongoDB.

## <a name="create-the-app"></a>Creación de la aplicación

1. Cree un archivo *app.js* y copie y pegue el código siguiente.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```

2. Modifique las siguientes variables en el archivo *app.js* según la configuración de la cuenta (aprenda a buscar la [cadena de conexión](documentdb-connect-mongodb-account.md)):
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';
    ```
     
3. Abra su terminal favorito, ejecute **npm install mongodb --save** y ejecute luego su aplicación con **node app.js**.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo [usar MongoChef](documentdb-mongodb-mongochef.md) con la cuenta de DocumentDB: API para MongoDB.

