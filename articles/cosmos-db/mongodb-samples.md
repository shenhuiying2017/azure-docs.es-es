---
title: Uso de las API de MongoDB para compilar una aplicación de Azure Cosmos DB | Microsoft Docs
description: Tutorial que crea una base de datos en línea mediante las API de Azure Cosmos DB para MongoDB.
keywords: ejemplos de mongodb
services: cosmos-db
author: AndrewHoh
manager: kfile
editor: ''
documentationcenter: ''
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: anhoh
ms.openlocfilehash: 81eff479c94af938918e6a221d45184ca1a84aef
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Creación de una aplicación de Azure Cosmos DB: API para MongoDB con Node.js
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js para MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
>

En este ejemplo se muestra cómo crear una aplicación de consola de Azure Cosmos DB: API para MongoDB con Node.js.

Para usar este ejemplo, tendrá que:

* [Cree](create-mongodb-dotnet.md#create-account) una cuenta de Azure Cosmos DB: API para MongoDB.
* Recuperar información de la [cadena de conexión](connect-mongodb-account.md) de MongoDB.

## <a name="create-the-app"></a>Creación de la aplicación

1. Cree un archivo *app.js* y copie y pegue el código siguiente.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

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
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Opcional**: si usa el **controlador Node.js 2.2 de MongoDB**, reemplace el fragmento de código siguiente:

    Original:

    ```nodejs
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Se debe reemplazar por:

    ```nodejs
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
    
2. Modifique las siguientes variables en el archivo *app.js* según la configuración de la cuenta (aprenda a buscar la [cadena de conexión](connect-mongodb-account.md)):

    > [!IMPORTANT]
    > El **controlador Node.js 3.0 de MongoDB** requiere la codificación de caracteres especiales en la contraseña de Cosmos DB. Asegúrese de codificar los caracteres "=" como %3D
    >
    > Ejemplo: la contraseña *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* se codifica como *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > El **controlador Node.js 2.2 de MongoDB** no requiere la codificación de caracteres especiales en la contraseña de Cosmos DB.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Abra su terminal favorito, ejecute **npm install mongodb --save** y ejecute luego su aplicación con **node app.js**.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo [usar MongoChef](mongodb-mongochef.md) con una cuenta de Azure Cosmos DB: API para MongoDB.
