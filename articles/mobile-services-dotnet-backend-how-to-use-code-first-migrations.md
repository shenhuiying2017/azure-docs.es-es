<properties pageTitle="How to use Code First Migrations .NET backend (Mobile Services)" metaKeywords="" description="" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Considerations for supporting multiple clients from a single mobile service" authors="glenga" solutions="mobile" writer="glenga" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Modificación del modelo de datos de un servicio móvil back-end .NET

En este tema, se muestra cómo utilizar las Migraciones de Code First de Entity Framework para realizar cambios en el modelo de datos de una base de datos SQL de Azure existente para evitar perder datos existentes. Se asume que ya publicó el proyecto de servicio móvil en Azure, que hay datos guardados en la base de datos y que los modelos de datos remoto y local están sincronizados. En este tema también se describen los inicializadores Code First predeterminados que implementan los Servicios móviles de Azure que se usan durante el desarrollo. Estos inicializadores le permiten realizar cambios de esquema fácilmente sin usar las migraciones Code First cuando no sea necesario para conservar los datos existentes.

## Inicializadores de modelos de datos

La solución Servicios móviles admite dos clases base de inicializador de modelo de datos en un proyecto de servicio móvil de back-end de .NET. Estos inicializadores eliminan y vuelven a crear tablas en la base de datos cuando Entity Framework detecta un cambio de modelo de datos en el [DbContext][]. Estos inicializadores están diseñados para funcionar tanto si el servicio móvil se ejecuta en un equipo local y como si se hospeda en Azure. Ambas clases de base de inicializador eliminan de la base de datos todas las tablas, vistas, funciones y procedimientos del esquema que usa el servicio móvil.

-   Los objetos de esquema **ClearDatabaseSchemaIfModelChanges**
     solo se eliminan cuando Code First detecta un cambio en un modelo de datos. El inicializador predeterminado de un proyecto de back-end de .NET descargado del [Portal de administración de Azure][] hereda elementos de esta clase base.

-   **ClearDatabaseSchemaAlways**:
     Los objetos de esquema se eliminan cada vez que se accede al modelo de datos. Use esta clase base para restablecer la base de datos sin tener que realizar una modificación del modelo de datos.

En el proyecto de inicio rápido descargado, el inicializador Code First se define en el archivo WebApiConfig.cs. Reemplace el método **Seed** para agregar filas iniciales de datos a las tablas nuevas. Para obtener ejemplos de datos de propagación, consulte [Propagación de datos en migraciones][]. Puede usar los inicializadores de modelo de datos Code First al realizar la ejecución en un equipo local. No obstante, se producirá un error en Azure de los inicializadores que intenten eliminar la base de datos porque el usuario no cuenta con los permisos para eliminar la base de datos, que es un comportamiento deseado.

Puede seguir utilizando los inicializadores durante el desarrollo local del servicio móvil. Además, en los tutoriales de back-end .NET se presupone que está utilizando inicializadores. Sin embargo, en aquellos casos en que desee realizar cambios en el modelo de datos y conservar los datos existentes en la base de datos, debe utilizar las Migraciones de Code First.

> [WACOM.NOTE]A la hora de desarrollar y probar el proyecto de servicio móvil con los servicios de Azure en directo, debe utilizar siempre una instancia del servicio móvil destinada a pruebas. No debe utilizar nunca un servicio móvil que se encuentre en fase de producción o que esté siendo utilizado por aplicaciones cliente.

## <a name="migrations"></a>Habilitación de las Migraciones de Code First

Las Migraciones de Code First utilizan un método de instantáneas para generar código que, al ejecutarse, realiza cambios en el esquema de la base de datos. Con las Migraciones, puede realizar cambios incrementales en el modelo de datos y conservar los datos existentes en la base de datos.

> [WACOM.NOTE] Si ya publicó el proyecto de servicio móvil back-end .NET en Azure y el esquema de tablas de la base de datos SQL no concuerda con el modelo de datos actual del proyecto, debe usar un inicializador, eliminar las tablas manualmente o sincronizar el esquema y el modelo de datos antes de intentar la publicación mediante las Migraciones de Code First.

Los siguientes pasos sirven para activar las Migraciones y aplicar los cambios en el modelo de datos en el proyecto, la base de datos local y Azure.

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

    En este punto, la base de datos está sincronizada con el modelo de datos. Si proporcionó datos de inicialización, puede comprobarlo haciendo clic en **Try it out**, **GET tables/todoitem**, **Try this out** y **Send**. Para obtener más información, consulte [Propagación de datos en migraciones][].

8.  Ahora haga un cambio en el modelo de datos, como agregar una nueva propiedad UserId al tipo TodoItem, vuelva a compilar el proyecto y, a continuación, en el Administrador de paquetes ejecute el siguiente comando:

        PM> Add-Migration NewUserId

    De esta forma, se crea una nueva migración denominada *NewUserId*. Se agrega un nuevo archivo de código, que implementa este cambio, a la carpeta de migraciones.

9.  Presione F5 de nuevo para reiniciar el proyecto de servicio móvil en el equipo local.

    La migración se aplica a la base de datos, que vuelve a estar sincronizada con el modelo de datos. Si proporcionó datos de inicialización, puede comprobarlo haciendo clic en **Try it out**, **GET tables/todoitem**, **Try this out** y **Send**. Para obtener más información, consulte [Propagación de datos en migraciones][].

10. Vuelva a publicar el servicio móvil en Azure, y, a continuación, ejecute la aplicación cliente para obtener acceso a los datos y compruebe que estos se cargan sin errores.

11. (Opcional) En el [Portal de administración de Azure][], seleccione el servicio móvil, haga clic en la pestaña **Configurar** y, por último, haga clic en el vínculo **Base de datos SQL**.

    ![][]

    De este modo, se abre la página de la base de datos SQL del servicio móvil.

12. (Opcional) Haga clic en **Manage**, inicie sesión en el servidor de la base de datos SQL, a continuación haga clic en **Design** y compruebe que los cambios en el esquema estén aplicados en Azure.

    ![][1]

## <a name="seeding"></a>Propagación de datos en migraciones

Puede hacer que las Migraciones agreguen datos de inicialización a la base de datos cuando se ejecuta una migración. La clase **Configuration** incluye un método **Seed** que puede reemplazar para insertar o actualizar datos. El archivo de código Configuration.cs se agrega a la carpeta de migraciones cuando están habilitadas las Migraciones. Estos ejemplos muestran cómo reemplazar el método [Seed][] para propagar datos a la tabla **TodoItems**. Se llama al método [Seed][] una vez realizada la migración a la versión más reciente.

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

Este código llama al método auxiliar de extensión [AddOrUpdate][] para agregar datos de inicialización a la nueva columna UserId. Al utilizar [AddOrUpdate][], no se crean filas duplicadas.

<!-- Anchors -->
<!-- Images -->
<!-- URLs -->

  [DbContext]: http://msdn.microsoft.com/es-es/library/system.data.entity.dbcontext(v=vs.113).aspx
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [Propagación de datos en migraciones]: #seeding
  []: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png
  [1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png
  [Seed]: http://msdn.microsoft.com/es-es/library/hh829453(v=vs.113).aspx
  [AddOrUpdate]: http://msdn.microsoft.com/es-es/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx
