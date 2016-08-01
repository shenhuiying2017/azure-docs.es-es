<properties 
	pageTitle="Ejemplos de DocumentDB para MongoDB | Microsoft Azure" 
	description="Buscar ejemplos de soporte de protocolo de DocumentDB para MongoDB" 
	keywords="ejemplos de mongodb"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

# Soporte de protocolo de DocumentDB para ejemplos de MongoDB
Para usar estos ejemplos, debe:

- [Cree](documentdb-create-mongodb-account.md) una cuenta de Azure DocumentDB con soporte de protocolo para MongoDB.
- Recuperar la cuenta de DocumentDB con soporte de protocolo para la información de [cadena de conexión](documentdb-connect-mongodb-account.md) de MongoDB.

## Introducción a una aplicación de ejemplo de lista de tareas de ASP.NET MVC

Puede usar el tutorial [Creación de una aplicación web de Azure que se conecta a MongoDB en una máquina virtual](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md), con una mínima modificación, para configurar rápidamente una aplicación de MongoDB (ya sea localmente o publicada en una aplicación web de Azure) que se conecte a una cuenta de DocumentDB con soporte de protocolo para MongoDB.

1. Siga las instrucciones del tutorial con una modificación. Reemplace el código de Dal.cs por lo siguiente:
	
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

2.	Modifique las variables siguientes en el archivo Dal.cs según la configuración de cuenta:

        private string userName = "<your user name>";
    	private string host = "<your host>";
        private string password = "<your password>";

3. Use la aplicación.

## Pasos siguientes

- Obtenga información sobre la [versión preliminar de las instrucciones de desarrollo](documentdb-mongodb-guidelines.md) para DocumentDB con soporte de protocolo para MongoDB.
- Obtenga información acerca de cómo [usar MongoChef](documentdb-mongodb-mongochef.md) con una cuenta de DocumentDB con compatibilidad de protocolo con MongoDB.

 

<!---HONumber=AcomDC_0720_2016-->