<properties linkid="dev-net-2-how-to-table-services" urlDisplayName="Table Service (2.0)" pageTitle="How to use table storage | Microsoft Azure" metaKeywords="Get started Azure table, Azure nosql, Azure large structured data store, Azure table, Azure table storage, Azure table .NET, Azure table storage .NET, Azure table C#, Azure table storage C#" description="Learn how to use table storage to create and delete tables and insert and query entities in a table." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Table Storage Service" authors="" solutions="" manager="paulettm" editor="cgronlun" />

Uso del servicio de almacenamiento de tablas
============================================

[Versión 1.7](/es-es/develop/net/how-to-guides/table-services-v17/ "version 1.7") [Versión 2.0](/es-es/develop/net/how-to-guides/table-services/ "version 2.0")

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de tablas de Azure. Los ejemplos se escriben en código C\# y usan la API .NET. Entre los escenarios descritos se incluyen la **creación y eliminación de una tabla, la inserción y la consulta de entidades en una tabla**. Para obtener más información acerca de las tablas, consulte la sección [Pasos siguientes](#next-steps).

Tabla de contenido
------------------

-   [Qué es el servicio Tabla](#what-is)
-   [Conceptos](#concepts)
-   [Creación de una cuenta de almacenamiento de Azure](#create-account)
-   [Configuración de una cadena de conexión de almacenamiento](#setup-connection-string)
-   [Acceso al almacenamiento de tablas mediante programación](#configure-access)
-   [Creación de una tabla](#create-table)
-   [Incorporación de una entidad a una tabla](#add-entity)
-   [Inserción de un lote de entidades](#insert-batch)
-   [Recuperación de todas las entidades de una partición](#retrieve-all-entities)
-   [Recuperación de un rango de entidades de una partición](#retrieve-range-entities)
-   [Recuperación de una sola entidad](#retrieve-single-entity)
-   [Actualización de una entidad](#update-entity)
-   [Consulta de un subconjunto de propiedades de las entidades](#query-entity-properties)
-   [Inserción o reemplazo de una entidad](#insert-entity)
-   [Eliminación de una entidad](#delete-entity)
-   [Eliminación de una tabla](#delete-table)
-   [Pasos siguientes](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

Creación de una cuentaCreación de una cuenta de almacenamiento de Azure
-----------------------------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Configuración de una cadena de conexiónConfiguración de una cadena de conexión de almacenamiento
------------------------------------------------------------------------------------------------

La API de almacenamiento para .NET de Azure admite el uso de una cadena de conexión de almacenamiento para configurar extremos y credenciales a fin de obtener acceso a los servicios de almacenamiento. Puede poner la cadena de conexión de almacenamiento en un archivo de configuración en vez de codificarla de forma rígida:

-   Si utiliza los servicios en la nube de Azure, es recomendable que almacene la cadena de conexión mediante el sistema de configuración de servicios de Azure (archivos `*.csdef` y `*.cscfg`).
-   Al usar Sitios web Azure o Máquinas virtuales de Azure, es recomendable que almacene su cadena de conexión usando el sistema de configuración .NET (por ejemplo, el archivo `web.config`).

En cualquiera de los dos casos, puede recuperar la cadena de conexión utilizando el método `CloudConfigurationManager.GetSetting`, como se indica más adelante en esta guía.

### Configuración de la cadena de conexión si utiliza los servicios en la nube

El mecanismo de configuración de servicios es exclusivo para los proyectos de los servicios en la nube de Azure y le permite cambiar dinámicamente la configuración desde el Portal de administración de Azure sin volver a implementar la aplicación.

Siga estos pasos para configurar la cadena de conexión mediante la configuración de servicios de Azure:

1.  En el Explorador de soluciones de Visual Studio, en la carpeta **Roles** del proyecto de implementación de Azure, haga clic con el botón derecho en su rol web o de trabajo y, a continuación, haga clic en **Propiedades**.
     ![Blob5](./media/storage-dotnet-how-to-use-table-storage-17/blob5.png)

2.  Haga clic en la pestańa **Configuración** y, a continuación, en el botón **Agregar configuración**.
     ![Blob6](./media/storage-dotnet-how-to-use-table-storage-17/blob6.png)

    Entonces aparecerá una nueva entrada, **Setting1** en la cuadrícula de configuración.

3.  En el menú desplegable **Tipo** de la nueva entrada **Setting1**, elija **Cadena de conexión**.
     ![Blob7](./media/storage-dotnet-how-to-use-table-storage-17/blob7.png)

4.  Haga clic en el botón **...** situado en el extremo derecho de la entrada **Setting1**. Se abrirá el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**.

5.  Elija si desea utilizar el emulador de almacenamiento (almacenamiento de Azure simulado en su equipo local) o una cuenta de almacenamiento real en la nube. El código de esta guía funciona con cualquiera de estas opciones. Escriba el valor **Clave de acceso primaria** copiado del paso anterior de este tutorial si desea almacenar los datos de blobs en la cuenta de almacenamiento que se creó anteriormente en Azure.
     ![Blob8](./media/storage-dotnet-how-to-use-table-storage-17/blob8.png)

6.  Cambie el contenido del campo **Nombre** de **Setting1** a algo más intuitivo, como **CadenaConexiónAlmacenamiento**. Más adelante en esta guía hará referencia a esta cadena de conexión.
     ![Blob9](./media/storage-dotnet-how-to-use-table-storage-17/blob9.png)

### Configuración de la cadena de conexión al usar Sitios web o Máquinas virtuales

Al usar Sitios web o Máquinas virtuales, es recomendable que utilice el sistema de configuración .NET (por ejemplo, `web.config`). La cadena de conexión se almacena utilizando el elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

Consulte [Configurar las cadenas de conexión de Azure](http://msdn.microsoft.com/es-es/library/windowsazure/ee758697.aspx) para obtener más información acerca de las cadenas de conexión de almacenamiento.

Ahora está preparado para realizar los procedimientos de esta guía.

Acceso mediante programaciónAcceso al almacenamiento de tablas mediante programación
------------------------------------------------------------------------------------

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C\# en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Puede usar el tipo **CloudStorageAccount** y el tipo **CloudConfigurationManager** para recuperar la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Creación de una tablaCreación de una tabla
------------------------------------------

Los objetos **CloudTableClient** le permiten obtener objetos de referencia para las tablas y las entidades. El código siguiente crea un objeto **CloudTableClient** y lo utiliza para crear una nueva tabla. Todo el código que contiene esta guía utiliza una cadena de conexión de almacenamiento almacenada en la configuración de servicios de la aplicación de Azure. Además, existen otros métodos de creación de un objeto **CloudStorageAccount**.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Cree la tabla si todavía no existe.
    string tableName = "personas";
    tableClient.CreateTableIfNotExist(tableName);

Incorporación de una entidad a una tablaIncorporación de una entidad a una tabla
--------------------------------------------------------------------------------

Las entidades se asignan a objetos C\# utilizando una clase personalizada derivada de **TableServiceEntity**. Para agregar una entidad a una tabla, cree primero una clase que defina las propiedades de la entidad. El código siguiente define una clase de entidad que utiliza el nombre de pila del cliente como clave de fila y el apellido como clave de partición. En conjunto, la clave de partición y la clave de fila de una entidad la identifican inequívocamente en la tabla. Puede realizarse una consulta en las entidades con la misma clave de partición de manera más rápida que en aquellas que tienen claves de partición distintas.

    public class CustomerEntity : TableServiceEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

Las operaciones de tabla que afectan a las entidades requieren un objeto **TableServiceContext**. Este objeto realiza un seguimiento del estado del cliente en todas las entidades de tabla creadas y a las que se accede en el código de cliente. El mantenimiento de un objeto del cliente que represente a cada entidad hace que las operaciones de escritura sean más eficaces, ya que solo se actualizan los objetos con cambios en el servicio de tabla cuando se ejecutan las operaciones de guardado. El siguiente código crea un objeto **TableServiceContext** mediante la llamada del método **GetDataServiceContext**. A continuación, el código crea una instancia de la clase **CustomerEntity**. El código llama a **serviceContext.AddObject** para insertar la nueva entidad en la tabla. De esta forma, se agrega el objeto de entidad a **serviceContext**, pero no se producen operaciones de servicio. Finalmente, el código envía la nueva entidad al servicio de tabla cuando se llama al método **SaveChangesWithRetries**.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenga el contexto de servicio de datos.
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Cree una nueva entidad de cliente.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Agregue el nuevo cliente a la tabla "personas".
    serviceContext.AddObject("personas", customer1);

    // Envíe la operación al servicio de tabla.
    serviceContext.SaveChangesWithRetries();

Inserción de un lote de entidadesInserción de un lote de entidades
------------------------------------------------------------------

Puede insertar un lote de entidades en el servicio de tabla mediante una operación de escritura. El siguiente código crea tres objetos de entidad y agrega cada uno al contexto de servio mediante el método **AddObject**. A continuación, el código llama a **SaveChangesWithRetries** con el parámetro **SaveChangesOptions.Batch**. Si omite **SaveChangesOptions.Batch**, se producirán tres llamadas independientes en el servicio de tabla. Otras notas acerca de las operaciones por lotes:

1.  Puede realizar inserciones, eliminaciones y actualizaciones por lotes.
2.  Una única operación por lotes puede incluir hasta 100 entidades.
3.  Todas las entidades de la misma operación por lotes deben compartir la clave de partición.

<!-- -->

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    string tableName = "personas";

    // Obtenga el contexto de servicio de datos.
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Cree una entidad de cliente y agréguela a la tabla.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.Email = "Jeff@contoso.com";
    customer.PhoneNumber = "425-555-0104";
    serviceContext.AddObject(tableName, customer);

    // Cree otra entidad de cliente y agréguela a la tabla.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";
    serviceContext.AddObject(tableName, customer2);

    // Cree una entidad de cliente y agréguela a la tabla.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.Email = "Denise@contoso.com";
    customer3.PhoneNumber = "425-555-0103";
    serviceContext.AddObject(tableName, customer3);

    // Envíe la operación al servicio de tabla.
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.Batch);

Recuperación de todas las entidadesRecuperación de todas las entidades de una partición
---------------------------------------------------------------------------------------

Para consultar una tabla a fin de obtener las entidades de una partición, use una consulta LINQ. Llame a **serviceContext.CreateQuery** para crear una consulta desde el origen de datos. El código siguiente especifica un filtro para las entidades en las que "Smith" es la clave de partición. Llame a **AsTableServiceQuery&lt;CustomerEntity\>** en el resultado de la consulta LINQ para finalizar la creación del objeto **CloudTableQuery**. Puede usar el objeto **partitionQuery** creado en un bucle **foreach** para consumir los resultados. En este código, los campos de cada entidad se imprimen en la consola, como parte de los resultados de la consulta.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenga el contexto de servicio de datos.
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Especifique una consulta de partición con "Smith" como clave de partición.
    CloudTableQuery<CustomerEntity> partitionQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("personas")
         where e.PartitionKey == "Smith"
         select e).AsTableServiceQuery<CustomerEntity>();

    // Aplique un bucle a través de los resultados, mostrando información acerca de la entidad.
    foreach (CustomerEntity entity in partitionQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

Recuperación de un rango de entidadesRecuperación de un rango de entidades de una partición
-------------------------------------------------------------------------------------------

Si no desea realizar una consulta en todas las entidades de una partición, puede especificar un rango mediante el método **CompareTo** en lugar de usar los operaciones normales mayor que (\>) y menor que (&lt;). Esto se debe a que el último provocará una construcción incorrecta de la consulta. El código siguiente utiliza dos filtros para obtener todas las entidades de la partición "Smith" en las que la clave de fila (nombre de pila) empieza por una letra hasta "E" en el alfabeto. A continuación, imprime los resultados de la consulta. Si usa las entidades agregadas a la tabla en la sección de inserción por lotes de esta guía, solo se devuelven dos entidades en este momento (Ben y Denise Smith); Jeff Smith no se incluye.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenga el contexto de servicio de datos.
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Especifique una consulta de partición con "Smith" como clave de partición
    // con la clave de fila hasta la letra "E".
    CloudTableQuery<CustomerEntity> entityRangeQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("personas")
         where e.PartitionKey == "Smith" && e.RowKey.CompareTo("E") < 0
         select e).AsTableServiceQuery<CustomerEntity>();

    // Aplique un bucle a través de los resultados, mostrando información acerca de la entidad.
    foreach (CustomerEntity entity in entityRangeQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

Recuperación de una sola entidadRecuperación de una sola entidad
----------------------------------------------------------------

Puede enviar una consulta para recuperar una sola entidad concreta. El código siguiente usa dos filtros para especificar el cliente "Jeff Smith". En lugar de llamar a **AsTableServiceQuery**, este código llama a **FirstOrDefault**. Con este método se obtiene solo una entidad, en lugar de una recopilación, por lo que el código asigna el valor de retorno directamente a un objeto **CustomerEntity**. Se devuelve un valor nulo si no coincide exactamente la clave de fila y de partición de ninguna entidad. La forma más rápida de recuperar una sola entidad del servicio Tabla es especificar claves tanto de partición como de fila en las consultas.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenga el contexto de servicio de datos.
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Devuelva la entidad con la clave de partición "Smith" y la clave de fila "Jeff".
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("personas")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

Actualización de una entidadActualización de una entidad
--------------------------------------------------------

Para actualizar una entidad, recupérela del servicio Tabla, modifique su objeto y guarde los cambios de nuevo en el servicio Tabla. El código siguiente cambia el número de teléfono de un cliente. En lugar de llamar a **AddObject** como se hizo en la inserción, este código llama a **UpdateObject**. El método **SaveChangesWithRetries** llama al servicio Tabla y la entidad se actualiza, a no ser que otra aplicación la haya modificado desde que la aplicación la recuperó. Cuando se produce esa situación, se muestra una excepción y la entidad debe recuperarse, modificarse y guardarse de nuevo. Este patrón de reintento es común en un sistema de almacenamiento distribuido.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenga el contexto de servicio de datos.
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Devuelva la entidad con la clave de partición "Smith" y la clave de fila "Jeff".
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("personas")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Especifique un nuevo número de teléfono.
    specificEntity.PhoneNumber = "425-555-0105";

    // Actualice la entidad.
    serviceContext.UpdateObject(specificEntity);

    // Envíe la operación al servicio de tabla.
    serviceContext.SaveChangesWithRetries();

Consulta de un subconjunto de propiedadesConsulta de un subconjunto de propiedades de las entidades
---------------------------------------------------------------------------------------------------

Una consulta de tabla puede recuperar solo algunas propiedades de una entidad. Esta técnica, denominada proyección, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamańo. La consulta del código siguiente devuelve solo las direcciones de correo electrónico de las entidades de la tabla. Puede obtener más información acerca de la proyección en esta [entrada de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx) (en inglés). Tenga en cuenta que la proyección no es compatible con el emulador de almacenamiento local, por lo que este código solo se ejecuta cuando se utiliza una cuenta del servicio Tabla.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenga el contexto de servicio de datos.
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Defina una consulta de proyección que recupere solo la propiedad Email.
    var projectionQuery = 
        from e in serviceContext.CreateQuery<CustomerEntity>("personas")
        select new
        {
            Email = e.Email
            // Puede especificar campos adicionales aquí.
        };

    // Aplique un bucle a través de los resultados mostrando el valor Email.
    foreach (var person in projectionQuery)
    {
        Console.WriteLine(person.Email);
    }

Inserción o reemplazo de una entidadInserción o reemplazo de una entidad
------------------------------------------------------------------------

En ocasiones, es posible que desee agregar una entidad a una tabla sin saber si ya existe en la tabla. Una operación de inserción o reemplazo le permite realizar una consulta única que insertará la entidad si no existe o que reemplazará la existente si la hubiera. Según los ejemplos anteriores, el siguiente código inserta o reemplaza la entidad de "Walter Harp". Después de crear una nueva entidad, este código llama al método **serviceContext.AttachTo**. Este código llama a continuación a **UpdateObject** y finalmente llama a **SaveChangesWithRetries** con el parámetro **SaveChangesOptions.ReplaceOnUpdate**. La omisión del parámetro **SaveChangesOptions.ReplaceOnUpdate** provoca una operación de inserción o fusión. Tenga en cuenta que la inserción o el reemplazo no son compatibles con el emulador de almacenamiento local, por lo que este código solo se ejecuta cuando se utiliza una cuenta del servicio Tabla. Puede obtener más información sobre la inserción o el reemplazo y sobre la inserción o la fusión en esta [entrada de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx).

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenga el contexto de servicio de datos.
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Cree una nueva entidad de cliente.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.Email = "Walter@contoso.com";
    customer5.PhoneNumber = "425-555-0106";

    // Asocie este cliente a la tabla "personas".
    serviceContext.AttachTo("personas", customer5);

    // Inserte este cliente si es nuevo o reemplácelo si ya existe.
    serviceContext.UpdateObject(customer5);

    // Envíe la operación al servio de tabla con la opción ReplaceOnUpdate.
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.ReplaceOnUpdate);

Eliminación de una entidadEliminación de una entidad
----------------------------------------------------

Puede eliminar fácilmente una entidad después de que la haya recuperado. También puede usar el método **AttachTo** para comenzar a realizar el seguimiento de ella sin recuperarla del servidor (consulte la operación de inserción o reemplazo anterior). Una vez que se haya realizado un seguimiento de la entidad con **serviceContext**, llame a **DeleteObject** con la entidad que eliminar. A continuación, llame a **SaveChangesWithRetries**. El código siguiente recupera y elimina una entidad de cliente.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Obtenga el contexto de servicio de datos.
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("personas")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Elimine la entidad.
    serviceContext.DeleteObject(specificEntity);

    // Envíe la operación al servicio de tabla.
    serviceContext.SaveChangesWithRetries();

Eliminación de una tablaEliminación de una tabla
------------------------------------------------

Finalmente, el código siguiente elimina una tabla de una cuenta de almacenamiento. Las tablas eliminadas no podrán volver a crearse durante un tiempo tras la eliminación.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente de tabla.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Elimine la tabla si existe.
    tableClient.DeleteTableIfExist("personas");

Pasos siguientesPasos siguientes
--------------------------------

Ahora que está familiarizado con los aspectos básicos del almacenamiento de tablas, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la documentación de referencia del servicio de blobs para obtener información detallada acerca de las API disponibles:
    -   [Referencia a la biblioteca de clases de .NET](http://msdn.microsoft.com/es-es/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [Referencia de la API REST](http://msdn.microsoft.com/es-es/library/windowsazure/dd179355)
-   Obtenga información acerca de las tareas más avanzadas que se pueden realizar con el almacenamiento de Azure en [Almacenamiento](http://msdn.microsoft.com/es-es/library/windowsazure/gg433040.aspx).
-   Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.
    -   Utilice [Almacenamiento de blobs](/es-es/develop/net/how-to-guides/blob-storage/) para almacenar datos no estructurados.
    -   Utilice [Base de datos SQL](/es-es/develop/net/how-to-guides/sql-database/) para almacenar datos relacionales.

