<properties umbracoNaviHide="0" pageTitle="How to Manage SQL Database" metaKeywords="Azure SQL database, SQL database, manage sql database, add logins, connect to sql database" description="Learn how to manage Azure SQL database." linkid="devnav-manage-services-cloud-services" urlDisplayName="Cloud Services" headerExpose="" footerExpose="" disqusComments="1" title="How to Manage SQL Database" authors="" />

Administración de Base de datos SQL
===================================

Este tema muestra cómo realizar tareas de administración sencillas en Base de datos SQL de Azure.

Tabla de contenido
------------------

-   [Conexión con Base de datos SQL en Azure con Management Studio](#connect)
-   [Incorporación de inicios de sesión y usuarios a Base de datos SQL en Azure](#addlogins)

Conexión con Base de datos SQL en Azure con Management Studio
-------------------------------------------------------------

Management Studio es una herramienta administrativa que permite administrar varias instancias y servidores de SQL Server en un espacio de trabajo único. Si ya tiene una instancia SQL Server local, puede abrir una conexión tanto a la instancia local como a un servidor lógico en Azure para realizar las tareas de manera paralela.

Management Studio incluye características que no están actualmente disponibles en el portal de administración, como un comprobador de sintaxis y la posibilidad de guardar scripts y consultas con nombre para volver a usarlos. Base de datos SQL es un extremo de secuencia de datos tubular (TDS). Todas las herramientas que funcionan con TDS, incluido Management Studio, son válidas para las operaciones de Base de datos SQL. Los scripts que desarrolla para el servidor local se ejecutarán en un servidor lógico de Base de datos SQL.

En el siguiente paso, utilizará Management Studio para conectarse a un servidor lógico en Azure. Este paso requiere tener SQL Server Management Studio versión 2008 R2 o 2012. Si necesita ayuda para descargar o conectarse a Management Studio, consulte [Administración de Base de datos SQL con Management Studio](http://www.windowsazure.com/en-us/develop/net/common-tasks/sql-azure-management/) en este sitio.

Antes de poder conectarse, a veces es necesario crear una excepción de firewall que permita realizar solicitudes salientes en el puerto 1433 en el sistema local. Los equipos que están asegurados de forma predeterminada suelen tener el puerto 1433 cerrado.

Configuración del firewall para un servidor local
-------------------------------------------------

1.  En el Firewall de Windows con seguridad avanzada, cree una nueva regla saliente.

2.  Seleccione **Puerto**, especifique TCP 1433, especifique **Permitir la conexión** y asegúrese de que el perfil **Público** esté seleccionado.

3.  Proporcione un nombre significativo, como *AzureSQLDatabase puerto (salida tcp) 1433*.

Conexión con un servidor lógico
-------------------------------

1.  En Management Studio, en Conectar al servidor, asegúrese de que Motor de base de datos esté seleccionado y, a continuación, escriba el nombre del servidor lógico con el formato siguiente: *nombre del servidor*.database.windows.net

    También puede obtener el nombre completo del servidor en el portal de administración, en el panel del servidor, en Administrar dirección URL.

2.  En Autenticación, seleccione **Autenticación de SQL Server** y, a continuación, escriba el inicio de sesión de administrador que creó al configurar el servidor lógico.

3.  Haga clic en **Opciones**.

4.  En Conectar con base de datos, especifique **master**.

Conexión con un servidor local
------------------------------

1.  En Management Studio, en Conectar al servidor, asegúrese de que Motor de base de datos esté seleccionado y, a continuación, escriba el nombre de una instancia local con el formato siguiente: *nombre del servidor*\\*nombre de la instancia*. Si el servidor es local y una instancia predeterminada, escriba el *host local*.

2.  En Autenticación, seleccione **Autenticación Windows** y, a continuación, escriba una cuenta de Windows que pertenezca al rol sysadmin.

Incorporación de inicios de sesión y usuarios a Base de datos SQL en Azure
--------------------------------------------------------------------------

Después de implementar una base de datos, debe configurar los inicios de sesión y asignar permisos. En el siguiente paso, ejecutará dos scripts.

Con el primer script, se conectará con master y ejecutará un script para crear los inicios de sesión. Los inicios de sesión se utilizarán para admitir las operaciones de lectura y escritura y para delegar las tareas de las operaciones, como la capacidad de ejecutar las consultas del sistema sin permisos.

Los inicios de sesión que cree deben ser inicios de sesión de autenticación de SQL Server. Si tiene scripts ya preparados que utilicen identidades de usuario Windows o identidades de notificaciones, esos scripts no funcionarán en Base de datos SQL.

El segundo script asigna permisos de usuario de base de datos. Con este script, se conectará con una base de datos que ya esté cargada en Azure.

Creación de inicios de sesión
-----------------------------

1.  En Management Studio, conéctese con un servidor lógico en Azure, expanda la carpeta Bases de datos, haga clic con el botón secundario en **master** y seleccione **Nueva consulta**.

2.  Utilice las siguientes instrucciones Transact-SQL para crear inicios de sesión. Sustituya la contraseña por una contraseña válida. Seleccione cada una de forma individual y, a continuación, haga clic en **Ejecutar**. Repita la operación con los inicios de sesión restantes.

``` {}
    -- ejecútelo en master, ejecute cada línea por separado
    -- utilice este inicio de sesión para administrar otros inicios de sesión en este servidor
    CREATE LOGIN sqladmin WITH password='<ProvidePassword>'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- utilice este inicio de sesión para crear o copiar una base de datos
    CREATE LOGIN sqlops WITH password='<ProvidePassword>';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
```

Creación de usuarios de bases de datos
--------------------------------------

1.  Expanda la carpeta Bases de datos, haga clic con el botón secundario en **school** y seleccione **Nueva consulta**.

2.  Utilice las siguientes instrucciones Transact-SQL para agregar usuarios de bases de datos. Sustituya la contraseña por una contraseña válida.

``` {}
    -- ejecútelo en una base de datos normal, ejecute cada línea por separado
    -- utilice este inicio de sesión para leer operaciones
    CREATE LOGIN sqlreader WITH password='<ProvidePassword>';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- utilice este inicio de sesión para escribir operaciones
    CREATE LOGIN sqlwriter WITH password='<ProvidePassword>';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- conceda permisos DMV en la base de datos school para "sqlops"
    GRANT VIEW DATABASE STATE to 'sqlops';
```

Visualización y prueba de inicios de sesión
-------------------------------------------

1.  En una ventana de nueva consulta, conéctese con **master** y ejecute la siguiente instrucción:

         SELECT * from sys.sql_logins;

2.  En Management Studio, haga clic con el botón secundario en la base de datos **school** y seleccione **Nueva consulta**.

3.  En el menú Consulta, seleccione **Conexión** y, a continuación, haga clic en **Cambiar conexión**.

4.  Inicie sesión como *sqlreader*.

5.  Copie e intente ejecutar la siguiente instrucción. Debería recibir un error que informe de que el objeto no existe.

         INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
         VALUES (1061, 30, 9);

6.  Abra una segunda ventana de consulta y cambie el contexto de conexión a *sqlwriter*. La misma consulta debería ejecutarse ahora correctamente.

Ya ha creado y probado varios inicios de sesión. Para obtener más información, consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee336235.aspx) y [Supervisar Base de datos SQL de Azure mediante vistas de administración dinámica](http://msdn.microsoft.com/en-us/library/windowsazure/ff394114.aspx).

