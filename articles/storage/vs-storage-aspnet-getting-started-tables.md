---
title: "Introducción a Azure Table Storage y a Servicios conectados de Visual Studio (ASP.NET) | Microsoft Docs"
description: "Introducción al uso de Azure Table Storage en un proyecto ASP.NET en Visual Studio después de conectarse a una cuenta de almacenamiento mediante Servicios conectados de Visual Studio"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 07f827e13e5f01a373e69b90e8a5a0c72081acd0
ms.openlocfilehash: 24cfb3217dbadba3a086a8b0251efe5ab0173d1f


---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Introducción a Azure Table Storage y a Servicios conectados de Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Información general

El almacenamiento de tabla de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales.

En este artículo se describe cómo administrar entidades de Azure Table Storage mediante programación, realizar tareas comunes, como crear y eliminar una tabla, así como trabajar con entidades de tabla. 

> [!NOTE]
> 
> En las secciones de código de este artículo se supone que ya se ha conectado a una cuenta de almacenamiento de Azure mediante Servicios conectados. Para configurar Servicios conectados, abra el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto y, en el menú contextual, seleccione la opción **Agregar-> Servicio conectado**. Desde allí, siga las instrucciones del cuadro de diálogo para conectarse a la cuenta de almacenamiento de Azure que desee.      

## <a name="create-a-table-in-code"></a>Crear una tabla en el código

Los siguientes pasos muestran cómo crear una tabla mediante programación. En una aplicación ASP.NET MVC, el código iría en un controlador.

1. Agregue las siguientes directivas *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure. (Cambie *<storage-account-name>* por el nombre de la cuenta de almacenamiento de Azure a la que está accediendo).

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de tabla.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenga un objeto **CloudTable** que representa una referencia al nombre de tabla que desea. (Cambie * <table-name> * por el nombre de la tabla que desea crear).

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Llame a la **CloudTable.CreateIfNotExists** método para crear la tabla si aún no existe.   
   
        table.CreateIfNotExists();

## <a name="add-an-entity-to-a-table"></a>Adición de una entidad a una tabla

Los siguientes pasos muestran cómo agregar una entidad a una tabla mediante programación. En una aplicación ASP.NET MVC, el código iría en un controlador. 

1. Agregue las siguientes directivas *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure. (Cambie *<storage-account-name>* por el nombre de la cuenta de almacenamiento de Azure a la que está accediendo).

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de tabla.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenga un objeto **CloudTable** que representa una referencia al nombre de tabla que desea. (Cambie *<table-name> * por el nombre de la tabla a la que desea agregar la entidad).

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Para agregar una entidad a una tabla, defina una clase derivada de **TableEntity**. El código siguiente define una clase de entidad llamada **CustomerEntity** que usa el nombre del cliente como clave de fila y el apellido como clave de partición.

        public class CustomerEntity : TableEntity
        {
            public CustomerEntity(string lastName, string firstName)
            {
                this.PartitionKey = lastName;
                this.RowKey = firstName;
            }
    
            public CustomerEntity() { }
    
            public string Email { get; set; }
        }

6. Cree una instancia de la entidad.

        CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
        customer1.Email = "Walter@contoso.com";

7. Cree el objeto **TableOperation** que inserta la entidad del cliente.

        TableOperation insertOperation = TableOperation.Insert(customer1);

8. Ejecute la operación de inserción mediante una llamada al método **CloudTable.Execute**. Puede comprobar el resultado de la operación inspeccionando la propiedad **TableResult.HttpStatusCode**. Un código de estado de 2xx indica que la acción solicitada por el cliente se procesó correctamente. Por ejemplo, las inserciones correctas de las nuevas entidades dan lugar a un código de estado HTTP de 204, lo que significa que la operación se procesó correctamente y el servidor no devolvió ningún contenido.

        TableResult result = table.Execute(insertOperation);

        // Inspect result.HttpStatusCode for success/failure.

## <a name="add-a-batch-of-entities-to-a-table"></a>Incorporación de un lote de entidades a una tabla

Además de poder agregar una entidad a una tabla de una en una, también puede agregar entidades por lotes. Esto reduce el número de idas y vueltas entre el código y Azure Table service. En los pasos siguientes se muestra cómo agregar varias entidades a una tabla mediante programación con una única operación. En una aplicación ASP.NET MVC, el código iría en un controlador.

1. Agregue las siguientes directivas *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure. (Cambie *<storage-account-name>* por el nombre de la cuenta de almacenamiento de Azure a la que está accediendo).

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de tabla.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenga un objeto **CloudTable** que representa una referencia al nombre de tabla que desea. (Cambie *<table-name> * por el nombre de la tabla a la que desea agregar las entidades).

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Para agregar una entidad a una tabla, defina una clase derivada de **TableEntity**. El código siguiente define una clase de entidad llamada **CustomerEntity** que usa el nombre del cliente como clave de fila y el apellido como clave de partición.

        public class CustomerEntity : TableEntity
        {
            public CustomerEntity(string lastName, string firstName)
            {
                this.PartitionKey = lastName;
                this.RowKey = firstName;
            }
    
            public CustomerEntity() { }
    
            public string Email { get; set; }
        }

6. Cree una instancia de las entidades.

        CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
        customer1.Email = "Jeff@contoso.com";
    
        CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
        customer2.Email = "Ben@contoso.com";

7. Obtenga un objeto **TableBatchOperation**.

        TableBatchOperation batchOperation = new TableBatchOperation();

8. Agregue entidades a la operación de inserción de por lotes.

        batchOperation.Insert(customer1);
        batchOperation.Insert(customer2);

9. Ejecute la operación de inserción por lotes mediante una llamada al método **CloudTable.ExecuteBatch**. El método **CloudTable.ExecuteBatch** devuelve una lista de objetos **TableResult**. Puede comprobar el resultado de la operación de inserción por lotes inspeccionando la propiedad **TableResult.HttpStatusCode** de cada objeto **TableResult** de la lista. Un código de estado de 2xx indica que la acción solicitada por el cliente se procesó correctamente. Por ejemplo, las inserciones correctas de las nuevas entidades dan lugar a un código de estado HTTP de 204, lo que significa que la operación se procesó correctamente y el servidor no devolvió ningún contenido.
    
        IList<TableResult> results = table.ExecuteBatch(batchOperation);

        // Inspect the HttpStatusCode property of each TableResult object
        // in the results list for success/failure.

## <a name="get-a-single-entity"></a>Obtención de una sola entidad

Los siguientes pasos muestran cómo obtener una entidad de una tabla mediante programación. En una aplicación ASP.NET MVC, el código iría en un controlador. 

