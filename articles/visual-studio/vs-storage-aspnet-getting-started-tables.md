---
title: "Introducción a Azure Table Storage y a Servicios conectados de Visual Studio (ASP.NET) | Microsoft Docs"
description: "Introducción al uso de Azure Table Storage en un proyecto ASP.NET en Visual Studio después de conectarse a una cuenta de almacenamiento mediante Servicios conectados de Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: kraigb
ms.openlocfilehash: 32a57e77bf6fe3cff88b9d6772ede9e6669ec75f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Introducción a Azure Table Storage y a Servicios conectados de Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Información general

El almacenamiento de tabla de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales.

Este tutorial muestra cómo escribir código ASP.NET para algunos escenarios comunes mediante entidades de Azure Table Storage. Entre los escenarios descritos se incluyen crear, consultar y eliminar entidades de tabla. 

##<a name="prerequisites"></a>Requisitos previos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Cuenta de Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Creación de un controlador MVC 

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **Controladores** y, en el menú contextual, seleccione **Agregar > Controlador**.

    ![Incorporación de un controlador a una aplicación de ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. En el cuadro de diálogo **Agregar scaffold**, seleccione **Controlador MVC 5 - Vacío** y seleccione **Agregar**.

    ![Especifique el tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. En el cuadro de diálogo **Agregar controlador**, denomine *TablesController* al controlador y seleccione **Agregar**.

    ![Asignación de nombre al controladorMVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Agregue las siguientes directivas *using* al archivo `TablesController.cs`:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Creación de una clase de modelo

Muchos de los ejemplos de este artículo utilizan una clase derivada de **TableEntity** llamada **CustomerEntity**. Los pasos siguientes lo guiarán por el proceso de declarar esta clase como una clase de modelo:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **Modelos** y, en el menú contextual, seleccione **Agregar > Clase**.

1. En el cuadro de diálogo **Agregar nuevo elemento**, denomine la clase **CustomerEntity**.

1. Abra el archivo `CustomerEntity.cs` y agregue el siguiente la directiva **using**:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Modifique la clase para que, cuando termine, se declare como en el código siguiente. La clase declara una clase de entidad llamada **CustomerEntity** que usa el nombre del cliente como clave de fila y el apellido como clave de partición.

    ```csharp
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
    ```

## <a name="create-a-table"></a>Creación de una tabla

Los siguientes pasos muestran cómo crear una tabla:

> [!NOTE]
> 
> En esta sección se da por supuesto que ha completado los pasos [Configuración del entorno de desarrollo](#set-up-the-development-environment). 

1. Abra el archivo `TablesController.cs` .

1. Agregue un método llamado **CreateTable** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro del método **CreateTable**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de tabla.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenga un objeto **CloudTable** que representa una referencia al nombre de tabla que desea. El método **CloudTableClient.GetTableReference** no hace ninguna solicitud en Table Storage. Se devuelve la referencia tanto si la tabla existe como si no. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Llame a la **CloudTable.CreateIfNotExists** método para crear la tabla si aún no existe. El método **CloudTable.CreateIfNotExists** devuelve **True** si la tabla no existe y se ha creado correctamente. En caso contrario, se devuelve **False**.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Actualice **ViewBag** con el nombre de la tabla.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Tables** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **CreateTable** como nombre de vista y seleccione **Agregar**.

1. Abra `CreateTable.cshtml` y modifíquelo de modo que se parezca al siguiente fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Ejecute la aplicación y seleccione **Crear tabla** para ver resultados similares a la siguiente captura de pantalla:
  
    ![Crear tabla](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Como se dijo anteriormente, el método **CloudTable.CreateIfNotExists** devuelve **true** solo si la tabla no existía, pero se creó. Por lo tanto, si se ejecuta la aplicación cuando la tabla existe, el método devuelve **False**. Para ejecutar la aplicación varias veces, debe eliminar la tabla antes de ejecutar la aplicación de nuevo. La eliminación de la tabla puede realizarse a través del método **CloudTable.Delete**. También puede eliminar la tabla mediante [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) o [el Explorador de Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Adición de una entidad a una tabla

Las *entidades* se asignan a objetos C\# utilizando una clase personalizada derivada de **TableEntity**. Para agregar una entidad a una tabla, cree una clase que defina las propiedades de la entidad. En esta sección, descubrirá cómo definir una clase de entidad que utiliza el nombre de pila del cliente como clave de fila y el apellido como clave de partición. En conjunto, la clave de partición y la clave de fila de una entidad la identifican inequívocamente en la tabla. Las entidades con la misma clave de partición pueden consultarse más rápidamente que aquellas con diferentes claves de partición, pero el uso de claves de partición diversas permite una mayor escalabilidad de las operaciones paralelas. Toda propiedad que deba almacenarse en Table service debe ser una propiedad pública de un tipo compatible que exponga tanto el establecimiento como la recuperación de valores.
La clase de entidad *must* declara un constructor sin parámetros público.

> [!NOTE]
> 
> En esta sección se da por supuesto que ha completado los pasos [Configuración del entorno de desarrollo](#set-up-the-development-environment).

1. Abra el archivo `TablesController.cs` .

1. Agregue la siguiente directiva para que el código del archivo `TablesController.cs` pueda tener acceso a la clase **CustomerEntity**:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Agregue un método llamado **AddEntity** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro del método **AddEntity**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de tabla.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenga un objeto **CloudTable** que represente una referencia a la tabla a la que va a agregar la nueva entidad. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Cree instancias e inicialice la clase **CustomerEntity**.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Cree el objeto **TableOperation** que inserta la entidad del cliente.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Ejecute la operación de inserción mediante una llamada al método **CloudTable.Execute**. Puede comprobar el resultado de la operación inspeccionando la propiedad **TableResult.HttpStatusCode**. Un código de estado de 2xx indica que la acción solicitada por el cliente se procesó correctamente. Por ejemplo, las inserciones correctas de las nuevas entidades dan lugar a un código de estado HTTP de 204, lo que significa que la operación se procesó correctamente y el servidor no devolvió ningún contenido.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Actualice **ViewBag** con el nombre de tabla y los resultados de la operación de inserción.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Tables** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **AddEntity** como nombre de vista y seleccione **Agregar**.

1. Abra `AddEntity.cshtml` y modifíquelo de modo que se parezca al siguiente fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Ejecute la aplicación y seleccione **Agregar entidad** para ver resultados similares a la siguiente captura de pantalla:
  
    ![Agregar entidad](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Puede comprobar que se ha agregado la entidad siguiendo los pasos descritos en la sección [Obtención de una sola entidad](#get-a-single-entity). También puede usar [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para ver todas las entidades de las tablas.

## <a name="add-a-batch-of-entities-to-a-table"></a>Incorporación de un lote de entidades a una tabla

Además de poder [agregar una entidad a una tabla de una en una](#add-an-entity-to-a-table), también puede agregar entidades por lotes. Esto reduce de forma masiva el número de idas y vueltas entre el código y Azure Table service. Los siguientes pasos muestran cómo agregar varias entidades a una tabla con una sola operación de inserción:

> [!NOTE]
> 
> En esta sección se da por supuesto que ha completado los pasos [Configuración del entorno de desarrollo](#set-up-the-development-environment).

1. Abra el archivo `TablesController.cs` .

1. Agregue un método llamado **AddEntities** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro del método **AddEntities**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de tabla.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenga un objeto **CloudTable** que represente una referencia a la tabla a la que va a agregar las nuevas entidades. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Cree instancias de algunos objetos de cliente basados en la clase de modelo **CustomerEntity** de la sección [Adición de una entidad a una tabla](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Obtenga un objeto **TableBatchOperation**.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Agregue entidades al objeto de operación de inserción de por lotes.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Ejecute la operación de inserción por lotes mediante una llamada al método **CloudTable.ExecuteBatch**.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. El método **CloudTable.ExecuteBatch** devuelve una lista de objetos **TableResult** donde cada **TableResult** se puede examinar para determinar el éxito o el fracaso de cada operación individual. En este ejemplo, pase la lista a una vista y permita que se muestren los resultados de cada operación. 
 
    ```csharp
    return View(results);
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Tables** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **AddEntities** como nombre de vista y seleccione **Agregar**.

1. Abra `AddEntities.cshtml` y modifíquelo de modo que se parezca a lo siguiente.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Ejecute la aplicación y seleccione **Agregar entidades** para ver resultados similares a la siguiente captura de pantalla:
  
    ![agregar entidades](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Puede comprobar que se ha agregado la entidad siguiendo los pasos descritos en la sección [Obtención de una sola entidad](#get-a-single-entity). También puede usar [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para ver todas las entidades de las tablas.

## <a name="get-a-single-entity"></a>Obtención de una sola entidad

En esta sección se muestra cómo obtener una sola entidad de una tabla mediante la clave de fila y la clave de partición de la entidad. 

> [!NOTE]
> 
> En esta sección se da por supuesto que ha completado los pasos descritos en [Configuración del entorno de desarrollo](#set-up-the-development-environment) y utiliza los datos de [Incorporación de un lote de entidades a una tabla](#add-a-batch-of-entities-to-a-table). 

1. Abra el archivo `TablesController.cs` .

1. Agregue un método llamado **GetSingle** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro del método **GetSingle**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de tabla.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenga un objeto **CloudTable** que represente una referencia a la tabla de la que va a recuperar la entidad. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Cree un objeto de operación de recuperación que tome un objeto de entidad derivado de **TableEntity**. El primer parámetro es *partitionKey* y el segundo parámetro es *rowKey*. Mediante la clase **CustomerEntity** y los datos presentados en la sección [Incorporación de un lote de entidades a una tabla](#add-a-batch-of-entities-to-a-table), el fragmento de código siguiente consulta la tabla para una entidad **CustomerEntity** con un valor *partitionKey* de "Smith" y un valor *rowKey* de "Ben":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Ejecute la operación de recuperación.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Pase el resultado a la vista para que se muestre.

    ```csharp
    return View(result);
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Tables** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **GetSingle** como nombre de vista y seleccione **Agregar**.

1. Abra `GetSingle.cshtml` y modifíquelo de modo que se parezca al siguiente fragmento de código:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Ejecute la aplicación y seleccione **GetSingle** para ver resultados similares a la siguiente captura de pantalla:
  
    ![GetSingle](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Obtención de todas las entidades de una partición

Como se mencionó en la sección [Adición de una entidad a una tabla](#add-an-entity-to-a-table), la combinación de una partición y una clave de fila identifican de forma única una entidad de una tabla. Puede realizarse una consulta en las entidades con la misma clave de partición de manera más rápida que en aquellas que tienen claves de partición distintas. Esta sección muestra cómo consultar una tabla en todas las entidades de una partición especificada.  

> [!NOTE]
> 
> En esta sección se da por supuesto que ha completado los pasos descritos en [Configuración del entorno de desarrollo](#set-up-the-development-environment) y utiliza los datos de [Incorporación de un lote de entidades a una tabla](#add-a-batch-of-entities-to-a-table). 

1. Abra el archivo `TablesController.cs` .

1. Agregue un método llamado **GetPartition** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro del método **GetPartition**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de tabla.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenga un objeto **CloudTable** que represente una referencia a la tabla de la que va a recuperar las entidades. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Cree una instancia de un objeto **TableQuery** especificando la consulta en la cláusula **Where**. Mediante la clase **CustomerEntity** y los datos presentados en la sección [Incorporación de un lote de entidades a una tabla](#add-a-batch-of-entities-to-a-table), el fragmento de código siguiente consulta la tabla para todas las entidades donde **PartitionKey** (apellidos del cliente) tiene un valor de "Smith":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Dentro de un bucle, llame al método **CloudTable.ExecuteQuerySegmented** pasando el objeto de consulta del que creó una instancia en el paso anterior.  El método **CloudTable.ExecuteQuerySegmented** devuelve un objeto **TableContinuationToken** que, cuando es **null**, indica que no hay más entidades para recuperar. Dentro del bucle, use otro bucle para iterar las entidades devueltas. En el ejemplo de código siguiente, cada entidad devuelta se agrega a una lista. Una vez que finaliza el bucle, la lista se pasa a una vista para mostrar los resultados: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Tables** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **GetPartition** como nombre de vista y seleccione **Agregar**.

1. Abra `GetPartition.cshtml` y modifíquelo de modo que se parezca al siguiente fragmento de código:

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Ejecute la aplicación y seleccione **GetPartition** para ver resultados similares a la siguiente captura de pantalla:
  
    ![GetPartition](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Eliminación de una entidad

Esta sección muestra cómo eliminar una entidad de una tabla.

> [!NOTE]
> 
> En esta sección se da por supuesto que ha completado los pasos descritos en [Configuración del entorno de desarrollo](#set-up-the-development-environment) y utiliza los datos de [Incorporación de un lote de entidades a una tabla](#add-a-batch-of-entities-to-a-table). 

1. Abra el archivo `TablesController.cs` .

1. Agregue un método llamado **DeleteEntity** que devuelve un **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro del método **DeleteEntity**, obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Utilice el código siguiente para obtener la información de cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure: (cambie *&lt;storage-account-name>*  por el nombre de la cuenta de Azure Storage a la que está obteniendo acceso).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenga un objeto **CloudTableClient** que representa un cliente de servicio de tabla.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenga un objeto **CloudTable** que represente una referencia a la tabla de la que va a eliminar la entidad. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Cree un objeto de operación de eliminación que tome un objeto de entidad derivado de **TableEntity**. En este caso, usamos la clase **CustomerEntity** y a los datos presentados en la sección [Incorporación de un lote de entidades a una tabla](#add-a-batch-of-entities-to-a-table). La **ETag** de la entidad debe establecerse en un valor válido.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Ejecute la operación de eliminación.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Pase el resultado a la vista para que se muestre.

    ```csharp
    return View(result);
    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas**, haga clic con el botón derecho en **Tables** y, en el menú contextual, seleccione **Agregar > Vista**.

1. En el cuadro de diálogo **Agregar vista**, escriba **DeleteEntity** como nombre de vista y seleccione **Agregar**.

1. Abra `DeleteEntity.cshtml` y modifíquelo de modo que se parezca al siguiente fragmento de código:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. En el **Explorador de soluciones**, expanda la carpeta **Vistas->Compartido** y abra `_Layout.cshtml`.

1. Después del último **Html.ActionLink**, agregue el siguiente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Ejecute la aplicación y seleccione **Eliminar entidad** para ver resultados similares a la siguiente captura de pantalla:
  
    ![GetSingle](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Pasos siguientes
Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.

  * [Introducción a Azure Blob Storage y los servicios conectados de Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Introducción a Azure Queue Storage y a Servicios conectados de Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
