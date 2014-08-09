<properties pageTitle="How to use Code First Migrations .NET backend (Mobile Services)" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Considerations for supporting multiple clients from a single mobile service" authors="glenga" solutions="" writer="glenga" manager="dwrede" editor="" />

Modificación del modelo de datos de un servicio móvil back-end .NET
===================================================================

En un proyecto de servicio móvil back-end .NET, el inicializador de base de datos de Entity Framework Code First predeterminado se deriva de la clase [DropCreateDatabaseIfModelChanges]. Este inicializador ordena a Entity Framework eliminar y volver a crear la base de datos cuando detecta un cambio en el modelo de datos expuesto por la clase [DbContext]. Debe continuar utilizando este inicializador durante el desarrollo local del proyecto de servicio móvil; además, en los tutoriales de back-end .NET se asume que está utilizando este inicializador. Sin embargo, en aquellos casos en que desee realizar cambios en el modelo de datos y conservar los datos existentes en la base de datos, debe utilizar las Migraciones de Code First. Las Migraciones de Code First son además una buena solución para publicar los cambios en el modelo de datos en Azure, ya que no es posible eliminar las bases de datos SQL.

En este tema, se muestra cómo utilizar las Migraciones de Code First para realizar cambios en el modelo de datos de una base de datos SQL sin perder los datos existentes. Se asume que ya publicó el proyecto de servicio móvil en Azure, que hay datos guardados en la base de datos y que los modelos de datos remoto y local están sincronizados.

> [WACOM.NOTE]Es recomendable que avance lo máximo posible en el desarrollo del modelo de datos en el equipo local antes de publicarlo en Azure. Si ya publicó el proyecto de servicio móvil back-end .NET en Azure y el esquema de tablas de la base de datos SQL no concuerda con el modelo de datos actual del proyecto, debe eliminar las tablas o bien sincronizarlas manualmente antes de la publicación mediante las Migraciones de Code First.

A la hora de desarrollar un proyecto de servicio móvil back-end .NET en el equipo local, el modo más sencillo de hacer frente a los cambios en el modelo de datos es seguir utilizando el inicializador predeterminado, que elimina y vuelve a crear la base de datos cuando se detecta un cambio en el modelo de datos. Este mismo enfoque no funciona al volver a publicar el proyecto en Azure. El inicializador da error, ya que el tiempo de ejecución no tiene permiso para eliminar una base de datos SQL en Azure, lo cual es positivo.

> [WACOM.NOTE]A la hora de desarrollar y probar el proyecto de servicio móvil con los servicios de Azure en directo, debe utilizar siempre una instancia del servicio móvil destinada a pruebas. No debe utilizar nunca un servicio móvil que se encuentre en fase de producción o que esté siendo utilizado por aplicaciones cliente.

Eliminación de tablas en la base de datos SQL
---------------------------------------------

Antes de poder utilizar las Migraciones con una base de datos SQL en Azure, debe eliminar manualmente todas las tablas existentes en el esquema de base de datos utilizado por el servicio móvil. Siga estos pasos para eliminar las tablas existentes en la base de datos SQL. Si el esquema de base de datos ya está sincronizado con el modelo de datos actual, puede omitir este paso y directamente utilizar las [Migraciones](#migrations).

1.  Inicie sesión en el [Portal de administración de Azure], seleccione el servicio móvil, haga clic en la pestaña **Configure** y, por último, haga clic en el vínculo **Base de datos SQL**.

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png)

    De este modo, se abrirá la página del portal correspondiente a la base de datos utilizada por el servicio móvil.

2.  Haga clic en el botón **Manage** e inicie sesión en el servidor de la Base de datos SQL.

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png)

3.  En el administrador de la base de datos SQL, haga clic en **Design**, haga clic en **Tables**, seleccione una tabla del esquema de servicio móvil, haga clic en **Drop table** y, a continuación, haga clic en **OK** para confirmar.

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png)

4.  Repita el paso anterior con cada tabla del esquema del servicio móvil.

    Una vez eliminadas las tablas existentes, pueden inicializarse las Migraciones de Code First en la Base de datos SQL. Las tablas que no pertenecen al esquema del servicio móvil no afectan al servicio y no deben eliminarse.

Habilitación de las Migraciones de Code First
---------------------------------------------

Las Migraciones de Code First utilizan un método de instantáneas para generar código que, al ejecutarse, realiza cambios en el esquema de la base de datos. Con las Migraciones, puede realizar cambios incrementales en el modelo de datos y conservar los datos existentes en la base de datos. Los siguientes pasos sirven para activar las Migraciones y aplicar los cambios en el modelo de datos en el proyecto, la base de datos local y Azure.

1.  En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto de servicio móvil y, a continuación, en **Establecer como proyecto de inicio**.

2.  En el menú **Herramientas**, expanda **Administrador de paquetes NuGet** y, a continuación, haga clic en **Consola del Administrador de paquetes**.

    De este modo, se muestra la Consola del Administrador de paquetes, que se puede utilizar para administrar las Migraciones de Code First.

3.  En la Consola del Administrador de paquetes, ejecute el siguiente comando:

         PM> Enable-Migrations

    De este modo, se activan las Migraciones de Code First para el proyecto.

4.  En la consola, ejecute el siguiente comando:

         PM> Add-Migration Initial

    De esta forma, se crea una nueva migración denominada *Initial*. El código de la migración se almacena en la carpeta de proyecto de migraciones.