> [!NOTE]
> 
> El código de esta sección hace referencia a la clase **CustomerEntity** y a los datos presentados en la sección [Incorporación de un lote de entidades a una tabla](#add-a-batch-of-entities-to-a-table). 

1. Agregue las siguientes directivas *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure. (Cambie *<storage-account-name>* por el nombre de la cuenta de almacenamiento de Azure a la que está accediendo).

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de tabla.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenga un objeto **CloudTable** que representa una referencia al nombre de tabla que desea. (Cambie *<table-name> * por el nombre de la tabla a la que desea agregar las entidades).

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Cree un objeto de operación de recuperación que tome un objeto de entidad derivado de **TableEntity**. El primer parámetro es *partitionKey* y el segundo parámetro es *rowKey*. Mediante la clase **CustomerEntity** y los datos presentados en la sección [Incorporación de un lote de entidades a una tabla](#add-a-batch-of-entities-to-a-table), el fragmento de código siguiente consulta la tabla para una entidad **CustomerEntity** con un valor *partitionKey* de "Smith" y un valor *rowKey* de "Ben".  

        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

6. Ejecute la operación de recuperación.   

        TableResult retrievedResult = table.Execute(retrieveOperation);

7. Compruebe el resultado de la operación mediante la inspección de la propiedad **TableOperation.HttpStatusCode** donde un código de estado 200 indica que la acción solicitada por el cliente se procesó correctamente. También puede inspeccionar la propiedad **TableResult.Result** que contendrá la entidad devuelta (si la operación se realiza correctamente).

        CustomerEntity customer = null;

        if (retrievedResult.HttpStatusCode == 200 && retrievedResult.Result != null)
        {
            // Process the customer entity.
            customer = retrievedResult.Result as CustomerEntity;
        }

## <a name="get-all-entities-in-a-partition"></a>Obtención de todas las entidades de una partición

Los pasos siguientes ilustran muestran cómo obtener todas las entidades de una partición mediante programación. En una aplicación ASP.NET MVC, el código iría en un controlador. 

> [!NOTE]
> 
> El código de esta sección hace referencia a la clase **CustomerEntity** y a los datos presentados en la sección [Incorporación de un lote de entidades a una tabla](#add-a-batch-of-entities-to-a-table). 

1. Agregue las siguientes directivas *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure. (Cambie *<storage-account-name>* por el nombre de la cuenta de almacenamiento de Azure a la que está accediendo).

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de tabla.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenga un objeto **CloudTable** que representa una referencia al nombre de tabla que desea. (Cambie *<table-name> * por el nombre de la tabla a la que desea agregar las entidades).

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Cree una instancia de un objeto **TableQuery** especificando la consulta en la cláusula **Where**. Mediante la clase **CustomerEntity** y los datos presentados en la sección [Incorporación de un lote de entidades a una tabla](#add-a-batch-of-entities-to-a-table), el fragmento de código siguiente consulta la tabla para todas las entidades donde **PartitionKey** tiene un valor de "Smith".

        TableQuery<CustomerEntity> query = 
            new TableQuery<CustomerEntity>()
            .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

6. Dentro de un bucle, llame al método **CloudTable.ExecuteQuerySegmented** pasando el objeto de consulta del que creó una instancia en el paso anterior.  El método **CloudTable.ExecuteQuerySegmented** devuelve un objeto **TableContinuationToken** que, cuando es **null**, indica que no hay más entidades para recuperar. Dentro del bucle, use otro bucle para iterar las entidades devueltas.

        TableContinuationToken token = null;
        do
        {
            TableQuerySegment<CustomerEntity>resultSegment = table.ExecuteQuerySegmented(query, token);
            token = resultSegment.ContinuationToken;

            foreach (CustomerEntity customer in resultSegment.Results)
            {
                // Process customer entity.
            }
        } while (token != null);

## <a name="delete-an-entity"></a>Eliminación de una entidad

Los siguientes pasos muestran cómo buscar y luego eliminar una entidad.

1. Agregue las siguientes directivas *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure. (Cambie *<storage-account-name>* por el nombre de la cuenta de almacenamiento de Azure a la que está accediendo).

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de tabla.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenga un objeto **CloudTable** que representa una referencia al nombre de tabla que desea. (Cambie *<table-name> * por el nombre de la tabla a la que desea agregar las entidades).

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Cree un objeto de operación de recuperación que tome un objeto de entidad derivado de **TableEntity**. El primer parámetro es *partitionKey* y el segundo parámetro es *rowKey*. Mediante la clase **CustomerEntity** y los datos presentados en la sección [Incorporación de un lote de entidades a una tabla](#add-a-batch-of-entities-to-a-table), el fragmento de código siguiente consulta la tabla para una entidad **CustomerEntity** con un valor *partitionKey* de "Smith" y un valor *rowKey* de "Ben".  

        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

6. Ejecute la operación de recuperación.   

        TableResult retrievedResult = table.Execute(retrieveOperation);

7. Compruebe el resultado de la operación mediante la inspección de la propiedad **TableOperation.HttpStatusCode** donde un código de estado 200 indica que la acción solicitada por el cliente se procesó correctamente. También puede inspeccionar la propiedad **TableResult.Result** que contendrá la entidad devuelta (si la operación se realiza correctamente). Dentro de la instrucción condicional para comprobar que la operación se realizó correctamente, cree una operación de eliminación (pasando la entidad devuelta de la consulta) y ejecute la operación de eliminación.

        if (retrievedResult.HttpStatusCode == 200 && retrievedResult.Result != null)
        {
            CustomerEntity customer = retrievedResult.Result as CustomerEntity;

            // Create the delete operation.
            TableOperation deleteOperation = TableOperation.Delete(customer);

            // Execute the delete operation.
            table.Execute(deleteOperation);
        }

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]




<!--HONumber=Dec16_HO2-->


