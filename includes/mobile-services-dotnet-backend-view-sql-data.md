El último paso opcional de este tutorial es comprobar en la Base de datos SQL asociada con el servicio móvil una revisión de los datos almacenados.

1.  En el Portal de administración de Azure, haga clic en la opción Manage correspondiente a la base de datos asociada con el servicio móvil.

    ![iniciar sesión para administrar la Base de datos SQL][iniciar sesión para administrar la Base de datos SQL]

2.  En el Portal de administración, ejecute una consulta para ver los cambios que realiza la aplicación de la Tienda Windows. La consulta será similar a la siguiente, pero con el nombre de la base de datos en lugar de `todolist`.</p>

        SELECT * FROM [todolist].[todoitems]

    ![consultar en la Base de datos SQL los elementos almacenados][consultar en la Base de datos SQL los elementos almacenados]

    La tabla incluye las columnas Id, \_\_createdAt, \_\_updatedAt y \_\_version. Estas columnas admiten la sincronización de datos sin conexión y se implementan en la clase base [EntityData][EntityData]. Para más información, vea [Introducción a la sincronización de datos sin conexión].

  [iniciar sesión para administrar la Base de datos SQL]: ./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png
  [consultar en la Base de datos SQL los elementos almacenados]: ./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png
  [EntityData]: http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.mobile.service.entitydata.aspx