5.  Expanda la carpeta App\_Start, abra el archivo de proyecto WebApiConfig.cs y agregue las siguientes instrucciones **using**:

         using System.Data.Entity.Migrations;
         using todolistService.Migrations;

    En el código anterior, debe reemplazar la cadena *todolistService* por el espacio de nombres del proyecto, que en el caso del proyecto de inicio rápido descargado es *mobile\_service\_name*.

6.  En este mismo archivo de código, convierta en comentario la llamada al método **Database.SetInitializer** y agregue el código siguiente a continuación:

         var migrator = new DbMigrator(new Configuration());
         migrator.Update();

    De este modo, se deshabilita el inicializador de base de datos de Code First que elimina y vuelve a crear la base de datos y se reemplaza por una solicitud explícita de aplicación de la migración más reciente. En este momento, cualquier cambio en el modelo de datos provocará una excepción InvalidOperationException al obtener acceso a los datos, a menos que se cree una migración para él. A partir de ahora, el servicio debe utilizar las Migraciones de Code First para migrar los cambios en el modelo de datos a la base de datos.

7.  Presione F5 para iniciar el proyecto de servicio móvil en el equipo local.

    En este punto, la base de datos está sincronizada con el modelo de datos. Si proporcionó datos de inicialización, puede comprobarlo haciendo clic en **Try it out**, **GET tables/todoitem**, **Try this out** y **Send**. Para obtener más información, consulte [Propagación de datos en migraciones](#seeding).

8.  Ahora haga un cambio en el modelo de datos, como agregar una nueva propiedad UserId al tipo TodoItem, vuelva a compilar el proyecto y, a continuación, en el Administrador de paquetes ejecute el siguiente comando:

		PM\> Add-Migration NewUserId


    De esta forma, se crea una nueva migración denominada *NewUserId*. Se agrega un nuevo archivo de código, que implementa este cambio, a la carpeta de migraciones.  

1.  Presione F5 de nuevo para reiniciar el proyecto de servicio móvil en el equipo local.

    La migración se aplica a la base de datos, que vuelve a estar sincronizada con el modelo de datos. Si proporcionó datos de inicialización, puede comprobarlo haciendo clic en **Try it out**, **GET tables/todoitem**, **Try this out** y **Send**. Para obtener más información, consulte [Propagación de datos en migraciones](#seeding).

2.  Vuelva a publicar el servicio móvil en Azure, y, a continuación, ejecute la aplicación cliente para obtener acceso a los datos y compruebe que estos se cargan sin errores.

3.  (Opcional) En el [Portal de administración de Azure], seleccione el servicio móvil, haga clic en la pestaña **Configure** y, por último, haga clic en el vínculo **Base de datos SQL**.

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png)

    De este modo, se abre la página de la base de datos SQL del servicio móvil.

4.  (Opcional) Haga clic en **Manage**, inicie sesión en el servidor de la base de datos SQL, a continuación haga clic en **Design** y compruebe que los cambios en el esquema estén aplicados en Azure.

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png)

Propagación de datos en migraciones
-----------------------------------

Puede hacer que las Migraciones agreguen datos de inicialización a la base de datos cuando se ejecuta una migración. La clase Configuration incluye un método Seed que puede reemplazar para insertar o actualizar datos. El archivo de código Configuration.cs se agrega a la carpeta de migraciones cuando están habilitadas las Migraciones. Estos ejemplos muestran cómo reemplazar el método [Seed] para propagar datos a la tabla **TodoItems**. Se llama al método [Seed] una vez realizada la migración a la versión más reciente.

### Propagación de una nueva tabla

El código siguiente propaga la tabla **TodoItems** con nuevas filas de datos:

        List<TodoItem> todoItems = new List<TodoItem>
        {
            new TodoItem { Id = "1", Text = "First item", Complete = false },
            new TodoItem { Id = "2", Text = "Second item", Complete = false },
        };

        foreach (TodoItem todoItem in todoItems)
        {
            context.Set<TodoItem>().Add(todoItem);
        }
        base.Seed(context);

### Propagar una nueva columna de una tabla

El código siguiente únicamente propaga la columna UserId:

        context.TodoItems.AddOrUpdate(
            t => t.UserId,
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 2 }
            );
        base.Seed(context);

Este código llama al método auxiliar de extensión [AddOrUpdate] para agregar datos de inicialización a la nueva columna UserId. Al utilizar [AddOrUpdate], no se duplican filas.

<!-- Anchors -->
[Migrations]: #migrations
[Seeding data in migrations]: #seeding

<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png
[1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png
[2]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png

<!-- URLs -->
[DropCreateDatabaseIfModelChanges]: http://msdn.microsoft.com/query/dev12.query?appId=Dev12IDEF1&l=EN-US&k=k("System.Data.Entity.DropCreateDatabaseIfModelChanges`1");k(TargetFrameworkMoniker-.NETFramework,Version%3Dv4.5);k(DevLang-csharp)&rd=true
[Seed]: http://msdn.microsoft.com/es-es/library/hh829453(v=vs.113).aspx
[Azure Management Portal]: https://manage.windowsazure.com/
[DbContext]: http://msdn.microsoft.com/es-es/library/system.data.entity.dbcontext(v=vs.113).aspx
[AddOrUpdate]: http://msdn.microsoft.com/es-es/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx