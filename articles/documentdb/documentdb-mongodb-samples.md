---
title: Ejemplos de DocumentDB para MongoDB | Microsoft Docs
description: Buscar ejemplos de soporte de protocolo de DocumentDB para MongoDB
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
ms.date: 11/28/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 8d4c3aaefee502e79dd1ea3e074937bb9a1d4593
ms.openlocfilehash: 703373c2c0090a2c6ffef3433aee3a05f0f41475


---
# <a name="documentdb-protocol-support-for-mongodb-examples"></a>Soporte de protocolo de DocumentDB para ejemplos de MongoDB
Para usar estos ejemplos, debe:

* [Cree](documentdb-create-mongodb-account.md) una cuenta de Azure DocumentDB con soporte de protocolo para MongoDB.
* Recuperar la cuenta de DocumentDB con soporte de protocolo para la información de [cadena de conexión](documentdb-connect-mongodb-account.md) de MongoDB.

## <a name="get-started-with-a-sample-nodejs-getting-started-app"></a>Primeros pasos con una aplicación de ejemplo de Node.js

1. Cree un archivo *app.js* y copie y pegue el código siguiente.

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

2. Modifique las siguientes variables en el archivo *app.js* según la configuración de la cuenta (aprenda a buscar la [cadena de conexión](documentdb-connect-mongodb-account.md)):
   
         var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';
     
3. Abra su terminal favorito, ejecute **npm install mongodb --save** y ejecute luego su aplicación con **node app.js**.

## <a name="get-started-with-a-sample-aspnet-mvc-task-list-application"></a>Introducción a una aplicación de ejemplo de lista de tareas de ASP.NET MVC
Puede usar el tutorial [Creación de una aplicación web de Azure que se conecta a MongoDB en una máquina virtual](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md) , con una mínima modificación, para configurar rápidamente una aplicación de MongoDB (ya sea localmente o publicada en una aplicación web de Azure) que se conecte a una cuenta de DocumentDB con soporte de protocolo para MongoDB.  

1. Siga las instrucciones del tutorial con una modificación.  Reemplace el código de Dal.cs por lo siguiente:
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using MyTaskListApp.Models;
        using MongoDB.Driver;
        using MongoDB.Bson;
        using System.Configuration;
        using System.Security.Authentication;
   
        namespace MyTaskListApp
        {
            public class Dal : IDisposable
            {
                //private MongoServer mongoServer = null;
                private bool disposed = false;
   
                // To do: update the connection string with the DNS name
                // or IP address of your server. 
                //For example, "mongodb://testlinux.cloudapp.net
                private string connectionString = "mongodb://localhost:27017";
                private string userName = "<your user name>";
                private string host = "<your host>";
                private string password = "<your password>";
   
                // This sample uses a database named "Tasks" and a 
                //collection named "TasksList".  The database and collection 
                //will be automatically created if they don't already exist.
                private string dbName = "Tasks";
                private string collectionName = "TasksList";
   
                // Default constructor.        
                public Dal()
                {
                }
   
                // Gets all Task items from the MongoDB server.        
                public List<MyTask> GetAllTasks()
                {
                    try
                    {
                        var collection = GetTasksCollection();
                        return collection.Find(new BsonDocument()).ToList();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<MyTask>();
                    }
                }
   
                // Creates a Task and inserts it into the collection in MongoDB.
                public void CreateTask(MyTask task)
                {
                    var collection = GetTasksCollectionForEdit();
                    try
                    {
                        collection.InsertOne(task);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
   
                private IMongoCollection<MyTask> GetTasksCollection()
                {
                    MongoClientSettings settings = new MongoClientSettings();
                    settings.Server = new MongoServerAddress(host, 10250);
                    settings.UseSsl = true;
                    settings.SslSettings = new SslSettings();
                    settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                    MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                    MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                    settings.Credentials = new List<MongoCredential>()
                    {
                        new MongoCredential("SCRAM-SHA-1", identity, evidence)
                    };
   
                    MongoClient client = new MongoClient(settings);
                    var database = client.GetDatabase(dbName);
                    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                    return todoTaskCollection;
                }
   
                private IMongoCollection<MyTask> GetTasksCollectionForEdit()
                {
                    MongoClientSettings settings = new MongoClientSettings();
                    settings.Server = new MongoServerAddress(host, 10250);
                    settings.UseSsl = true;
                    settings.SslSettings = new SslSettings();
                    settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                    MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                    MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                    settings.Credentials = new List<MongoCredential>()
                    {
                        new MongoCredential("SCRAM-SHA-1", identity, evidence)
                    };
                    MongoClient client = new MongoClient(settings);
                    var database = client.GetDatabase(dbName);
                    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                    return todoTaskCollection;
                }
   
                # region IDisposable
   
                public void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }
   
                protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                        }
                    }
   
                    this.disposed = true;
                }
   
                # endregion
            }
        }
2. Modifique las variables siguientes en el archivo Dal.cs según la configuración de cuenta:
   
         private string userName = "<your user name>";
         private string host = "<your host>";
         private string password = "<your password>";
3. Use la aplicación.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información acerca de cómo [usar MongoChef](documentdb-mongodb-mongochef.md) con una cuenta de DocumentDB con compatibilidad de protocolo con MongoDB.



<!--HONumber=Nov16_HO4-->


